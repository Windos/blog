+++
categories = ["PowerShell", "BurntToast"]
date = 2018-02-03T18:45:00Z
description = ""
draft = false
featured_image = "/2018/05/thumb-19.jpg"
images = ["/2018/05/thumb-19.jpg"]
slug = "burnttoast-appid-installer"
summary = "I can't tell you how happy I am to be writing this post."
tag = ["PowerShell", "BurntToast"]
title = "BurntToast + AppId = It's Working!"
+++


I can't tell you how happy I am to be writing this post.

AppId's have been a [thorn in my side](https://king.geek.nz/2017/10/09/burnttoast-no-appid/) for a long time. I've been using a loophole to get toasts displayed which recent versions of Windows 10 closed. This resulted in my removing the ability to easily set a custom AppId.

The fix in the short term was to hard code [BurntToast](https://www.powershellgallery.com/packages/BurntToast)'s default AppId to that of powershell.exe. This allowed toasts to be displayed again, but in the Action Center they would appear greyed out… not nice.

That's no longer the case.

## **Inspiration**

First, I'd like to thank [Adam Driscoll](https://twitter.com/adamdriscoll) for providing the thread to pull on to solve this issue.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Cool or nah? Simple, little installers from <a href="https://twitter.com/hashtag/PowerShell?src=hash&amp;ref_src=twsrc%5Etfw">#PowerShell</a> <a href="https://t.co/inCCnEukgO">pic.twitter.com/inCCnEukgO</a></p>&mdash; Adam Driscoll (@adamdriscoll) <a href="https://twitter.com/adamdriscoll/status/957059357694414848?ref_src=twsrc%5Etfw">January 27, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

I'd looked into [WiX](http://wixtoolset.org/) in the past. I didn't instantly get my head around it and moved on. Adam's tweet (and [answer](https://twitter.com/adamdriscoll/status/957251989259374592) to my AppId question) prompted me to give it another look.

## **BurntToast Installer**

Over on [GitHub you'll find an msi](https://github.com/Windos/BurntToast/releases/tag/v0.0.1) you can use to "install" a dummy application for BurntToast complete with an AppId laden shortcut in the start menu.

The installer isn't signed, so you will get a warning when you run it.

It'll put a PNG file into "**C:\Program Files\BurntToast\**" and throw a shortcut to that file into your start menu.

To actually use it, find the directory where the BurntToast module lives and open **config.json**. Change the AppId in this file to "**King.Geek.NZ!BurntToast**"

{{< figure src="/2018/05/config.png" >}}

If you don't know where the module is installed, run this:

```powershell
Get-Module -Name BurntToast -ListAvailable | Select -Property ModuleBase

```

**Proof Is in the (Bread and Butter) Pudding**

And here's the result:

{{< figure src="/2018/05/proof.jpg" >}}

## **Next Steps**

The next version of this installer will likely change the configured AppId in BurntToast automatically. And the next version of BurntToast will include a better method for updating the default AppId without having to dig up the json file.

My next blog post will be on how you can use the WiX Toolset to create your own fully working AppId, for those situations when the name BurntToast isn't suitable.

