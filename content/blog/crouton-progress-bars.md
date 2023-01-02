+++
categories = ["PowerShell", "BurntToast"]
date = 2017-04-29T12:00:00Z
description = ""
draft = false
featured_image = "/2018/05/thumb.JPG"
images = ["/2018/05/thumb.JPG"]
slug = "crouton-progress-bars"
summary = "April saw the release of the Creators Update for Windows 10 which brought with it a handful of new options for toast notifications."
tag = ["PowerShell", "BurntToast"]
title = "Crouton #2 - You Can't Stop Progress (Bars)"
aliases = [
    "/crouton-progress-bars/amp/",
    "/crouton-progress-bars/",
    "/crouton-progress-bars"
]
+++


April saw the release of the Creators Update for Windows 10 which brought with it a handful of new options for toast notifications. This post will serve as a quick rundown for one of these new options: Progress Bars.

A note before we get started, the examples below require version 0.6.0 of the BurntToast module.

## **Creating a progress bar**

Progress bars are created using a dedicated function, `New-BTProgressBar`, the resultant object can then be passed to `New-BurntToastNotification`.

Let's say you have a long running script working through all the servers in your environment, and you want be kept in the loop about the how far through it is. You might have previously created toasts of this nature:

```powershell
New-BurntToastNotification -Text 'Server Update', '50 servers out of 2,000 completed'

```

{{< figure src="/2018/05/FirstToast.PNG" >}}

That gets the job done, sure, but now consider:

```powershell
$ProgressBar = New-BTProgressBar -Status 'Running' -Value (50 / 2000)
New-BurntToastNotification -Text 'Server Update' -ProgressBar $ProgressBar

```

{{< figure src="/2018/05/SecondToast.PNG" >}}

Obviously in the real world you'd want to parameterize the values in there, but I've kept this fairly simple for demo purposes.

## **So, it's just a one off thing?**

You've got a keen eye! In the form presented above, this would create a lot of toasts, all of theme distinct. If you provided 10 updates while the script ran, you'd end up with 10 toasts.

A better option is to replace the previous toasts, and you can do this using the `UniqueIdentifier` parameter.

```powershell
New-BurntToastNotification -Text 'Server Update' -ProgressBar $ProgressBar -UniqueIdentifier 'Toast001'

```

Whenever you submit a new toast with the same identifier as a previous toast, the previous toast will be replaced. The new toasts will still be displayed to the user as if they were new (including any sounds you've configured), but there won't be a history of them in the Action Center.

It should theoretically be possible to 'update' toasts in the future, meaning the content will be refreshed but not showing to the user as a new toast, but this isn't working at the moment. When it is an option I'll include it in the release notes for BurntToast.

## **A few final thoughts**

The above covers the basics, but I figured this post should be closed off with a few finer points that may help when you're using these progress bars.

That 'Value' should be a decimal value between 0 and 1 (inclusive.) So if you're using a calculation to come up with a percentage, don't do any multiplying by 100.

Also, the display text in the example showing 3% in the example above is automatically generated, but can be overridden with any string with the ValueDisplay parameter.

Finally, if you want to use a progress bar but aren't sure of what value at a given time, you can use the `Indeterminate` switch, which replaced the bar itself with an animation.

{{< figure src="/2018/05/animated.gif" >}}

That's it for this one. I imagine there should be another post in this Crouton series in the next week or so covering headers, another new option thanks to the Creators Update.

