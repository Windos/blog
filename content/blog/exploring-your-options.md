+++
categories = ["PowerShell", "Nugget"]
date = 2018-06-29T15:12:52Z
description = ""
draft = false
thumbnail = "/2018/06/nousnou-iwasaki-45718-unsplash.jpg"
images = ["/2018/06/nousnou-iwasaki-45718-unsplash.jpg"]
slug = "exploring-your-options"
summary = "We've been looking into PowerShell Scheduled Jobs lately, today let's have a quick look at the \"options\" available to us."
tag = ["PowerShell", "Nugget"]
title = "PS Scheduled Jobs: Exploring Your Options"
+++


There are a total of 13 different options you can configure using the `New-ScheduledJobOption` and `Set-ScheduledJobOption` cmdlets. All of these options have a default value, and the resulting object will list out each of them. If you do not configure an option, it is assumed that you're happy with the default.

One example of this that may catch you out if you are working on a laptop is **StartIfOnBatteries**. This defaults to **false** and means that if your laptop isn't plugged into AC power, your scheduled job will not run.

You can see all of the default values by creating a new job [**ScheduledJobOptions**](https://docs.microsoft.com/en-us/powershell/module/psscheduledjob/new-scheduledjoboption?view=powershell-5.1#outputs) object.

```powershell
PS C:\> New-ScheduledJobOption

```

```
StartIfOnBatteries     : False
StopIfGoingOnBatteries : True
WakeToRun              : False
StartIfNotIdle         : True
StopIfGoingOffIdle     : False
RestartOnIdleResume    : False
IdleDuration           : 00:10:00
IdleTimeout            : 01:00:00
ShowInTaskScheduler    : True
RunElevated            : False
RunWithoutNetwork      : True
DoNotAllowDemandStart  : False
MultipleInstancePolicy : IgnoreNew
JobDefinition          :

```

It is possible to supply a hash table of options rather than using `New-ScheduledJobOption`. Both of the following examples produce the same result.

```powershell
PS C:\> $O = New-ScheduledJobOption -ContinueIfGoingOnBattery -StartIfOnBattery
PS C:\> Register-ScheduledJob -Name 'OptionObject' -ScheduledJobOption $O

```

```powershell
PS C:\> $O = @{StartIfOnBatteries = $true; StopIfGoingOnBatteries = $false}
PS C:\> Register-ScheduledJob -Name 'OptionHashTable' -ScheduledJobOption $O

```

If you want to update a job's options, use`Set-ScheduledJobOption`. This cmdlet has the same parameters as the “New” equivalent, however it also requires an existing instance of an options object. This existing object indicates which scheduled job it belongs to through the **JobDefinition** property.

Do be aware that turning off a Boolean option requires specifically setting that option to _false_. To show this in action, let's look at how we would turn off the RunElevated option on our [previously created](https://king.geek.nz/2018/06/18/powershell-orchestration-with-scheduled-jobs-the-start-of-a-series/) “Update PS Help” scheduled job.

```powershell
PS C:\> Get-ScheduledJobOption -Name 'Update PS Help' |
    Set-ScheduledJobOption -RunElevated:$false

```

The final note on options is to be aware that while some options have a value against them, they may not take effect unless another option is set to a specific value. For example, there are default values for **IdleDuration** and **IdleTimeout** but these do not come into play unless **StartIfNotIdle** is set to **False**.

## **Wrap Up**

This was a nice short post, but I hope it was useful nonetheless. Next up we'll be diving into schedules and recurrence.

Also, remember that if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can always find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

