+++
categories = ["PowerShell"]
date = 2018-06-17T22:12:00Z
description = ""
draft = false
thumbnail = "/2018/06/estee-janssens-396889-unsplash.jpg"
slug = "powershell-orchestration-with-scheduled-jobs-the-start-of-a-series"
summary = "In an effort to spread my love of Windows PowerShell scheduled jobs, this is the start of series on the subject."
tag = ["PowerShell"]
title = "PowerShell Orchestration with Scheduled Jobs - The Start of a Series"

+++


I love [**Windows PowerShell scheduled jobs**](https://docs.microsoft.com/en-us/powershell/module/psscheduledjob/about/about_scheduled_jobs?view=powershell-5.1). It occurred to me recently that I've used them in plenty of posts, but I've never really delved into them any deeper than getting a specific task sorted.

This is the first in a series of posts, the aim of which is to make you love them too.

You may have noticed that I referenced scheduled jobs with the Windows PowerShell prefix above. That was not a mistake, or subconscious twitch. At the time of writing, the required cmdlets have not ported to PowerShell Core and as such the Windows moniker is very important.

With that caveat out of the way, I will be simply referring to **scheduled jobs** throughout this series.

## **Not Your Father's Scheduled Tasks**

Scheduled jobs are a hybrid of two concepts: Windows PowerShell jobs, often referred to as background jobs, and Task Scheduler tasks. Like tasks, scheduled jobs are saved to disk and are visible within the Task Scheduler management console; this will be explored further in a latter post.

Where scheduled jobs differ is the fact that when one runs it happens inside a background job. This job natively captures the object returned at the end of whatever script was executed and is saved for later retrieval.

When setting up a job you are also able to specify your code exactly as you would at a PowerShell prompt. Tasks, on the other hand, require you to call `powershell.exe` with messy flags and arcane syntax.

## **Creating a Scheduled Job**

Before we get too far ahead of ourselves, we should create our first scheduled job.

This scheduled job automatically updates the help files on the local computer. I have this on every computer I work on and it means I never have to worry about missing help or it otherwise being out of date.

```powershell
PS C:\> $Option = New-ScheduledJobOption -RunElevated -RequireNetwork
PS C:\> $Trigger = New-JobTrigger -Daily -At 01:00
PS C:\> $JobSplat = @{
    Name               = 'Update PS Help'
	ScriptBlock        = {Update-Help}
	Trigger            = $Trigger
	ScheduledJobOption = $Option
}
PS C:\> Register-ScheduledJob @JobSplat

```

```
Id     Name            JobTriggers     Command         Enabled
--     ----            -----------     -------         -------
1      Update PS Help  1               Update-Help     True

```

If you have jumped right in and tried running this example on your workstation, you likely just ran across the first thing to know about registering a new scheduled job: you have to do so in an elevated PowerShell session. In other words, make sure you first run PowerShell "as Administrator."

We will cover each component of this example in more depth throughout the next couple of posts, however we should quickly cover each line in sequence.

The `New-ScheduledJobOption` command specifies options which are applied to the within the Task Scheduler side of things. These are optional, but paying attention to them can help avoid potential pitfalls.

The first option we have chosen indicates that we want the PowerShell job to be running "as Administrator," this is helpful since you need elevated privileges to update system wide help files.

The second options means that the job will not run if the workstation does not have a network connection. As `Update-Help` downloads new help files from the internet, there is no point trying to run if a connection to it doesn't exist.

Next, `New-JobTrigger` defines when this job will run. Strictly speaking you do not have to supply a trigger, meaning that your job will only run if you manually trigger it. I have tried to list the parameters here is a Human readable format: "run every day at 1 AM."

Finally, we register the job. This final command is what records our scheduled job within the Task Scheduler. As part of this we supply the options and trigger that were defined earlier and also give the job a name and provide the code, in the form of a script block, that the job will execute.

## **Wrap Up**

That's a good point to end this first post on scheduled jobs. Up next will be a deeper look at the options afforded to us by `New-ScheduledJobOption`.

Be aware that I'll be trying to slot content about other subjects in between posts in this series... I don't want to flood feeds with a single topic!

Also, remember that if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can always find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

