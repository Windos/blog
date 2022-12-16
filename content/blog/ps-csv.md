+++
categories = ["PowerShell", "Nugget"]
date = 2018-04-18T20:30:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/3500720731_fd6be590fe_b.jpg"
slug = "ps-csv"
summary = "CSVs are pretty handy, right? And there is a cmdlet that'll handle this for you!"
tags = ["PowerShell", "Nugget"]
title = "All I Want Is a Damned CSV!"

+++


Spurred on by a common issue that comes up on Twitter from time to time, this will be a rather short post about CSVs and PowerShell.

## **What's the Issue?**

Let's say you have an array of strings.

```powershell
$Servers = @(
    'AD1',
    'FILE1',
    'DB1',
    'APP1'
)

```

You want to save these to disk. CSVs are pretty handy, right? And there is a cmdlet that'll handle this for you!

```powershell
$Servers | Export-Csv -Path C:\temp\servers.csv

```

What is this nonsense?! That's not what you had in your array!

```
#TYPE System.String
"Length"
"3"
"5"
"3"
"4"

```

## **Demystifying the Nonsense**

This comes down to understanding what `Export-Csv` is actually doing. In the strictest sense, it's taking the object(s) you're giving it and writing that object's properties into a CSV file.

So why doesn't this work when you've got a bunch of strings?

Take a look at what you're actually sending down the pipeline:

```powershell
PS C:\> $Servers | Get-Member -MemberType Properties


   TypeName: System.String

Name   MemberType Definition       
----   ---------- ----------       
Length Property   int Length {get;}

```

Yup, you're reading that correctly. Strings only have one property, and that's the length. So when you export strings to a CSV you're actually asking for the length of each of those strings.

## **Ok, So What Do I Do?**

Well, given you're just dealing with a simple array of strings consider whether you actually need a CSV. Consider instead just writing that array directly to a file:

```powershell
$Servers | Set-Content -Path C:\temp\servers.txt

```

When you need to read this back in simply grab it back:

```powershell
$ServersFromFile = Get-Content -Path C:\temp\servers.txt

```

## **You Don't Understand, I Really Need a CSV!**

Fair enough, I won't dig into your business. Consider what you'd normally see at the top of a CSV file: headings.

You need to decide "what" these strings represent. Are they usernames, UNC paths, or server names?

Now you're going to want to make an object for each of the entries in your array with your string being a property of that object (named to reflect what the strings actually are.)

```powershell
$Servers | foreach {[PSCustomObject] @{Server = $_}} | Export-Csv -Path C:\temp\servers.csv

```

Now you should see what you actually want in the CSV file.

```
#TYPE System.Management.Automation.PSCustomObject
"Server"
"AD1"
"FILE1"
"DB1"
"APP1"

```

## **Wrap Up**

The next question that always comes up with CSV files is, "what the hell is that type information about?" That's a topic for another post... and I'll actually refer you over to [Dan Blank](https://danblank.co.uk/2018/04/05/export-csv-pshelp/) to hear a bit about that.

What other gotcha's did/do you run into? Hit me up in the comments or on [Twitter](https://twitter.com/WindosNZ)!

Also, remember that if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

