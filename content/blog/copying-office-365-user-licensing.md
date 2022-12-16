+++
categories = ["PowerShell", "Office 365"]
date = 2018-07-23T17:06:00Z
description = ""
draft = false
thumbnail = "/2018/07/freestocks-org-540554-unsplash.jpg"
slug = "copying-office-365-user-licensing"
summary = "Spoiler alert: this post didn't end up the way I expected it to when I started writing..."
tag = ["PowerShell", "Office 365"]
title = "Copying Office 365 User Licensing"

+++


I don't know if this is an issue for anyone else, or if it's unique to me and the organisation I work for.

As you [may know](https://king.geek.nz/tag/office-365/) from some of my previous posts, we're dipping our toes into Office 365. This means we're only giving users access to certain features as and when we're ready and we're controlling this by turning on and off parts of their licenses.

As an example of this, checkout this fictional user's license configuration:

{{< figure src="/2018/07/License-Example.png" >}}

## **The Problem**

Unless I'm missing something, there's no decent way of going "this user has their license configured how I like it, make this other user (or more likely group of users) look like that."

In an ideal world, maybe I would have a config file listing which license is the standard, and which features are to be disabled. This would work for me, and anyone using my tooling. But not everyone **is** using my tooling. What if there's a panicked call to enable something, and the person tasked to do it falls back on just using the GUI (and be driven mad as a result)?

Additionally, there's been a couple of features added to Office 365 over the past year that were enabled by default. Sure, I could make that config file list what I need **enabled** and then dynamically figure out what to disable. But building that doesn't sound worth it when you consider the previous problem.

A quick disclaimer, this post is going to work through how I initially hacked together a script to do this task. It doesn't end in a polished function. This may happen down the track, and may be a second blog post (or tweet), but don't scroll to the bottom expecting to see one.

## **The Prep Work**

To get this particular job done, we'll be using the [AzureAD](https://www.powershellgallery.com/packages/AzureAD) PowerShell module. This is the V2, _but completely different_, version of the old MSOnline module (don't get me started on that...)

If you don't have it already, crack open a PowerShell console and install it from the PowerShell Gallery:

```powershell
PS C:\> Install-Module -Name 'AzureAD'

```

Once you have the module, get connected to Azure Active Directory:

```powershell
PS C:\> Connect-AzureAD

```

When doing this interactively, I tend to run this without any arguments. This results in the standard login box popping up and allows me to benefit from our SSO (sometimes...).

{{< figure src="/2018/07/2018-07-24--3-.png" >}}

The first thing I do once connected is ensure that all of my users have a usage location set. If this isn't set, you won't be able to apply licenses.

```powershell
# Check for users without a UsageLocation
PS C:\> Get-AzureADUser -All $true | where {$_.UsageLocation -eq $null}

# Set the UseageLocation, in my case this is 'NZ'
PS C:\> Get-AzureADUser -All $true |
    where {$_.UsageLocation -eq $null} |
    Set-AzureADUser -UsageLocation 'NZ'

# Check again, you shouldn't get back any results
PS C:\> Get-AzureADUser -All $true | where {$_.UsageLocation -eq $null}

```

Note, if your organisation is a multinational, something this simple won't suite you. You also may like to only set the usage location on members and not guests.

## **The Code**

Now that the ground work is out of the way, let's get down to the fun stuff. First, grab your "model user", or more specifically their license(s):

```powershell
PS C:\> $ExistingLicense = (Get-AzureADUser -ObjectId fabio@king.geek.nz).AssignedLicenses

```

Now we need to bundle this up as an "AssignedLicenses" (the plural is important) object, and assign this to a user:

```powershell
# Create an AssignedLicenses object
PS C:\> $Licenses = New-Object -TypeName Microsoft.Open.AzureAD.Model.AssignedLicenses

# Add the existing license(s)
PS C:\> $Licenses.AddLicenses = $ExistingLicense

# Assign to the target user
Get-AzureADUser -ObjectId target@king.geek.nz |
    Set-AzureADUserLicense -AssignedLicenses $Licenses

```

This will take a little bit of time to be reflected in the GUI, but job done! You can pipe any users to want and they'll pick up that license.

## **Hindsight / Confessional**

When I first wrote a script to fulfil this task, I used the old MSOnline module (which I was clinging onto for some reason). I was converting the script over to the AzureAD module while writing this post.

Wow. The AzureAD module has simplified the job **Dramatically**. I won't hinder you with what that other script looked like because that way was dumb.

This was the straw that broke my back, convinced me to get out of old habits, and start using the AzureAD module instead.

I don't know why I was resisting this change.

This post started with the intention of it being a "hey, check out this cool script I wrote" type of thing. Instead it is more a tale of not resisting change for the sake of resisting change.

