+++
categories = ["PowerShell"]
date = 2018-06-19T19:41:46Z
description = ""
draft = false
thumbnail = "/2018/06/chris-geirman-421715-unsplash.jpg"
slug = "scheduled-job-qualified"
summary = "Much sooner than expected, let's talk about Windows PowerShell scheduled jobs again."
tag = ["PowerShell"]
title = "Fully Qualified Path to PowerShell.exe in Scheduled Jobs?"

+++


Two things before we jump into this post:

1. Yes, I struggled for literally minutes with the capitalization of "PowerShell.exe" in the title.
2. No, I didn't expect to be writing about Windows PowerShell scheduled jobs again so soon.

The reason I am writing about scheduled jobs is because of an awesome question from Twitter.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Is it possible to set the action to have a fully qualified path to powershell.exe? This is one of our security teams requirements.</p>&mdash; Perry Harris (@PHactotum) <a href="https://twitter.com/PHactotum/status/1009128070161289216?ref_src=twsrc%5Etfw">June 19, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

This ended up being a neat little experiment, and I wanted to get it documented before it was flushed from memory. This post will be jumping the gun a little bit, and go into topics I wasn't planning on covering yet... but oh well!

## **Clarification**

I first read this message bright and early in the morning. Before Coffee. After far too little sleep.

My first reading of the question had me thinking that they wanted to call PowerShell within a job's ScriptBlock. Sure... that's doable, but why? As I said in my original reply, this would result in a PowerShell process calling another PowerShell process.

```powershell
Register-ScheduledJob -Name 'PowerShell Inception' -ScriptBlock {
    powershell.exe -Command {Update-Help}
}

```

I cogitated during my morning routine before the actual problem "clicked" in my head.

When you create a scheduled job, and then looks at what is executing via the Task Scheduler you'll notice that **powershell.exe** is the spring board:

{{< figure src="/2018/06/DefaultJobAction.png" >}}

That makes sense, right? And for me this is perfectly fine. Evidently, though, some security teams may not be comfortable with this. As it is, there is a possibility of someone dropping a malicious version of **powershell.exe** into a path location or otherwise intercepting the lookup of this executable.

Helping to mitigate against this means providing the full path to the executable you want to run, such as `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe`.

So... let's figure out how to change this without resorting to the GUI (because why not?).

## **The Code**

Check out the [Gist](https://gist.github.com/Windos/acf8fb5a211c327ed2c1ed426ca23750) of the complete code if you want to avoid reading my ramblings.

First, let's create the job that featured in the previous screen shot.

```powershell
$Option = New-ScheduledJobOption -RunElevated -RequireNetwork
Register-ScheduledJob -Name 'Test Job' -ScheduledJobOption $Option -ScriptBlock {
    Add-Content -Path C:\temp\joblog.txt -Value (Get-Date)
}

```

What this job does isn't important, I simply made it write to a file so I knew that it was running.

We're interested in the "action" this generated against the associated scheduled task. Note that the task name matches the name we provided our scheduled job and I only care about the **actions** property from it.

```powershell
$CurrentAction = (Get-ScheduledTask -TaskName 'Test Job').actions

```

Next, create a new "action". We're pulling the existing **Id**, **Arguments**, and **WorkingDirectory** if it's set (which it shouldn't be, but we'll account for it regardless.)

**Execute** will be the only thing that we end up changing.

```powershell
$Params = @{
    Id               = $CurrentAction.Id
    Argument         = $CurrentAction.Arguments
    Execute          = 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe'
}

if ($CurrentAction.WorkingDirectory) {
    $Params.Add('WorkingDirectory', $CurrentAction.WorkingDirectory)
}

$NewAction = New-ScheduledTaskAction @Params

```

Unfortunately, `New-ScheduledTaskAction` doesn't currently honor the **Id** provided, so we have to add it to our new object.

```powershell
$NewAction.Id = $CurrentAction.Id

```

Finally, let's update the action on our scheduled task job.

```powershell
Set-ScheduledTask -TaskName 'Test Job' -TaskPath '\Microsoft\Windows\PowerShell\ScheduledJobs\' -Action $NewAction

```

The only thing to note in this last code snippet is the **TaskPath**, which reflects where scheduled jobs live in the Task Scheduler.

And finally, check the GUI again and you'll notice that we're now using the full path to **powershell.exe**.

{{< figure src="/2018/06/UpdatedJobAction.png" >}}

## **Wrap Up**

I hope this post helps someone out there. While the premise may not apply to you, it gives a glimpse into what's coming up in my series on scheduled jobs... mainly having to use scheduled task cmdlets from time to time.

If you've got any specific questions you want answered (whether it ends up as a blog post or not) please do reach out in the comments or on [Twitter](https://twitter.com/WindosNZ)!

Also, remember that if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can always find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

