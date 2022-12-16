+++
categories = ["PowerShell", "Nugget"]
date = 2014-09-13T12:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/ArtAndFeminismNYC-Generations.jpg"
slug = "ps-important-resource"
summary = "There is one thing I learnt to do early on and continue to do to this day: Ask for help."
tags = ["PowerShell", "Nugget"]
title = "The Single Most Important Resource When Learning Windows PowerShell"

+++


So you've decided to learn Windows PowerShell. You're probably reading a fair share of blogs, forums and even books. Maybe there is a specific task you're trying to complete and have found out that PowerShell is the only avenue to do so. Or maybe you've heard a lot about PowerShell, and it's ever increasing functionality within Windows, and are curious about how you can make it work for you in your day job.

I'm not going to pretend that I'm a PowerShell guru, or even an expert. But I do use it daily. There is one thing I learnt to do early on and continue to do to this day:

**Ask for help**

I don't mean jumping on a forum or [Super User](http://superuser.com/), though these are both great options for figuring out some of the more challenging dilema you may face. I'm referring to PowerShell's build in help cmdlet; [**Get-Help**](http://go.microsoft.com/fwlink/p/?linkid=289584)

## Making the help come to you

Using a PowerShell cmdlet brings the help to you, where you need it. Whether you're working in the prompt, or the ISE, you'll get the information you're after without having to minimize, open, or move anything.

That's not to say you're limited to reading the help only within the same window that you're working in. To quickly open the help document in another window, use the `-ShowWindow` switch.

If you prefer to read your help documents on a nicely formatted website (like I do), then use the `-Online` switch. This will open up the relevant TechNet page for the cmdlet in question.

## Always the latest

Earlier versions of PowerShell, specifically v1 and 2, shipped with the help files. This means if there was a mistake in the help files, or they just needed updating in general, the developers had to wait for the next PowerShell update.

From version 3 and above the help files are now downloaded and aren't included by default. To download them for the first time, or update existing help files, simply run `Update-Help`.

The PowerShell developers can now push updates to the help whenever it is needed, meaning you get the most up to date help available.

## How do you actually use Get-Help?

Using `Get-Help` is fairly easy, though it does have it's own help file if you're interested (`Get-Help Get-Help`)

Simply specify the cmdlet you need help with. This will give you a standard, yet limited, subset of the help available for the cmdlet, including:

* A quick synopsis
* The cmdlets syntax
* A longer description
* Related links, including related cmdlets and a TechNet URL if available.

If you need more detailed information, including extra notes about every parameter and examples of how to use the cmdlet you can use `-detailed` or `-full` switch. Detailed gives more information, but not as much as full. I only ever use the full switch.

If you only want to see some examples of the cmdlet in action, use the `-examples` switch.

## Help to the rescue

Say that you're trying to get Windows Events. You know, maybe from past experience, google, or someone else's script, that you can use `Get-EventLog` to perform the task. You have seen that you can specify the times between which you would like to see events. However, you're not sure if the correct parameters are "start" and "end" or "before" and "after."

```powershell
PS C:\> help Get-EventLog

NAME
    Get-EventLog

SYNOPSIS
    Gets the events in an event log, or a list of the event logs, on the local or remote computers.

SYNTAX
    Get-EventLog [-LogName] <String> [[-InstanceId] <Int64[]>] [-After <DateTime>] [-AsBaseObject] [-Before <DateTime>] [-ComputerName <String[]>] [-EntryType <String[]>] [-Index <Int32[]>] [-Message <String>] [-Newest <Int32>] [-Source <String[]>] [-UserName <String[]>] [<CommonParameters>]

-- More --
```

With a quick glance at the syntax, you can see both `-After` and `-Before` and that they both take DateTime values.

Without leaving the window, you've got your answer and can keep on working.

## What now?

Start using `Get-Help`, or more likely `help`, whenever you're trying to figure out how to use a new cmdlet. Use it when you're trying to use a cmdlet you've used in the past in a different way. Make it your first port of call. Often times you'll find that if it doesn't give you the answer outright, you'll pick up the breadcrumbs you need to find it.

Use `help` to read about more advanced topics or topics that don't relate to a specific cmdlet. These topics start with "about_" so use a wildcard to get a list of them and pick something interesting: `Get-Help about_*`

```powershell
PS C:\> get-help about_Arrays
TOPIC
    about_Arrays

SHORT DESCRIPTION
    Describes arrays, which are data structures designed to store collections of items.

-- More --
```

Most importantly keep going with PowerShell. A new version of Windows is just around the corner and with that comes a new version of PowerShell with even greater functionality. The foundation you build now will help you do even more going forward.

PowerShell is a broad topic. Even it's help goes much deeper than I could ever explore in one post; this one only scratches the surface. I hope, however, that it has at least introduced you to your new go-to resource when you need a little guidance



