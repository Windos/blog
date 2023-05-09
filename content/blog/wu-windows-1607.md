+++
categories = ["PowerShell", "Windows"]
date = 2016-10-17T10:00:00Z
description = ""
draft = false
featured_image = "/2018/05/need-update.png"
images = ["/2018/05/need-update.png"]
slug = "wu-windows-1607"
summary = "Over a year ago, I answered a question on Super User. The question was about stopping the very persistent Windows Update from rebooting without the users' consent, and wow was that ever a popular topic."
tag = ["PowerShell", "Windows"]
title = "Validating Prevention of Automatic Reboots on Windows 10, Version 1607"
+++

Over a year ago, I [answered a question](https://superuser.com/questions/957267/how-to-disable-automatic-reboots-in-windows-10) on [Super User](https://superuser.com/). The question was about stopping the very persistent Windows Update from rebooting without the users' consent, and wow was that ever a popular topic. The question is up to about 91K views, and my answer is rapidly approaching 100 up votes.

Since then there has been a number of large updates to Windows 10, chief among them being the [Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/new-video-series-this-week-on-windows-highlights-windows-10-anniversary-update/#DQMex8PHh4jPJDqd.97), after which there has been a lot of conflicting reports of whether or not the 'fix' still worked.

Sounds like its time to throw a small amount of science at it and find out for myself.

## **What was the 'fix'?**

Check out the Super User thread for the full details. The TL; DR version is to set Windows Update to download and prompt to install via Local Group Policy (if running an edition of Windows with this ability) or through the registry.

Windows would get rather pushy, showing a banner right across the middle of the screen from time to time, but it would never reboot on its own.

## **You mentioned science?**

In order to test this out, I downloaded ISOs of Windows 10, Version 1607 in both Professional and Enterprise flavours. I've fired up two VMs in an VMware ESXi environment, and prepped them for observation:

* Installed VMware Tools.
* Applied the appropriate MAK key to each of them so that they don't nag about not being activated
* Applied the 'fix', Local Group Policy on Enterprise and Registry on Professional
* Ensured the screen won't be turned off (important later) and Windows won't sleep.
* Gave them a reboot and logged in.

The final piece of the puzzle was a little bit of PowerShell to help keep an eye on things and keep a record of the goings on over time.

## **You got a long way without mentioning PowerShell…**

Yep, I can't help myself.

My script is pretty crude, but it gets the jobs done. When I get a chance I'll clean it up a bit and pop it onto GitHub on the off chance that I might use it again in the future.

```powershell
Import-Module -Name VMware.VimAutomation.Core

$Server = 'vcenter.example.com'

Connect-VIServer -Server $Server

$Cred = Get-Credential -Credential 'example.com\kinggeek'

$VMNames = 'Win10-Ent', 'Win10-Pro'

$VMs = Get-VM $VmNames

while ($true) {
    foreach ($VM in $VMs) {
        $MoRef = $VM.ExtensionData.MoRef.value

        $Path = 'd:\Screenshots'
        $FileName = "Screenshot-$($VM.Name)-$(Get-Date -UFormat "%Y%m%d%H%M").png"

        Invoke-WebRequest "https://$Server/screen?id=$MoRef" -Credential $Cred -OutFile "$Path\$FileName"
    }

    Start-Sleep -Seconds 3600
}

```

Looking back, that seems like a lot of code for what boils down to one web call per VM. As it's just thrown together to get the immediate job done, the actual meat is wrapped in a while true loop and an hour long sleep between iterations.

## **How do things look at the moment?**

It appears that Windows Update knows there are updates available and is prompting me, through the Action Center, to install them.

{{< figure src="/2018/05/enterprise-current.png" >}}

My Screenshots folder is filling up and I'll keep an eye on it. Now we wait.

{{< figure src="/2018/05/folder.png" >}}

If it makes it through to a week without a reboot, I'll consider saving these screenshots directly into a public OneDrive folder so that anyone who's interest can follow along in 'real time.'
