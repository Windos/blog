+++
categories = ["BurntToast", "PowerShell"]
date = 2019-04-01T20:49:54Z
description = ""
draft = false
thumbnail = "/2019/04/ben-kolde-470579-unsplash.jpg"
slug = "crouton-10"
summary = "If you can forgive incomplete help, the latest update of BurntToast has some cool new features. First one we'll be covering is Shoulder Taps!"
tags = ["BurntToast", "PowerShell"]
title = "Crouton #10 - Cold Shoulder (Taps)"

+++


Last night I pushed go on a big update to [BurntToast](https://www.powershellgallery.com/packages/BurntToast), bringing the version number up to [0.7.0](https://github.com/Windos/BurntToast/releases/tag/v0.7.0).

The features present in this update were done a short while ago, and then I hit a bit of a slow down when it came to cleaning up the documentation and help. Instead of delaying getting these features out into the wild, I elected to push the update with "enough" help.

I'll be making help the big focus of [v0.7.1](https://github.com/Windos/BurntToast/projects/3), and if you feel like chipping in at all please do feel free to fire through pull requests on GitHub.

With a few cool "headline" features, I needed a little help in deciding what to write about first so I reached out to Twitter.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Help me choose which new BurntToast feature to write about first (they&#39;ll both be done... just interested in knowing what people want *first*). <a href="https://twitter.com/hashtag/PowerShell?src=hash&amp;ref_src=twsrc%5Etfw">#PowerShell</a> <a href="https://twitter.com/hashtag/Toast?src=hash&amp;ref_src=twsrc%5Etfw">#Toast</a></p>&mdash; Josh (Windos) King (@WindosNZ) <a href="https://twitter.com/WindosNZ/status/1112645914647126017?ref_src=twsrc%5Etfw">April 1, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The answer was pretty clear, so today we'll be having a look at Shoulder Taps and how to make use of them yourself with BurntToast.

## But First, What the Heck Is a Shoulder Tap?

They're a feature of Windows 10 that sits somewhere between the Notifications API and the My People app. They were introduced in the Fall Creators Update for Windows 10 (1709), and the UWP Community Toolkit (now [Windows Community Toolkit](https://github.com/windows-toolkit/WindowsCommunityToolkit)) introduced support for them back in [February of 2018](https://github.com/windows-toolkit/WindowsCommunityToolkit/releases/tag/v2.2.0).

But, what are they?

They're an image based "notification" that can pop up against your contacts that you've got pinned to the task bar. The intention with them is that if you pin a contact and they send you an emoji or sticker in Skype, then you'll see a large version of it pop up.

<img src="/2019/04/heart-emoji-notification-small.gif" caption="Image from <a href="https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/my-people-notifications">Microsoft Docs</a>" />

For our purposes though, we can pin our own "notifier" contact and pop images up (backed up by a traditional toast notification) for a little more attention grabbing goodness.

## Pinning a Contact

So I've mentioned pinning a contact, and Shoulder Taps don't work without that so I guess we should find our way through this first. Unfortunately, I haven't (yet) found a way to script this so we're stuck with the GUI.

Start by firing up the People app, I'm assuming you've never done this before. If you have you can skip down the page a bit.

<img figure src="/2019/04/2019-04-02--1-.png" />

Head through the initial setup, assigning permissions as required (I chose not to let it send emails or have access to my calendar.

<img figure src="/2019/04/2019-04-02--4-.png" />

You'll be asked to import contacts. Unless you plan on actually using the People app for more than just these notifications, click the smaller "**Ready to go**" button.

Hit the plus to add a contact, give it a picture (this will show on the task bar), a name, and an email (it doesn't have to be real.)

<img figure src="/2019/04/2019-04-02--7-.png" />

Once added, click the pin at the top right of the contact's page. All going well, you'll see a small version of the avatar to the left of your clock.

<img figure src="/2019/04/Pinned.PNG" />

If you see it, job done. If not, let's try some troubleshooting; make sure that you're showing People on the task bar.

<img figure src="/2019/04/ShowPeople.PNG" />

Since we're talking troubleshooting, also head into the **Settings** app, navigate to **Personalization** and choose **Taskbar**. Here is where you'll find settings specific to Shoulder Tap notifications.

<img figure src="/2019/04/2019-04-02--9-.png" />

You'll want to make sure that at least the "**Show My People notifications**" option is enabled.

## Finally, Time for PowerShell!

Let's not waste anytime, here's the code for a Shoulder Tap:

```powershell
$Image = 'https://i.imgur.com/WKiNp5o.gif'
$Contact = 'stormy@example.com'
$Text = 'First Shoulder Tap', 'This is for the fallback toast.'

New-BurntToastShoulderTap -Image $Image -Person $Contact -Text $Text
```

<img src="/2019/04/Carlton.gif" />

Success!

... or not. You may get a note in the console about the AppId (a little on this mentioned below). To get rid of the warning, run `New-BTAppId -AppId 'Microsoft.People_8wekyb3d8bbwe!x4c7a3b7dy2188y46d4ya362y19ac5a5805e5x'`

In this example, that image can be a static image or an animated gif. The "**Person**" needs to be an email that matches the contact you've pinned to the task bar.

The "**Text**" is a little weird though, right? It's actually used to make a traditional toast, which is shown in the Action Center. It will also be used as a fall back option if you haven't got your contact pinned or you're on an older version of Windows 10.

<img src="/2019/04/FallBack.PNG" />

You'll notice that this toast has shown up under "People". This was a deliberate choice when putting this update together, rather than having it show up as coming from PowerShell. I felt this was a decent was of separating where different types of notifications were coming from.

### Not Shown Here

The command shown here, `New-BurntToastShoulderTap`, is the quickest way to get a Shoulder Tap. There are more advanced options, including using a sprite sheet instead of a gif. This is be a post in and of itself.

## But Why?

This is the part I often forget to think about... why bother writing this feature into BurntToast and why would one use it?

I'm personally planning on using this for personal alerts, showing a siren gif when my attention is needed.

<img src="/2019/04/Alarm.gif" />

I'm also planning on an alert to let me know I've been outbid on an auction.

<img src="/2019/04/Joey.gif" />

Basically, anytime you've got a toast for yourself you could feasibly swap it out for a shoulder tap. You can still get the full toast in the action center, including buttons, but you can have a little more fun with what shows up on your desktop.

Let me know on [Twitter](https://twitter.com/WindosNZ/) what you plan on doing with these notifications!

