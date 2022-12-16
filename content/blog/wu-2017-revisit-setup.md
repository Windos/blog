+++
categories = ["PowerShell", "Windows"]
date = 2017-08-16T12:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-7.jpg"
slug = "wu-2017-revisit-setup"
summary = "It's been nine months, time to revisit the prevention of Windows Update reboots."
tag = ["PowerShell", "Windows"]
title = "Revisiting Windows Updates in 2017: The Lab Setup"

+++


It’s been nine months since I last looked into preventing Windows Update related reboots in Windows 10. It feels like so much has changed since then and a [recent comment](http://disq.us/p/1kfm9ch) made me realize there is more to test.

With that in mind, I’m rebuilding my lab and will be introducing test cases over time. This will be a very short post detailing the first two test cases and how you can follow along with the results as they come about.

### What’s different this time?

[Last time](https://king.geek.nz/2016/10/18/wu-windows-1607/) we monitored the prevention of reboots by being logged into the virtual machines and taking timed screenshots.

The result was a **lot** of files, using a lot of OneDrive space.

This time around I’ll be feeding the VM’s uptime into a file which GitHub renders as a nicely formatted webpage. [Check it out](https://king.geek.nz/WindowsUpdateUptime/)!

If you’re so inclined, [check out the code](https://github.com/Windos/WindowsUpdateUptime/blob/master/ReportUptime.ps1) gathering this data and getting it up to GitHub.

Oh, and the lab is running inside Hyper-V on Windows Server 2016 (Core).

### Test cases

The first two test cases are nice and simple:

1.  Windows 10 Professional, Version 1703 with a user logged in.
2.  Windows 10 Enterprise, Version 1703 not logged in.

### Closing comments

At this stage I can’t use Windows 10 Home in my tests as I don’t have a license for it. This is a real bummer as I’ve heard a lot of reports that the method being used to stop these reboots doesn’t work in Home but I’ve never been able to see it myself.

This may change in the future, but for now all test cases will be on Professional or Enterprise.

The results page will be updated as more tests are included, so if you want to know any developments look there. It’ll be at least two months before the first follow up post with the first round of properly documented info



