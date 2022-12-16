+++
categories = ["PowerShell"]
date = 2020-05-27T06:30:00Z
description = ""
draft = false
thumbnail = "/2020/05/alex-iby-HfR0W6HW_Cw-unsplash.jpg"
slug = "what-outbuffer-is-for"
summary = "OutBuffer has been lurking in amongst the common parameters on most cmdlets for years, but I've never taken the time to ask what it's for."
tags = ["PowerShell"]
title = "So *That's* What OutBuffer Is For!"

+++


[Don Jones](https://twitter.com/concentrateddon) has been talking at a lot of PowerShell User Groups lately. One of the topics he's been covering is little nuggets from his latest (and probably last) PowerShell focused book, [Shell of an Idea](https://leanpub.com/shell-of-an-idea).

In his talk with the [Chicago PowerShell User Group](https://twitter.com/chgopsug) earlier this month, Don told the story about how [OutBuffer](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_commonparameters#outbuffer) came to be... and what it does. You could see in people's video feeds a collective "huh, that's actually really cool."

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Slide 7 and I&#39;ve already learnt something.<br><br>... So *that&#39;s* what -outbuffer is for!</p>&mdash; Josh (Windos) King (@WindosNZ) <a href="https://twitter.com/WindosNZ/status/1261070657694994433?ref_src=twsrc%5Etfw">May 14, 2020</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<p class="success">This post is inspired by Don's talks and book. It's going to look at what OutBuffer is and how to use it.<br /><br />
This post won't cover the associated story, for that I highly recommend picking up a copy of <a target="_blank" href="https://leanpub.com/shell-of-an-idea">Shell of an Idea</a>. It's well worth a read, even if you're not a PowerShell die hard. The topics and lessons are universal.</p>

## First, What Is It?

Chances are good that you see OutBuffer often when tabbing through available parameters on commands. You'll have not paid it much mind as it's not the parameter you're looking for.

{{< figure src="/2020/05/OutBuffer-Tab-Completion.gif" >}}

So... what is it?

Well it all has to do with the [pipeline](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_pipelines), the [life blood of PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/learn/understanding-the-powershell-pipeline) which enables you to pass objects from one command directly into another.

<p class="note">The examples used here are adaptions of the one found in the <a target="_blank" href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_commonparameters#outbuffer">OutBuffer documentation</a>. It's a great illustration and I couldn't come up with a better one that wasn't more complicated than need be.</p>

Consider this example, which demonstrates how you're used to working with pipelines.

```powershell
1..4 |
    ForEach-Object {Write-Host "$($_): First"; $_} |
    ForEach-Object {Write-Host "$($_): Second"}
```

This example generates the numbers 1 through 4, passes them down the pipeline to [ForEach-Object](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/foreach-object), where a statement is printed to the screen including the current number and a note that it's the first foreach loop. It then passes the numbers down the pipeline to a second foreach loop which prints to the screen again.

```output
1: First
1: Second
2: First
2: Second
3: First
3: Second
4: First
4: Second
```

From the output you can see that it processes each number in sequence. "1" passes through both loops, then "2" passes through both, etc.

This is because the default behavior is to pass objects down the pipeline one at a time as soon as the previous command is done with one object it's past along.

OutBuffer specifies how many objects to store up before passing them down the pipeline in a larger batch. The trick is that the number of objects passed down the pipeline is what you provide to the OutBuffer parameter plus 1, so `-OutBuffer 1` passes objects in batches of 2, and `-OutBuffer 2` would pass batches on 3.

Let's revisit our example with `-OutBuffer 1` added to the first foreach loop.

```powershell
1..4 |
    ForEach-Object {Write-Host "$($_): First"; $_} -OutBuffer 1 |
    ForEach-Object {Write-Host "$($_): Second"}
```

```output
1: First
2: First
1: Second
2: Second
3: First
4: First
3: Second
4: Second
```

This time you'll notice that the output shows that the numbers are working their way through the loops in pairs. "1" and "2" both show output from the first loop, then the second loop, and then "3" and "4" take their turn.

We can turn this all the way up, so that all four numbers are processed in batches.

```powershell
1..4 |
    ForEach-Object {Write-Host "$($_): First"; $_} -OutBuffer 3 |
    ForEach-Object {Write-Host "$($_): Second"}
```

```output
1: First
2: First
3: First
4: First
1: Second
2: Second
3: Second
4: Second
```

## But Why?

For the full story, including the funny scenario under which it got implemented, go read Shell of an Idea.

As for why it was added, consider the fact that you can pass objects between commands that are technically running on different machines on your network. If you're dealing with a **lot** of objects this can get very chatty on your network. OutBuffer allows you to queue up objects to send larger, but less frequent, chunks.

This still works today and may be an interesting tool certainly for network-based operations but also if you're wanting to control the flow of objects through loops for whatever reason.

This has been in PowerShell since day one, and in hindsight I wish I knew about it sooner. Thinking back this could have helped in a few niche scenarios that I'd struggled with at the time.

So... **that's** what OutBuffer is for!

## Credit

Hero image by [Alex Iby](https://unsplash.com/@alexiby?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/lightbulb?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

