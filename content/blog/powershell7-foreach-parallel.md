+++
categories = ["PowerShell"]
date = 2019-09-09T21:38:52Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2019/09/bence-boros-l3_9j916sh0-unsplash.jpg"
slug = "powershell7-foreach-parallel"
summary = "PoshRSJob has been my go to module for Parallelization for years... let's see if a head to head test with the new PowerShell 7 feature will change that."
tags = ["PowerShell"]
title = "Can Parallel For Each Loops in PowerShell 7 Tear Me Away from PoshRSJob?"

+++


I've been a long time user of [Boe Prox's](https://twitter.com/proxb) module, [PoshRSJob](https://www.powershellgallery.com/packages/PoshRSJob). For the uninitiated, this module lets you easily add a touch of [parallelization](https://en.wikipedia.org/wiki/Parallel_computing) to your PowerShell scripts. It allows you to spin up runspaces without the headache of having to try and figure them out yourself.

## Why Bother?

If you've ever found yourself needing to reach out onto your network and find a particular file on each of 500 different workstations you have a couple of options for how to tackle the job.

First, you could iterate through the list of workstations, one at a time, and look for the file. Once you have your answer from the first machine, you move onto the second, then the third, and so on.

Alternatively, you can leverage PoshRSJob so run this check on 20 (or whatever threshold you set) workstations at the same time. As the search finishes on one workstation, it starts on another.

An analogy that's a little easier to visualize is supermarket checkouts.

Imagine it takes a full minute to process a single customer wanting to buy some groceries. The supermarket only has one checkout open, so it would take them 15 minutes to empty a queue of 15 customers.

The supermarket realizes if they make customers wait that long, they might end up losing sales. To get through the queue faster they open up another four checkouts and can now process five customers at once.

With the additional checkouts open, they can now empty the queue in just three minutes.

Thinking back to our workstation example, let's say that it takes five minutes to check the entire hard drive of a given workstation for the file we're looking for. This means checking one workstation at a time will take a grand total of over 41 hours to check all 500!

Now imagine being able to check 50 at a time, we should be able to get the job done in under an hour.

_Note, there's some overhead involved, both in spinning up runspaces AND processing customers at the checkout, so this napkin maths isn't perfect._

## So What's New?

Microsoft, and many open source contributors, have been hard at work on [PowerShell 7](https://github.com/powershell/powershell/). This upcoming release is now on its [third preview](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.0-preview.3), and is full of awesome new features that I can't wait to use in production.

The headliner for me is the new [ForEach-Object Parallel feature](https://devblogs.microsoft.com/powershell/powershell-foreach-object-parallel-feature/).

This feature, like PoshRSJob, allows you to leverage runspaces to run parts of your scripts in parallel.

This raises a question, when PowerShell 7 is generally available and I look to start using it in production, do I start using the new feature instead of PoshRSJob?

One is a known entity that I've used for years, but the other is built directly into the core of the language, and I know I can use it without having to grab a module from the [PowerShell Gallery](https://www.powershellgallery.com/).

The only fair thing to do, is to put both options to the test.

## Two Options Enter

Technically, there's three options because I'm going to include just running everything sequentially as an option... but only to show the potential gains in parallelization in general.

This test involves pinging 10 different addresses on the internet, five will be supplied as IP Addresses and the other five will be supplied as names that need to be resolved. Each address will have two echo requests sent to it, and a [PSCustomObject](https://powershellexplained.com/2016-10-28-powershell-everything-you-wanted-to-know-about-pscustomobject/) will be returned stating the address and if it responded or not.

If you've seen [my presentation](https://www.youtube.com/watch?v=Yp_m5T_kyJU) on optimizing PowerShell for speed, you will have seen a smaller version of this test. Given I'm not having to worry about on stage pacing in this format, instead of only repeating each test only five to ten times they have each been run 1000 times.

Let's start with the sequential runs:

```powershell
$RawTimes = [System.Collections.Generic.List[int]]::new()

foreach ($Run in 1..1000) {
    $Stopwatch = [System.Diagnostics.Stopwatch]::new()
    $Stopwatch.Start()
    $Targets = @('8.8.8.8',
                 '8.8.4.4',
                 '1.1.1.1',
                 '1.0.0.1',
                 '9.9.9.9',
                 'stuff.co.nz',
                 'trademe.co.nz',
                 'google.com',
                 'example.com',
                 'toastit.dev')
    
    foreach ($Target in $Targets) {
        $Response = Test-Connection -ComputerName $Target -Count 2 -Quiet -InformationAction Ignore
    
        [PSCustomObject] @{
            Host = $Target
            Online = $Response
        }
    }
    $Stopwatch.Stop()
    $RawTimes.Add($Stopwatch.ElapsedMilliseconds)
}

$RawTimes | Measure -Average -Minimum -Maximum
```

```output
Count             : 1000
Average           : 10620.135
Sum               :
Maximum           : 11046
Minimum           : 10525
StandardDeviation :
Property          :
```

_Note, the `-InformationAction Ignore` on `Test-Connection` is included because the [current behaviour in PowerShell 7](https://github.com/PowerShell/PowerShell/issues/6768) is that the text from the echo request is returned on the information stream even when the `-Quiet` switch is specified (the expected behaviour from Windows PowerShell 5.1 is that only a Boolean value would be output.)_

The average time to complete each iteration was over 10 seconds. Meaning the overall test took just shy of three hours to complete... my poor test VM.

Next, let's try my old faithful PoshRSJob.

```powershell
$PoshRSJobTimes = [System.Collections.Generic.List[int]]::new()

foreach ($Run in 1..1000) {
    $Stopwatch = [System.Diagnostics.Stopwatch]::new()
    $Stopwatch.Start()
    $Targets = @('8.8.8.8',
                 '8.8.4.4',
                 '1.1.1.1',
                 '1.0.0.1',
                 '9.9.9.9',
                 'stuff.co.nz',
                 'trademe.co.nz',
                 'google.com',
                 'example.com',
                 'toastit.dev')
    
    $Targets | Start-RSJob -Throttle 5 -ScriptBlock {
        $Response = Test-Connection -ComputerName $_ -Count 2 -Quiet -InformationAction Ignore
    
        [PSCustomObject] @{
            Host = $_
            Online = $Response
        }
    } | Wait-RSJob | Receive-RSJob
    $Stopwatch.Stop()
    $PoshRSJobTimes.Add($Stopwatch.ElapsedMilliseconds)
}

$PoshRSJobTimes | Measure -Average -Minimum -Maximum
```

```output
Count             : 1000
Average           : 3254.613
Sum               :
Maximum           : 5987
Minimum           : 2422
StandardDeviation :
Property          :
```

This one took a little over 3 seconds per iteration, and the entire test completed in under an hour.

Finally, the new `ForEach-Object` feature.

```powershell
$Ps7Times = [System.Collections.Generic.List[int]]::new()

foreach ($Run in 1..1000) {
    $Stopwatch = [System.Diagnostics.Stopwatch]::new()
    $Stopwatch.Start()
    $Targets = @('8.8.8.8',
                 '8.8.4.4',
                 '1.1.1.1',
                 '1.0.0.1',
                 '9.9.9.9',
                 'stuff.co.nz',
                 'trademe.co.nz',
                 'google.com',
                 'example.com',
                 'toastit.dev')
    
    $Targets | ForEach-Object -ThrottleLimit 5 -Parallel {
        $Response = Test-Connection -ComputerName $_ -Count 2 -Quiet -InformationAction Ignore
    
        [PSCustomObject] @{
            Host = $_
            Online = $Response
        }
    }
    $Stopwatch.Stop()
    $Ps7Times.Add($Stopwatch.ElapsedMilliseconds)
}

$Ps7Times | Measure -Average -Minimum -Maximum
```

```output
Count             : 1000
Average           : 2461.979
Sum               :
Maximum           : 6028
Minimum           : 2413
StandardDeviation :
Property          :
```

This one managed to beat out PoshRSJob by about 0.8 seconds.

Note the syntax of this command for when you go to run it yourself, there's a new `-Parallel` parameter on `ForEach-Object` which takes a script block as input. This is different from the normal use of ForEach-Object where `-Process` would normally consume your scriptblock.

The `-ThrottleLimit` parameter allows you to control how many iterations can be run at the same time and in both this test and the PoshRSJob test this was set to five. This could easily have gone higher, and for your tasks it'll be a case of working out your sweet spot for the throttle that will give you the best speed vs. resource usage (e.g. CPU/memory usage or network capacity.)

## Not the Full Picture

Looking at only those numbers, the new PowerShell 7 feature did come out ahead of PoshRSJob, however with only 100 iterations each the results were much closer: 2.8 vs. 2.7.

You'll also notice that there are more pipelines involved in the PoshRSJob tests, where we wait for the jobs to finish and then receive their output (`| Wait-RSJob | Receive-RSJob`). It's possible to use a similar construct with `ForEach-Object`, but supplying the `-AsJob` switch and then using the `Wait-Job` and `Receive-Job` cmdlets.

I didn't go for that, because I don't need to store my jobs in a variable and come back to them later. It's very possible that this is what gave the new feature the edge over a higher number of iterations.

## The Verdict

I'm not 100% sure what I was expecting when I started the project. Part of me assumed that the new feature would be handy, but not as performant as PoshRSJob, and that it would be an easy decision to just keep using it.

What will actually happen is that I won't be re-writing my scripts, and will continue to use PoshRSJob where it is already installed and already in use. However, going forward if the system I'm using has PowerShell 7 installed I'll make use of the new feature instead.

## A Note on Parallelization in General

I linked to a Microsoft PowerShell blog post about this feature earlier, but one section to pay some attention to is "[When should it be avoided](https://devblogs.microsoft.com/powershell/powershell-foreach-object-parallel-feature/#when-should-it-be-avoided)" section.

In short, parallelization isn't a silver bullet. If you're doing something that takes a **very** short amount of time to complete, the overhead involved in creating a tearing down runspaces will outweigh the benefit from running in parallel making the script take longer than it would have done.

As ever, it's important to test **your** use cases and pick the best solution for it.

