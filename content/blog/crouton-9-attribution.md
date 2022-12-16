+++
categories = ["BurntToast", "PowerShell"]
date = 2018-10-04T02:07:00Z
description = ""
draft = false
thumbnail = "/2018/10/rukma-pratista-1054296-unsplash.jpg"
slug = "crouton-9-attribution"
summary = "Where did that toast come from? Who cooked it?! If only it was attributed to someone..."
tag = ["BurntToast", "PowerShell"]
title = "Crouton #9 - Free, with Attribution"

+++


There's a lot of hidden gems within the toast notification framework that allow you to tailor your toasts to your taste.

One of these features that I haven't given enough love to (so much so, that it isn't even properly implemented in [BurntToast](https://powershellgallery.com/packages/BurntToast) yet) is [attribution](https://docs.microsoft.com/en-us/windows/uwp/design/shell/tiles-and-notifications/adaptive-interactive-toasts#attribution-text).

It's a small thing (literally), but attribution text let's you provide more context about the source of your notification. The official documentation demonstrates using this text to identify the communication channel that a message was received on, such as "Via SMS".

But we're PowerShell people, and we're creating these toasts from various scripts. We can put anything we want here... so why not the server from which the toast was generated, the user that generated it, or even the name of the script?

## **Current State: Patchwork**

I must have been in a rush, or hadn't thought through attribution text fully, when implementing this in BurntToast. There's a skeleton there... and lots of room for improvement.

These examples will be using BurntToast's "advanced" functions. Check out [Crouton #3](https://king.geek.nz/2017/05/08/crouton-clickable) for a little more of a primer on these.

Let's start with where your attribution text lives within the toast schema. It sits within the "binding" along with all of your regular text and images, and as such there's a parameter for it on `New-BTBinding`:

```powershell
New-BTBinding -Attribution 'Via PowerShell'

```

**But** it doesn't work that way, unfortunately. If you try running that example, you'll get a nice error about not being able to convert a string to "ToastGenericAttributionText". Yep, sure, whatever that means.

At the moment, there's no function to create an object of the right type for you. However, once the BurntToast module is imported you'll be able to use the classes within the [Microsoft Community Toolkit](https://docs.microsoft.com/en-us/windows/communitytoolkit/). Let's go ahead and start by creating one of the desired objects and assign our text to it:

```powershell
$Attrib = [Microsoft.Toolkit.Uwp.Notifications.ToastGenericAttributionText]::new()
$Attrib.Text = 'Via PowerShell'

```

And now we can pass this object through:

```powershell
New-BTBinding -Attribution $Attrib

```

Awesome, that works! To show that in action, let's see a full example and the result. This time however, I'm going to use `$MyInvocation.MyCommand.Name` to get the name of the script that's creating the toast and use that if it's available... if not "Via PowerShell" it is!

```powershell
$Text1 = New-BTText -Content 'Default Notification'
$ImagePath = Join-Path -Path (Get-Module BurntToast -ListAvailable).ModuleBase -ChildPath 'Images\BurntToast.png'
$Image1 = New-BTImage -Source $ImagePath -AppLogoOverride -Crop Circle
$Audio1 = New-BTAudio -Source 'ms-winsoundevent:Notification.Default'

$Attrib = [Microsoft.Toolkit.Uwp.Notifications.ToastGenericAttributionText]::new()

if ($MyInvocation.MyCommand.Name) {
    $Attrib.Text = 'Via ' + $MyInvocation.MyCommand.Name
} else {
    $Attrib.Text = 'Via PowerShell'
}

$Binding1 = New-BTBinding -Children $Text1 -AppLogoOverride $Image1 -Attribution $Attrib
$Visual1 = New-BTVisual -BindingGeneric $Binding1
$Content1 = New-BTContent -Visual $Visual1 -Audio $Audio1

Submit-BTNotification -Content $Content1

```

{{< figure src="/2018/10/Attrib.gif" >}}

Notice the text at the bottom of the toast, first we see the display name representation of my AppId (BurntToast), a separator, and then my attribution text.

## **The Future**

There are a couple of ways that this whole thing can be simplified. I'll quickly mention two of them, and I'm pretty sure they will both be part of the module when v0.7.0 rolls around.

The first option is the embrace the fact that this is just a slightly different type of text and add an "Attribution" switch to `New-BTText`. This will be similar to the "AppLogoOverride" switch on `New-BTImage`. The resulting object could then be provided with the rest of your text.

The second option is to allow the "Attribution" parameter to convert strings, so that one can use it as demonstrated closer to the start of this post.

The only real question is: should this be exposed though `New-BurntToastNotification`? That function is already fairly bloated, but this is a relatively lightweight addition.

Would you use this, let me know in the comments or over on [Twitter](https://twitter.com/WindosNZ)!

