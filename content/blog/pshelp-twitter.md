+++
categories = ["PowerShell", "BurntToast"]
date = 2018-03-19T19:00:00Z
description = ""
draft = false
featured_image = "/2018/05/pexels-photo-167964.jpeg"
images = ["/2018/05/pexels-photo-167964.jpeg"]
slug = "pshelp-twitter"
summary = "If you're looking for some help, try #PSHelp. If you've got knowledge to share (and you do), make sure you jump in and get involved!"
tag = ["PowerShell", "BurntToast"]
title = "Need PowerShell Help? Use #PSHelp!"
+++


Lately, I've been thinking about how much I [enjoy](https://king.geek.nz/2018/03/12/where-are-you/#pstweetchat) taking part in the monthly [#PSTweetChat](https://twitter.com/search?f=tweets&vertical=default&q=%23PSTweetChat&src=typd) event on Twitter and how awesome the PowerShell community on there is (seriously, y'all rock!)

If possible, I'd love to try and help steer that amazing community towards a hashtag specifically for helping with short (hopefully) PowerShell problems.

## **There's Always a Hashtag**

As it turns out, there's already a hashtag which sees some use: [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23PSHelp&src=typd)

I've added this to my TweetDeck layout and will be keeping an eye on it from now on. If you're looking for help with a PowerShell issue, fire off a tweet with that hashtag and I'll do my best to try and help out.

{{< figure src="/2018/05/tweetdeck.png" >}}

Obviously, I'm not all knowing. I have my slither of technology that I focus on, or have experience with via my day job, and so I hope that others will start keeping an eye on the hashtag as well.

Trust me, you may not realize it, but I'm sure you **can** answer someone's question and be their hero for a day.

## **Why Not Stack Overflow?**

Don't get me wrong, I have a soft spot for Stack Overflow and [associated sites](https://stackexchange.com/users/56811/windos?tab=accounts). But it isn't the best site for more informal, short, questions. And it's certainly not the place for any conversations that may spring up following on from a question.

The other issue is, for a topic as broad as PowerShell it _can_ be hard to know which site your question belongs on: Stack Overflow, Super User, or Server Fault?

I'm on Twitter anyway, and no question is too off topic for that forum

## **Where's the Code?!**

You've obviously been here before! I can't write a post like this without having a bit of PowerShell flavored fun. Below is a snippet that has been modified from a [previous post](https://king.geek.nz/2017/03/20/crouton-stackwatch/) (coincidentally, that post was about getting toast notifications for new PowerShell questions on Stack Overflow.) This time, however, the script is keeping an eye on #PSHelp on Twitter.

You'll need to have [BurntToast](https://powershellgallery.com/packages/BurntToast) installed for this to work.

Note that this code is intended to be run once. Parts of it will throw an error if you run is a second time, specifically creating a registry key and registering the scheduled job.

```powershell
# This needs to be run in an elevated PowerShell session.

# Setup a 'scratch' location in the registry, to prevent re-alerting on the same tweet.
New-Item -Path 'HKCU:\SOFTWARE\PowerShell-Scratch'
Set-ItemProperty -Path 'HKCU:\SOFTWARE\PowerShell-Scratch' -Name 'PSHelp-LastId' -Value 'FirstRun'

# Define the job block whick will be run on a schedule via a Scheduled Job
$JobBlock = {
    $URL = 'https://queryfeed.net/twitter?q=%23pshelp&title-type=user-name-both&geocode=&omit-retweets=on'
    $RSS = Invoke-RestMethod -Uri $URL

    # The Tweet URL acts as the Id, the Id from the feed and the Id in the registry are compared to see if
    # the latest Tweet has been toasted or not.
    $LastNotifyId = Get-ItemPropertyValue -Path 'HKCU:\SOFTWARE\PowerShell-Scratch' -Name 'PSHelp-LastId'
    $LatestId = $RSS[0].link

    if ($LastNotifyId -ne $LatestId) {
        Import-Module -Name BurntToast -Force

        $Title = $RSS[0].title.'#cdata-section'

        # Display a Toast Notification, with a button to open the Tweet.
        $Button = New-BTButton -Content 'Open' -Arguments $LatestId
        New-BurntToastNotification -Text 'New PSHelp Question on Twitter', "From: $Title" -Button $Button

        Set-ItemProperty -Path 'HKCU:\SOFTWARE\PowerShell-Scratch' -Name 'PSHelp-LastId' -Value $LatestId
    }
}

# Register the scheduled job, it will repeat until the end of time and check Twitter every five minutes.
$Trigger = New-JobTrigger -Once -At "12:00" -RepetitionInterval (New-TimeSpan -Minutes 5) -RepeatIndefinitely
Register-ScheduledJob -Name StackWatch-PowerShell -Trigger $Trigger -ScriptBlock $JobBlock -Credential domain\user

```

## **Dive In!**

That's all I've got for you today. If you're looking for some help, try #PSHelp. If you've got knowledge to share (and you do), make sure you jump in and get involved!

If you're on the fence, hang out anyway. You might be drawn into an interesting conversation.

