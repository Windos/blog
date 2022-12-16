+++
categories = ["PowerShell"]
date = 2017-03-05T12:54:00Z
description = ""
draft = false
slug = "trello-report"
summary = "Nothing distracts a room full of nerds faster than technology."
tag = ["PowerShell"]
title = "Reporting on Trello Boards with PowerTrello"

+++


At work we've recently ramped up our use of [Trello](https://trello.com/) to manage our tasks. It's great, and I recommend anyone that works with lists of any description to give it a look. The only problem is, how does one show off the work the team has coming up, what they're working on, and what they have ticked off?

You could just bring the Trello board up on a TV or laptop, but nothing distracts a room full of nerds faster than technology.

Print the board(s) directly? Sure, but the formatting could be… interesting.

## **There's a PowerShell module for that!**

I sound like a broken record at times, but there really is a PowerShell module to help with this.

I've used [Adam Bertram's](https://twitter.com/adbertram) module [PowerTrello](https://github.com/adbertram/PowerTrello) for a number of Trello related tasks, including importing a JSON dump into the team board and, more recently, generating a report of all the cards on our team board.

It's not scheduled or anything yet, so at the moment it's a case of running the script, opening the report and printing a couple of copies off to bring along to the team meetings.

The script was useful for us, and may be useful for you, or trigger an idea for your own utilization of PowerTrello, so here we go:

## **First things first**

There's some preliminary things to do before diving into automating Trello. Obviously the first thing on the list is installing the module. There's a few ways to do this, and Adam include some methods on the GitHub repo; I'll include the one I used here:

```powershell
$uri = 'https://raw.githubusercontent.com/adbertram/PowerTrello/master/Trello.psm1'
$sysModulePath = 'C:\Program Files\WindowsPowerShell\Modules'
if ($sysModulePath -in $env:PSModulePath.Split(';')) {
    $fileName = $uri | Split-Path -Leaf
    $null = mkdir "$sysModulePath\$($fileName.Trim('.psm1'))"
    $filePath = "C:\Program Files\WindowsPowerShell\Modules\$($fileName.Trim('.psm1'))\$($uri | Split-Path -Leaf)"
    Invoke-WebRequest -Uri $uri -OutFile $filePath
} else {
    throw 'Unable to download PowerTrello into system module path. Please put it there manually.'
}

```

First hurdle done! But there's some more prep work to sort.

Head over to Trello, and generate an [API Key](https://trello.com/app-key) (Make sure you're signed into to Trello before heading to that URL), and copy it.

Fire up an elevated PowerShell console (i.e. Run as Administrator), and pop your API Key into a variable:

```powershell
$apikey = ‘pastedkeyhere'

```

Now request a token, using the relevant command in the PowerTrello module:

```powershell
$token = Request-TrelloAccessToken –ApiKey $apikey

```

Save the key and token, this creates entries in your registry for future use:

```powershell
Set-TrelloConfiguration –ApiKey $apikey –AccessToken $token

```

Going forward, you can surface the token and key for use within the module by running:

```powershell
Get-TrelloConfiguration

```

## **Now script away!**

There's a bunch of commands included with PowerTrello, the names of which generally give you a good idea of what they do. Dive in and check them out, run a couple of commands and see what come back.

As for my report, the first couple of lines are simply importing the module explicitly and grabbing the configuration we sorted earlier:

```powershell
Import-Module Trello
Get-TrelloConfiguration

```

Next we grab the relevant data from Trello, first selecting the proper board by name, then all the cards, lists and member on that board:

```powershell
$Board = Get-TrelloBoard -Name 'Team Tasks'
$Cards = Get-TrelloCard -Board $Board
$BoardLists = Get-TrelloBoardList -BoardId $Board.id
$Members = Get-TrelloMember -BoardId $Board.id

```

Now we'll do a bit of grouping. On our board we split cards into lists for "To Do", "Doing", "Waiting", "Done" and finally a register for things we know are on the horizon but aren't actively putting any brain power behind yet.

```powershell
$ToDoCards = $Cards.Where({ $_.idList -eq $BoardLists.Where({ $_.Name -eq 'To Do' }).id })
$DoingCards = $Cards.Where({ $_.idList -eq $BoardLists.Where({ $_.Name -eq 'Doing' }).id })
$WaitingCards = $Cards.Where({ $_.idList -eq $BoardLists.Where({ $_.Name -eq 'Waiting' }).id })
$DoneCards = $Cards.Where({ $_.idList -eq $BoardLists.Where({ $_.Name -eq 'Done' }).id })
$JobRegisterCards = $Cards.Where({ $_.idList -eq $BoardLists.Where({ $_.Name -eq 'Job Register' }).id })

```

Just to make things easy as far as pretty headings go when building up the report, I put the cards into an array, with corresponding labels, and an integer to counter through each of them in a loop:

```powershell
$AllCards = @($DoneCards, $DoingCards, $WaitingCards, $ToDoCards, $JobRegisterCards)
$CategoryLabels = @('Done', 'Doing', 'Waiting', 'To Do', 'Job Register')
$x = 0

```

Now we start on the report. The report itself is HTML, so I dump the framework of that and the style elements into it as a start:

```powershell
$Report = "<html><head><style>"
$Report += "BODY{font-family:arial;}"
$Report += "H2{font-size: 22px;padding:3px 3px 3px 6px;border-left:3px solid #c6c1b8;background:#f2efe9;display:block;}"
$Report += "</style></head><body><h1>Team Task List</h1>"

```

And now we start iterating through the array that contains the lists of cards. We'll look at each individual card, grabbing its name, who is it assigned to (separated by commas if there is more than one member on the card) and the description of the card if it is set.

```powershell
foreach ($Category in $AllCards) {
    $Report += "<h2>$($CategoryLabels[$x])</h2>"

    foreach ($Card in $Category)     {
        $Report += "<p>$($Card.Name)"

        if ($Card.idMembers.count -gt 0)         {
            $Assignees = $Members.Where({ $_.Id -in $Card.idMembers }).fullName.foreach({ $_.Split(' ')[0] }) -join ', '
             $Report += "<br>Assigned to: $Assignees"
        }

        if ($Card.desc -ne '' -and $Card.desc -ne $null)         {
             $Report += "<br>$($Card.desc)"
        }

        $Report += "</p>"
    }

    $x += 1
}

```

Finally, we close off the HTML, and save the results to disk:

```powershell
$Report += '</body></html>'
$Report | Out-File -FilePath c:\temp\task.html

```

## **The end result**

For obvious reasons, I can't show the full text of a real report, but here's a censored version:

{{< figure src="/2018/05/report.png" >}}

I'm not designer, so I'm sure playing around with the HTML styles could do a lot for the output but I'm happy with it as a means of bringing the info to team meetings without the distraction that a laptop or TV would cause.

If you do any sort of Trello automation, let me know here or on [Twitter](https://twitter.com/WindosNZ)!

