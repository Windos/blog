+++
categories = ["PowerShell"]
date = 2019-09-24T21:17:30Z
description = ""
draft = false
featured_image = "/2019/09/pablo-heimplatz-an3qaxZ-2bY-unsplash.jpg"
images = ["/2019/09/pablo-heimplatz-an3qaxZ-2bY-unsplash.jpg"]
slug = "ternary-operator-powershell-7"
summary = "What the heck is a \"ternary\" and what's it doing in my PowerShell?!"
tag = ["PowerShell"]
title = "Getting Familiar with the Ternary Operator in PowerShell 7"
+++


It was only a fortnight ago that I was digging into the [preview 3](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.0-preview.3) release of PowerShell 7 looking at the new [parallel for each loop feature](__GHOST_URL__/2019/09/10/powershell7-foreach-parallel/). Since then [preview 4](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.0-preview.4) has been released, and that introduced yet another feature I want to have a closer look at: the [ternary operator](https://en.wikipedia.org/wiki/%3F:).

## What's a Ternary?

The ternary operator is present in many programming languages and, according to the [PowerShell team's DevBlog](https://devblogs.microsoft.com/powershell/powershell-7-preview-4) on this release, is quite popular with C# developers.

It is a rather succinct way of writing if/else type logic constructs. I wouldn't be surprised if we see a lot of this operator the next time someone holds a one liner competition.

The ternary operator itself can be represented as `?:` as those are the two characters used in the operator's syntax.

To use the operator, follow this formula:

```
[Boolean] ? [Output if $true] : [Output if $false]
```

<p class="warning">It's a little unfortunate overlap in syntax, but don't confuse this syntax with the question mark alias of <code>Where-Object</code></p>

Or to make things a little more familiar, consider this if/else statement:

```powershell
$Path = 'C:\Support\ImportantFile.csv'

if (Test-Path $Path) {
	'File exists'
} else {
    'THE FILE IS MISSING!'
}
```

This can instead be written using the ternary operator:

```powershell
$Path = 'C:\Support\ImportantFile.csv'

(Test-Path $Path) ? 'File exists' : 'THE FILE IS MISSING!'
```

You'll notice that I wrapped `Test-Path` in brackets before the question mark. Without that, the question mark is interpreted as a parameter on the cmdlet... and that doesn't end well.

## But Why? Speed?

We've already got if/else statements, and years of experience putting them to use. So what magic sauce is behind the ternary operator that would cause me to want to use them? Is there some sort of trickery in the code that make this new operator perform faster than the traditional method?

The short answer here is: no.

The long answer is, let's double check. In order to test for any potential speed differences, I ran the following test:

```powershell
$IfElseTimes = [System.Collections.Generic.List[int]]::new()

foreach ($Run in 1..1000) {
    $Stopwatch = [System.Diagnostics.Stopwatch]::new()
    $Stopwatch.Start()

    if (Test-Connection -ComputerName '8.8.8.8' -Count 2 -Quiet -InformationAction Ignore) {
        'Google DNS Online'
    } else {
        'Google DNS Offline'
    }

    $Stopwatch.Stop()
    $IfElseTimes.Add($Stopwatch.ElapsedMilliseconds)
}

$TernaryTimes = [System.Collections.Generic.List[int]]::new()

foreach ($Run in 1..1000) {
    $Stopwatch = [System.Diagnostics.Stopwatch]::new()
    $Stopwatch.Start()

    (Test-Connection -ComputerName '8.8.8.8' -Count 2 -Quiet -InformationAction Ignore) ? 'Google DNS Online' : 'Google DNS Offline'

    $Stopwatch.Stop()
    $TernaryTimes.Add($Stopwatch.ElapsedMilliseconds)
}
```

The result of running each test 1000 times results in an averages of 1029 milliseconds and 1027 milliseconds. Those are so close that I'm not even going to tell you which figure belonged to which method.

## So Why Use It?

Here's the rub, if you're comfortable using the traditional if/else statements you can continue to do so. You're not missing out on anything by not adopting the ternary operator.

Like PowerShell in general, it's just another tool in the toolbox.

Also consider the developers coming to PowerShell from C#, having the ternary operator removes one more barrier if they're used to using it.

In saying that, there is one scenario where I'll be considering this operator going forward: trivial conditional statements.

Consider this example:

```powershell
$SoundElement = if ($Silent) {
    '<audio silent="true" />'
} else {
    '<audio src="ms-winsoundevent:Notification.Default" />'
}
```

This is testing to see if the `Silent` switch was used on a function call and setting a variable based on the switch.

This could instead be written as:

```powershell
$SoundElement = $Silent ? '<audio silent="true" />' : '<audio src="ms-winsoundevent:Notification.Default" />'
```

I personally find that easier to read, but I get that it's an acquired taste.

It's also interesting that each of the question mark and colon can be used to break onto a new line:

```powershell
$SoundElement = $Silent ?
    '<audio silent="true" />' :
    '<audio src="ms-winsoundevent:Notification.Default" />'
```

This is handy, though I don't find it as easy to read as the one line variant. Personally if the statement is long enough that it needs to be on multiple lines, I'd just end up using an if/else statement.

## The Obvious Draw Back

Of course, using the ternary operator requires PowerShell 7. If you include the operator, your code is never going to work on older versions of PowerShell. Unfortunately, that means I'm not going to be able to use the operator in production code for a while... though we can always hope.

I for one am very much looking forward to PowerShell 7's full release. There are so many cool features like this on the way on top of those from PowerShell Core (6.x) that I've been missing out on while clinging onto Windows PowerShell.

This may just be the release that I can eventually roll out to more endpoints than just my workstation.



### Credit

_Hero image by [Pablo Heimplatz](https://unsplash.com/@pabloheimplatz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)_

