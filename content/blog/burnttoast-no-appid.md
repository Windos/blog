+++
categories = ["PowerShell", "BurntToast"]
date = 2017-10-08T10:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-11.jpg"
slug = "burnttoast-no-appid"
summary = "I hate reducing customizability, just as much as I hate making breaking changes in my projects."
tag = ["PowerShell", "BurntToast"]
title = "BurntToast Breaking Change: Hiding Customizable AppID"

+++


I hate reducing customizability, just as much as I hate making breaking changes in my projects.

Unfortunately, [BurntToast](https://www.powershellgallery.com/packages/BurntToast) has been making use of a bug to display Toast Notifications in Windows 10 despite it not being a UWP app. The [Fall Creators Update appears](https://github.com/Windos/BurntToast/issues/20) to be patching this bug and people running Insider builds have already noted that BurntToast isn’t functioning like they’re used to.

## **What was the bug?**

First, I should point out that [Microsoft’s guidance](https://msdn.microsoft.com/en-us/library/windows/desktop/hh802762(v=vs.85).aspx) has always been that for a desktop application (or in this case a script) to display a Toast Notification, you must have a shortcut in the start menu with an _AppUserModeId_ (Referred to in this post as _AppId_.)

This AppId is then provided when submitting a Toast.

It’s a touch, erm, difficult to create these shortcuts. Especially if you want to have customizable display names and the like.

Up until now, I could get Toasts to display without a shortcut simply by registering an entry in the registry. For those that care about specifics, that location is:

```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Notifications\Settings

```

It should be noted, that the entries here are still important, and determine whether something goes to the Action Centre when they time out and what priority they get… it’s just that a matching start menu shortcut is also need.

## **The breaking change**

To get things working reliably when the Fall Creators Update rolls out, I’ll be changing the default AppId to PowerShell’s.

I’ll also be removing the `AppId` parameter from the `New-BurntToastNotification` function.

It will still be possible to supply your own AppId using the “advanced” functions, and I’ll be providing guidance on how to get a custom shortcut registered in the near future.

## **Can you help?**

If you happen to be a particularly gifted scripter (or know what I’m missing, which is pretty likely), and can brew up a PowerShell based method for creating AppId-laden-shortcuts, then feel free to send through a pull request!

{{< figure src="/2018/05/toast.gif" >}}

