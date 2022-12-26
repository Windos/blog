+++
categories = ["PowerShell", "BurntToast"]
date = 2018-05-12T14:45:00Z
description = ""
draft = false
thumbnail = "/2018/05/6211443671_2739e09986_b-1.jpg"
images = ["/2018/05/6211443671_2739e09986_b-1.jpg"]
slug = "crouton-heroimages"
summary = "Ever find yourself needing to get an image up on your screen in a timely manner?"
tag = ["PowerShell", "BurntToast"]
title = "Crouton #7 - Superhero Landing"
+++


Ever find yourself needing to get an image up on your screen in a timely manner?

Yeah, me neither. But if you do find this to be the case then you can use a Toast Notification to do the job.

As with all of these "[Crouton](https://king.geek.nz/tags/#crouton)" posts, you'll need to have the [BurntToast](https://www.powershellgallery.com/packages/BurntToast) module installed in order to follow along.

## **The Scenario**

Let's say you have CCTV cameras outside your server rooms, and you want to get a snapshot when they pickup movement. Assuming your CCTV system can handle dumping a picture into a folder, you can watch for these images and display them as part of a toast.

I'm only going to focus on the construction of the Toast itself, not generating the picture or watching for new images (maybe we'll do a post on FileSystemWatcher events soon.)

## **Plug in the Toaster**

Unfortunately, we won't be able to use `New-BurntToastNotification` for this, so right off the bat you'll have a few more lines of code than you might normally have had.

First, let's sort out the text and image we'll be using:

```powershell
$Text1 = New-BTText -Content 'Intruder Alert!'
$Text2 = New-BTText -Content 'CCTV has picked up motion outside Server Room 1'
$Image1 = New-BTImage -Source 'C:\cctv\2018-05-13-12-53.jpg' -HeroImage

```

Notice the switch on our image, `-HeroImage`. This designates that the image is to be featured **very** prominently at the top of the Toast.

Next "bind" together all of our text elements and the hero image, and pass that binding into a visual element:

```powershell
$Binding = New-BTBinding -Children $Text1, $Text2 -HeroImage $Image1
$Visual = New-BTVisual -BindingGeneric $Binding

```

Finally, choose a sound to accompany the toast. I'm going with one of the built in alarm sounds, but you could [choose a claxon](https://king.geek.nz/2018/04/02/crouton-sounds/) if you really wanted to. Create a content element then submit it to be displayed:

```powershell
$Audio1 = New-BTAudio -Source ' ms-winsoundevent:Notification.Looping.Alarm' -Loop
$Content = New-BTContent -Audio $Audio1 -Visual $Visual -Duration Long

Submit-BTNotification -Content $Content

```

I set `-Loop` on my sound so that it keeps playing while the Toast is on screen, and set the duration to Long so that it's on the screen for a while (I really want to get this CCTV notification.)

When you run this, you'll get your rather spiffy looking Toast:

{{< figure src="/2018/05/herotoast.png" >}}

## **Wrap-up**

This post has been a long time coming. My last one went out the day I went back to work after a couple of weeks off... and it took a while to catch back up. Doesn't help that I'm also working on a couple of side projects at the moment which means this blog took a back seat for a little while.

The only project I'll mention now (and expect to hear more about it in the future), is [Adam Bertram's](https://twitter.com/adbertram/) upcoming e-learning service [TechSnips](https://techsnips.io/). I've been busy recording "snips" ready for the [official launch](https://www.adamtheautomator.com/introducing-techsnips-a-new-it-screencast-learning-platform/) on the 5th of June. If you're interested in getting in on the early soft launch, and get a 25% discount, make sure to check out [www.techsnips.io](https://www.techsnips.io/) and register your interest at the bottom of the page!

Also, remember that if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

