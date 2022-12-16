+++
categories = ["PowerShell", "Meta"]
date = 2018-05-29T05:54:09Z
description = ""
draft = false
thumbnail = "/2018/05/samuel-zeller-118195-unsplash.jpg"
slug = "automatic-blog-archive-tweets"
summary = "This morning I was up at 4 AM, live streaming the creation of an auto-tweet script."
tag = ["PowerShell", "Meta"]
title = "Automatic Blog Archive Tweets"

+++


It's been a while since I worked on a PowerShell project "for myself." By that I mean creating a script to automate something that I might do myself, in my personal life rather than at work.

This morning I was up at 4 AM, [live streaming](https://youtu.be/KnaTkmdi_u4) the creation of a script to automatically pick a random tweet from my archive and Tweet out a link to it.

## **Just Give Me the Code!**

You can see the finished script over on [GitHub](https://github.com/Windos/powershell-depot/blob/master/GalleryScripts/Send-BlogTweet.ps1). Be aware that you need to use the [PoshTwit](https://www.powershellgallery.com/packages/PoshTwit/) module, available on the PowerShell Gallery, and have included your API access tokens/keys/secrets in the module's `token.json` file.

This script will end up on the PowerShell Gallery eventually too. It's my candidate for a [TechSnips](https://www.techsnips.io/) video on publishing scripts to it!

{{< figure src="/2018/05/trello-1.png" >}}

Let's go ahead and walk through what the script is doing.

## **Volunteering (a Post) as Tribute**

The first thing is that this blog's RSS feed is paginated, meaning that multiple calls need to be made against it to load all available posts. I'll commentate this process with comments in the snippet below.

```powershell
$BlogFeed = 'https://king.geek.nz/rss/'
$Posts = [System.Collections.ArrayList]::new()
$PageNumber = 1
$More = $true

# Keep looping while there *may* be more blog posts.
while ($More) {
    try {
        # Using a try/catch block to catch 404 errors from Invoke-RestMethod
        $Page = Invoke-RestMethod -Uri "$BlogFeed$PageNumber" -ErrorAction Stop
    } catch {
        $Page = $null
    }

    if ($Page.Count -gt 0) {
        foreach ($Post in $Page) {
            # We're looping through each post, otherwise we were getting a
            # three dimensional array
            $null = $Posts.Add($Post)
        }

        $PageNumber += 1
    } else {
        # We'll only end up here if we hit a 404 page. When that happens we
        # know that there are not more posts to collect...
        $More = $false
    }
}

# In an effort not to make this harder than it needs to be, we pipe our
# collection of posts to Get-Random
$PostToPost = $Posts | Get-Random

```

## **Hear Ye! Hear Ye!**

And now it's time to send a tweet. I'm not sure if I'm using PoshTwit wrong, but even though my I've included my API details in the json file, I still had to read them in for use. No biggie.

```powershell
# First lets ensure we're dealing with the raw strings for the elements of our
# post that we're interested in
$Title = $PostToPost.title.'#cdata-section'
$Excerpt = $PostToPost.description.'#cdata-section'
$Link = $PostToPost.link
$Categories = $PostToPost.category | foreach {$_.'#cdata-section'.replace(' ', '')}

# I'm using the tags on each blog post as hashtags. 9 time out of 10 this will
# result in a #PowerShell
$Hashtags = ''
foreach ($Category in $Categories) {
    $Hashtags += " #$Category"
}

# Now we set the actual text content of our Tweet, I'm using newlines (`n) to
# make it look how I want it to look when it gets to Twitter
$TweetText = "From the blog archive: ""$Title""`n`n$Excerpt$Hashtags`n$link"

# As mentioned above, I'm reading in the token.json file with contains my API info
$TokenPath = 'C:\Program Files\WindowsPowerShell\Modules\PoshTwit\0.1.6\token.json'
$Token = Get-Content -Path $TokenPath | ConvertFrom-Json

# And finally, splat my tweet content, and the API details
# All going well, there is now a new Tweet on my profile
$ParamSplat = @{
    Tweet          = $TweetText
    ConsumerKey    = $Token.ConsumerKey
    ConsumerSecret = $Token.ConsumerSecret
    AccessToken    = $Token.AccessToken
    AccessSecret   = $Token.AccessSecret
}

Publish-Tweet @ParamSplat

```

## **Fire! (Automatically)**

The last step in this process was to register a scheduled job on my home server. It's on 24/7 so it should always be on and ready to send out my tweet.

```powershell
$Option = New-ScheduledJobOption -RequireNetwork
$Trigger = New-JobTrigger -Daily -At '12:00' -RandomDelay 01:30:00
Register-ScheduledJob -FilePath C:\Auto\Send-BlogTweet.ps1 -Name 'BlogArchive' -Triger $Trigger -ScheduledJobOption $Option -RunNow

```

This job runs the `.ps1` file, resulting in a Tweet, every day. The timing of this could be anytime between noon and 1:30 PM local time, giving a degree of randomness to the process. Not that it matters for this, but over time it may look like it's not an automatic thing because it won't be going out at the exact same time every day.

Just to make sure it works, I put a `-RunNow` on the end. If I get a Tweet, the job is working.

## **Wrap Up**

At the time of writing this (it's still before 6 AM... I went directly from streaming to writing) the first Tweet from the script is 6 to 7.5 hours away from going out. Assuming it works without hitch I might not know exactly when it goes out, as my first indication will likely be likes and/or retweets.

As I mentioned above, I'll be using this script in a [TechSnips](https://www.techsnips.io/) demo on publishing scripts to the PowerShell gallery. That site is free until the 5th of June, so know is your chance to go check it out and see if it's your thing before a subscription is required! Don't wait!

Remember that [#PSTweetChat](https://twitter.com/search?f=tweets&vertical=default&q=%23pstweetchat) is this coming Friday (1 - 2 PM Eastern Time). Also, if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

<iframe width="480" height="270" src="https://www.youtube.com/embed/KnaTkmdi_u4?feature=oembed" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



