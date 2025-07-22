+++
categories = ["Meta", "Nugget", "PowerShell"]
date = 2019-09-29T21:37:45Z
description = ""
draft = false
featured_image = "/2019/09/umanoide-8NvFlA8DO6Q-unsplash.jpg"
images = ["/2019/09/umanoide-8NvFlA8DO6Q-unsplash.jpg"]
slug = "hacktoberfest-2019"
summary = "I'm gearing up for my fourth Hacktoberfest event, and so should you. You could even come away from this month with a fancy new t-shirt!"
tag = ["Meta", "Nugget", "PowerShell"]
title = "Roll Your Sleeves Up, It's Hacktoberfest Time!"
+++


We're now about half an hour away from October, at least here in New Zealand, meaning that it's just about time for [Hacktoberfest](https://hacktoberfest.digitalocean.com/) 2019 to kick off.

This will be my fourth Hacktoberfest, and with any luck the fourth event t-shirt for my wife to eventually claim (not by my choice though!) I've written about the event a couple of times in the past, in [2017](__GHOST_URL__/2017/10/01/oct-17/) and [2018](__GHOST_URL__/2018/10/02/hacktoberfest-2018/).

## What's a "Hacktoberfest"?

Hacktoberfest is a yearly month long event fostering contributions to open source projects.

{{< figure src="/2019/09/Hacktoberfest_19_Events_500x500.png" >}}

The first 50,000 people to complete four pull requests to open source projects will be able to claim a t-shirt which will be sent out with a few cool stickers. Every years' design has been awesome, but this year has by far and away the coolest, in my opinion.

If you've never done the whole GitHub Pull Request thing, check out [this post](https://thenewstack.io/getting-legit-with-git-and-github-your-first-pull-request/) by [Michelle Gienow](https://thenewstack.io/author/michelle_gienow/) for a primer.

Don't be afraid, this event in 2016 was the first time I ever submitted a PR and I've never looked back!

## What Do I Work On?

My go to answer to this question is to give back to any projects you've found value in. Is there a PowerShell module you use every day? Find out if the code is on GitHub and see if you're able to fix a bug, provide some more documentation, or write missing test.

Still not sure, head onto GitHub and search for open issues with the #Hacktoberfest label and the language set to PowerShell... or just click [this link](https://github.com/search?q=label%3Ahacktoberfest+state%3Aopen+language%3Apowershell&type=Issues).

I've added Hacktoberfest label to issues on a few of my own projects:

* [BurntToast](https://github.com/Windos/BurntToast/issues?q=is%3Aissue+is%3Aopen+label%3AHACKTOBERFEST)
* [PoshBot.Joker](https://github.com/ToastIT-dev/PoshBot.Joker/issues?q=is%3Aissue+is%3Aopen+label%3Ahacktoberfest)
* [PoshBot.Sentiment](https://github.com/ToastIT-dev/PoshBot.Sentiment/issues?q=is%3Aissue+is%3Aopen+label%3Ahacktoberfest)

## Just Throw a Random Issue at Me!

I couldn't finish out this post without a little PowerShell fun, this code will get all the appropriately labeled issues from GitHub, select a random one, and open it in a browser.

```powershell
$Issues = @()

$URI = 'https://api.github.com/search/issues?q=label:hacktoberfest+language:powershell+state:open&page={0}&per_page=100'

$Page = 1
$More = $true

while ($More) {
    $Result = Invoke-RestMethod -Uri ($Uri -f $Page)

    if ($Result.items.Count -eq 100) {
        $Page ++
    } else {
        $More = $false
    }

    $Issues += $Result.items
}

Start-Process ($Issues | Get-Random).html_url
```

### Credit

_Hero image by [Umanoide](https://unsplash.com/@umanoide?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/work-shirt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)_

