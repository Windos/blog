+++
categories = ["BurntToast", "PowerShell"]
date = 2022-02-12T08:00:00Z
description = "It may have taken 10 months, but preview releases are finally coming for BurntToast v1."
draft = false
thumbnail = "/2022/02/photo-1499244571948-7ccddb3583f1.jpg"
images = ["/2022/02/photo-1499244571948-7ccddb3583f1.jpg"]
slug = "finally-introducing-the-first-preview-of-burnttoast-v1"
summary = "It may have taken 10 months, but preview releases are finally coming for BurntToast v1."
tag = ["BurntToast", "PowerShell"]
title = "Finally, Introducing the First Preview of BurntToast v1"
aliases = [
    "/2022/02/12/finally-introducing-the-first-preview-of-burnttoast-v1/amp/",
    "/2022/02/12/finally-introducing-the-first-preview-of-burnttoast-v1/",
    "/2022/02/12/finally-introducing-the-first-preview-of-burnttoast-v1"
]
+++

Evidently, this last year has gone by very fast. I [announced](https://toastit.dev/2021/04/20/road-to-burnttoast-v1-1/) that I was starting to work on the re-write of BurntToast that will be released as v1 back in April of 2021 and then promptly... didn't get around to working on it. With a job change and generally being busy, BurntToast took a back seat for a while there.

But I'm glad to say that the first preview release is up on the PowerShell Gallery: [BurntToast 1.0.0-Preview1](https://www.powershellgallery.com/packages/BurntToast/1.0.0-Preview1).

## What's New?

As I've said before, this is going to be an almost complete re-write of the module. How it's used is going to change, both because the upstream .NET resources have enabled slicker experiences and also the benefit of having made my own UX mistakes in previous versions that I've always wanted a do-over on.

To that end, this first preview release is very limited, featuring only a small handful of functions:

* [Add-BTAppLogo](https://docs.toastit.dev/commands/add-btapplogo)
* [Add-BTHeroImage](https://docs.toastit.dev/commands/add-btheroimage)
* [Add-BTImage](https://docs.toastit.dev/commands/add-btimage)
* [Add-BTText](https://docs.toastit.dev/commands/add-bttext)
* [New-BTContentBuilder](https://docs.toastit.dev/commands/new-btcontentbuilder)
* [Show-BTNotification](https://docs.toastit.dev/commands/show-btnotification)

If you've used the current versions of BurntToast, you'll notice a lack of functions with the `New` verb. Almost everything in [v0.8.5](https://www.powershellgallery.com/packages/BurntToast/0.8.5), and earlier, results in an object, be that a "text" object or an "image" object. The whole thing relied on you knowing how and where to use those objects.

That's all well and good if you've got a good understanding of the [underlying XML](https://toastit.dev/2020/07/29/anatomy-of-toast/), but arcane if you don't. Where, for example, does the "binding" object fit? How is it different to the "visual" object?

The difference with v1 is that it all revolves around a single object, the "Toast Content Builder" which you create with the `New-BTContentBuilder` function. You take that and _build_ your toast on top of it by adding elements to it.

## A Tale of Two Usage Models

This first set of functions were chosen as a good show case for the new usage of the BurntToast module. With them you can create visually interesting toasts quickly, without getting bogged down with too many advanced options (for now.)

For example, this toast was created using the available commands and just five lines of code:

{{< figure src="/2022/02/image.png" >}}

The first method for doing this with BurntToast v1 is to store the Toast Content Builder in a variable and pass it into each command in turn until you're ready to display it using the `Show-BTNotification` function.

```powershell
$Builder = New-BTContentBuilder
$Builder | Add-BTAppLogo
$Builder | Add-BTText "BurntToast v1 is in the works!", "It took a while, but progress is being made"
$Builder | Add-BTImage "https://raw.githubusercontent.com/Windos/BurntToast/main/Media/BurntToast-Wide.png"
$Builder | Show-BTNotification
```

Having to specify the Toast Content Builder all the time is a bit of a drag, right? That's why each of the functions with the `Add` verb supports a `PassThru` parameter that outputs the updated builder object.

You could add this to each command, and pipe through each command.

```powershell
New-BTContentBuilder |
    Add-BTAppLogo -PassThru |
    Add-BTText "BurntToast v1 is in the works!", "It took a while, but progress is being made" -PassThru |
    Add-BTImage "https://raw.githubusercontent.com/Windos/BurntToast/main/Media/BurntToast-Wide.png" -PassThru |
    Show-BTNotification
```

That's better, but having to specify `-PassThru` all the time is a bit of a drag. Instead, you can use a [default parameter value](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_parameters_default_values) to say it should always be used for any function starting with `Add-BT`.

```powershell
$PSDefaultParameterValues = @{
    "Add-BT*:PassThru" = $true
}

New-BTContentBuilder |
    Add-BTAppLogo |
    Add-BTText "BurntToast v1 is in the works!", "It took a while, but progress is being made" |
    Add-BTImage "https://raw.githubusercontent.com/Windos/BurntToast/main/Media/BurntToast-Wide.png" |
    Show-BTNotification
```

## You Feedback Needed

You'll note that there's three functions included in this preview that deal with different types of images:

* Regular images that show in the body of a notification, under any text.
* Hero images that show as large, bold, banners across the top of a notification.
* App logos, which are generally the icon for the app generating a notification or possibly a user avatar for chat notifications (for example)

These functions are all very similar and could be a single command, like they are in the pre v1 BurntToast versions.

Chances are Preview2 will roll these into one function. If you have opinions for either option, I want to hear from you. Please do drop your thoughts into [this discussion](https://github.com/Windos/BurntToast/discussions/192).

Your feedback on any other issue is also very much appreciated, so don't hesitate to get in touch outside of that particular discussion too!

For now, please try out the current preview and keep your eye out for the next one.

## Credit

Hero image by [Ross Findon](https://unsplash.com/@rossf) on [Unsplash](https://unsplash.com/photos/mG28olYFgHI).
