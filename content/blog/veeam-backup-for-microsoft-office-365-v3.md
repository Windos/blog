+++
categories = ["Backup", "Office 365"]
date = 2019-02-18T21:29:41Z
description = ""
draft = false
thumbnail = "/2019/02/katya-austin-554633-unsplash.jpg"
slug = "veeam-backup-for-microsoft-office-365-v3"
summary = "I've been testing a beta of Veeam Backup for Microsoft Office 365 v3. It ticked some boxes of mine (spoiler alert.)"
tags = ["Backup", "Office 365"]
title = "Veeam Backup for Microsoft Office 365 v3.0: It's Good, Very Good"

+++


Towards the end of last week, a tweet with a nice big "[Veeam Green](https://www.color-hex.com/color/54b948)" logo caught my eye.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Veeam Backup for Microsoft Office 365 v3 BETA is here! Looking for your feedback! Get it now via <a href="https://t.co/ahLz7QUfKX">https://t.co/ahLz7QUfKX</a> <a href="https://twitter.com/hashtag/veeam?src=hash&amp;ref_src=twsrc%5Etfw">#veeam</a> <a href="https://twitter.com/hashtag/office365?src=hash&amp;ref_src=twsrc%5Etfw">#office365</a> <a href="https://twitter.com/hashtag/beta?src=hash&amp;ref_src=twsrc%5Etfw">#beta</a></p>&mdash; Niels Engelen (@nielsengelen) <a href="https://twitter.com/nielsengelen/status/1095782484816605185?ref_src=twsrc%5Etfw">February 13, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

A beta preview of the next version of [Veeam Backup for Microsoft Office 365](https://www.veeam.com/backup-microsoft-office-365.html)? Yes please!

I don't know what I was expecting when I clicked through, but I certainly wasn't expecting anything from [my wish list](https://king.geek.nz/2018/10/10/my-wish-list-vbo365/) to be in the change notes. Boy was I happy when I saw what was being introduced, I went so far as to start screen shotting my highlights and sending it through to colleagues.

_If you're interested in testing out the v3 beta yourself, check out the [Veeam Community Forum](https://forums.veeam.com/veeam-backup-for-microsoft-office-365-f47/veeam-backup-for-microsoft-office-365-3-0-beta-t57218.html) for details._ _Do remember that this is a **beta** and should not be used in production. In fact there is no upgrade path to this beta, nor an upgrade path from this beta to the GA release._

## **An Item Checked off of My Wish List**

One of the items on my wish list was "Granularity within OneDrive for Business" and the beta has introduced "Support for including/excluding specific folders in OneDrive for Business (via RESTful API/PowerShell)"

This works really well, and addresses one of my key concerns... trying to back up 100's of users OneDrives when they all have terabytes of space available to them.

I'm currently kicking around the idea of choosing a name, like "Critical", and only including folder(s) named that. Users can then be told to put things they want backed up into that folder... and if they don't then good luck to them!

There's some cool stuff you can do with inclusions and exclusions of folders, and I'll likely do a post on just this topic once I've had some more time to experiment.

Could more be done? Yes...

There's two further developments I'd like to see to enable even more OneDrive granularity:

1. Support for wildcards in folder names. Allowing one to include "Critical*" which matches folders like "Critical" and "CriticalData".
2. File based filters. This would allow excluding all ISO files (is it only me that has gigabytes of these for adhoc VMs on the go?) or known media files we don't care about.

## **The Real Winner: SPEED**

There's one item I didn't include on my wish list, but in hindsight I wish I had (because then I could have claimed two items checked off...) and that's speeding up SharePoint Online (and by extension OneDrive) backups.

The problem was that they were **SLOW**. Mainly because it had to check each and every file individually every time the backup ran, there wasn't a shortcut to say "hey, only these files have changed since last time maybe only grab those."

Why didn't I include it on the list? By the time I wrote my wish list, I'd already opened a ticket with Veeam support and had accepted the it was just the way of things for the foreseeable future.

{{< figure src="/2019/02/VeeamTicket.png" >}}

But, much to my surprise it's sorted: "Up to 30x times faster SharePoint Online and OneDrive for Business incremental backups"

30x faster? That's got to be a hyperbolic claim, right?

Well, not in the slightest. In my initial testing I actually saw backups as much as 75x faster!

To be fair, these numbers came from a Friday night when **nothing** was happening on the SharePoint site in question. Now that things have bedded in, I've pulled together some numbers.

### **SharePoint Online**

Currently with v2, SharePoint backups can be anywhere from 3 hours, 45 minutes through to 5 hours. Being ever hopeful the job is running every four hours, but this is a longer RPO than I was hoping for... and often the mark is missed as the previous backup is still running.

{{< figure src="/2019/02/SharePoint-v2.PNG" >}}

On v3 the same backup job takes about 20 minutes, and I've never seen it take longer than 30 minutes. This allows me to run the job hourly, and I'm much happier with that RPO.

{{< figure src="/2019/02/SharePoint-v3.PNG" >}}

### **OneDrive for Business**

Our OneDrive job on v2 was ok. It'd take about an hour, and I was happy with that. The schedule was set to every two hours, just in case it ran over the hour mark too often.

{{< figure src="/2019/02/OneDrive-v2.PNG" >}}

Once again, the v3 update delivers. The same OneDrive backup job now only takes 20 minutes and the schedule on the job is also down to hourly.

{{< figure src="/2019/02/OneDrive-v3.PNG" >}}

### **Selective OneDrive Backups**

But what about these new granular backup options for OneDrive backups? I put my "Critical" folder idea to the test. The incremental backups are about twice as fast as the non-selective backups (keeping in mind, I've drastically limited the scope of the backups.)

{{< figure src="/2019/02/OneDrive-Selected.PNG" >}}

What I really liked though, was the speed of the initial full backup. Without selecting a specific folder to be backed up, the full backup of OneDrive on v3 took over 7 hours.

{{< figure src="/2019/02/OneDrive-Full-Initial.PNG" >}}

My "Critical" folder test, however?

{{< figure src="/2019/02/OneDrive-Selected-Initial.PNG" >}}

Just a little over an hour. Woah, colour me impressed!

## **Let's Talk Colour**

[Veeam Backup & Replication 9.5 Update 4](https://www.veeam.com/kb2878) hints at a change in the colour scheme for Veeam products. With VBR this can be seen in the splash screen while the console is loading, and it's smattered around the other products in the line up.

The console for Veeam Backup for Microsoft Office 365 v3 now defaults to a darker green theme called "Sea Green" while the original "Veeam Green" can still be chosen. I don't know that I "like" the new default... but certainly is a little easier on the eyes.

It does however make me want to add a feature request for a proper dark theme to the wish list for v4 (and VBR...)

## **The Verdict**

This is an awesome update, and I can't wait for it to go GA so that I can start using it "for real".

The last update was ground breaking as it introduced the ability to backup SharePoint Online and OneDrive for Business. But while it was possible, it was **slow**.

I [sung Veeam's praises](https://www.veeam.com/success-stories/veeam-hastings-district-council-success-story.html) for their Availability Suite and the speed increases we saw with that over previous backup options. And in all honesty, I was initially really disappointed with the speed of their Office 365 offering. I accepted it (begrudgingly) as I knew that if Veeam couldn't get this going faster, then no one on the market could.

I'd argue that this is an even more important update than the last, as it feels like these features are now "ready" for prime time. Anyone that was on the fence about getting this product to protect their data in Office 365, now's the time to start getting quotes sorted.

**This update is good... very good.**

