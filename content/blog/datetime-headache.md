+++
categories = ["PowerShell", "Nugget"]
date = 2017-11-05T19:15:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-14.jpg"
slug = "datetime-headache"
summary = "Sometimes PowerShell just throws the system culture out the window and assumes that you meant to supply the date in the American style."
tags = ["PowerShell", "Nugget"]
title = "Avoiding DateTime Headaches"

+++


There's a number of things that - as a non-American - can cause confusion when dealing with America:

* Imperial rather than Metric units of measurement,
* Words missing the letter 'U,'
* Tipping,
* Backwards dates.

A number of these applies to systems or software developed in America. The last one specifically, dates, is the subject of this short post.

## **When dates don't go as planned**

Before getting into things, I wanted to thanks [Dan Blank](https://twitter.com/danblank000) for inspiring this post during a conversation we had on Twitter.

As a primer for any American readers, how would you say the date: 12/10/2017?

Chances are you're saying "December 10, 2017", but to a lot of the world this is actually "12 October, 2017."

That's just a human reading the date out loud. You can imagine how this might cause some level of confusion, and even frustration, when you're providing dates in this format to PowerShell (as an example) and it helpfully interprets it incorrectly.

## **You'd better get cultured!**

Windows allows us to set the culture on our workstations. This changes the currency, date and other number formats. With my culture set to "en-NZ" (New Zealand English), the system displays the date in the correct format:

{{< figure src="/2018/05/SystemDate.png" >}}

_Note for clarity that this is the 6th of November._

PowerShell will even output the date in this format when I ask for it in the "short date" format:

{{< figure src="/2018/05/PowerShell1.png" >}}

The trouble is that it isn't consistent. Sometimes it just throws the system culture out the window and assumes that you meant to supply the date in the American style, such as when converting a string to a datetime:

{{< figure src="/2018/05/PowerShell2.png" >}}

## **My "solution"**

I use the term solution loosely, but my advice is to never assume that the system (PowerShell or otherwise) knows which date format is appropriate.

If I'm supplying a date as a string, I always go with the unambiguous [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) date format, YYYY-MM-DD. To revisit the previous example, that would be:

{{< figure src="/2018/05/PowerShell3.png" >}}

## **But that's not what I have to work with!**

Then make it so!

I'm being serious, leverage PowerShell to change the date format coming from your (properly localized) log files:

```powershell
$InputDate = '6/11/2017'
$SplitDate = $InputDate.Split('/')
$OutputDate = '{0}-{1:D2}-{2:D2}' -f $SplitDate[2], [int] $SplitDate[1], [int] $SplitDate[0]
$OutputDate
# 2017-11-06

```

Obviously, if you're doing this a lot, you might consider making a function to do this over a number of different scripts.

## **We live in an international world**

I have volunteered my time to a number of American websites in the past to the point where I'm used to just spelling things according to the American dictionary. I tend to lean towards that as the safer option when there is a binary choice between American or British/New Zealand styles.

However, we can all help each other by picking an unambiguous middle ground where they exist, such as the ISO 8601 dates.

This helps to ensure readers (from anywhere in the world) can parse what we're writing with ease and avoids confusion or misunderstandings.

