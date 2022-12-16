+++
categories = ["Backup", "PowerShell", "Office 365"]
date = 2019-02-25T21:08:57Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2019/02/vinicius-amano-566532-unsplash.jpg"
slug = "veeam-365-selective"
summary = "I can't stand \"expected warnings,\" but getting selective in my VBO jobs got me back to my happy place."
tags = ["Backup", "PowerShell", "Office 365"]
title = "Getting Selective with Veeam Backup for Microsoft Office 365"

+++


Let's say you're midway through a migration to Office 365. Some of your user's still have mailboxes on-site and others are with Exchange Online. You're not yet advising users to throw data into OneDrive for Business... but you're also not telling people **not** to use it.

And, of course, even though you're moving to the cloud you understand that your data is still your problem. With that in mind, you've got yourself a product to backup your Office 365 data such as [Veeam Backup for Microsoft Office 365 (VBO)](https://www.veeam.com/backup-microsoft-office-365.html) - yes, there are other products out there, but Veeam is what I know so it's what I use in my "hypothetical" scenarios.

While you're in limbo, how do you decide which users to backup and how do you set things up so that you don't miss people as they are migrated or start to use OneDrive for Business?

_Screenshots in this post were taken using the VBO v3 Beta. If you're interested in checking out the beta yourself, head over to the [Veeam Community Forum.](https://forums.veeam.com/veeam-backup-for-microsoft-office-365-f47/veeam-backup-for-microsoft-office-365-3-0-beta-t57218.html)_

## **The Easy Answer & What I Did Originally**

Honestly, the easiest way to get the job done is just to setup a job and select to backup the "entire organization."

{{< figure src="__GHOST_URL__/content/images/2019/02/EntireOrg.png" >}}

Given the [speed difference](https://king.geek.nz/2019/02/19/veeam-backup-for-microsoft-office-365-v3/) between Exchange and SharePoint backups (at least before v3), I'd originally opted to split out into two different jobs. One for SharePoint sites (not personal ones) and one for user based items.

This user job backed up Exchange and OneDrive and I was adding people to the jobs manually as migration occurred.

{{< figure src="__GHOST_URL__/content/images/2019/02/Individual.png" >}}

This worked except for two issues:

1. It's a manual process and humans make mistakes.
2. Users can start using OneDrive before their mailbox is online... durr.

I tried a number of different options for solving these issues, and ended up causing a new issue.

## **"Normalization of Deviance"**

At one point I was attempting to backup the OneDrive of every user that was licensed for it. This worked, in so far as I was getting the data backed up that needed to be backed up.

I also started getting jobs finishing with warnings instead of success. It's fine though, right? Those warnings are expected!

{{< figure src="__GHOST_URL__/content/images/2019/02/Warnings.png" >}}

Yes, strictly speaking these warnings are expected. It basically means that the user is licensed for OneDrive but hasn't opened it yet. Nothing to backup, so no issue right?

It didn't sit well with me. When everything is working right, I was to see green check marks. If there's a warning (or a pattern of warnings) it should be a sign that something isn't quite right and needs some investigation.

If these warnings are accepted as normal, then other unexpected warnings are going to slip through the cracks.

So I set about finding a better way.

## **My Solution**

I was already happy with the SharePoint side of things, as I only had to backup a handful of sites and their sub-sites. It's only Exchange Online and OneDrive for Business I had to worry about.

### **The Simple One: Exchange Online**

Once I stepped back to think about this one for a moment, it was plainly obvious: Dynamic Distribution List.

If I set one of these up in the Exchange Online Admin Center, it's only going to pull in mailboxes that exist in the cloud... simple!

Start by connecting to Exchange Online via PowerShell:

```powershell
$Cred = Get-Credential
$ExSession = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri "https://outlook.office365.com/powershell-liveid/" -Credential $Cred -Authentication "Basic" –AllowRedirection
Import-PSSession $ExSession

```

Then, create your distribution list choosing a name of your liking:

```powershell
New-DynamicDistributionGroup -Name 'O365 Mail Users' -IncludedRecipients 'MailboxUsers'

```

I also hid mine from from the Global Address List, as this was for Veeam... not for emails.

```powershell
Set-DynamicDistributionGroup -Name 'O365 Mail Users' -HiddenFromAddressListsEnabled

```

Now head into the VBO console and set your job to target our new group, and edit the listing to only backup Mail (and Archive if needed.)

{{< figure src="__GHOST_URL__/content/images/2019/02/MailUsers.png" >}}

Done!

The dynamic distribution list automatically includes new Exchange Online mailboxes, and I get no warnings.

### **The More Involved One: OneDrive for Business**

First, get yourself the [SharePoint Online Management Shell](https://www.microsoft.com/en-us/download/details.aspx?id=35588) as you'll need it very soon.

Next, create a new group to which you'll add your OneDrive users. Don't be like me, and spend an hour wondering why you can't see your group in VBO... it needs to be mail enabled in some regard. I opted for a mail enabled security group.

I created mine via our on-site Exchange server. You probably could do this via Exchange Online, you'll just need to adjust the script later on as I'll be adding group members via my on-site Active Directory domain.

Connecting is very similar, just change the Uri and authentication method:

```powershell
$Cred = Get-Credential
$ExSession = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri "http://mail.example.com/PowerShell/" -Credential $Cred -Authentication "Kerberos" –AllowRedirection
Import-PSSession $ExSession

```

Then create your group:

```powershell
New-DistributionGroup -Name 'OneDriveUsers' -Alias 'OneDriveUsers' -Type security

```

And, as always, hide it if that's your thing:

```powershell
Set-DistributionGroup -Name 'OneDriveUsers' -HiddenFromAddressListsEnabled 

```

I don't cover it in this post, but everything from this point needs to be bundled up and scheduled to run on a timer to keep the group up to date. I do this with [PowerShell Scheduled Jobs](https://king.geek.nz/2018/06/18/powershell-orchestration-with-scheduled-jobs-the-start-of-a-series/).

With the group setup, connect to SharePoint Online (yes, this is for OneDrive... trust me):

```powershell
# Replace "example" with the name of your tenant
$TenantUrl = 'https://example-admin.sharepoint.com'
Connect-SPOService -Url $TenantUrl

```

Now get all of the personal SharePoint sites on your tenant (hint... these are the back end for OneDrive)

```powershell
$Sites = Get-SPOSite -IncludePersonalSite $true -Limit all -Filter "Url -like '-my.sharepoint.com/personal/" |
    select Url, StorageUsageCurrent |
    where StorageUsageCurrent -gt 1 |
    select -ExpandProperty Url -Unique

```

I'm interested here in two properties, the URL of the site and the Storage Usage. I use the storage usage to make sure that this site is actually being used and then, to avoid any duplicates I select just the unique URLs.

There is actually a property which allows us to get to the next point easier... but I wrote this next line prior to finding it, and didn't go back to change things.

We're doing a little string manipulation to extract the username from each of the site URLs:

```powershell
$Users = $Sites | foreach {$_.Split('/')[-1].Split('_')[0]}

```

Next get our group and empty it of its current membership, just in case someone has cleared out their OneDrive or otherwise needs to removed from the list. In writing this out, I'm sure there is a better way to go about it this task... but here we are!

```powershell
$Group = Get-ADGroup -Identity 'OneDriveUsers'

Get-ADGroupMember $Group.DistinguishedName |
    ForEach-Object {Remove-ADGroupMember $Group.DistinguishedName $_ -Confirm:$false}

```

And finally, I use the usernames from earlier to get their ADUser objects and add them all to the group:

```powershell
$ADUsers = foreach ($User in $Users) {
    Get-ADUser -Identity $User
}

Add-ADGroupMember -Identity $Group.DistinguishedName -Members $ADUsers

```

Once again, head into the VBO console and setup a job using this group and limit it to OneDrive (and also personal sites, if needed.)

{{< figure src="__GHOST_URL__/content/images/2019/02/OneDrive.png" >}}

## **No More Warnings**

This has done the job for me, though I may still tweak things from time to time. I no longer have to manually keep jobs and/or groups up to date so I don't have to worry about missing data.

I'm also now in a state where I can expect jobs to end without warnings. This actually helped tracked down when SharePoint was (incorrectly) treating something in a user's OneDrive as a virus and stopping VBO from getting a copy of it.

I'm sure there are better ways to achieve my goals here, but these two are getting the job done.

