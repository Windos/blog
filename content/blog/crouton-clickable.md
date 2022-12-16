+++
categories = ["PowerShell", "BurntToast"]
date = 2017-05-07T12:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-3.jpg"
slug = "crouton-clickable"
summary = "Buttons can look pretty ugly when only using one, so just click your Toast instead!"
tags = ["PowerShell", "BurntToast"]
title = "Crouton #3 - Sometimes You Just Want to Click Your Toast"

+++


Buttons, as we have discussed [previously](https://king.geek.nz/2017/03/20/crouton-stackwatch/), are all well and good. However, if you’re only adding one to your toast, they can look a little… out of place if not outright ugly.

[Surely](https://github.com/Windos/BurntToast/issues/15) you can just set an action on the toast itself, making it clickable?

Before we jump in, I’m aware that I’ve been terrible about mentioning the PowerShell module these posts rely on, and linking to other relevant info. Going forward these posts should be laden with link, including the relevant module: [BurntToast](https://www.powershellgallery.com/packages/BurntToast).

## **Click!**

You better believe it’s possible! A caveat upfront is that this isn’t (currently) possible using only `New-BurntToastNotification` function and you’ll have to jump into some of the more "advanced" functions.

First, create a handful of text object that you want displayed on the toast, add them to a binding and in turn add the binding to a visual element.

```powershell
$Text1 = New-BTText -Content 'Google!'
$Text2 = New-BTText -Content 'Clicking this will open Google.'
$Binding1 = New-BTBinding -Children $Text1, $Text2
$Visual1 = New-BTVisual -BindingGeneric $Binding1

```

Now, you create the actual “content” object of your toast, and specify the URL/path to open with the `Launch` parameter and set the `ActiviationType` to Protocol.

```powershell
$Content1 = New-BTContent -Visual $Visual1 -Launch 'https://google.com' -ActivationType Protocol

```

Finally, submit the toast for display to the user.

```powershell
Submit-BTNotification -Content $Content1

```

Now you can click the toast that is displayed, and it’ll open the specified URL.

{{< figure src="/2018/05/ToastClick.gif" >}}

That’s all there is to it!

As always, if you’ve got any feedback, questions or requests don’t hesitate to leave a comment here or file an issue over on [GitHub](https://github.com/Windos/BurntToast).

