+++
categories = ["PowerShell", "Meta"]
date = 2018-04-16T13:15:00Z
description = ""
draft = false
thumbnail = "/2018/05/56012414_699aac9e8e_o.jpg"
slug = "summit2018-hindsight"
tag = ["PowerShell", "Meta"]
title = "A Quick Look Back at #PSHSummit 2018"

+++


After another three flights, and 16 hours in the air, I'm back home in New Zealand. I can certainly say that the change in time zones has hit me harder coming back. Going over to Bellevue, I got right into things and felt (for the most part) normal.

Last week I did a [blog post for each day](https://king.geek.nz/tags/#pshsummit) of the [PowerShell + DevOps Global Summit](https://powershelldevopsglobalsummit2018.sched.com/), going over what I took part in that day and what my main takeaways were.

This post will be an overarching lookback at the Summit. I wanted to do this close enough to the event that I still remembered things fairly well, but not so soon that I hadn't had a chance to process everything.

Here's hoping I picked the right time!

## **My Main Takeaways**

I came home from the Summit with three main things I'm going to be following up on right away. You'll probably be seeing blog posts on these topics as I work my way through them.

### **WebJEA**

I've got a handful of scripts that it'd be very handy for the support team at work to be able to use, but there's two problems:

1. They don't have the permissions needed to run them
2. They aren't fully up to speed on PowerShell (yet!)

My hope was that I'd be able to use [ChatOps](https://docs.stackstorm.com/chatops/chatops.html) to solve these problems, and the POC I did with [Slack](https://slack.com/) and [PoshBot](https://github.com/poshbotio/PoshBot) was promising. But the collaboration/chat tool we'll be using going forward is [Microsoft Teams](https://products.office.com/en-us/microsoft-teams/group-chat-software) so putting a bunch of effort into a different service now seems fruitless.

The alternative is to expose these scripts through a secure web interface and [Mark Domansky's](https://twitter.com/markdomansky)  [WebJEA](https://github.com/markdomansky/WebJEA) does just that.

### **Neo4j/Dots**

For the longest time we've been looking for a way of graphically representing our servers, systems, and services and the dependencies between them. The ultimate goal being a way of seeing "if this thing goes down, it affects these other things."

[Warren Frame](https://twitter.com/psCookieMonster) showed a simple CMDB, [Dots](https://github.com/RamblingCookieMonster/Dots), which makes heavy use of [Neo4j](https://neo4j.com/).

As soon as I saw this up on the screen, I sent a picture back to the guys I work with NZ. Part of the magic may have been lost not seeing it "move," but this looks like exactly what we've been wanting to achieve.

### **Puppet**

Finally, [Puppet](https://puppet.com/).

I'll be frank, we're a small shop. We're not dealing with request for 30+ new IIS servers at a time. That doesn't mean we can't benefit from configuration management/automation to drive standardization, speed up delivery, and reduce human error.

I really like how Puppet handles inclusion of DSC, which is what got this on my list instead of just raw DSC.

## **The Content Was Great**

There was a good mix of new and experienced speakers and they covered a wide range of topics. The organizers did an excellent job of scheduling all of the content and every single time slot was a difficult choice between two or more talks.

They say in the brochure for the Summit that it's a highly technical event, and they weren't wrong. A good number of the talks were firmly over my head, and I could tell that from the synopsis, but even in those cases there was good information to be had.

I'm not impatiently waiting for the session recordings to be released (which could be a month or more) to catch the sessions I couldn't make it to and re-watch those I did attend but feel like I may have missed something.

## **But the Community Is What Makes the Summit**

The PowerShell community is one of the best tech communities out there. You can get a taste of this on Twitter, but that only tells half of the story.

When you get a bunch of PowerShell people in the same room, it's amazing the conversations that come up. Everyone comes from different backgrounds and works in different environments. But we're all facing the same issues (maybe at different scales) and solutions that you'd never have dreamed of always come up in this environment.

The community is endlessly supportive and eager for everyone to succeed and to share their success. I know of two blogs that have started (or are starting) as a direct response to gentle encouragement (read: peer pressure)

## **Will I Be Going Back?**

Hell yes!

The real question is: when? It's a real financial commitment getting over to America and I'm fairly certain I can't make it an annual thing. Though you shouldn't ever say never.

What I will say is that if **you** want to go next year that you really have to get in early. The event has been selling out quicker each and every year and people always miss out. Get your approval from work early and have it ready to go when sales open!

## **What's Next?**

I'm finally able to stream on YouTube again! I don't think I'll be doing on of those for another couple of days as I really should make sure I'm over the whole jetlag thing before I start getting up at 3 AM to do this streaming business.

You'll also catch me (virtually) speaking at a couple of PowerShell user groups through the year. Keep an eye on [my Twitter](https://twitter.com/WindosNZ) as I'll be advertising these as they're locked down.

Other than blog posts, which I hope to ramp up to two per week, the next thing you'll see come from me is video recording of what would my Community Lightning Demo on BurntToast. Should be a quick 5 to 10 minute video, and I'm really looking forward to doing it!

A little further down the track I'll be working on something with [Adam Murray](https://twitter.com/muzzar78). I won't tell you what yet… but, as always, keep an eye on Twitter.

## **The End**

And that's officially the end up my #PSHSummit coverage. Thanks for following along, I hope my ramblings did an ok job of giving you an idea of what the event is like.

Also, remember that if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

