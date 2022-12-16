+++
categories = ["Backup", "PowerShell"]
date = 2019-05-04T20:38:35Z
description = ""
draft = false
thumbnail = "/2019/04/kaitlyn-chow-554372-unsplash.jpg"
slug = "veeam-onedrive-selective"
summary = "I've been wanting to get this out for a while, but couldn't find the right way to present it... rather than stressing, here's a brain dump on VBO v3."
tags = ["Backup", "PowerShell"]
title = "Disjointed Thoughts on Veeam Backup for Office 365 v3"

+++


A while back, while it was in beta, I [sung the praises of Veeam Backup for Office 365 v3](__GHOST_URL__/2019/02/19/veeam-backup-for-microsoft-office-365-v3/) (VBO). Since then this update has been [officially released](https://www.veeam.com/news/new-veeam-backup-for-microsoft-office-365-version-3-now-available.html). I haven't yet (... at the time of writing) had the opportunity to update my production install, although I am champing at the bit to do so, so I've continued to poke around the beta until the license expires.

<p class="note">Before diving into this, I want to highlight that this is going to be a little different from my normal posts. I've been sitting on this content for a while but couldn't find the thread/story to wrap around it... rather than put if off, or scrap it, this is going to be a collection of disjointed thoughts about VBO v3 post release.</p>

## Selective Folders in OneDrive for Business Backup Jobs

One of my key features in this update was the option to selectively include or exclude folders from OneDrive for Business jobs. Why? I've [mentioned it before](__GHOST_URL__/2018/10/10/my-wish-list-vbo365/), but I really do want our users to be using OneDrive. We're paying for the service, so why not! What I don't want to do is end up backing up terabytes of data that isn't all that important.

If I can setup my VBO job to only backup folders of my choosing, then this can be communicated to users and everyone can win. My users can store all of their files and make the decision themselves about what is important enough to be backed up.

At the time of writing, you can't configure this via the graphical console, but the PowerShell is fairly simple (and frankly, Veeam's PowerShell modules in general are a pleasure to use and well documented. It's worth learning to use them.)

If you find yourself needing to setup a brand new job to backup OneDrive, you can do the whole thing from the shell:



I hope most of that is self documenting. This example specifically targets me on my Office 365 tenant and specifies that I want to backup my OneDrive.

The key part of this is the "SelectedOneDriveFolders" parameter, which specifies the names of the folders to be backed up. As you can see it will happily take a list of names.

If you've already got a job that you want to edit, you can achieve this using `Get-VBOJob` to specify your existing job, and `Set-VBOJob` to change the desired parameter.

### I Feel Excluded!

There's another parameter now available to you, "ExcludedOneDriveFolders".

This allows you to explicitly exclude certain folders from your backup job. This gives you a different method for solving my issue, where you can tell users to put items they **don't** want backed up into a specific folder (e.g. "Personal"?) and everything else will be collected.

### Super Speed!

My whole reason for being excited for this update was the speed increases promised.

Unfortunately, I don't have v2 screenshots but I can show all folders vs selective folders for my own account.

{{< figure src="/2019/05/Full.PNG" caption="Backing up all folders" >}}

{{< figure src="/2019/05/Incremental.PNG" caption="Selective folder backup" >}}

I can also say that even without selective folder backups, our production OneDrive for Business and SharePoint Online backup jobs sped up by 28.1x. The jobs now complete in under 5 minutes.

## Research Needed

There's a couple of things I need to double check through more testing.

First, it's my understanding that folders you include or exclude from your backup job can be at any depth of the folder structure. In my case that means that if a user has a folder called "Critical" inside the folder containing family movies... I'll be backing that up.

I don't want this to be the case and I hope Veeam adds the ability to either turn recursion on and off, or control the depth it will look.

Secondly, I'm not sure whether wildcards are supported or not.

## A Warning Prior to Upgrading

I heard in the comment of a previous post that the upgrade for someone's VBO repository took over 24 hours.

When I finally got a chance to upgrade my production instance it took about two hours.

All this is to say, be prepared that you'll have a bit of downtime between starting on your VBO upgrade and starting your jobs again.

Don't worry, and be patient!

