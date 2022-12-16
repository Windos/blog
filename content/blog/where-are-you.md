+++
categories = ["PowerShell", "Meta"]
date = 2018-03-11T20:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/thumb-21.jpg"
slug = "where-are-you"
summary = "This seems like a decent excuse for a 'State of the Blog' post."
tags = ["PowerShell", "Meta"]
title = "Where the bloody hell are ya?"

+++


I've been somewhat absent lately, both from this blog and [YouTube](https://www.youtube.com/c/JoshuaKingSolari). I did not fully appreciated how much until I'd had a message or two asking when my next video will be out.

This seems like a decent excuse for a "State of the Blog" post covering what I've been up to recently and what is coming up soon.

## **Work, work**

I'm not sure how much I can/should say, so I'll keep it somewhat vague.

The [Australasia Management Challenge](http://www.managementchallenge.com.au/about-the-challenge.html) is a professional development program and competition held in New Zealand and across Australia. I'm part of a team taking part and we're rapidly closing in on D-Day. The lead up has taken up a lot more of my personal time than I had anticipated, but it's been a great experience.

That said, I'm looking forward to seeing the tail end of the challenge day and finding out how the team and I did.

## **#PSHSummit**

We're less than a month away from the PowerShell + DevOps Global Summit and I have been, and will be for the next while, polishing off [my session](http://sched.co/Cq9V).

I've arranged a practice run at work for later in the month, which I'm both looking forward to and petrified of in equal measure.

My slide deck has been uploaded to Sched, but the bulk of the content is in the demos. These will be online before, but much closer to, the event.

## **What about the videos?**

So here's the bad news. I won't be doing more any videos prior to the Summit.

Once I'm back from there, I'll kick off live streaming again. My preference is streaming over pre-recorded videos, I find them taxing (it takes a surprising amount of energy to think and talk for two hours) but love the instant feedback from chat.

## **#PSTweetChat**

I **love**  [#PSTweetChat](https://twitter.com/search?f=tweets&vertical=default&q=%23PSTweetChat&src=typd), and so should you! It's a PowerShell community event on Twitter, facilitated by [Jeffery Hicks](https://twitter.com/JeffHicks).

It happens on the first Friday of every month, from 1PM Eastern US time. Officially it lasts for an hour, but there's no need to cut a conversation short.

It's a great time to ask any questions, share what you've been working on, and just get involved in the community.

You can run this PowerShell code to find out when the next chat is, in your local time zone:

```powershell
$Now = Get-Date

function Get-FirstFridayOfMonth ([DateTime] $Date) {
    # Get an object representing the first day of the specified month
    $Year = $Date.Year
    $Month = $Date.Month
    [DateTime] $TestDate = "$Year-$Month-01 13:00:00"

    # Increment through the month until the first Friday is found
    while ($TestDate.DayOfWeek -ne 'Friday') {
        $TestDate = $TestDate.AddDays(1)
    }

    # And spit out the Friday we found
    $TestDate
}

# Call the previously defined function
$PotentialFriday = Get-FirstFridayOfMonth -Date $Now

# It's no good to us if it's in the past though
if (($PotentialFriday - $Now) -lt 0) {
    # So try find next month's PSTweetChat
    $PotentialFriday = Get-FirstFridayOfMonth -Date $Now.AddMonths(1)
}

# Get that time in your local timezone
[System.TimeZoneInfo]::ConvertTimeBySystemTimeZoneId($PotentialFriday, 'US Eastern Standard Time', [System.TimeZoneInfo]::Local.Id)

```

## **Mississippi PowerShell User Group**

I'll be doing a (virtual) presentation for the [MSPSUG](http://mspsug.com/) in June. There's two months still needing a presenter, so don't forget to [submit a proposal](http://mspsug.com/2018/01/18/call-for-mississippi-virtual-powershell-user-group-speakers-2018/)!

In general, I've enjoyed attending this virtual user group. Their meeting times are pretty convenient for me (midafternoon local time) and the talks are always great. If you can be there, I highly recommend it!

## **Get in touch!**

Regardless of anything else, you can always catch me on [Twitter](https://twitter.com/WindosNZ), so feel free to hit me up on there.

