+++
categories = ["PowerShell", "Office 365"]
date = 2017-07-27T12:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/thumb.jpeg"
slug = "o365-connect"
summary = "Just like their on-premises counterparts, each of these products has their own PowerShell interface for management."
tags = ["PowerShell", "Office 365"]
title = "Office 365 & PowerShell: Jacking Into the Matrix"

+++


One thing became apparent very quickly during my recent foray into [Office 365](https://aka.ms/o365): it truly is one big amalgam of bunch of Microsoft products. Just like their on-premises counterparts, each of these products has their own PowerShell interface for management.

Almost every module in the courseware for the "[Enabling and Managing Office 365](https://www.microsoft.com/en-nz/learning/course.aspx?cid=20347)" course includes a "this is the thing you need to download" and/or "these are the PowerShell commands you need to run to connect.

I can only hope that one day Microsoft manages to create a unified installer that can install the various components in one go, but for now this is what we have.

This post will serve as some documentation, just as much as myself as for others out there, on the various bits that need to be installed and the specific commands to run. My next post will explore putting together a script that will connect you to "all the things" all at once.

I'll be using the tenant name `example.onmicrosoft.com` as an example, so remember to supplement your own.

## **Office 365**

First of all, you'll need to download and install the Azure AD module, available in [32-bit](https://aka.ms/fohrds) or [64-bit](https://aka.ms/siqtee) flavors.

_Note that this is V1 of the Azure AD module, and there is a_  [V2](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2)  _available. I haven't looked into, or used, the new module but on the surface it looks like cmdlet names have changes between the two version, so for now I'm just using what I know._

Once that's installed, go ahead and get connected to your tenant by running:

```powershell
Connect-MsolService

```

As shown, this will show a box where you can supply your username and password for your tenant.

{{< figure src="__GHOST_URL__/content/images/2018/05/msol.png" >}}

It's possible to suppress this by supplying credentials, which is something we'll be doing in the follow up post.

## **Exchange Online**

Just like on-premises Exchange you need to open a PowerShell session to your "Exchange Server", in this case though you're connecting through a "gateway" in the cloud.

First, store your credentials in a variable:

```powershell
$Cred = Get-Credential

```

Then go ahead and start, then import, a PowerShell session:

```powershell
$ExSession = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri "https://outlook.office365.com/powershell-liveid/" -Credential $Cred -Authentication "Basic" –AllowRedirection
Import-PSSession $ExSession

```

You'll now have access to the various Exchange related cmdlets.

## **Skype for Business Online**

This one is a little weird. Like Exchange Online, you create and import a PowerShell session however the session is created using a dedicated cmdlet.

Start by installing the [Skype for Business Online Connector](https://aka.ms/x3kyib). If you're running Windows 7 you'll also need to make sure that you're running PowerShell V3 and install the [Microsoft Online Services Sign-In Assistant](https://aka.ms/vl42dg).

Like Exchange, sore your credential then open and import Skype for Business Online session:

```powershell
$Cred = Get-Credential
$S4BSession = New-CSOnlineSession –Credential $Cred
Import-PSSession $S4BSession

```

## **SharePoint Online**

For our last service, you'll need to install the [SharePoint Online Management Shell](https://aka.ms/f04q5o) and again if you're on Windows 7 you'll need to be using PowerShell V3.

Once that's installed simply run this command, supplementing your own tenant name:

```powershell
Connect-SPOService -Url https://example-admin.sharepoint.com

```

Without specifying credentials, you'll see a very similar login box to what you got for the general Office 365 connection.

## **When in doubt, you probably need Exchange**

Several services without their own connection methods require you to open a session to Exchange Online, so chances are that will be your first port of call if you're unsure.

Keep an eye out for my next post where we'll wrap these disparate connection methods up into one function, for those times where you don't want to think about it and just want to get connected quickly.

As always, let me know your thoughts in the comments bellow or over on [Twitter](https://twitter.com/WindosNZ).

