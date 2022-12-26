+++
categories = ["PowerShell", "Nugget", "Windows"]
date = 2019-02-13T19:56:39Z
description = ""
draft = false
thumbnail = "/2019/02/daniel-watson-75027-unsplash.jpg"
images = ["/2019/02/daniel-watson-75027-unsplash.jpg"]
slug = "rsat-as-a-feature-i-thought-i-was-going-to-love-it-until-i-saw-it-in-action"
summary = "I'm a little late to this particular party, but I only just had the 1809 update on my workstation today."
tag = ["PowerShell", "Nugget", "Windows"]
title = "RSAT as a Feature: I Thought I Was Going to Love It, Until I Saw It in Action"
+++


Ok, that title may be a little hyperbolic. It's not like I **hate** the experience of having the Remote Server Administration Tools (RSAT) installable as a feature rather than a standalone download. It's just that it's not what I'd imagine when I first heard about it.

But what was I expecting? I thought we'd still be going into the classic Windows Features interface, just without the need to install the binaries (manually) before hand.

{{< figure src="/2019/02/WindowsFeatures.png" >}}

But as you can see... that isn't the case. Okay, no problem, it just means we do it through the Settings app, right? Correct! To get there, open **Settings** -> **Apps** -> **Manage optional features** -> **Add a feature**.

{{< figure src="/2019/02/2019-02-14--1-.png" >}}

Here's where I have my issue. Unlike the old interface, there no way to install all of (or a subset of) the RSAT tools at once. Okay sure, it means I can more carefully select the individual tools that I will use, though that's only one or two and will save me literally KBs.

## **Installing RSAT en Masse**

No one has time to sit there clicking through the GUI to get this particular job done. So off to PowerShell I went.

Falling back on old habits, I went straight to my old friend, `Get-WindowsOptionalFeature`. Just like the move from the classic GUI interface, this cmdlet no longer does the job either.

Instead you'll need to use `Get-WindowsCapability`. Usage is much the same as the older command, remember to specify the online flag as you're looking to change the running OS rather than an offline image.

To install **all** of the RSAT tools, run:

```powershell
Get-WindowsCapability -Name RSAT* -Online | Add-WindowsCapability -Online

```

Yes, frustratingly you do have to specify the online flag for both cmdlets (I tried to get away without it...).

All going well, the tools will install and you'll be good to go. For me, it threw an error with three to go. Trying the install a second time worked, but I put it through a filter to only try installing the features that hadn't already been done.

```powershell
Get-WindowsCapability -Name RSAT* -Online | Where State -ne 'Installed' | Add-WindowsCapability -Online

```

## **Moving Deck Chairs**

At the end of the day, this really is just a minor annoyance. Not having to go out to the net and look for the most recent version of RSAT every time there's a major Windows 10 update is going to be nice.

I just went into this update with my own assumptions, and got a touch frustrated as a result.

On the bright side, I'm loving how much of the UI is affected by the dark mode now.

