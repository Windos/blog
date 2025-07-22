+++
categories = ["BurntToast", "PowerShell"]
date = 2025-07-22T10:30:00Z
description = "It's been a long, eventful, decade. The perfect amount of time to burn some toast!"
draft = false
thumbnail = "/2025/07/BurntToast.png"
images = ["/2025/07/BurntToast.png"]
slug = "burnttoast-v1"
summary = "It's been a long, eventful, decade. The perfect amount of time to burn some toast!"
tag = ["BurntToast", "PowerShell"]
title = "Ten Years Toasting: BurntToast Hits the Big v1!"
aliases = [
    "/2025/07/22/burnttoast-v1/amp/",
    "/2025/07/22/burnttoast-v1/",
    "/2025/07/22/burnttoast-v1"
]
+++

## A Decade in the Oven

It feels like just yesterday that a little PowerShell module, then known as BurntToast, first saw the light of day. And by "yesterday," of course, I mean "ten years ago." Back in July 2015, [v0.3](https://www.powershellgallery.com/packages/BurntToast/0.3) made its initial public appearance on the PowerShell Gallery. Time, it seems, has a knack for always accelerating.

From those humble beginnings, the module has certainly come a long way. What started as and answer to a question [on Reddit](https://www.reddit.com/r/PowerShell/comments/3drm4w/comment/ct8iixa/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) turned into a personal project that scratched a particular itch has grown into something far more substantial than I could have ever anticipated. The sheer scale of its adoption is quite remarkable: BurntToast has been downloaded over 25 million times from the [PowerShell Gallery](https://www.powershellgallery.com/packages/BurntToast). This figure is not merely a number; it represents countless automations, critical alerts, and likely a few saved sanity points for system administrators and developers worldwide. Not having any telemetry in the module means I don't know who is actually using the module nor for what... but I have heard off-the-record stories about it being used in some _very_ interesting places.

## The Long Simmer to v1.0.0

Today marks another significant milestone: the official release of BurntToast [v1.0.0](https://github.com/Windos/BurntToast/releases/tag/v1.0.0). For those keeping score, yes, that's a full decade to hit what many might consider a baseline "stable" version. Why the long simmer, you ask? Sometimes quality toast starts with proofing the bread... it's also just been a busy time for me personally!

The journey to v1.0.0 has been, shall we say, eventful. There were initial plans for an "almost complete [rewrite](https://toastit.dev/2021/04/20/road-to-burnttoast-v1-1/)" of the module for this release, a vision that, while ambitious, proved to be a black hole where time entered but nothing could escape. Life, as it tends to do, also intervened, with factors like job changes and general busyness pushing BurntToast to the back burner at various points. The reality of maintaining an open-source project alongside a demanding career often means that grand re-architecture plans must yield to pragmatic realities. Ultimately, the decision was made to pivot away from a full, ground-up rewrite. My hope in changing course on this plan is to ensure the module's continued development and stability, focusing efforts where they matter most.

## What's Cooking in v1.0.0?

There are a couple of breaking changes in this release.

Firstly, [Shoulder Tap Notifications](https://toastit.dev/2019/04/01/crouton-10/) were removed. They were flaky at best, and I'd heard no feedback from anyone that they were using them. These were removed so that I could focus on other parts of the module.

Next, custom audio paths were removed. At some point, Microsoft removed support for this in their APIs (at least, in a way this module could use) so this feature was removed to avoid confusion. If you're on an old install of Windows 10, you'll want to stick to v0.8.5 of BurntToast if you want to use custom audio.

As for what's new: ACTIONABLE NOTIFICATIONS!

There will be more written about this soon (but I'm not going to commit to when at this point), but it is now possible to define a script block that will execute when a user clicks on your toast (or a button on it). What's more, this also works in Windows PowerShell without needing to mess around with protocol handlers.

This is HUGE, and honestly the one thing I was waiting to "solve" before releasing v1.0.0.

## A Community Effort

This v1.0.0 release, and indeed the entire journey of BurntToast, would not have been possible without the incredible support of the PowerShell community. Open-source projects thrive on collaboration, and this module is no exception. Many [individuals](https://github.com/Windos/BurntToast/graphs/contributors) have contributed their time, expertise, and patience over the years.

I wanted to name specific people here, but given the amount of time that has passed I know I will miss someone and I do not want to do that. You've all made valuable contributions for which I'm eternally grateful. The community’s engagement, whether through opening issues, suggesting features, or providing feedback on preview releases, has been instrumental in shaping the module into what it is today.

## BurntToast: What It Is, What It Isn't, What's Next

For those new to the party, BurntToast is a module designed to simplify the creation and display of Windows toast notifications. It provides a way for sysadmins to send rich (and now actionable) notifications to users' desktops in a language they already know and love; PowerShell!

However, it's important to clarify what BurntToast is not. It is not a full-blown notification framework with server-side components or complex delivery mechanisms. It's a client-side tool, designed to integrate seamlessly with your PowerShell scripts and automations to deliver timely, user-facing information.

_In saying that, you [don't actually need to install](https://toastit.dev/2018/08/20/crouton-8-i-like-your-code-but-i-dont-want-it-on-all-my-computers/) BurntToast on your users' devices._

Looking ahead, the release of v1.0.0 signifies a commitment to more predictable and stable development. Any future breaking changes will only occur with a major version bump, providing users with clear expectations and ample warning for necessary script adjustments.

To truly embrace this new chapter, the repository is being treated as a "blank slate." This means that all existing issues on the [GitHub repository](https://github.com/Windos/BurntToast) will be closed. If an issue is still relevant, or if you encounter a problem with the new v1.0.0 release, please feel free to open a new issue or discussion. This "issue bankruptcy" is a practical step to ensure the issue tracker accurately reflects the current state of the module and helps focus development efforts on active concerns.

The future of BurntToast will continue to be driven by the needs of the community and the evolution of the Windows notification platform. There are no concrete plans to announce just yet, but the focus will remain on enhancing the module's core capabilities and ensuring it remains a reliable tool in your automation arsenal.

## Raising a Glass to the Future

It’s been an incredible ten years, and watching BurntToast grow from a small script to a module with millions of downloads has been, frankly, humbling. A huge thank you to everyone who has used it, contributed to it, reported bugs, or just quietly admired it from afar. Your support is what keeps the toaster warm.

Here's to many more years of perfectly charred toast notifications. Cheers!
