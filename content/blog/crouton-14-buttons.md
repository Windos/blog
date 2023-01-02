+++
categories = ["BurntToast", "PowerShell"]
date = 2020-01-22T21:50:06Z
description = ""
draft = false
featured_image = "/2020/01/waldemar-brandt-H7gNBeSy7eE-unsplash.jpg"
images = ["/2020/01/waldemar-brandt-H7gNBeSy7eE-unsplash.jpg"]
slug = "crouton-14-buttons"
summary = "It's all well and good putting buttons all over your toast notification, but text is a little plain. Let's see how we can dress out buttons up a little bit!"
tag = ["BurntToast", "PowerShell"]
title = "Crouton #14 - Fancy Buttons"
aliases = [
    "/2020/01/23/crouton-14-buttons/amp/",
    "/2020/01/23/crouton-14-buttons/",
    "/2020/01/23/crouton-14-buttons"
]
+++


Toast notifications really shine when you move from just showing information to allowing the user to take some sort of action on it. Unfortunately, our choices when doing this via PowerShell is limited but we can present the user with buttons which can link through to said information in other applications or online.

This is all well and good, and works, but what if these buttons look a little plain?

<p class="note">This post is a "crouton" and is a short post about a specific feature or use case of the <a href="https://www.powershellgallery.com/packages/BurntToast" target="_blank">BurntToast PowerShell module</a>.<br /><br />
For more of these posts check out the <a href="__GHOST_URL__/tag/burnttoast/" target="_blank">BurntToast tag</a>.</p>

## Naked Buttons

Let's start by checking out buttons in general. You create them using the `[New-BTButton](https://github.com/Windos/BurntToast/blob/master/Help/New-BTButton.md)` function and must at least provide "Content" (the text displayed on the button) and "Arguments" (the link to the file or web page to open.)

This function creates an object which represents your button and you can then pass this through to other functions, such as `[New-BurntToastNotification](https://github.com/Windos/BurntToast/blob/master/Help/New-BurntToastNotification.md)`.

As an example, I'm running a script that generates a report. It takes a long time to complete so I show a toast notification when it completes and I want to use buttons so that the user can open the report in either a CSV or XLSX format.

We'll start by creating two buttons, one for each file format:

```powershell
$CsvArgs = @{
    Content   = 'Open as CSV'
    Arguments = 'C:\Reports\long-report.csv'
}
$ButtonCsv = New-BTButton @CsvArgs

$XlsxArgs = @{
    Content   = 'Open as XLSX'
    Arguments = 'C:\Reports\long-report.xlsx'
}
$ButtonXlsx = New-BTButton @XlsxArgs
```

We then take the two button objects and include them when displaying the notification:

```powershell
$Text = 'Report Completed',
        'Something needs you attention, please open the report in your desired format.'

New-BurntToastNotification -Text $Text -Button $ButtonCsv, $ButtonXlsx
```

The result is a toast notification which does the job, but those buttons look a little... boring.

{{< figure src="/2020/01/image-4.png" >}}

## Fancy Buttons

The option available to us for dressing up our buttons is to add a picture to them. In order to do this you use the "ImageUri" parameter, though there's an issue in the current version (0.7.0) where only local files work.

This image needs to be a square. Wide images will be squashed, and it doesn't look pretty.

Let's re-run the previous code, but this time we'll provide and icon image for each file format:

```powershell
$CsvArgs = @{
    Content   = 'Open as CSV'
    Arguments = 'C:\Reports\long-report.csv'
    ImageUri  = 'C:\Icons\019-csv file.png'
}
$ButtonCsv = New-BTButton @CsvArgs

$XlsxArgs = @{
    Content   = 'Open as XLSX'
    Arguments = 'C:\Reports\long-report.xlsx'
    ImageUri  = 'C:\Icons\082-xlsx format.png'
}
$ButtonXlsx = New-BTButton @XlsxArgs

$Text = 'Report Completed',
        'Something needs you attention, please open the report in your desired format.'

New-BurntToastNotification -Text $Text -Button $ButtonCsv, $ButtonXlsx
```

Now, we have a much different notification.

{{< figure src="/2020/01/image-5.png" >}}

Admittedly, the icon images didn't fair too well when they were shrunk down. Also, in all honesty, I'm not too fond of the light gray back being removed when you're using these images.

## Another Option

If you want to use images and have a light gray background on your buttons, you need to include at least one button without an image on it.

To demo that, I'll add a dismiss button without an image:

```powershell
$CsvArgs = @{
    Content   = 'CSV'
    Arguments = 'C:\Reports\long-report.csv'
    ImageUri  = 'C:\Icons\019-csv file.png'
}
$ButtonCsv = New-BTButton @CsvArgs

$XlsxArgs = @{
    Content   = 'XLSX'
    Arguments = 'C:\Reports\long-report.xlsx'
    ImageUri  = 'C:\Icons\082-xlsx format.png'
}
$ButtonXlsx = New-BTButton @XlsxArgs

$ButtonDismiss = New-BTButton -Dismiss

$Text = 'Report Completed',
        'Something needs you attention, please open the report in your desired format.'

New-BurntToastNotification -Text $Text -Button $ButtonCsv, $ButtonXlsx, $ButtonDismiss
```

{{< figure src="/2020/01/image-6.png" >}}

You'll note that I also had to shorten the text on the original two buttons in order to fit all three on the notification.

## Final Notes

As mentioned above, the images only work if they are local. This will be fixed in the next version of BurntToast.

You also want your image to be squares, and look good (or at least easy to read) when small.

I hope you find this feature useful, or at least entertaining. Please do let me know how you use it!

## Credit

Hero image by [Waldemar Brandt](https://unsplash.com/@waldemarbrandt67w?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

Icon pack used in these demos by [Payungkead](https://www.flaticon.com/authors/payungkead) on [flaticon.com](https://www.flaticon.com/packs/file-format-3)

