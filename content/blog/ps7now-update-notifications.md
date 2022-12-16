+++
categories = ["PowerShell"]
date = 2020-03-12T20:47:55Z
description = ""
draft = false
thumbnail = "/2020/03/birmingham-museums-trust-p49Q9HRPN7I-unsplash.jpg"
slug = "ps7now-update-notifications"
summary = "If you're not stalking the PowerShell team on Twitter, or obsessively refreshing the PowerShell repo on GitHub, how are you meant to be aware of new releases?"
tag = ["PowerShell"]
title = "Staying up to Date on PowerShell Releases with Update Notifications - #PS7Now"

+++


<p class="note"><em>This post is part of the <a href="https://twitter.com/search?q=%23PS7Now" target="_blank">#PS7Now</a> PowerShell Blog Week celebrating the general availability launch of <a href="https://docs.microsoft.com/en-us/powershell/" target="_blank">PowerShell 7.</a></em><br /><br />
<em>There's a lot of great content coming out this week from a number of different authors, so make sure you keep an eye on the <a href="https://twitter.com/search?q=%23PS7Now" target="_blank">hashtag</a> over on Twitter or checkout the table at the bottom of this post for direct links!</em></p>

During this week of blog posts about the latest PowerShell [release](https://github.com/powershell/powershell/releases), you'll have read a lot about many of the new features it delivers and with any luck you've been sold on it and [installed](__GHOST_URL__/2020/03/08/ps7now-installing/)/updated it.

But now you're itching for the next release and the next round of new features and bug fixes, right? How on earth are you meant to know when those are out? Are we going to have to do a blog week for each?

While it's always fun to write about one of your favorite technologies, these are a lot of effort to put together (thanks [Jeff](https://twitter.com/JeffHicks)!). Luckily, PowerShell 7 shipped with an answer: [Update Notifications](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_update_notifications?view=powershell-7).

{{< figure src="/2020/03/image.png" >}}

Once in a given 24 hours window, PowerShell checks to see if there is a new version available. This check happens three seconds (or more) after you've opened PowerShell for the first time in that window to limit any impact on performance.

Any subsequent time you open up a PowerShell session you'll see the notification itself.

## What Notifications Will I See?

By default, a generally available (GA) release will only be notified about the next GA release. Any preview and release candidate will be notified about all preview and GA releases.

What's really cool, especially if you were only allowed to install PowerShell 7 in your environment because it has Long Term Support (LTS), is that you can specify that you only want to be notified about the next LTS release and not the releases in between.

<p class="note">Support is complicated, there's <a target="_blank" href="https://docs.microsoft.com/en-nz/powershell/scripting/powershell-support-lifecycle?view=powershell-7#lifecycle-of-powershell-7">a page</a> with info specifically for PowerShell.<br /><br />
The short and simplified version is that PowerShell 7's LTS is locked to that of .NET Core 3.1's release on 3 December 2019. Support lasts through until 3 December 2022.<br /><br />
"Current" (the GA releases that aren't LTS) releases are supported until three months after the release of the next current release.</p>

In order to set this, you'll want to configure an [environment variable](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables?view=powershell-7).

```powershell
$Env:POWERSHELL_UPDATECHECK = 'LTS'
```

Note though that this only applies to your current session, so for that to work you'll want to add it to your [profile](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7), or make it a [persistent variable](https://trevorsullivan.net/2016/07/25/powershell-environment-variables/).

## Ergh, How Do I Turn Them Off?

Spoilsport, but I won't begrudge you the option! Much like setting the environment variable for only getting LTS notifications, you can also specify that you don't want any. You do this by setting the variable to "Off".

```powershell
$Env:POWERSHELL_UPDATECHECK = 'Off'
```

In the interest of completeness, you can also set this to "Default" to set everything back to how it was out of the box.

```powershell
$Env:POWERSHELL_UPDATECHECK = 'Default'
```

This has the same effect as the environment variable not being defined at all, so if you do persist it and want to go back to default you can just remove the variable completely.

## What's Happening Under the Hood?

There are three JSON endpoints out there, the one that is checked depends on the release used and the previously discussed environment variable.

* LTS Only Preference = [https://aka.ms/pwsh-buildinfo-lts](https://aka.ms/pwsh-buildinfo-lts)
* Default Preference on GA Release (Stable) = [https://aka.ms/pwsh-buildinfo-stable](https://aka.ms/pwsh-buildinfo-stable)
* Default Preference on Preview Release = [https://aka.ms/pwsh-buildinfo-preview](https://aka.ms/pwsh-buildinfo-preview)

_Note: Preview installs actually check_ **_both_** _the stable and preview endpoints. You won't see GA info by querying the preview uri._

Because this data is available to anyone and everyone, you can really get your geek on and build your own update notifications. Maybe display a [toast notification](https://www.powershellgallery.com/packages/PoshNotify) in the OS or send out an [automatic tweet](https://www.powershellgallery.com/packages/PSTwitterAPI)?

I'll leave that one to your imagination.

## That's a Wrap

This is my third and final post for this blog week celebrating the release of PowerShell 7. It's been a blast, both in writing these posts and getting involved with the other authors on Twitter.

Make sure you go check out everything that's been happening via the [#PS7Now](https://twitter.com/hashtag/PS7Now?f=live) hashtag or click through to each of the authors here:

| Author         | Twitter                                               | Blog                                                                   |
|----------------|-------------------------------------------------------|------------------------------------------------------------------------|
| Adam Bertram   | [@adbertram](https://twitter.com/adbertram)           | [https://adamtheautomator.com](https://adamtheautomator.com)           |
| Dan Franciscus | [@dan_franciscus](https://twitter.com/dan_franciscus) | [https://winsysblog.com](https://winsysblog.com)                       |
| Dave Carroll   | [@thedavecarroll](https://twitter.com/thedavecarroll) | [https://powershell.anovelidea.org](https://powershell.anovelidea.org) |
| Jeff Hicks     | [@JeffHicks](https://twitter.com/JeffHicks)           | [https://jdhitsolutions.com](https://jdhitsolutions.com)               |
| Jonathan Medd  | [@jonathanmedd](https://twitter.com/jonathanmedd)     | [https://www.jonathanmedd.net](https://www.jonathanmedd.net)           |
| Josh Duffney   | [@joshduffney](https://twitter.com/joshduffney)       | [http://duffney.io](http://duffney.io)                                 |
| Mike Kanakos   | [@MikeKanakos](https://twitter.com/MikeKanakos)       | [https://www.commandline.ninja](https://www.commandline.ninja)                 |
| Prateek Singh  | [@singhprateik](https://twitter.com/singhprateik)     | [https://ridicurious.com](https://ridicurious.com)                     |
| Thomas Lee     | [@doctordns](https://twitter.com/doctordns)           | [https://tfl09.blogspot.com](https://tfl09.blogspot.com)               |

## Credit

Hero image by [Birmingham Museums Trust](https://unsplash.com/@birminghammuseumstrust?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

