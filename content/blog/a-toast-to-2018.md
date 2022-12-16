+++
categories = ["PowerShell", "Meta"]
date = 2018-12-30T19:20:59Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/12/joe-leahy-391693-unsplash.jpg"
slug = "a-toast-to-2018"
summary = "The community, in and of itself, made 2018 for me. If you haven’t already gotten engaged in it… put it on your list for 2019. You wont regret it."
tags = ["PowerShell", "Meta"]
title = "A Toast to 2018"

+++


New Zealand is one of the first countries to see in the New Year. Though I’m not usually the type of person to specifically stay awake past midnight to see the calendar change myself. It has, however, got me into a mood to look back on the past year.

In the next day or two, I’ll be doing my resolutions for 2019 (including looking back at [this year’s ones](https://king.geek.nz/2018/01/03/resolution-2018/)), so this isn’t going to be along those lines… instead I wanted to touch on one thing that’s been really important to me.

## ****The PowerShell Community****

You all rock!

The internet, and particularly Twitter, _can_ be toxic. But the online PowerShell community (as well as a few other tech communities… looking at you SQL) is a shining example of what online communities should be.

This year I’ve seen many people start new blogs… a lot of these after some “gentle encouragement” from fellow summit attendees. Every announcement that I’ve seen about a new blog has been met by lots of retweets and general signal boosting. Everyone in the community is out to see everyone else do well. We all want to see everyone succeed, and that’s an awesome environment to be a part of.

Having issues with a script or some obscure concept? Reach out to the community and you’ll either get a well thought out answer, an interesting discussion that will put you on the right path, or an introduction to one of the “big guys” at Microsoft.

Thinking of talking at a conference? There’s a large subset of the community willing to help you refine your CFP to help make your idea shine. There’s also dozens of community managed PowerShell User Groups that’re always eager to host first time speakers if you’re wanting to cut your teeth on a smaller audience.

The community, in and of itself, made 2018 for me. If you haven’t already gotten engaged in it… put it on your list for 2019. You wont regret it.

## ****A (Burnt)Toast for 2019****

Just because I love a good pun, and I feel bad for making a post as “PowerShell” without any code, I wanted to quickly demo using [BurntToast](https://powershellgallery.com/packages/BurntToast) to “Toast” the New Year.

As this is using a Scheduled Job for timing (and persistence), you’ll need to run the following in an administrative PowerShell session.

```powershell
$ToastScript = {
    $Fireworks = 'https://pngimage.net/wp-content/uploads/2018/06/fireworks-icon-png-9.png'
    New-BurntToastNotification -Text 'Happy New Year!', 'It''s officially 2019' -Sound Call10 -AppLogo $Fireworks
}

$Trigger = New-JobTrigger -Once -At (Get-Date -Year 2019 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0)
Register-ScheduledJob -Name 'NewYearToast' -Trigger $Trigger -ScriptBlock $ToastScript

```

