+++
categories = ["PowerShell", "BurntToast"]
date = 2018-04-01T16:30:00Z
description = ""
draft = false
featured_image = "/2018/05/feature.jpg"
images = ["/2018/05/feature.jpg"]
slug = "crouton-sounds"
summary = "It gives me a glimpse into how people are using the module and it also focuses my documentation efforts on something that might actually be useful."
tag = ["PowerShell", "BurntToast"]
title = "Crouton #6 - Dance to Your Own Beat"
+++


[BurntToast](https://www.powershellgallery.com/packages/BurntToast) is long overdue an update (sorry!) and right alongside that is the project's need for better documentation.

I recently got a tweet about how to get a toast to play custom sound. I really like getting messages like this for two reasons: it gives me a glimpse into how people are using the module and it also focuses my documentation efforts on something that might actually be useful.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr"><a href="https://twitter.com/WindosNZ?ref_src=twsrc%5Etfw">@WindosNZ</a> hi.  using burnt toast can&#39;t seem to specify audio file.  How do I do this:<br><br>new-burnttoastnotification -xxsoundsxx &#39;c:\users...\foobar.wav&#39;<br><br>Thanks!</p>&mdash; Jim Dorf (@jimdorf) <a href="https://twitter.com/jimdorf/status/979940137080537089?ref_src=twsrc%5Etfw">March 31, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

**Take off the Training Wheels**

The natural first steps for someone creating toast notifications is the `New-BurntToastNotification` function. This function is meant to be a short runway for getting off the ground, it exposes enough customizability to tailor your own basic notifications. It also has enough default values that you can run the function raw and get _something_ on screen.

Behind the scenes, there are 15 other functions available to create your preferred flavor of toast.

Unfortunately, this is an all or nothing switch. You can't use these 15 "advanced" functions alongside the "basic" one, and vice versa.

**The Basics of Going Advanced**

The best way to get your head around this switch is to see it in practice. The default toast from `New-BurntToastNotification` looks like this:

{{< figure src="/2018/05/defaulttoast.png" >}}

To replicate this with the advanced function is somewhat more involved:

```powershell
$Text1 = New-BTText -Content 'Default Notification'
$ImagePath = Join-Path -Path (Get-Module BurntToast -ListAvailable).ModuleBase -ChildPath 'Images\BurntToast.png'
$Image1 = New-BTImage -Source $ImagePath -AppLogoOverride -Crop Circle
$Audio1 = New-BTAudio -Source 'ms-winsoundevent:Notification.Default'

$Binding1 = New-BTBinding -Children $Text1 -AppLogoOverride $Image1
$Visual1 = New-BTVisual -BindingGeneric $Binding1
$Content1 = New-BTContent -Visual $Visual1 -Audio $audio1

Submit-BTNotification -Content $Content1

```

Yes, there's a lot going on there, especially compared to the basic function. I'll go over more of those commands another day and try my best to focus the rest of this post on the original tweet.

## **This Song Is My Jam**

Luckily, switching to your own audio file is pretty simple, once you're already using the advanced functions. Do drill down on that, if you take the previous audio component:

```powershell
$Audio1 = New-BTAudio -Source 'ms-winsoundevent:Notification.Default'

```

Instead of a **Source**, you want to specify a **Path**. This path only supports a set of common file extensions (check the help), though you'd have to work pretty hard to be using something that's not supported. An example of this in action, is:

```powershell
$Audio1 = New-BTAudio -Path 'C:\Music\FavSong.mp3'

```

## **Closing Thoughts**

That's really all there is to getting toasts to play custom sounds. The biggest hurdle really is just switching over from `New-BurntToastNotification`. Once you've made that change, the rest is simple.

If you're doing something interesting with BurntToast, or you're figuring out how to do something with it, let me know in the comments below or over on [Twitter](https://twitter.com/WindosNZ).

Also, remember that if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

