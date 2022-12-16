+++
categories = ["BurntToast", "PowerShell"]
date = 2019-01-09T16:37:08Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2019/01/ryoji-iwata-669950-unsplash.jpg"
slug = "crouton-8-redux"
summary = "I didn't do the best job of the examples in my original post on this topic. Let's put that right."
tags = ["BurntToast", "PowerShell"]
title = "Crouton #8 Redux - Now with a More Complete Example"

+++


It feels like only yesterday that I wrote [Crouton #8](https://king.geek.nz/2018/08/21/crouton-8-i-like-your-code-but-i-dont-want-it-on-all-my-computers/), but it’s actually been over four months. Obviously, time has been flying by recently. I’m revisiting the topic following a question from [@jkrhrt](https://twitter.com/jkrhrt) on Twitter asking for example files, and I figured it might make for a decent mini blog post.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr"><a href="https://twitter.com/WindosNZ?ref_src=twsrc%5Etfw">@WindosNZ</a> Do you have any example files relating to your &#39;Crouton #8&#39; blog post? I just can&#39;t quite wrap my head around it. TIA.<a href="https://t.co/oJYQ6hy2SQ">https://t.co/oJYQ6hy2SQ</a></p>&mdash; 𝕵𝖆𝖈𝖐 (@jkrhrt) <a href="https://twitter.com/jkrhrt/status/1081694822191300609?ref_src=twsrc%5Etfw">January 5, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## **Recap**

To very quickly recap the previous post, we were looking at how to use [BurntToast](https://powershellgallery.com/packages/BurntToast) to author toasts and display them on remote computers without having to have the BurntToast module itself on all those computers.

To do this you’d have the module installed on your workstation (or wherever you’re wanting to “design” your toasts). On this computer, you import the module and use all the functions within it to create an XML file that you’d then store in a central location like a file server.

Then on your remote end points, you can read the contents of your XML file and use the native Windows APIs to show your toasts without calling on BurntToast.

With that in mind, let’s look at those two scenarios in their own sections.

_As a disclaimer, I’ve edited the formatting of the XML output below to make it easier to read. You don’t need to do this… I just wanted it to look nicer on the blog._

## **Authoring Workstation**

I’m cheating a little bit here and re-using the second example from the original Crouton post, but let’s build it out fully instead of simply referring to the first example like we’d done on the first go around.

Assuming you’ve already got the module installed, it should automatically import so let’s jump straight in. You must use the “advanced” functions to build the toast, and a simple one could look a little something like this:

```powershell
$Text1 = New-BTText -Content 'No Time For Questions'
$Text2 = New-BTText -Content 'All servers are being shut down in __WARNTIME__, save your work!'
$Audio1 = New-BTAudio -Source 'ms-winsoundevent:Notification.Looping.Alarm'
$Binding1 = New-BTBinding -Children $Text1, $Text2
$Visual1 = New-BTVisual -BindingGeneric $Binding1
$Content1 = New-BTContent -Audio $Audio1 -Visual $Visual1

```

Once all of this has been run, you’ll have a variable called `$Content1`. This object has a method, `GetContent()` which allows you to extract the XML data from it. If you look at that now, however, you will see a lot of extra characters around the text fields, and these will render in the final toast.

```powershell
$Content1.GetContent()

```

```xml
<?xml version="1.0" encoding="utf-8"?>
<toast>
  <visual>
    <binding template="ToastGeneric">
      <text>{No Time For Questions}</text>
      <text>{All servers are being shut down in __WARNTIME__, save your work!}</text>
    </binding>
  </visual>
  <audio src="ms-winsoundevent:Notification.Looping.Alarm" />
</toast>

```

_Notice the curly braces..._

BurntToast would normally handle these for you when displaying the toast, but since you won’t be using BurntToast for this you’ll need to do the clean-up yourself:

```powershell
$CleanContent = $Content1.GetContent().Replace('<text>{', '<text>')
$CleanContent = $CleanContent.Replace('}</text>', '</text>')
$CleanContent = $CleanContent.Replace('="{', '="')
$CleanContent = $CleanContent.Replace('}" ', '" ')

```

_This is the full suite of replacements that could be needed for all sorts of toasts, and yes it is very much overkill for this example but I've included it for completeness sake._

And that’s going to look a lot better:

```xml
<?xml version="1.0" encoding="utf-8"?>
<toast>
  <visual>
    <binding template="ToastGeneric">
      <text>No Time For Questions</text>
      <text>All servers are being shut down in __WARNTIME__, save your work!</text>
    </binding>
  </visual>
  <audio src="ms-winsoundevent:Notification.Looping.Alarm" />
</toast>

```

Now that we’ve got that XML all cleaned up, we need to save it to our central server:

```powershell
Set-Content -Value $CleanContent -Path '\\FileServer\toasts$\RemoteToast.xml'

```

I’ve saved a copy of what this file would look like on [Gist](https://gist.github.com/Windos/16ad5ca744bc5c38cd4fb86f6e8067f9), without my “nice” blog formatting.

## **End Points**

On another computer, without BurntToast installed, go ahead an pull the XML content from the file server (or wherever you ended up saving the file):

```powershell
$Xml = Get-Content -Path '\\FileServer\toasts$\RemoteToast.xml'

```

The original text had a placeholder, which we’ll replace with real information now.

```powershell
$Xml = $Xml.Replace('__WARNTIME__', '2 Minutes')

```

I won’t break down these steps, there’s some of that in the original post. Effectively what you’re doing here is loading the native Windows runtimes and using them to pass the XML through to the Windows API responsible for displaying toasts.

```powershell
$AppId = '{1AC14E77-02E7-4E5D-B744-2EB1AE5198B7}\WindowsPowerShell\v1.0\powershell.exe'

$null = [Windows.UI.Notifications.ToastNotificationManager, Windows.UI.Notifications, ContentType = WindowsRuntime]
$null = [Windows.Data.Xml.Dom.XmlDocument, Windows.Data.Xml.Dom.XmlDocument, ContentType = WindowsRuntime]

$ToastXml = [Windows.Data.Xml.Dom.XmlDocument]::new()
$ToastXml.LoadXml($Xml)

$Toast = [Windows.UI.Notifications.ToastNotification]::new($ToastXml)
[Windows.UI.Notifications.ToastNotificationManager]::CreateToastNotifier($AppId).Show($Toast)

```

All going well, you’ll see a toast that looks like this:

{{< figure src="__GHOST_URL__/content/images/2019/01/UpdatedToastExample.PNG" >}}

## **Wrap Up**

I hope that made sense and was a lot clearer than my original post. Re-reading it while writing this… yeah, I could have done a better job of it. Ah well, it’s all a learning process.

