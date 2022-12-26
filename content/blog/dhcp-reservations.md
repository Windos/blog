+++
categories = ["PowerShell", "Nugget"]
date = 2017-01-29T12:50:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-3.png"
images = ["/2018/05/thumb-3.png"]
slug = "dhcp-reservations"
summary = "Have multiple DHCP servers that aren't in a cluster? Don't worry!"
tag = ["PowerShell", "Nugget"]
title = "DHCP Reservations: No Cluster? No Problem!"
+++


Have multiple DHCP servers that aren't in a cluster? Don't worry, there are more people in this boat than you might think.

From time to time you'll need to convert a lease into a reservation, but how do you go about making sure your clients reliably pick up their DHCP reservations (regardless of which DHCP server responds first)?

## **Just copy the reservations!**

Ergh, but that sounds like a lot of work.

It sure would be if you did it through the GUI, but your first thought these days is PowerShell, right?

In my environment I have two DHCP servers, that share half of a DHCP scope each. For example, DHCP1 might lease 192.168.1.1 thought 192.168.1.99 and DHCP2 might lease 192.168.1.100 to 192.168.1.199.

Whenever I create a new reservation on one server, I copy the reservations from both to make sure they are all in sync (you never know when someone else in your team has snuck a change in without you knowing about it.)

First thing is to make sure you're got the “Remote Server Administration Tools” (RSAT) installed, which include the associated PowerShell modules. Just head over to Google and find the latest install for your version of Windows.

Next, get a 'copy' of the current reservations on each server:

```powershell
$1 = Get-DhcpServerv4Reservation -ComputerName DHCP1 -ScopeId 192.168.1.0

$2 = Get-DhcpServerv4Reservation -ComputerName DHCP2 -ScopeId 192.168.1.0

```

Then 'paste' them to the alternative server:

```powershell
$1 | Add-DhcpServerv4Reservation -ComputerName DHCP2

$2 | Add-DhcpServerv4Reservation -ComputerName DHCP1

```

If you've got more than two servers, you might want to consider iterating through them and if you find yourself doing this a lot you'd benefit from turning it into a function.

That's all there is to it!

