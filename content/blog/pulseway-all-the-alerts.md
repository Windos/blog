+++
categories = ["PowerShell", "Monitoring"]
date = 2015-06-29T12:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/1280px-US_Navy_030516-N-5862D-010_A_recruit_division_sings_as_they_successfully_complete_the_final_leg_of_a_Battle_Stations_drill.jpg"
images = ["/2018/05/1280px-US_Navy_030516-N-5862D-010_A_recruit_division_sings_as_they_successfully_complete_the_final_leg_of_a_Battle_Stations_drill.jpg"]
slug = "pulseway-all-the-alerts"
summary = "Need to know when your servers CPU is pegged at 100%? Check. Need to know when a disk simply isn't present in the OS after booting up? Uhh..."
tag = ["PowerShell", "Monitoring"]
title = "With PowerShell, Pulseway Can Alert on Anything"
+++


## **The Pitch**

Need to monitor some servers (and/or workstations)? [Pulseway](http://www.pulseway.com/) is a good solution. The configuration of what you want alerts on is as simple as putting checks in boxes.

{{< figure src="/2018/05/standard-notify.png" >}}

* Need to know when someone logs into your server? **Check**
* Need to know when someone installs something on your server? **Check**
* Need to know when your servers CPU is pegged at 100%? **Check**
* Need to know when your servers data drive is running out of free space? **Check**
* Need to know when a disk simply isn't present in the OS after booting up? Uhh...

## **The Fix (or, PowerShell to the Rescue!)**

One seemingly overlooked option for Pulseway notifications is monitoring the event log. Sure, you could look for your bog standard event log entries. Audit results. Failed login attempts. GPO failures.

You know what is really good at manipulating the event log, not just [viewing](https://technet.microsoft.com/en-us/library/ee176846.aspx)/[filtering](http://blogs.technet.com/b/heyscriptingguy/archive/2011/01/24/use-powershell-cmdlet-to-filter-event-log-for-easy-parsing.aspx)/[clearing](https://technet.microsoft.com/en-us/library/hh849789.aspx) logs, but also [adding](https://technet.microsoft.com/en-us/library/hh849768.aspx) events? PowerShell.

Yes, I was being vague and mysterious there. Don't worry we'll get into the nuts and bolts soon.

At a high level:

* PowerShell checks for 'stuff', possibly using a PS Scheduled Job.
* PowerShell registers events in a systems event log.
* Pulseway monitors the event log.
* Pulseway sends out alerts when it sees certain events.
* You sleep easy(er)

## **The Nut and Bolts**

The first thing you need to do is get your event log ready to accept your custom events. This allows you to specify the 'source' of the events, which makes it easier to filter on them. I also like to put my PowerShell generated events in the Windows PowerShell log (rather than application, or security, or the like.)

```powershell
New-EventLog -LogName 'Windows PowerShell' -Source 'DiskCheck'

```

This is something you only have to do once, going forward you'll just be referencing the source you've created.

Next, you need to figure out what PowerShell should be checking and script it, including writing to the event log.

```powershell
function Test-ConnectedDisk
{
    [CmdletBinding()]
    Param (
        [string] $Server,
        [string] $Disk
    )

    $OsPartition = Get-Partition -DriveLetter $Disk -CimSession $Server -ErrorAction SilentlyContinue

    if (!$OsPartition)
    {
        Write-EventLog -LogName 'Windows PowerShell' -Source 'DiskCheck' -EntryType Error -EventId 120 -Message 'DISK MISSING!' -ComputerName $Server
    }
    else
    {
        Write-EventLog -LogName 'Windows PowerShell' -Source 'DiskCheck' -EntryType Information -EventId 121 -Message 'Disk is present and accounted for.' -ComputerName $Server
    }
}

```

I like to put functions like the one above into a module so that they can be easily called by a PS Scheduled Job, and updated without touching the job itself. As for creating the job, I want this check to happen once an hour, on the hour. The job requires network access, as I run a few jobs from a 'script' server and touch remote servers.

```powershell
$firstRun = Get-Date -Hour $((Get-Date).AddHours(1).Hour) -Minute 0 -Second 0

$jobTrigger = New-JobTrigger -Once -At $firstRun -RepetitionInterval (New-TimeSpan -Minutes 60) -RepeatIndefinitely
$jobOptions = New-ScheduledJobOption -RequireNetwork
$cred = Get-Credential

Register-ScheduledJob -Name 'Check Server Disk' -ScriptBlock {
    Test-ConnectedDisk -Server 'test1.example.com' -Disk 'd'
} -Credential $cred -Trigger $jobTrigger -ScheduledJobOption $jobOptions

```

Now we need to get Pulseway checking for our new events, this is a case of opening up Pulseway Manager on the monitored server, selecting the _Notifications_ tab, then the _Event Log_ tab, hitting the check box, clicking add and filling out the details.

{{< figure src="/2018/05/notify-eventlog.PNG" >}}

{{< figure src="/2018/05/event-log-filter.png" >}}

It's important to remember to select the right Event Log (Windows PowerShell in this case), the event level, event ID and source that you specified within your script.

You can specify Pulseway's notification level here. My boss set his notification for critical events to an air-raid siren, so I like to set things to that level.

Depending on your Pulseway configuration, and what level you set the notification to, you'll get an email and/or alert whenever the event you're looking for occurs. Note that unless you specifically allow repeat notifications, you won't get more than one Pulseway alert until you have cleared the alarm.

## **Closing Notes**

* I like to have a matching pair of events, one for the error state and one for logging that everything is normal. As you can see in the script above, I set the normal event to information. This allows me to check the event log an now that the job has been running.
* In general, you can use any EventId you want, as long as it is a 16bit integer. However, I read somewhere (and am currently unable to find the source) that the range of 100-999 isn't used by any Microsoft application and is free to be used.
* I like to use unique IDs for each event log I create. Remember to document which ID is for what. If you work in a team and have a wiki, you could log it there, or put it in a CSV file.
* Bonus points if you write a function to determine the next available EventId.
* Be careful about over saturating yourself with notifications. Set the appropriate notification level based on your needs and those of the business.
* If you're getting flooded with alerts you could become desensitized to the really important ones.

