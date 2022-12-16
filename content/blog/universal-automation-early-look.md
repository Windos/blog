+++
categories = ["PowerShell", "Toolbox"]
date = 2020-01-15T22:21:38Z
description = ""
draft = false
thumbnail = "/2020/01/franck-v-U3sOwViXhkY-unsplash.jpg"
slug = "universal-automation-early-look"
summary = "I had the pleasure of testing Universal Automation while it was in private beta. Now the beta is open to all and I'm really excited for it to be in more hands because it's a very interesting project!"
tags = ["PowerShell", "Toolbox"]
title = "Universal Automation: An Early Look"

+++


Chances are good that if you've been around any part of the PowerShell community during the past couple of years, you've at least heard of [Universal Dashboard](https://universaldashboard.io/) (UD).

If, somehow, you haven't heard of it yet UD is a product from [Ironman Software](https://ironmansoftware.com/), which is headed up by [Adam Driscoll](https://twitter.com/adamdriscoll). It started as a tool focused on easily spinning up dashboards using PowerShell and has since developed into a full-on web framework. It has even given birth of a [community of its own](https://forums.universaldashboard.io/).

But UD isn't the topic of this post, at least not directly.

## Enter Universal Automation

The latest offering from Ironman Software (or [one of them](https://ironmansoftware.com/psscriptpad/)...) is [Universal Automation](https://ironmansoftware.com/universal-automation/) (UA).

As the name implies, UA is an automation platform for PowerShell.

If you're anything like me, you've probably got a bunch of scripts running as scheduled tasks or jobs on a server (or two). UA is a nice step up in [IT maturity](https://www.thinkhdi.com/library/supportworld/2018/it-maturity-matters-because-it-matters.aspx.) from that scenario.

What do you get over and above just throwing your scripts at the task scheduler?

First, it's backed by a database that logs all of the times your scripts ran, their status, their output, and how much time you've saved by automating a task instead of performing it manually. All of this opens up a number of options for reporting on your automation wins.

Second, it leveraged git for built in version control. You can even configure a remote to which will automatically pull and push changes. This means you can set UA up on a server, configure the git remote, and then manage the scripts and their schedules from the comfort of your workstation!

Third is really nice, it runs on Linux. If you're the type to have random scripts you run at home (I'm not the only one, I'm sure), then you could run UA on a Raspberry Pi.

There are more features, but the final one I'll talk about here is that it leverages UD to give you a dashboard for your UA server. This dashboard is optional but very, very cool. It gives you a visual of the status of all your scripts and allows you to manage them (heck, you can even edit your scripts directly within the dashboard!)

{{< figure src="/2020/01/image-3.png" >}}

## Go Play!

This tool has been in private beta for a little longer than a month, and just in the last couple of days it graduated to public beta. You can get your hands on it now via the PowerShell Gallery remembering that there are two components, the [server](https://www.powershellgallery.com/packages/UniversalAutomation) and [companion dashboard](https://www.powershellgallery.com/packages/UniversalAutomation.Dashboard).

While not yet ready to be deployed in production and take on all of the scheduled scripts you're running today, I would encourage you to give it a go.

As you test out your unique use cases, you're bound to run into small issues here and there. If you do, please do open an [issue on GitHub](https://github.com/ironmansoftware/universal-automation). Adam and the crew can't fix problems that they don't yet know exist!

You'll notice I haven't given any instructions here on how to use, or even install, UA. The thing is that it is under very active development at the moment and the best thing I can do to ensure instructions are up to date is point you at the documentation.

Go check out the [docs](https://docs.universalautomation.io/), install the tool, and have fun!

## More to Come

There's going to be more content about Universal Automation coming soon. I've got a couple of cool use cases I want to show off (with code samples that you won't find in the docs), but they deserve posts of their own.

In the meantime, I'd love [to hear](https://twitter.com/WindosNZ) what sort of use cases you could be looking to throw at UA.

## Credit

Hero image by [Franck V.](https://unsplash.com/@franckinjapan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/automation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

