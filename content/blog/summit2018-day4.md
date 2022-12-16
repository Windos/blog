+++
categories = ["PowerShell", "Meta"]
date = 2018-04-12T17:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/1024px-Flickr_-_Official_U.S._Navy_Imagery_-_U.S._Naval_Academy_plebes_carry_a_log_as_part_of_teamwork_training_during_Sea_Trials..jpg"
slug = "summit2018-day4"
summary = "That's it. It's over. I've shaken many hand, met many people, and seen lots of PowerShell."
tag = ["PowerShell", "Meta"]
title = "Day Four of #PSHSummit 2018 - Iron Scripter"

+++


That's it. It's over. I've shaken many hand, met many people, and seen lots of PowerShell.

This'll be the last of my recap posts, and in a couple of days I'll be following up with a "this was Summit, and you should be there next year" type post.

Please do read the [previous three recaps](https://king.geek.nz/tags/index.html#pshsummit) if you haven't already!

## **Managing Advanced DSC Configurations and Configuration Data**

[Jason Helmick](https://twitter.com/theJasonHelmick)

"Do you want your morning to be easy?"

{{< figure src="/2018/05/helmick-large.jpg" >}}

I said it yesterday, and I'll say it again today: Jason's energy is amazing.

The point of this session was separating your DSC configurations from your configuration data in an effort to preserve your configuration and not screw it up (as deeply flawed humans tend to do.)

That's just the start though. He then stepped through what you're going to find as you do this at scale and how to continue to manage your configuration data.

Effectively, he was giving us the answer to a problem we hadn't had yet so that it's tucked away somewhere in the dark corners of our minds when we do come across the problem.

## **WebJEA: PowerShell Driven Web Forms for Secure Self-Service**

[Mark Domansky](https://twitter.com/MarkDomansky)

{{< figure src="/2018/05/mark-large-1.jpg" >}}

Wow. Just wow.

I remember talking with Mark over a meal earlier in the week. He mentioned what his talk was about and I knew right away that I absolutely had to go watch it.

I've been keenly interested in ChatOps recently, as it's a mechanism for exposing PowerShell functions to less privileged users who may not have permission to run the scripts themselves in a user friendly manner. We use MS Teams, however, so implementing a PowerShell based chat bot is a while away.

Mark's talk was about [WebJEA](https://github.com/markdomansky/WebJEA), which provides a web interface for triggering PowerShell functions. His project ticked a major box for me and, as I said in my session feedback, I will be playing with it on the plane home if there happens to be in-flight Wi-Fi.

## **Code Writing Code, Say What Now! Meta Programming & PowerShell**

[Wesley Kirkland](https://twitter.com/unleashthecloud)

{{< figure src="/2018/05/wesley-large.jpg" >}}

This was a session about meta programming and how we're probably doing it already and not realizing it (which I am, and didn't!)

Wesley is sharp, and this session was deeply technical. A lot of it did go over my head... I may need to re-watch the recording to make sure I got everything.

Also, I picked up a new phrase that is pretty close to my new favorite: **Tempermanent**. The act of hacking something together quickly with the intention of going back to fix it up later, but never doing so.

## **Building Cross Platform PowerShell Modules**

[Adam Driscoll](https://twitter.com/adamdriscoll/)

{{< figure src="/2018/05/adam-large.jpg" >}}

Adam did a great job of putting the requirements of creating cross platform compatible PowerShell modules into terms that non-developers could follow. I point this out because it's all about .NET and DLLs and the like.

This session inspired me to check out if Linux (or specific distributions) thereof have native mechanisms for toast notifications.

## **Invoke-ChatOps: Level up and Change Your Culture With Chat and PowerShell**

[Brandon Olin](https://twitter.com/devblackops)

{{< figure src="/2018/05/brandon-large.jpg" >}}

I mentioned ChatOps earlier, and Brandon is **the** ChatOps guy. I've seen this presentation via a virtual PowerShell User Group.

Last time the talk lasted 90 minutes, but this was a 45-minute time slot. There's a lot to cover on this topic and it lasted slightly longer than 45 minutes, but Brandon did a great job of compressing his material (I couldn't pick out what he removed).

WebJEA may have doused my need to rush into ChatOps, but it's still a really interesting topic.

## **Iron Scripter!**

The day, and by extension the Summit, was capped off by the [Iron Scripter](http://ironscripter.us/) competition. The people that were participating divided into three teams, and were given an hour to complete a scripting challenge.

We had to create a script for new user creation, with a bunch of specific design requirements.

I was part of the Flawless Faction, which probably has the toughest mandate of all three factions... code that works flawlessly. This means error handling, tests, input validations. The works.

We didn't win.

Naturally, a tight time frame lends itself to the Battle Faction, whose mandate is a script that's quick and dirty and good enough to get the job done.

It was a hell of a lot of fun!

Next year, I hope there is one or two tweaks. I think the event should be two hours long, but the Battle Faction (and only the Battle Faction) be awarded bonus points for turning their solution in as early as possible. This incentivizes them to work fast on their code, and gives the other two factions time to put the required polish on their solutions.

## **The End**

Now it's time to be a tourist in Seattle for a day or two before flying home. I'm feeling amped up by the PowerShell community, and so you can expect plenty of blogs, livestreams, and possibly a secret project or two coming down the pipeline.

