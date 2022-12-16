+++
categories = ["BurntToast", "PowerShell"]
date = 2021-01-31T21:08:16Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2020/09/kristina-flour-BcjdbyKWquw-unsplash.jpg"
slug = "crouton-15-you-cant-silence-toast"
summary = "Focus assist is a favoured tool of practiced speakers and presenters on Windows. It does put you in a bit of a bind if the thing you're presenting requires notifications though."
tags = ["BurntToast", "PowerShell"]
title = "Crouton #15 - You Can't Silence Toast"

+++


This post has been in the works for a long time now. We're talking almost half a year, and it's been one heck of an uphill battle putting fingers on keys and getting this written.

{{< figure src="__GHOST_URL__/content/images/2021/02/image.png" >}}

So, what ended up being the catalyst that got this post out into the wild? This morning I woke up to a great tweet from [Rob Sewell](https://twitter.com/sqldbawithbeard) with a video about how he's dealing with focus assist so that his toast notification can get through.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">I wanted a notification when my Azure SQL database was ready &amp; later when Azure AAD had synced in my demo. <a href="https://twitter.com/WindosNZ?ref_src=twsrc%5Etfw">@WindosNZ</a> BurntToast module is cool. With Focus Assist on, no notification. I created a horrid that works with Win10 2004<a href="https://t.co/0HSeBFyHUd">https://t.co/0HSeBFyHUd</a><a href="https://t.co/nCc6Okkc2Z">https://t.co/nCc6Okkc2Z</a></p>&mdash; Rob Sewell - Beardy McBeardFace (@sqldbawithbeard) <a href="https://twitter.com/sqldbawithbeard/status/1355863947044392963?ref_src=twsrc%5Etfw">January 31, 2021</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The solution presented is simply brilliant and is an excellent example of what you can get done with PowerShell.

## But Why?

So, what's the problem Rob (and this post back when I started writing it) is trying to solve?

When you're presenting, sharing your screen on a video call, or just want to tune out some digital noise while trying to get work done it's a pain (and sometimes embarrassing) to have notifications pop up.

Windows has a built-in feature to tame those notifications, [Focus Assist](https://support.microsoft.com/en-us/windows/turn-focus-assist-on-or-off-in-windows-10-5492a638-b5a3-1ee0-0c4f-5ae044450e09). Turn that bad boy on and you can say goodbye to those pesky notifications.

But then what happens when you're using [BurntToast](https://www.powershellgallery.com/packages/BurntToast), as a self-promoting example, to get notifications that you _actually_ want to see?

This is the problem: stop most notifications showing up on screen, except for the ones you're creating yourself.

Rob's solution is to automate the keystrokes needed to turn Focus Assist off, pop the toast, then turn focus assist back on.

There are just two issues with this:

1. The act of toggling focus assist is a little... jarring (watch the video, it's worth it.)
2. This gives a window where other unwanted notifications can sneak in.

## Priority Guest List

There are two levels you can set for focus assist, "priority only" and "alarms only." We'll tackle the first one first.

You're able to define a list of apps on your priority list. Notifications from these apps will show on screen when you have focus assist set to priority only (makes sense, right?)

To define this list, open up the  [**Settings** app](https://support.microsoft.com/en-us/windows/find-settings-in-windows-10-6ffbef87-e633-45ac-a1e8-b7a834578ac6), click into the **System** category and choose **Focus assist**. For here you'll see you current focus assist and under "Priority only" is a link to customize your priority list.

{{< figure src="__GHOST_URL__/content/images/2021/02/image-1.png" >}}

When you click that link you'll see a little more than just a list of apps. You can also see that by default this focus assist level will also show notifications for incoming calls (e.g. from Teams) and reminders.

You can customize specific contacts that can break through focus assist across a number of apps, but most importantly is the list of apps themselves.

There are a few apps listed by default, including some Xbox apps (probably because playing games can automatically turn on focus assist, so they expect you may still want your gaming notifications.) While you're here you can take a moment to clean up this list if you'd like by clicking on a given app and clicking **Remove**.

{{< figure src="__GHOST_URL__/content/images/2021/02/image-2.png" >}}

To add an app, click Add an app (surprise, surprise) and choose the app from the list.

You'll note that the list of available apps isn't exhaustive. My assumption here is that it only list apps that either have already sent a notification or that Windows knows explicitly that it is capable of sending a notification.

The default app used by BurntToast when sending a notification is Windows PowerShell, so select that from the list.

Voilà, you can now set focus assist to priority only and your toast notifications from Windows PowerShell will still show!

This doesn't solve Rob's problem though. You'll note that he is cranking focus assist all the way up to the max. In fact, if you pay attention to most any practiced presenter and you'll see that they default to the maximum focus assist level as priority only has a lot of potential for still letting unwanted notifications through.

## Sound the Alarm!

There's a hint in the name of the max focus assist level that gives away how we can break through. Just in case the hint is buried a little deep, check out the description under it while you're in the settings app:

{{< figure src="__GHOST_URL__/content/images/2021/02/image-3.png" >}}

The only notifications that are allowed are alarms. Those come from clocks... and when you're creating your own toasts you can just say: "Psst, Windows. This toast isn't a reminder... it's an alarm. Trust me. Thanks bud."

Really, all that's happening for this categorization of toast notifications is tweaking the notification's "[scenario](https://docs.microsoft.com/en-us/windows/uwp/design/shell/tiles-and-notifications/toast-schema#toastscenario)". One of these happens to be an alarm scenario, and all focus assist is doing is blocking notifications that do not have that particular scenario set.

_One caveat, just the scenario isn't enough. The notification **also** needs to have snooze and dismiss buttons on it to be considered a "real" alarm._

At the time of writing, this can't be done with just the `New-BurntToastNotification` function. Instead, you have to use the component functions. Learning these functions are a great way to get more control over your notifications... but it is one heck of a learning curve.

We'll start by defining an app logo and a hero image.

```powershell
$AppIcon = New-BTImage -Source 'https://i.imgur.com/ZbdyG6F.gif' -AppLogoOverride
$HeroImage = New-BTImage -Source 'https://pbs.twimg.com/media/EEsoTNdXYAEYd73.jpg' -HeroImage
```

Next, two lines of text. The first is the heading and will show embolden compared to the second.

```powershell
$Text1 = New-BTText -Text "You Can't Silence Toast!"
$Text2 = New-BTText -Text 'Seriously, with the "Alarm" scenario, this will break through focus assist.'
```

Now we bind those images and text together... into a binding object. That binding object then feeds into a visual object.

```powershell
$Binding1 = New-BTBinding -Children $Text1, $Text2 -AppLogoOverride $AppIcon -HeroImage $HeroImage 
$Visual1 = New-BTVisual -BindingGeneric $Binding1
```

When using the alarm scenario locks you into using one of the 10 alarm sounds. This example uses "Alarm5" but you can use tab completion to pick a different one. You can also omit the `-Loop` switch if you only want to hear the sound play once, otherwise it will loop as long as the notification is on screen.

```powershell
$Audio1 = New-BTAudio -Source ms-winsoundevent:Notification.Looping.Alarm5 -Loop
```

You can opt for a silent notification if you want to not have an annoying alarm sound in your ear. In that case replace the previous line with the following.

```powershell
$Audio1 = New-BTAudio -Silent
```

I mentioned needed snooze and dismiss buttons above. Let's go ahead and sort them out.

```powershell
$Action1 = New-BTAction -SnoozeAndDismiss
```

Like with thing binding object, we now combine all of our various components into a content object, remember to set the `-scenario` to **Alarm**. We finally take the content and submit it to the OS... all going well we will have out toast.

```powershell
$Content1 = New-BTContent -Visual $Visual1 -Actions $Action1 -Audio $Audio1 -Scenario Alarm
Submit-BTNotification -Content $Content1
```

{{< figure src="__GHOST_URL__/content/images/2021/02/2021-02-01_22-52-01.gif" >}}

## Pros and Cons

The priority list option is the easiest to pull off. It allows you the maximum amount of freedom in the creation of your notifications as the buttons and sounds are not dictated for you.

The downside is that all notifications from PowerShell will break through. If you're a heavy user (I don't have a toast problem, you have a toast problem!) then you may get more notifications come through than you'd ideally want.

The alarm option means you can be more selective in which notifications do end up on screen, but the style of them is a little more rigid (I don't normally like buttons for most of my use cases). You're also forced to use the more advanced functions, which mean a steeper learning curve.

Regardless, there are options.

You can always toggle focus assist off and on as needed if that's your jam 😉

## Credit

Hero Image by [Kristina Flour](https://unsplash.com/@tinaflour?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/silence?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

