+++
categories = ["PowerShell"]
date = 2017-12-11T14:46:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/thumb-15.jpg"
slug = "kovertkringle"
summary = "Given it's the holiday season, I worked on a PowerShell based Secret Santa Organizer"
tags = ["PowerShell"]
title = "KovertKringle: Secret Santa With a Dash of PowerShell"

+++


Given it's the holiday season, I recently spent about 3 hours live streaming the development of a PowerShell based [Secret Santa](https://en.wikipedia.org/wiki/Secret_Santa) Organizer. You can watch the videos from these streams, if you're so inclined, over on [YouTube](https://youtu.be/Xr5FKSVu-E4).

I can't live with giving my projects straight forward names; with that in mind let's check out [KovertKringle](https://github.com/Windos/powershell-depot/tree/master/General/KovertKringle).

## **Slow down… what's a Secret Santa?**

If you've never done it before, Secret Santa basically boils down to a group of people who are each assigned another member of the group to buy a present for. One should only know who they're buying for and not, for example, who is buying something for them.

This would often be done by drawing names from a hat… but that's not nearly "geeky" enough in 2017, is it?

## **Laying the foundation**

KovertKringle is a PowerShell module you can use to organize a Secret Santa for your family, friends, or office.

It stores the list of participants and "who is buying for who" in [module scoped variables](http://mikefrobbins.com/2017/06/08/what-is-this-module-scope-in-powershell-that-you-speak-of/).

There's a host of functions that manage these variables and then sending out of notifications:

* [Add-KKParticipant](https://github.com/Windos/powershell-depot/blob/master/General/KovertKringle/Public/Add-KKParticipant.ps1)
* [Clear-KKPairing](https://github.com/Windos/powershell-depot/blob/master/General/KovertKringle/Public/Clear-KKPairing.ps1)
* [Clear-KKParticipant](https://github.com/Windos/powershell-depot/blob/master/General/KovertKringle/Public/Clear-KKParticipant.ps1)
* [Get-KKParticipant](https://github.com/Windos/powershell-depot/blob/master/General/KovertKringle/Public/Get-KKParticipant.ps1)
* [Remove-KKParticipant](https://github.com/Windos/powershell-depot/blob/master/General/KovertKringle/Public/Remove-KKParticipant.ps1)
* [Send-KKNotification](https://github.com/Windos/powershell-depot/blob/master/General/KovertKringle/Public/Send-KKNotification.ps1)
* [Start-KKPairing](https://github.com/Windos/powershell-depot/blob/master/General/KovertKringle/Public/Start-KKPairing.ps1)

This post won't be a detailed description of each and every function and possible edge cases. Rather, it'll cover an example use for a "100% made up and totally not representative of people I know" family, including a "snitch."

## **Uh, Snitch?**

I mentioned that people should only know who it is they're buying for… and none of the other pairings.

That doesn't always fly.

There's often someone in the group that **needs** to know all the details. Rather than fight this, it's built into the module.

I refer to anyone in that demands to know each of the pairings as "snitch."

## **Secret Santa Example**

Our example family is made up of five people: Josh (not me…), John, Wendy (the snitch), George, and Fiona. We'll load them up into KovertKringle, each with an email address:

```powershell
Add-KKParticipant -Name 'Josh' -Email 'josh@example.com'
Add-KKParticipant -Name 'John' -Email 'john@example.com'
Add-KKParticipant -Name 'Wendy' -Email 'wendy@example.com' -Snitch
Add-KKParticipant -Name 'George' -Email 'george@example.com'
Add-KKParticipant -Name 'Fiona' -Email 'fiona@example.com'

```

Now that we have our list of participants (which you can check by running `Get-KKParticipant`), it's time to pair them up randomly. To do this, run:

```powershell
Start-KKPairing

```

Finally, it's time to let people know who they're buying for.

```powershell
Send-KKNotification

```

This will send off an email to each of the participants, letting them know their "giftee" and also reminds them of the spend limit (in this example, $20.)

{{< figure src="__GHOST_URL__/content/images/2018/05/example.png" >}}

What about the snitch? They get BCC'ed on all of the email notifications.

## **Disclaimer**

This module was put together rather hastily. All up, including random tangents, development time clocked in at 3 hours.

It is currently not very portable to other people, specifically the `Send-KKNotification` function. It uses SendGrid, has my email as the "from address", and has the spend limit hard coded in.

With that in mind, if you're thinking of using this, please remember to edit _Send-KKNotification.ps1_ before use.

<iframe width="480" height="270" src="https://www.youtube.com/embed/OTd5An2ug10?feature=oembed" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



