+++
categories = ["BurntToast", "PowerShell"]
date = 2019-07-16T22:20:04Z
description = ""
draft = false
thumbnail = "/2019/05/mae-mu-1547158-unsplash.jpg"
images = ["/2019/05/mae-mu-1547158-unsplash.jpg"]
slug = "crouton-11"
summary = "Following the release of BurntToast v0.7.0 there were a couple of headline features that needed to be written about. Shoulder Taps had their time in the spotlight and now it's the turn of Updatable Toasts."
tag = ["BurntToast", "PowerShell"]
title = "Crouton #11 - Updating Toast Notifications, Gracefully"
+++


Wow, it feels like forever since I was last able to sit down and write a blog post. What better way to get back into the swing of things than to make good on the post I alluded to in my [last Crouton](__GHOST_URL__/2019/04/02/crouton-10/): Updatable Toast Notifications.

## The Old Method

Prior to version 0.7.0 of [BurntToast](https://powershellgallery.com/packages/BurntToast) you had one option for updating the content of a toast. That options was what I'd probably refer to as "_Brute Force_."

In short, you could specify the same unique identifier on multiple toasts. Each subsequent toast would replace the last. This would mean that the content is updated, and you wouldn't end up with a flood of similar toasts in the Action Center. The down side was the each new toast would be displayed on screen as if it were a wholly new notification.

It was a rather jarring experience, especially if you found yourself needing to update a progress bar rather rapidly.

```powershell
foreach ($Number in 1..100) {
    $ProgressBar = New-BTProgressBar -Status 'Running' -Value ($Number / 100)

    $ToastSplat = @{
        Text             = 'Demo Replacement'
        UniqueIdentifier = 'DemoReplace'
        ProgressBar      = $ProgressBar
    }
    New-BurntToastNotification @ToastSplat

    Start-Sleep -Seconds 1
}
```

{{< figure src="/2019/07/Replace.gif" caption="Example of replacing a toast by force" >}}

No, that GIF isn't glitching. The animation is a good depiction of what forcibly replacing a toast looks like. The animation gets all wonky, and the UI can't really decide on where the toast itself is meant to come from.

What I can't replicate with a GIF is the sound. If you've got a sound set for your notifications it will play. Every. Single. Time.

Do note that the number of notifications in the Action Center goes up to one when the toasts start, and stay at one as the follow ups come through. If you hadn't set a unique identifier that number would be ticking up with each one and they would also be queued one after the other rather than removing it from the screen.

This type of replacement has its place—generally when you need to update a toast after an amount of time has passed and you want your user to know that the toast has been updated—but it's certainly not when you just want to update a progress bar.

## Show Your Graceful Side

Instead of ripping out and replacing an existing toast, you can update its metadata. This is a little more involved initially, but then updates are pretty simple.

Start by creating your toast, but rather than including your text directly in the toast you use place holder text which matches keys in a hashtable. This hashtable is then provided via the `-DataBinding` parameter.

```powershell
$FirstDataBinding = @{
    FirstLine = 'Example Toast Heading'
    SecondLine = 'This toast is still the original'
}
New-BurntToastNotification -Text 'FirstLine', 'SecondLine' -UniqueIdentifier 'ExampleToast' -DataBinding $FirstDataBinding
```

This will display a toast as you would expect, with the text from your hashtable in place of the keywords.

{{< figure src="/2019/07/FirstToast.PNG" caption="Example toast with a side of data binding" >}}

You can now update this toast by constructing a new hashtable with at least one of the placeholders (you don't need to list all of them, just what you want to update.) This time, use the `Update-BTNotification` function, providing this hashtable and the original toast's unique identifier.

```powershell
$SecondDataBinding = @{
    SecondLine = 'This toast has been updated!'
}
Update-BTNotification -UniqueIdentifier 'ExampleToast' -DataBinding $SecondDataBinding
```

If you happen to update the toast while it's on-screen, you can see how seamlessly it updates.

{{< figure src="/2019/07/UpdateOnScreen.gif" caption="Updating a toast while it is on-screen" >}}

This will also work if the notification has expired from the screen and is waiting in the Action Center. The content will be updated, but the toast will not be shown on the desktop again.

This means you're free to keep updating your progress bar, or any other text, as often as you'd like and you won't be annoying your users.

## A Note on Missing Notifications

When you run `Update-BTNotification` you'll get some output. Generally speaking, you'll get the string "Succeeded" letting you know that your update was received and processed.

However, you can only update a toast that actually exists.

If your user dismisses a toast, either by clicking it while it was on-screen or clearing it from the Action Center, the toast is gone and there will be nothing to update. When this happens, the update function will return "NotificationNotFound".

This means your scripts should check for the output and take different actions depending on the outcome you want to achieve. As an example you might just end your process if the toast isn't there or you might decide to resend the toast anew (be careful not to annoy people by resurrecting dismissed notifications!)

## There's More to Discuss About Data Bindings

... but these topics deserve their own posts.

The first topic that will be getting a post is localization of the text on your notifications.

Next will be more advanced usages of bound strings, things get a little more interesting than just changing the bog standard strings like we did in this post. No spoilers here though!

Until next time, please do let me know in the comments or on [Twitter](https://twitter.com/WindosNZ) if and how you use (or want to use) these updatable toasts.

