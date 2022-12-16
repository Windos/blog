+++
categories = ["PowerShell", "BurntToast"]
date = 2017-03-19T10:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-1.jpg"
slug = "crouton-stackwatch"
summary = "A crouton is a small piece of toast for soup or salads. Or, a small piece of (burnt) toast"
tag = ["PowerShell", "BurntToast"]
title = "Crouton #1 - Toast Notifications for New Stack Overflow Questions"

+++


Last week I pushed an update for the [BurntToast](https://github.com/Windos/BurntToast) PowerShell module to the [PowerShell Gallery](https://www.powershellgallery.com/packages/BurntToast). This update, version 0.5.2, exposed the ability to add custom buttons to your toast notifications instead of just the standard Snooze and Dismiss option from previous versions.

This dramatically increases the flexibility and usability of these PowerShell generated toasts.

In an effort to show off the cool things you can accomplish with BurntToast, and to demo new features as they come out, I’ll be writing a series of ‘Croutons.’

## **What the heck is a Crouton?**

A crouton is a small piece of toast for soup or salads.

Onto the post!

Seriously though, I have way too much fun naming projects and tools. In the context of this site a ‘Crouton’ is a (hopefully) short post detailing one use for the BurntToast module.

Get it? Small piece of (burnt) toast.

## **Buttons!**

The first thing that needs to be addressed is the fact that there is a little more to adding buttons than text, or changing the sound. With that in mind think of a button as a complete object, which is created using the `New-BTButton` function. You can add up to five of these objects to your toast using the `-Button` parameter.

There’s technically a bunch of ways a button can ‘activate’, though PowerShell cannot consume most of theme. The practical outcome of this is that these buttons are limited to the ‘protocol’ activation method, which opens a path (or url) in the operating system’s default application for that file type.

A png will open in your photo viewer, a doc in MS Word, html file in your web browser.

But how? Create a button object with content (display text) and arguments (the path/url):

```powershell
$Button1 = New-BTButton -Content 'Google' -Arguments 'https://google.com'

```

Add the resulting object to a toast:

```powershell
New-BurntToastNotification -Text "There's things on Google!" -Button $Button1

```

Ooh, shiny!

{{< figure src="/2018/05/GoogleToast.png" >}}

## **The setup**

As a heads up before diving into the code proper, I like to have a location set aside in my registry for scratch PowerShell info. This lets me persist data between runs of a PowerShell job without having to write random files to disk or setting up a database for random tidbits of data.

In order to create the same location that I’ve used below, run this:

```powershell
New-Item -Path 'HKCU:\SOFTWARE\PowerShell-Scratch'

```

If you don’t want to keep this data in your registry, or have some need to persist this across multiple machines, just keep in mind that you’ll need to make the relevant changes to the script.

Also, if you want to avoid an error on the first run of the code below, you can also preemptively create the property we’ll be using:

```powershell
Set-ItemProperty -Path 'HKCU:\SOFTWARE\PowerShell-Scratch' -Name 'StackWatch-LastId' –Value ‘FirstRun’

```

## **StackWatch**

As this will be run within a PowerShell scheduled job, the first thing we’re going to do is define a script block which is the actual work horse of this project.

```powershell
$JobBlock = {
    $Category = 'powershell'

    $URL = 'https://stackoverflow.com/feeds/tag?tagnames={0}&sort=newest' -f $Category
    $LastNotifyId = Get-ItemPropertyValue -Path 'HKCU:\SOFTWARE\PowerShell-Scratch' -Name 'StackWatch-LastId'

    $RSS = Invoke-RestMethod -Uri $URL
    $LatestId = $RSS[0].Id

    if ($LastNotifyId -ne $LatestId) {
        Import-Module -Name BurntToast -Force

        $QuestionTitle = $RSS[0].title.'#text'

        $SOButton = New-BTButton -Content 'Open' -Arguments $LatestId
        New-BurntToastNotification -AppLogo 'C:\temp\stackoverflow-icon.png' -AppId 'StackWatcher' -Text 'New Question on Stack Overflow', $QuestionTitle -Button $SOButton

        Set-ItemProperty -Path 'HKCU:\SOFTWARE\PowerShell-Scratch' -Name 'StackWatch-LastId' -Value $LatestId
    }
}

```

The code here is fairly straight forward. You can change the category to whatever you want to be tracking on Stack Overflow. Obviously I’ve set this to PowerShell.

We go ahead and pull up the RSS feed for the desired category, sorted so that the latest question is at the ‘top.’

In this example we only check the latest question, so if multiple questions are asked within your polling interval (defined later), you wont get a toast for all of them. This doesn’t seem to happen all that often for the PowerShell category unless you only poll once an hour or longer.

We check the ID of the latest question against what is stored in the registry, and if it is different we show a toast and update the registry value so that we don’t generate duplicate toasts for the same question.

The cool thing about this is that the toast includes a button labeled ‘Open’ and clicking it will open your default browser directly to the question.

Oh, I’ve also got a Stack Overflow icon sitting in a temp directory on my hard drive for display on the toast, you can get a copy of that image file for your own use [here](/2018/05/stackoverflow-icon.png).

The resulting toast comes out all pretty:

{{< figure src="/2018/05/SO-Toast.gif" >}}

And if you miss it, it’ll get stored in the Action Center (assuming you’ve configured the relevant AppId using the New-BTAppId function…)

{{< figure src="/2018/05/ActionCenter.PNG" >}}

So, that’s the actual logic done, now you need it to run periodically. To do this I use a [PowerShell Scheduled Job](https://blogs.technet.microsoft.com/heyscriptingguy/2014/05/12/introduction-to-powershell-scheduled-jobs/).

First, create a trigger, specifying the repetition interval you want. I have this set to five run every five minutes but you can tweak it to fit your needs/agility (if you really have to get onto of those new questions before the other guy!)

```powershell
$Trigger = New-JobTrigger -Once -At "12:00" -RepetitionInterval (New-TimeSpan -Minutes 5) -RepeatIndefinitely

```

Finally, register your job using the script block and trigger you’ve already defined. Note that I have included the credential parameter. If this is not included the job runs as the system and therefore no toasts are displayed. Set the credentials to yourself, which means the toasts will be generated in your user context and you’ll see them!

**Edit: This needs to be run from an elevated PowerShell sessions, so make sure you right click and "Run as Administrator"!***

```powershell
Register-ScheduledJob -Name StackWatch-PowerShell -Trigger $Trigger -ScriptBlock $JobBlock -Credential domain\user

```

## **That’s all there is to it**

You’ve now for a script checking for new PowerShell questions on Stack Overflow every five minutes with a convenient button that’ll take you directly to it in your browser. No more excuses for not helping out your fellow PowerShell users in need.

This first Crouton ended up a little longer than I intended it to be, but I’m sticking with the name!

This will be an ongoing series where I show off how I use BurntToast. If you have any cool ideas, or want to show off your use cases please feel free to reach out to me in the comments here, on [Twitter](https://twitter.com/WindosNZ) or open an issue on [GitHub](https://github.com/Windos/BurntToast/issues).

