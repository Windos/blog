+++
categories = ["PowerShell", "Windows", "Nugget"]
date = 2014-09-21T12:00:00Z
description = ""
draft = false
featured_image = "/2018/05/1024px-CISV_trust_game.JPG"
images = ["/2018/05/1024px-CISV_trust_game.JPG"]
slug = "trust-no-reboot"
summary = "Perhaps it was a helpdesk technician powering up a cloned desktop while connected to the network."
tag = ["PowerShell", "Windows", "Nugget"]
title = "Repairing Trust Relationships Without a Reboot"
+++


It's hopefully not something that comes up often, but if you work in a Windows environment you will undoubtedly come across machines with trust relationship issues with the domain. Maybe it was an overeager High Availability action on a vSphere guest resulting in two copies of the same server running at once. Or perhaps it was a helpdesk technician powering up a cloned desktop while connected to the network.

{{< figure src="/2018/05/trustrelationship.png" >}}

Suddenly you can't get onto the machine using your domain credentials.

Up until recently, the method I used to fix these trust relationships was:

1. Log into the machine with a local admin account.
2. Change the domain name from `example.com` to just `example` to force the machine to rejoin the domain.
3. Oblige the operating system's request for a reboot (not ideal if the issue was discovered during business hours.)

Recently I watched [Orin Thomas'](https://twitter.com/orinthomas) New Zealand TechEd session, [30 common tasks you perform using the GUI that you can do faster in Windows PowerShell](http://channel9.msdn.com/Events/TechEd/NewZealand/2014/DCIM324). One of the 30 tasks Orin covered was fixing domain trust issues.

```powershell
Test-ComputerSecureChannel -Credential example.com\admin -Repair

```

After running this command, the trust relationship is repaired. No reboot needed.

This one nugget of information will potentially save me 15 to 20 minutes per month, and I've made sure our helpdesk team know about it to save them a little time in their travels.

