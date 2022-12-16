+++
categories = ["BurntToast", "PowerShell"]
date = 2019-11-20T20:09:45Z
description = ""
draft = false
thumbnail = "/2019/11/sophie-elvis-msC14JchkKU-unsplash.jpg"
slug = "crouton-13-hitting-snooze"
summary = "Hitting snooze on your alarm clock is probably rather common. Let's take a look at how you can do the same thing with your Toast Notifications."
tags = ["BurntToast", "PowerShell"]
title = "Crouton #13 - Hitting Snooze"

+++


Earlier this year an [issue](https://github.com/Windos/BurntToast/issues/71) came through on the [BurntToast GitHub repo](https://github.com/Windos/BurntToast) about binding custom snooze times to a toast's snooze button.

If my documentation was a little more complete, the answer would have been in the built in help... but I'm still working on that (while hanging my head in shame!) This post is the first step in getting something written on the topic of Snoozing and/or Dismissing toast notifications.

## First Option: Let the System Handle It

If all you're looking for is a quick pair of snooze and dismiss buttons with some default options for a selectable amount of time snooze time, just let Windows handle it.

To do this, specify the `SnoozeAndDismiss` switch when calling the `New-BurntToastNotification` function.

```powershell
New-BurntToastNotification -SnoozeAndDismiss
```

{{< figure src="/2019/11/image.png" caption="" >}}

_(No... I don't know where that "9 minutes" comes from either. I'll be ignoring it going froward.)_

You can achieve the same thing using the component functions if you're doing something a little more advanced. You can do this by specifying the `-SnoozeAndDismiss` switch on the `New-BtAction` function.

You'll likely note that when you use the system snooze and dismiss option, that the toast stays on the screen for a lot longer than a normal toast. We're talking three minutes compared to about six seconds for a regular toast.

```powershell
$Text1 = New-BTText -Content 'Default Notification'
$ImagePath = Join-Path -Path (Get-Module BurntToast -ListAvailable).ModuleBase -ChildPath 'Images\BurntToast.png'
$Image1 = New-BTImage -Source $ImagePath -AppLogoOverride -Crop Circle
$Audio1 = New-BTAudio -Source 'ms-winsoundevent:Notification.Default'

$Binding1 = New-BTBinding -Children $Text1 -AppLogoOverride $Image1
$Visual1 = New-BTVisual -BindingGeneric $Binding1

$Action = New-BTAction -SnoozeAndDismiss
$Content1 = New-BTContent -Visual $Visual1 -Audio $Audio1 -Actions $Action

Submit-BTNotification -Content $Content1
```

### What do the buttons actually do?

Dismiss is simply acknowledges the toast and removes it from the screen. It doesn't go to the Action Center to be viewed later, it is gone for good. This is the same behavior as if the user clicked on a toast that had no [on-click action](__GHOST_URL__/2017/05/08/crouton-clickable/).

Snooze sends the toast back to the operating system as a scheduled toast with a set time to reappear of whenever the button was pressed plus the amount of minutes in the drop down box.

_A future version of BurntToast will allow you to inspect what scheduled toasts are waiting on your system and remove them ahead of time if needed._

## Second Option: Do It Yourself

A quick note before we get into this section, in order to roll your own snooze and dismiss buttons your only option is to use the more verbose component functions. Rather than repeating the same boiler plate code over and over again, just assume that all examples from now on also contains the following snippet:

```powershell
$Text1 = New-BTText -Content 'Default Notification'
$ImagePath = Join-Path -Path (Get-Module BurntToast -ListAvailable).ModuleBase -ChildPath 'Images\BurntToast.png'
$Image1 = New-BTImage -Source $ImagePath -AppLogoOverride -Crop Circle
$Audio1 = New-BTAudio -Source 'ms-winsoundevent:Notification.Default'

$Binding1 = New-BTBinding -Children $Text1 -AppLogoOverride $Image1
$Visual1 = New-BTVisual -BindingGeneric $Binding1
```

In order to replicate the default snooze and dismiss behavior, we first need to create the items that will go into our drop down list of times to snooze.

```powershell
$5Min = New-BTSelectionBoxItem -Id 5 -Content '5 minutes'
$10Min = New-BTSelectionBoxItem -Id 10 -Content '10 minutes'
$1Hour = New-BTSelectionBoxItem -Id 60 -Content '1 hour'
$4Hour = New-BTSelectionBoxItem -Id 240 -Content '4 hours'
$1Day = New-BTSelectionBoxItem -Id 1440 -Content '1 day'
```

The `Id` parameter indicates the number of minutes to snooze your toast if selected. The `Content` parameter can be whatever you want, though it helps to relate it to the amount of time you'll be snoozing for.

You can only have up to five of these selection box items.

Next, we need to add all of these items to the selection box itself.

```powershell
$Items = $5Min, $10Min, $1Hour, $4Hour, $1Day
$SelectionBox = New-BTInput -Id 'SnoozeTime' -DefaultSelectionBoxItemId 10 -Items $Items
```

When creating your selection box, the `Id` can be anything you want, it will be referenced later on your snooze button to tie the two elements together.

The `DefaultSelectionBoxItemId` needs to match one of your snooze times. This will, as you may have guessed, automatically select that item as the default when your toast displays.

Finally, you need to add all of your selection box items to the `Items` parameter.

This gives you the selection box, but that is useless without some buttons, isn't it?

```powershell
$SnoozeButton = New-BTButton -Snooze -Id 'SnoozeTime'
$DismissButton = New-BTButton -Dismiss
```

The only thing to take note of here, is that the `Id` on your snooze button needs to match the Id specified on your selection box. If you don't match IDs, then your selection will be ignored and hitting the snooze button will result in the system default time being used instead.

Next, we bundle our buttons and selection box up into an "action" object.

```powershell
$Action = New-BTAction -Buttons $SnoozeButton, $DismissButton -Inputs $SelectionBox
```

The order in which your specify your buttons determines in which order they appear on your toast.

Finally, let's see that this all works by submitting the toast to Windows.

```powershell
$Content1 = New-BTContent -Visual $Visual1 -Audio $Audio1 -Actions $Action

Submit-BTNotification -Content $Content1
```

You're now ready to create a toast notification with snooze times that you've defined. There is, however, a few other DIY options available to you... some are a little more useful than others.

### Snooze Without Options

As mentioned previously, if you don't tie your selection box to your snooze button with a matching ID, the default system time is used instead. You can use this fact to make a snoozable toast without a drop down box.

```powershell
$SnoozeButton = New-BTButton -Snooze
$DismissButton = New-BTButton -Dismiss

$Action = New-BTAction -Buttons $SnoozeButton, $DismissButton
$Content1 = New-BTContent -Visual $Visual1 -Audio $Audio1 -Actions $Action

Submit-BTNotification -Content $Content1
```

{{< figure src="/2019/11/image-1.png" caption="" >}}

It should be noted that without the selection box, this toast only displays for as long as a regular toast (6 seconds) rather than the much longer time that these snooze and dismiss toasts are normally afforded.

### Snooze Only

Since the dismiss button just does the same thing as clicking on a blank area of the toast itself... do we really need it? We can omit the dismiss button in favor of a super snooze button if desired.

```powershell
$5Min = New-BTSelectionBoxItem -Id 5 -Content '5 minutes'
$10Min = New-BTSelectionBoxItem -Id 10 -Content '10 minutes'
$1Hour = New-BTSelectionBoxItem -Id 60 -Content '1 hour'

$Items = $5Min, $10Min, $1Hour
$SelectionBox = New-BTInput -Id 'SnoozeTime' -DefaultSelectionBoxItemId 10 -Items $Items

$SnoozeButton = New-BTButton -Snooze -Id 'SnoozeTime'

$Action = New-BTAction -Buttons $SnoozeButton -Inputs $SelectionBox
$Content1 = New-BTContent -Visual $Visual1 -Audio $Audio1 -Actions $Action

Submit-BTNotification -Content $Content1
```

{{< figure src="/2019/11/image-2.png" caption="" >}}

### Dismiss Only

This one is probably the least useful, but let's have a look for the sake of completeness.

```powershell
$DismissButton = New-BTButton -Dismiss

$Action = New-BTAction -Buttons $DismissButton
$Content1 = New-BTContent -Visual $Visual1 -Audio $Audio1 -Actions $Action

Submit-BTNotification -Content $Content1
```

{{< figure src="/2019/11/image-3.png" >}}

## Disclaimer: Snoozed Toast Bug

There's currently an [issue](https://github.com/Windos/BurntToast/issues/72) where toasts that have been dismissed don't display correctly. You'll see curly braces around some of the text.

{{< figure src="/2019/11/image-4.png" >}}

I'm hoping this will be sorted by the time of the next BurntToast release because it does make these less polished than they should be.

Until this is fixed, I hope you can find some use in these snoozable toasts, even if you don't use them in a user facing role for the time being!

## Credit

Hero image by [Sophie Elvis](https://unsplash.com/@thetechnomaid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/snooze?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

