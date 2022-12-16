+++
categories = ["PowerShell", "Nugget"]
date = 2017-09-25T10:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-9.jpg"
slug = "recurrence-pattern"
summary = "There's one thing that bugs me when scheduling scripts from the prompt, however: Recurrence."
tags = ["PowerShell", "Nugget"]
title = "Recurrence Patterns and PowerShell"

+++


I love scheduling PowerShell scripts. I've got a server whose sole job in life is to be the place where my scripts are stored and executed on various schedules.

There's one thing that bugs me when managing this from the prompt, however: Recurrence.

## **For once, the GUI is king?!**

When setting up a task via the Task Scheduler, you have the option of triggering once a month. Not just on the "15th of every month," but also on the "third Wednesday of every month."

{{< figure src="/2018/05/GUI-TaskTrigger.png" >}}

But how the heck does one do this from within PowerShell?

Surely, it's a given, right?

{{< figure src="/2018/05/PS-JobTrigger.png" >}}

Apparently not…

## **The workaround**

I've had a snippet of code in a bunch of my scripts that would allow me to set which instance of a day in a month was "valid" for the script to run. If the day wasn't valid, the script would end without any action taken.

I realized that I did this so often, that I recently reworked this snippet into its own function and put it up on the PowerShell gallery: [Test-RecurrencePattern](https://www.powershellgallery.com/packages/Test-RecurrencePattern).

This script will return `true` or `false`, depending on whether the current date falls within the specified pattern. For instance, I'm writing this it is the 9th of September, 2017 and running this example today returns `true`:

```powershell
Test-RecurrencePattern -Day Tuesday – Instance Last

```

If I ran it any other time this month, it would return `false`.

## **Putting the function to work**

Using this function is a two-part solution. First, you'll want to include it inside your script. Preferably as soon as possible. As an example, lets say you have a report that needs to run on the second Friday of the month.

```powershell
If (Test-RecurrencePattern -Day Friday -Instance Second) {
    # Actual reporting script here.
}

```

Next you need to schedule the PowerShell job to run. Unfortunately, the best I can do on this front with the built-in tools is to have it run every week on a given day.

```powershell
$JobTrigger = New-JobTrigger -At 8:00 -DaysOfWeek Friday -Weekly
Register-ScheduledJob -Name 'Report - Second Friday' -Trigger $JobTrigger -FilePath C:\\path\\to\\report.ps1

```

This job will run every Friday from now on. By testing the recurrence pattern within the script file, it won't actually run the report until the 13th of October given that that will be the second Friday of that month.

## **Wrap up**

This was a pretty short post just to put some words around the script that I uploaded without much context to the PowerShell gallery (and replaced a week later when I realized I missed a 't' from 'pattern.')

At the moment, it only accounts for this specific recurrence pattern but I'll be looking to expand the function in the future. If there is a specific pattern that you're after, please do reach out and I'll give it priority.

If you're keen you could event tweak the function yourself and fire through a [pull request](https://github.com/Windos/powershell-depot/blob/master/GalleryScripts/Test-RecurrencePattern.ps1).

Finally, next month I'll be taking part in [#PSBlogWeek](http://psblogweek.com/). My post will be going live on the 17th of October and I'll be promoting all of the posts from the others taking part. Please do check out the website and follow the [hashtag](https://twitter.com/search?q=%23PSBlogWeek&src=typd) on Twitter.

