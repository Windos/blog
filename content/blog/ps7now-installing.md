+++
categories = ["PowerShell"]
date = 2020-03-07T23:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2020/01/IMG_3567.jpeg"
slug = "ps7now-installing"
summary = "PowerShell 7 is out now! The first step in being able to add it to our toolbox is to install it so let's check out our options."
tags = ["PowerShell"]
title = "Getting Your Hands on PowerShell 7 - #PS7Now"

+++


<p class="note"><i>This post is part of the <a href="https://twitter.com/search?q=%23PS7Now" target="_blank">#PS7Now</a> PowerShell Blog Week celebrating the general availability launch of <a href="https://docs.microsoft.com/en-us/powershell/" target="_blank">PowerShell 7.</a></i><br /><br />
<i>There's a lot of great content coming out this week from a number of different authors, so make sure you keep an eye on the <a href="https://twitter.com/search?q=%23PS7Now" target="_blank">hashtag</a> over on Twitter or checkout the table at the bottom of this post for direct links!</i></p>

It's finally here!

It was April of 2019, just after the Generally Available release of PowerShell Core 6.2, when the PowerShell team [announced](https://devblogs.microsoft.com/powershell/the-next-release-of-powershell-powershell-7/) that the next release of would be known as PowerShell 7.

There were two things that surprised people in that announcement, the first being that the next version number wouldn't just increment up to 6.3. Secondly, where'd that quantifier "Core" disappear to?

Since then, we've had six preview releases and 2 release candidates. There's been a huge number of community contributions and epic work from the PowerShell team.

Thanks to all the effort put in by so many people, Windows admins that have been bound to Windows PowerShell 5.1 can finally make the move over to the opensource version of their favorite (I assume!) management tool. This move allows them to benefit from all the new features that have been built in since development shifted from being focused solely on Windows. This should be reasonably frictionless[[1]](#footnote-one) due to greater compatibility (compared to 6.2).

But before all of the new features are explored by others throughout this blog week, we'll have a look at getting it installed.

## Install Manually

Our first option for installing PowerShell 7 is to grab the relevant installer from [GitHub](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.0). Chances are if you're installing on Windows you'll want to grab the x86 or x64 MSI file. Download and run that, click through the wizard and you're ready to rock.

If you're wanting to do a manual install on Linux or Mac... I'm not sure this is the best path to take. Instead, check out [the docs](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-7) about how to install on your distribution of choice.

_Note, the install does it's best to remove any existing installs of PowerShell Core 6.x, but will leave PowerShell 7 Preview installs untouched. You may want to go ahead and remove the preview installs yourself if you were following them._

### It's Portable

I wanted to call out here that PowerShell 7 is technically portable. The GitHub releases page has zipped versions of the Windows releases which you can extract and... put anywhere and then run `pwsh.exe`.

PowerShell 7 won't be on your path by default if you go this route but it does give you complete control.

## Scripted Install

There are a pair of scripts available to automatically install the latest PowerShell version available.

On Windows, run the following command in a PowerShell console:

```powershell
iex "& { $(irm https://aka.ms/install-powershell.ps1) } -UseMSI"
```

In the interest of completeness, and because I hate leaving aliases there let's just note the two in that snippet:

* `iex` = `Invoke-Expression`
* `irm` = `Invoke-RestMethod`

This time, I can be a little more helpful for those on Linux. While it's not the same script, it's just as simple to run:

```sh
wget https://aka.ms/install-powershell.sh; sudo bash install-powershell.sh
```

Execute this in your terminal of choice, unlike the Windows option you don't already need PowerShell.

## The Microsoft Store?!

The newest option that's been (being?) introduced for PowerShell 7 is the ability to install it as an app from the Microsoft store.

To get it, search for PowerShell and install it. The app will be from "Microsoft Corporation" and be under the developer tools category. Remember, there's no "Windows" or "Core" in the name anymore... just PowerShell.

At the time of writing the live version of PowerShell 7 isn't yet available, but I can attest to the Preview working rather well.

{{< figure src="__GHOST_URL__/content/images/2020/02/image-1.png" >}}

At least during the preview, the app was able to live alongside the MSI version, which in turn can happily live alongside Windows PowerShell.

In practice, I found it better to choose either the store version or the MSI version as I wasn't always 100% sure which I was opening (you can tell, you just need to pay closer than normal attention.)

The benefit to installing from the store is that you'll get automatic updates as they are released, though it's possible they may be slower (due to verification of the packages) than the general releases to GitHub.

The preview versions did have some issues related to resource virtualization. The hope is that this has been resolved, but we won't know for sure until the next version is up on the Store.

## One Final Option

There's one last option I wanted to include, as I know [someone](https://twitter.com/steviecoaster) would give me a hard time if I left it out: [Chocolatey](https://chocolatey.org/).

If you work on Windows and haven't heard about and installed Chocolatey, [fix that](https://chocolatey.org/install).

It's a tool that allows you to install, update, and otherwise manage software on Windows much like our friends on Linux can with their package managers.

To install [PowerShell 7 using Chocolatey](https://chocolatey.org/packages/powershell-core), run:

```cmd
choco install powershell-core
```

You'll note that the command mentions "PowerShell Core", that's a hangover from this package being how the previous releases were made available and keeping that name allows existing users to easily update.

It's possible that this will change to just `powershell` in the future or that the more "correct" name is added in addition.

## More to Come

Regardless of the method chosen, you'll now be up and running on PowerShell 7. Follow the [#PS7Now](https://twitter.com/search?q=%23PS7Now&f=live) hashtag on Twitter for more about this exciting release or check out the other blog week authors directly.

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

Hero image by [Erin King](http://livinglifeundecided.com/)

## Footnotes

<p>
    <a name="footnote-one">1.</a> Yes, there are some scripts that won't work in PS7 or will require some work. Don't feel like you <i>have</i> to transition completely and never look back.
</p>



