+++
categories = ["Backup", "Office 365"]
date = 2018-10-09T19:47:26Z
description = ""
draft = false
featured_image = "/2018/10/dawid-zawila-279998-unsplash.jpg"
images = ["/2018/10/dawid-zawila-279998-unsplash.jpg"]
slug = "my-wish-list-vbo365"
summary = "I heard a rumor that v3 is already well into development... hopeful it's not too late to register my feature wish list!"
tag = ["Backup", "Office 365"]
title = "My Wish List for Veeam Backup for Microsoft Office 365 v3"
+++


I've been testing out Veeam's offering for [backing up Office 365](https://www.veeam.com/backup-microsoft-office-365.html). Earlier versions of it could backup Exchange Online but the [recently released v2](https://www.veeam.com/whats-new-backup-microsoft-office-365.html) expanded to being able to backup SharePoint Online and OneDrive for Business.

It's a topic that deserves its own post (it's on the list, I swear!), but the reason for needing a product like this needs a quick explanation.

**But it's in the cloud, it's already backed up!**

No, just no.

Microsoft guarantees availability, but your data = your problem.

Yes, versioning may help, and retaining deleted items for x days is ok. However, Microsoft doesn't care if George from HR deleted an important email six months ago that he desperately needs now.

Thinking you don't need to backup your cloud data is like setting up a redundant pair of on-premises file servers and declaring that backups of them aren't needed.

Again, this topic deserves a post of its own, so I'll stop going on about it for now.

## **Initial Thoughts**

The product itself is great and does what it says on the box. And it does it well. The Exchange Online backups in particular are lightning quick.

If you want to get your feet wet, you've got two options. A [community edition](https://www.veeam.com/free-backup-microsoft-office-365.html), which is free but limited to 10 users, and a [3-month trial](https://www.veeam.com/backup-microsoft-office-365-download.html?ad=in-text-link) with unlimited users.

Also, the Veeam techs and sales team are great. If you've got specific questions, I encourage you to hit them up, and maybe arrange a WebEx for a demo or Q&A session.

As great as the product is, there's room for improvement, and this post is my "wish list" of three features I hope make their way into v3.

## **Integration with the Main Veeam Backup & Replication Console**

The first thing you'll find out about this Office 365 tool is that it's independent of your other Veeam products. I'm used to opening up the VBR console every morning to check the status of various backup and copy jobs... and now I have to open a second console to check the status of a second set of backup jobs.

Part of the allure of the [Veeam Availability Suite](https://www.veeam.com/data-center-availability-suite.html) is that it provides a single pane of glass for our disaster recovery infrastructure. Through it we can see all of the copies, on all of the media, that's protecting our data.

But not now.

In v3, I hope I'll be able to plug Office 365 functionality into the VBR console. Even if it has to distinguish backup proxies and repositories as being specifically for Office 365 jobs, I still want to see them in there.

## **Granularity within OneDrive for Business**

You know what's awesome about OneDrive for Business? Have 5 TB or more of storage space available to you.

You know what's not awesome about wanting to backup OneDrive for Business? 100's of users having 5 TB or more of storage space available to them.

Currently you can either backup OneDrive for Business or not. You **can** say which users you want to backup, and exclude the rest. So for example it'd be possible to backup your executive team's individual drives but not the rest of your staff.

But here's the thing: I want to be able to say "only backup this specific folder within each user's OneDrive." This would allow us to encourage all user's to use their 5 TB of storage, and let them know that nothing will be backed up if they don't put it in the one magic folder. This lets the user decide what's important or not and takes the strain off of my backup storage.

Conversely, filters would also be welcome. As an example if our Service Desk staff like to keep a bunch of ISOs in their drives we could say not to back them up. Once again this would allow us to backup most things except for that which is only going to chew up space on secondary storage.

## **Possibility to Include Office 365 Backups in Backup Copy Jobs**

To be fair, this ties a lot into my first request, as once the consoles are integrated I doubt this would be the same sort of an issue as it is now.

Currently, to include your Office 365 backups in a copy job you'd first have to backup the storage (or server) where **those** silo-ed backup files are stored with VBR and **then** include those new VBR backups in a copy job.

In order to get them back, I imagine you'd then have to restore the storage through VBR, import it into the Office 365 console, and then do another restore within that console.

**Phew**, no thanks.

## **Wrap Up**

None of these issues/requests are show stoppers. They come from a place of love for Veeam, as I know they can (and will) keep working on this product and do listen to customer requests. It is still fairly new and I'm sure there will be plenty of iterations over its lifetime.

Now I'll go back to obsessively waiting on news of what's actually being included in v3...

