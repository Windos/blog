+++
categories = ["Windows"]
date = 2016-11-06T10:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb.png"
images = ["/2018/05/thumb.png"]
slug = "win-10-update-up-time"
summary = "Three weeks on, did Windows 10 decide to reboot?"
tag = ["Windows"]
title = "Update on Windows Update... Up Time"
+++

Three weeks ago, I [started up an experiment](https://king.geek.nz/2016/10/18/wu-windows-1607/) to find out if an old [SuperUser answer](https://superuser.com/questions/957267/how-to-disable-automatic-reboots-in-windows-10/963933#963933) of mine was still valid.

The TL;DR of the previous post was:

* VMs built running Professional and Enterprise editions of Windows 10, version 1607.
* PowerShell script is running to collect a screenshot of the VMs every hour.
* These screenshots also show system uptime.

## **So, it rebooted right?**

Nope. Three weeks (nearly) later, and we still haven't had a reboot.

{{< figure src="/2018/05/Screenshot-Win10-Ent-201611071314.png" >}}

{{< figure src="/2018/05/Screenshot-Win10-Pro-201611071314.png" >}}

Now a caveat: I haven't set _any_ other Windows Update related settings. This includes, but is not limited to, setting active house, restart or advanced options. I don't know how these settings would affect, or potentially override, the 'fix' and the complexity in introducing them as variables to this test means... well I'm not doing it.

Also, **NEVER** click the install button within the Windows Update interface until you're ready to install **AND** reboot your box. Once you've clicked that install button, all hope is lost and a reboot will happen whenever Windows decides to.

{{< figure src="/2018/05/danger.png" >}}

## **That's it?**

Nope, again!

I'll keep the VMs running for as long as I can. The screenshots are now being saved to OneDrive, so anyone can follow along if they are interested.

Check out the folder: [Win10-Screenshots](https://1drv.ms/f/s!Ah37swlUrkCXk_oYpAxxlT9_89Xyog)

For the time being **all** screenshots are going into there. When I get a chance I'll write a script to clean them up.
