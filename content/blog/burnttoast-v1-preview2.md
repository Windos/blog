+++
categories = ["BurntToast", "PowerShell"]
date = 2023-05-07T08:00:00Z
description = "If the wait for Preview1 felt long, the wait for Preview2 was even longer."
draft = false
thumbnail = "/2023/05/levi-meir-clancy-jdIT3puximI-unsplash.jpg"
images = ["/2023/05/levi-meir-clancy-jdIT3puximI-unsplash.jpg"]
slug = "burnttoast-v1-preview2"
summary = "If the wait for Preview1 felt long, the wait for Preview2 was even longer."
tag = ["BurntToast", "PowerShell"]
title = "BurntToast v1 Preview2 Is Available, Only a Year Late"
aliases = [
    "/2023/05/07/burnttoast-v1-preview2/amp/",
    "/2023/05/07/burnttoast-v1-preview2/",
    "/2023/05/07/burnttoast-v1-preview2"
]
+++

It took 10 months between [announcing](https://toastit.dev/2021/04/20/road-to-burnttoast-v1-1/) that I was working on BurntToast v1 and releasing the [first preview](https://toastit.dev/2022/02/12/finally-introducing-the-first-preview-of-burnttoast-v1/), and now I'm feeling like a broken record because it took over a year to get the second preview out.

I would like to thank everyone that tracked me down to chat about BurntToast at the [PowerShell + DevOps Global Summit](https://powershellsummit.org/) last month. You very much inspired me to ensure I set aside time to dive back into this project and, hopefully, get a completed v1 out without a full decade going by in the meantime!

All that said, you'll now find the the second preview release up on the PowerShell Gallery: [BurntToast 1.0.0-Preview2](https://www.powershellgallery.com/packages/BurntToast/1.0.0-Preview2).

## What's Broken?

Based on [feedback](https://github.com/Windos/BurntToast/discussions/192) from the first preview, the various images functions were combined into one. Specifically this means that the `Add-BTAppLogo` and `Add-BTHeroImage` functions have been removed in Preview2. Their functionality can now be accessed using switch parameters on the `Add-BTImage` function.

For example, to add a App Logo to a toast notification, you would now run:

```powershell
$Builder = New-BTContentBuilder
$Builder | Add-BTImage -Source 'C:\Temp\ExampleAppLogo.png' -AppLogo
$Builder | Show-BTNotification
```

And similarly for a Hero Image, you would now run:

```powershell
$Builder = New-BTContentBuilder
$Builder | Add-BTImage -Source 'C:\Temp\ExampleHeroImage.png' -HeroImage
$Builder | Show-BTNotification
```

## What's New?

Firstly, thanks to [Mark Kraus](https://techhub.social/@markekraus) for helping to ensure that all invalid characters are removed when caching images. There had been an edge case where characters that local file systems don't support would be used in file names and would result in errors.

A new function, [`Add-BTInputTextBox`](https://docs.toastit.dev/commands/add-btinputtextbox) was added which allows you, as the name suggests, add a text box to your toast notifications. At the moment you won't be able to do much with this, but I'm hoping to allow for easily triggering events and getting data from these text boxes in the next preview release.

Note that a toast cannot _only_ contain an input box, there needs to be some other element such as text or an app logo.

```powershell
$Builder = New-BTContentBuilder
$Builder | Add-BTImage -AppLogo
$Builder | Add-BTInputTextBox -Id 'Example 01'
$Builder | Show-BTNotification
```

{{< figure src="/2023/05/TextBoxExample.png" >}}

The [`Add-BTText`](https://docs.toastit.dev/commands/add-bttext) function now has an `Attribution` switch parameter which allows you to specify attribution for the resulting notification. For example if you were doing notifications for a chat app, you may attribute toast notifications to a specific channel or 1:1 chat.

```powershell
$Builder = New-BTContentBuilder
$Builder | Add-BTText -Text 'Example Toast Source' -Attribution
$Builder | Show-BTNotification
```

{{< figure src="/2023/05/AttributionExample.png" >}}

The `Add-BTTest` function also has a new `Bindable` switch parameter which allows the text you add to your notifications to become "bindable" (it's almost like that's where the parameter name came from or something!) You can think of bindable text as a place holder and have two main use cases:

1. If you need to support multiple languages, these bindable strings can be replaces with the appropriate language based on the locale of your user(s).
1. You can update strings if information becomes outdated.

Note that the ability to update a toast notification was not included in this preview release, but the ground work has been laid. When you create a Toast Content Builder via `New-BTContentBuilder` you specify a tag and group which uniquely identifies your toast via the `Group` and `Tag` parameters, otherwise the function will automatically generate these values for you so you don't need to think about it.

As is, a bindable string appears in a toast notification as the placeholder text surrounded by curly braces.

Something tells me that curly braces aren't what you're after, so instead you need to have "data bindings" which map your placeholder to the actual value you want to display. You do this with the new [`Add-BTDataBinding`](https://docs.toastit.dev/commands/add-btdatabinding) function, via which you can either add each binding one at a time via the `Key` and `Value` parameters or add multiple at once by providing a hashtable.

```powershell
$Builder = New-BTContentBuilder
$Builder | Add-BTText -Text 'Heading' -Bindable
$Builder | Add-BTText -Text 'Body' -Bindable
$Builder | Add-BTDataBinding -Key 'Heading' -Value 'This is the heading'
$Builder | Add-BTDataBinding -Key 'Body' -Value 'This is the body'
$Builder | Show-BTNotification
```

{{< figure src="/2023/05/BindingExample.png" >}}

You'll note in this example that the value that matched "Heading" shows where the matching bindable string was added, and the same happened with "Body."

These strings can be anything, and can include spaces. The key thing is that whatever string yoy provide via `Add-BTText` must match a key in the Data Binding. There will be more tooling around these data bindings in the future, such as inspecting the current bindings, removing those that may have been added by mistake, or updating existing bindings.

## You Feedback Needed

I want to hear from you about your experience with BurntToast v1 so far. It's far from feature complete, and there are a number of large changes from previous versions.

Please feel free to provide any and all feedback via this [GitHub Discussion](https://github.com/Windos/BurntToast/discussions/218).

For now, please try out the [current preview](https://www.powershellgallery.com/packages/BurntToast/1.0.0-Preview2) and keep your eye out for the next one.

## Credit

Hero image by [Levi Meir Clancy](https://unsplash.com/@levimeirclancy) on [Unsplash](https://unsplash.com/photos/jdIT3puximI).
