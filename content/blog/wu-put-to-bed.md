+++
categories = ["Windows"]
date = 2018-01-15T18:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/thumb-17.jpg"
slug = "wu-put-to-bed"
summary = "There were, and still are, many people frustrated with the fact that Windows 10 would decide to forcibly reboot for updates."
tags = ["Windows"]
title = "Windows Updates: Putting Update Avoidance to Bed"

+++


This will be my fourth, and final, [post](https://king.geek.nz/tags/#windows-update) on the topic of stopping updates from automatically installing on Windows 10.

If you're wanting to skip to the punch line:

* Yes, you **can** prevent updates from being installed,
* No, you **shouldn't** prevent updates from being installed,
* Do updates on **your** time, and don't leave them sitting there for weeks.

## **The Road So Far**

This journey started with an [answer on Super User](https://superuser.com/a/963933/36030). There were, and still are, many people frustrated with the fact that Windows 10 would decide to forcibly reboot for updates. Early on this would happen at very inappropriate times, often while you're in the middle of something.

Needless to say, people wanted to stop this from happening.

That Super User question now has a quarter of a million views, so it's obviously a hot topic.

My posts on the matter were experiments to validate that my "fix" was effective. The tests were repeated towards the end of 2017, given that there had been a handful of substantial feature updates to Windows 10 since the original round of testing.

## **The "Fix"**

Go check out the [answer](https://superuser.com/a/963933/36030) for the full write up.

It boils down to configuring the **Configure Automatic Updates** option under **Computer Configuration \ Administrative Templates \ Windows Components \ Windows Update** in your local group policy.

## **Does It Still Work?**

Yep!

All Windows 10 VMs in [my lab](https://king.geek.nz/WindowsUpdateUptime/) have been running for 140 days without a forced reboot.

The caveat here is that it probably doesn't work with the Home edition (via the registry, rather than policy.) I don't have access to that edition in order to do a test, so I have to rely on reports from others.

## **The Landscape Now**

I haven't applied this tweak to any of my personal machines and I don't remember the last time I had a forced reboot at a bad time.

Their users were very vocal, and Microsoft didn't have much choice but to listen. Windows 10 now has built in options to defer certain types of updates, or even pause updates for a little over month at a time.

Microsoft still don't want you ignoring updates outright, and I think they've done a good job of finding the middle ground.

## **Why You Shouldn't Ignore Updates**

Remember all the talk about Wannacry a while back? Remember all the comments in reply to stories from people that got hit?

"This was patched months ago, it's their own fault."

I'm paraphrasing, but I read something very similar to that comment a lot.

The point is, these patches aren't being released simply to cause you a personal annoyance. They're being released to fix issues and make your machine more secure.

Yes, that means you need to reboot from time to time.

## **Reboot on Your Time**

So, to wrap up here's the real fix:

**Get into the habit of pro-actively installing updates from time to time.**

Pick a day of the week and manually check for updates. I tend to manually check for updates every Friday night (even though I know there's usually only patches once a month.)

Finally, **only** press install if you're willing to follow it up with a reboot. If you install the updates, Windows will eventually do the reboot one way or another. Commit to the follow up, or you've only got yourself to blame.

