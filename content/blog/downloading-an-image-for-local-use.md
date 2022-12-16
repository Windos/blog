+++
categories = ["PowerShell", "Nugget", "BurntToast"]
date = 2018-05-27T12:55:00Z
description = ""
draft = false
thumbnail = "/2018/05/emarts-emarts-68775-unsplash.jpg"
slug = "downloading-an-image-for-local-use"
summary = "This has been sitting as an open issue on the project for a long time and given how quick of a fix it was I'm disappointed that I didn't get to it sooner."
tags = ["PowerShell", "Nugget", "BurntToast"]
title = "Downloading an Image for Local Use"

+++


During my latest [livestream](https://youtu.be/OXFBHCY4Ils), after failing at what I intended to do, we got onto enabling the ability to use pictures from the Internet in toast notifications. All of the documentation for the UWP Toolkit suggests this should just work, but unfortunately it didn't in practice.

Rather than changing the code, and documentation, to explicitly not make this work from within the module we set out to enable this functionality ourselves.

## **The Beginning**

Let's start with code we were working with. I've simplified this to just the parts of the function we were worried about, but you can see the full function [on GitHub](https://github.com/Windos/BurntToast/blob/v0.6.2/BurntToast/Public/New-BTImage.ps1).

```powershell
function New-BTImage {
    param (
        [string] $Source
    )

    # Don't worry about this type and what it does, just know that this function
    # creates and object, adds properties to it, then outputs it at the end
    $Image = [Microsoft.Toolkit.Uwp.Notifications.AdaptiveImage]::new()

    if ($Source) {
        $Image.Source = $Source
    }

    $Image
}

```

Simply put, this function took a source and added this to an "AdaptiveImage" objects. This should have been able to take a local path or an Internet address. Local paths worked, but not Internet ones.

## **The First Attempt**

The answer that we came up with on stream was to download the image(s) to a local path (in the temp directory) and adjust the source to that new path. My first attempt at this included generating a GUID for the file name to ensure that it was unique.

```powershell
function New-BTImage {
    param (
        [string] $Source
    )

    $Image = [Microsoft.Toolkit.Uwp.Notifications.AdaptiveImage]::new()

    if ($Source) {
        if ($Source -like 'http?://*') {
            $FileExtension = $Source.Split('.')[-1]
            $Guid = [guid]::newGuid()

            $NewFilePath = '{0}\{1}.{2}' -f $Env:TEMP, $Guid, $FileExtension

            Invoke-WebRequest -Uri $Source -OutFile $NewFilePath

            $Image.Source = $NewFilePath
        } else {
            $Image.Source = $Source
        }
    }

    $Image
}

```

In this iteration I tested to see if the source started with an HTTP or HTTPS and then split the patch on periods. The last item in the result from splitting the source should give me the file extension.

Then I create a new GUID, and build the path to where the file will be downloaded to. The file is then downloaded and used as the source on the image object.

This worked, but there was one issue with it...

## **The Final Product**

The issue was that if you used the same image multiple times, then it would be downloaded every time. You'd end up with multiple copies of the same image in your temp directory and have to wait for it to be downloaded every time you generated a new toast.

The answer to this was to preserve the name from the Internet and test to see if it is already in the temp directory.

```powershell
function New-BTImage {
    param (
        [string] $Source
    )

    $Image = [Microsoft.Toolkit.Uwp.Notifications.AdaptiveImage]::new()

    if ($Source) {
        if ($Source -like 'http?://*') {
            $RemoteFileName = $Source.Split('/')[-1]

            $NewFilePath = '{0}\{1}' -f $Env:TEMP, $RemoteFileName

            if (!(Test-Path -Path $NewFilePath)) {
                Invoke-WebRequest -Uri $Source -OutFile $NewFilePath
            }

            $Image.Source = $NewFilePath
        } else {
            $Image.Source = $Source
        }
    }

    $Image
}

```

This time I split the source on a forward slash, and the last item would give me the full file name (including extension.)

Then `Test-Path` is used to see if that filename exists in the temp directory and if not then the file is downloaded.

With that in place, Internet based images could now be used!

This change will be part of the v0.6.2 release of [BurntToast](https://www.powershellgallery.com/packages/BurntToast) and my hope is that it will make displaying visually interesting toasts much easier.

## **Wrap Up**

This wasn't the problem I was intending to tackle during the livestream, but I'm glad we did get to it. It had been sitting as an open issue on the project for a long time and given how quick of a fix it was I'm disappointed that I didn't get to it sooner.

Remember that [#PSTweetChat](https://twitter.com/search?f=tweets&vertical=default&q=%23pstweetchat) is this coming Friday (1 - 2 PM Eastern Time). Also, if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

