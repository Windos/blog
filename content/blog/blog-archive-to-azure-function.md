+++
categories = ["PowerShell", "Azure"]
date = 2019-02-23T08:08:50Z
description = ""
draft = false
thumbnail = "/2019/02/josh-edgoose-1078641-unsplash.jpg"
images = ["/2019/02/josh-edgoose-1078641-unsplash.jpg"]
slug = "blog-archive-to-azure-function"
summary = "A few scripts run on my home server, but can happily run in a serverless environment. Time to give that poor server a rest, I think."
tag = ["PowerShell", "Azure"]
title = "Moving My Automatic Blog Archive Tweets to Azure Functions"
aliases = [
    "/2019/02/24/blog-archive-to-azure-function/amp/",
    "/2019/02/24/blog-archive-to-azure-function/",
    "/2019/02/24/blog-archive-to-azure-function"
]
+++


Mid way through last year I [live streamed and blogged](https://king.geek.nz/2018/05/30/automatic-blog-archive-tweets/) the creation of a script that automatically sends out "blog archive" tweets. It's been working well, and has been resurfacing topics that I sometimes forget I've written.

Until today, it'd been running on my home server... but why?

It's a fairly simple script, with no need to run on an actual server, let alone that specific one. It seems like the perfect script to move to a serverless service and learn how to use them in practice.

## **Azure Functions**

I opted to use [Azure Functions](https://azure.microsoft.com/en-us/services/functions/) as my serverless platform of choice. It is a paid service, after a certain threshold. That threshold? 1,000,000 requests / month... yeah I'm not going to be getting anywhere near that, so for my purposes I can consider it to be free!

As for PowerShell, it's available as an unsupported experimental language and only on the older version of function apps (we'll touch on this when we get to it.)

## **Making the Move**

I've already got an Azure account. I'm actually still in the middle of my [free trial](https://azure.microsoft.com/en-us/free/) with some available credit and extra services being free for 12 months (so expect a few blog posts while I don't have to pay for it?!)

However, I'm starting this task from a blank slate, I've got no existing Function Apps or Resource Groups or anything.

To get started, sign into you Azure account and load up the [portal](https://portal.azure.com/).

Head to the Function Apps page, and you can see any existing apps... and also find that you can't add a new one directly from this page. Instead hit the plus up the top left of the page.

{{< figure src="/2019/02/2019-02-23--0-.png" >}}

From here, either search for or scroll down to find "**Function App**". Choosing "**Compute**" from the left hand column will filter the list of options and make it easier to find.

{{< figure src="/2019/02/2019-02-23--1--1.png" >}}

This will open a blade where you can fill out info for your new Function App. The first option is the app name and this has to globally unique as it ends up with an internet resolvable domain name.

If you've got existing resource groups and storage provisioned, you can select them here... or just create new ones like I am.

Also, if locality of your app is important (because serverless != no servers), remember to pick a location. It wasn't too important for this task, but when I have to think about proximity to "my audience", West US tends to be best so I stuck with that here.

{{< figure src="/2019/02/2019-02-23--3--1.png" >}}

Once the deployment is finished, which could take a little while if you're setting up a brand new resource group, you'll be able to get straight to your new Function App via the shortcut in your notifications.

{{< figure src="/2019/02/2019-02-23--5--1.png" >}}

The overview tab gives a high level break down of your app, including the url and quick links to some of the settings we'll be going into soon. The "Platform features" tab is a listing of all of the features you may need to get into.

{{< figure src="/2019/02/2019-02-23--6--2.png" >}}

We'll start by looking into the "Function app settings" (which you can also get to from the Overview tab.)

{{< figure src="/2019/02/2019-02-23--7--1.png" >}}

In order to use PowerShell, this function needs to be running version ~1 but the default is ~2 (which makes sense, it is newer...)

Toggle the version to downgrade and wait for a moment to let it make these changes.

{{< figure src="/2019/02/2019-02-23--8--1.png" >}}

While we're looking at settings, we need to add some tokens which will be used to authenticate against the Twitter APIs when it's time to send our tweet.

You do this in the "Application settings". Once again, this is accessible via the Overview or Platform features tabs. Once you're in Application settings, scroll down to... Application settings (no I'm not joking) and add four new settings.

These are for the four different tokens and secrets needed by Twitter. You Twitter module of choice should point you in the right direction for getting these generated.

{{< figure src="/2019/02/2019-02-23--11--1.png" >}}

So far we've defined the platform on which our function (and future functions?) will run. Now we need to create an actual function.

There's a drop down for functions on the left of the screen which currently has nothing under it. Click the plus sign next to it though, to start the process of creating one.

The first screen you'll see gives a bunch of quick premade functions, but they aren't what we want. We need to create a custom function as we're planning to use PowerShell.

{{< figure src="/2019/02/2019-02-23--12--1.png" >}}

On the next page, toggle on "Experimental Language Support" which enables the PowerShell option we're looking for. You can pick any of the templates, but the one I want for this function is the "Timer trigger" as the script just runs on a given timer rather than being triggered via an API call.

Once you know your trigger click on your preferred language underneath it (... PowerShell!)

{{< figure src="/2019/02/2019-02-23--13--1.png" >}}

A new blade will fly out... from the right this time (I really don't get Azure's UX at times). Name the trigger, and define your schedule.

If you're a *nix user, this schedule probably makes sense to you, as it's in the format of a cron expression. I don't know that I've ever had to define one of these myself... and also this implementation may not be 100% standard as one of the cron expression websites wasn't creating schedules that Azure considered valid (and vice versa.)

If, like me, you need to get up to speed on cron expressions this [cheat sheet](https://codehollow.com/2017/02/azure-functions-time-trigger-cron-cheat-sheet/) will help.

My schedule is `0 0 0 * * MON,THU` which, all going well should meant that at midnight (UTC) on Mondays and Thursdays my function will fire. This _may_ happen at a different time, as the timer _could_ be set to server local time instead of UTC.

{{< figure src="/2019/02/2019-02-23--16--1.png" >}}

Once you create this function, you'll be dumped into your script, `run.ps1`, which has some demo code (writing some random output.) Instead, dump your scripts in there.

_This hasn't changed much since my original post, but I'll break it down in the next section._

Notice I've got some code up the top of my script checking for and installing my Twitter module of choice, [PSTwitterAPI](https://www.powershellgallery.com/packages/PSTwitterAPI).

You can click run to test out your code.

{{< figure src="/2019/02/2019-02-23--17--1.png" >}}

But... problems. As this is a headless situation, I can't actually install the module from the Gallery. It's possible there is a way to make this work, but Google suggested this is the intention and if you rely on a module, you should include it with your function.

{{< figure src="/2019/02/2019-02-23--18--1.png" >}}

So I went about including in PSTwitterAPI with my function, and the logical place to do this is the storage account that got created with my Function App.

{{< figure src="/2019/02/2019-02-23--20--1.png" >}}

There's a handy way of viewing and managing the content of this storage account within the browser, called **Storage Explorer**.

{{< figure src="/2019/02/2019-02-23--21--1.png" >}}

What you're looking for here is under **File Shares**. And you'll want to navigate through the file system to **wwwroot** under which you'll find the name of your trigger.

That directory will contain the json file containing the config for your function and that `run.ps1` script we were editing earlier.

{{< figure src="/2019/02/2019-02-23--23--1.png" >}}

From here, you can upload files... but not directories unfortunately, so I started by creating a new directory called "Modules".

{{< figure src="/2019/02/2019-02-23--24--1.png" >}}

And then I went about manually creating the folder structure for the module itself, and uploading files into the correct folders (making sure what I ended up with in the storage account matched the copy of the module on my workstation.)

{{< figure src="/2019/02/2019-02-23--26--1.png" >}}

Head back to your function, and if you see the "View Files" option on the right, you'll now see your new Modules directory.

{{< figure src="/2019/02/2019-02-23--28--1.png" >}}

Change your script to not try and install the module, and import the module via path instead of name and it should be working.

Now if we click run... it still comes out with errors, complaining about there not being a path argument on `Import-Module`.

This is a lie, and I have to assume it's a random scoping/experimental language problem. The function is actually working for me at this point, and tweets do go out.

{{< figure src="/2019/02/2019-02-23--29--1.png" >}}

It's just a shame that the status for the job is coming back as failed despite working perfectly.

## **The Script, with Small Updates**

Check out [Gist](https://gist.github.com/Windos/83b49044447ce8c156db017230274d52) for the full script.

We start with some variables we'll be using in the coming steps, the main one is the address of my RSS feed and the array list which will contain all of my blog posts.

```powershell
$Uri = 'https://king.geek.nz/rss/'
$Posts = [System.Collections.ArrayList]::new()
$PageNumber = 1
$More = $true

```

Now we build up that list of blog posts. I _may_ have over engineered this when I first wrote it, but this is how I deal with the pagination of my RSS feed. As long as I'm getting something back, I consider there to be more available and increment the page number and call it again.

```powershell
while ($More) {
    try {
        $Page = Invoke-RestMethod -Uri "$Uri$PageNumber" -ErrorAction Stop
    } catch {
        $Page = $null
    }

    if ($Page.Count -gt 0) {
        foreach ($Post in $Page) {
            $null = $Posts.Add($Post)
        }

        $PageNumber += 1
    } else {
        $More = $false
    }
}

```

Next, pick a post at random and pull the meta data from it, including title and link. I also turn all of the tags from my posts into hashtags.

Finally, all of that information is used to build the text of my tweet.

```powershell
$PostToPost = $Posts | Get-Random

$Title = $PostToPost.title.'#cdata-section'
$Excerpt = $PostToPost.description.'#cdata-section'
$Link = $PostToPost.link
$Categories = $PostToPost.category | foreach {$_.'#cdata-section'.replace(' ', '')}

$Hashtags = ''
foreach ($Category in $Categories) {
    $Hashtags += " #$Category"
}

$TweetText = "From the blog archive: ""$Title""`n`n$Excerpt$Hashtags`n$link"

```

wrapping up the script, we import that local copy of PSTwitterAPI. That ErrorAction is an attempt to suppress the errors coming out of my function.

Then we use all of those tokens we set to authenticate against twitter, and finally we send the tweet.

```powershell
Import-Module -Path .\Modules\PSTwitterAPI\PSTwitterAPI.psd1 -ErrorAction SilentlyContinue

$OAuthSettings = @{
    ApiKey            = $env:TWITTER_APIKEY
    ApiSecret         = $env:TWITTER_APISECRET
    AccessToken       = $env:TWITTER_ACCESSTOKEN
    AccessTokenSecret = $env:TWITTER_ACCESSTOKENSECRET
}

Set-TwitterOAuthSettings @OAuthSettings

Send-TwitterStatuses_Update -Status $TweetText

```

### **A Note, from the Future**

Rather than the ErrorAction, is put in a try/catch block around that one statement. The job is now completing without this false error.

```powershell
try {
    Import-Module -Path .\Modules\PSTwitterAPI\PSTwitterAPI.psd1
} catch {
    Write-Warning 'Import-Module complained about the Path argument.'
}

```

## **Next Up**

The first real test of this is coming up on Monday, when I should see a tweet go out without my input. If it doesn't work as expected this post will be updated with a new section about the fix.

I'll also continue to try and resolve or suppress that error as I really want to avoid the normalization of deviance and would like to be able to setup alerts for **real** failures.

I'm also toying with the idea of a second function that has an HTML trigger that returns a random blog post. That should be a fun experiment (whether or not it's actually useful!)

