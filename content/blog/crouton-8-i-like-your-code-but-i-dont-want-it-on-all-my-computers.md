+++
categories = ["BurntToast", "PowerShell"]
date = 2018-08-20T17:18:00Z
description = ""
draft = false
thumbnail = "/2018/08/kyle-glenn-629501-unsplash.jpg"
images = ["/2018/08/kyle-glenn-629501-unsplash.jpg]
slug = "crouton-8-i-like-your-code-but-i-dont-want-it-on-all-my-computers"
summary = "I get you; you like toast, but you don't want to put a toaster on 100 desks."
tag = ["BurntToast", "PowerShell"]
title = "Crouton #8 - I like Your Code, but I Don't Want It on All My Computers"
+++


[BurntToast](https://www.powershellgallery.com/packages/BurntToast) has been downloaded over 13,000 times. [1,400](https://www.powershellgallery.com/stats/packages/BurntToast?groupby=Version) of these were just in the past six weeks. While I haven't been able to chat with anyone responsible for these numbers, I have to assume it's because someone out there is deploying the module across a reasonably large network for notifications on numerous end points.

This thought ties in with a question I got recently. If you do want to use toast notifications across your network, do you have to deploy the module everywhere?

The short answer is: Nope!

The long answer is the rest of this post.

## **It's All XML, Baby!**

I mentioned it during my Mississippi PowerShell User Group [presentation](https://youtu.be/kI5Dsub8etw) back in June, and I should mention it more often: at the end of the day, toast notifications are just an XML payload.

A viable option is to only install BurntToast on your workstation and use it to author your toasts, then "send" the resulting XML out to your endpoints. You'll need to deal with the notification manager directly, but it isn't that bad in all honesty.

There's a couple of things you'll need to do to tidy things up, and you'll have to use the advanced (read: not `New-BurntToastNotification`) functions.

## **Example One: Static Content For All!**

In our first example, we'll create one toast notification and ship the XML as static content.

Let's start by creating our notification, down to the point where we have our "content."

```powershell
$Text1 = New-BTText -Content 'No Time For Questions'
$Text2 = New-BTText -Content 'All servers are being shut down in 30 minutes, save your work!'
$Audio1 = New-BTAudio -Source 'ms-winsoundevent:Notification.Looping.Alarm'
$Binding1 = New-BTBinding -Children $Text1, $Text2
$Visual1 = New-BTVisual -BindingGeneric $Binding1
$Content1 = New-BTContent -Audio $Audio1 -Visual $Visual1

```

Now this needs to be cleaned up a little bit, as the output from ther underlying toolkit has a habit of adding curly brackets which some through to the final toast:

```powershell
$CleanContent = $Content1.GetContent().Replace('<text>{', '<text>')
$CleanContent = $CleanContent.Replace('}</text>', '</text>')
$CleanContent = $CleanContent.Replace('="{', '="')
$CleanContent = $CleanContent.Replace('}" ', '" ')

```

I'll leave the mechanics of getting this out onto your network to you, but as an example, let's say you've put it on a share that all of your clients can read from: `\\files\notifications\`

The rest of this example is carried out your end point(s), to start you'll need to load the required classes.

```powershell
$null = [Windows.UI.Notifications.ToastNotificationManager, Windows.UI.Notifications, ContentType = WindowsRuntime]
$null = [Windows.Data.Xml.Dom.XmlDocument, Windows.Data.Xml.Dom.XmlDocument, ContentType = WindowsRuntime]

```

Now, load in your XML from the file share, and set your AppId. I'm using the one from a [previous post](https://king.geek.nz/2018/02/04/burnttoast-appid-installer/), but if you want it to come up as "PowerShell" set it instead to `{1AC14E77-02E7-4E5D-B744-2EB1AE5198B7}\\WindowsPowerShell\\v1.0\\powershell.exe`

```powershell
$AppId = 'King.Geek.NZ!BurntToast'
$ToastXml = [Windows.Data.Xml.Dom.XmlDocument]::new()
$ToastXml.LoadXml((Get-Content -Path '\\files\notifications\ShutDownToast.xml'))

```

Finally, prime a new "ToastNotification" using your "XMLDocument" and ship it off to the Toast Notification Manager.

```powershell
$Toast = [Windows.UI.Notifications.ToastNotification]::new($ToastXml)
[Windows.UI.Notifications.ToastNotificationManager]::CreateToastNotifier($AppId).Show($Toast)

```

{{< figure src="/2018/08/ExampleToast.png" >}}

## **Example Two: A Little Customization**

The next logical thing to want is a little more customization in these toasts. Luckily, this is actually pretty simple. Think of the XML content you've saved to your file share as a temple and include some placeholder tokens in the text.

For example, rather than setting the warning in our previous toast to always show 30 minutes, use this:

```powershell
$Text2 = New-BTText -Content 'All servers are being shut down in __WARNTIME__, save your work!'

```

Now, on your end point, you can swap out that token.

```powershell
$Xml = Get-Content -Path '\\files\notifications\ShutDownToastTemplate.xml'
$Xml = $Xml.Replace('__WARNTIME__', '2 Minutes')
$ToastXml.LoadXml($Xml)

```

Aside from that, everything else remains the same.

## **Wrap Up**

And that's it. There is a bit of a learning curve to using the "advanced" functions to build your toasts. But once you've got the hang of them it does opening up a bunch of extra options to you.

I'm interested in knowing how you personally tackle the problem of getting toasts out to end users, and if there is anything I can bake into the module that would help you out in this regards.

Also, if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

