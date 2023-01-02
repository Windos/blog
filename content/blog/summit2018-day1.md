+++
categories = ["PowerShell", "Meta"]
date = 2018-04-09T18:50:00Z
description = ""
draft = false
featured_image = "/2018/05/1024px-Bright-eyed_and_Bushy-tailed_-7976750718-.jpg"
images = ["/2018/05/1024px-Bright-eyed_and_Bushy-tailed_-7976750718-.jpg"]
slug = "summit2018-day1"
summary = "Why the hell are American toilets so full?"
tag = ["PowerShell", "Meta"]
title = "Day One of #PSHSummit 2018 - Bright-Eyed and Bushy-Tailed"
aliases = [
    "/2018/04/10/summit2018-day1/amp/",
    "/2018/04/10/summit2018-day1/",
    "/2018/04/10/summit2018-day1"
]
+++


Why the hell are American toilets so full?

Seriously, this isn't normal. My day started by nearly storming off to reception to complain that they'd left the toilet blocked courtesy of the previous guest. Luckily a quick Google short circuited that.

This is really something the travel websites should warn people about!

My hope is that I'll be doing a post like this for each day of the [PowerShell + DevOps Global Summit](https://powershell.org/summit). I'll not be writing about the breakfasts, evening events, and other networking (what happens at summit stays at summit, or something like that?)

I'm taking hand written notes during the day and trying to turn those into something readable. Given I'm working on these at night, possibly after having some "refreshing beverages," please do excuse any glaring errors.

## **Keynote**

The event proper started with a keynote from [Don Jones](https://twitter.com/concentrateddon). It kicked off with [Counting Crows' "Mr. Jones"](https://www.youtube.com/watch?v=VuHVZ_-b868) which got a decent laugh… eventually.

This is the biggest summit ever and it sold out two months ago. There are 365 attendees, of which only 3% come from Asia Pacific (this is the category I fit into.)

[Andrew](https://twitter.com/plaandrew22) got a shout out, owing to him being the first [Summit Scholarship](https://powershell.org/2017/11/10/powershell-devops-global-summit-2018-scholarship-recipient/) recipient.

There were a very limited number of PowerShell Community All-Star challenge coins awarded, and they look sexy! I'm not going to list all of the recipients, because I did a bad job of writing them all down and I _know_ that I will forget someone. I really don't wont to do that!

Education is a big thing for [the DevOps Collective](https://devopscollective.org/), and they've been supporting an awesome program called [ITWorks](https://techimpact.org/our-programs/itworks/). This is a program that teaches young people both the IT skills, and the soft skills they need to succeed in the industry.

Next year, the Summit will be help from April 29 through to May 2. They're planning a "OnRamp" track which will be a separately ticketed and feature entry-level content and hands on labs.

Finally, [Jason Helmick](https://twitter.com/theJasonHelmick) (who is just as friendly and energetic in person as he is on video) can't do math.

## **PowerShell 2018: State of the Art**

Next up was [Jeffrey Snover](https://twitter.com/jsnover), who had some great quotes as always:

"It's 2018 and PowerShell has never been so important."

"Automation enables digital transformation."

"Build what advances you mission. Buy what doesn't."

"It's 2018 and YOU has never been so important."

Snover defined DevOps as making smaller more frequent batches of changes… and not being assholes to other teams.

He put Software as a Service in a way that I hadn't thought about it before, for things like moving to hosted Exchange rather than maintaining your own Exchange server: An airline can't seat more passengers because of their Exchange server.

He demo'ed something called "rModule" which was a solution, using implicit remoting, to get access to modules within PowerShell Core that aren't currently available.

The next demo was of a better, or rather more standardized, way of handling errors of terminating and non-terminating flavours (`-PSOnError { }`).

Finally, here is Snover's list of predictions for the near future:

* "PowerShell Core" renamed to "PowerShell"
* SHiPS reinvigorates namespaces
* More Swagger-based cmdlets
* ClousShell ships as Linux only
* Hierarchical OSS DSC LCM
* Linux distributions include PowerShell
* CrossStack IT emerges as peer to FullStack Dev
* Community Supported PowerShell v6 on full .NET

## **PowerShell Team Insights and Direction**

A few members of the PowerShell team, headed by [Michael Greene](https://twitter.com/migreene) and [Joey Aiello](https://twitter.com/joeyaiello), gave some insights on the team, the community, and the direction it's all going.

Within Microsoft, the PowerShell team is now part of the Azure Management Team which in turn is now part of the Azure Compute team.

There have been one million Linux machines running PowerShell v6.

During this session, seven PowerShell heroes were awarded, based on community nominations:

* [Adam Bertram](https://twitter.com/adbertram)
* [Lee Dailey](https://www.reddit.com/user/Lee_Dailey)
* [Warren Frame](https://twitter.com/psCookieMonster)
* [Don Jones](https://twitter.com/concentrateddon)
* [Chrissy LeMaire](https://twitter.com/cl)
* [Kevin Marquette](https://twitter.com/KevinMarquette)
* [David Wilson](https://twitter.com/daviwil)

These are all great community contributors that I've personally learnt something from. Very well deserved awards!

I came away from this session needing to research "Hybrid Runbook Workers" and "Update Management Hybrid Nodes."

## **A Historical Architectural Tour of PowerShell**

Just after lunch [Bruce Payette](https://twitter.com/brucepayette) gave a history of PowerShell through until v1 shipped. It started as a project codenamed Kermit (the hermit crab that lost his shell.)

The project was spun up because of a server that effectively said that it took 10x the effort and 10x the time to automate Windows vs. Unix.

I won't lie, this was a very deep dive, and a lot of the deeper parts went over my head. Regardless I enjoyed getting some of the background.

As a bonus I also walked away with an idea for another project (like I have time for any more of those). Spoiler: it involves [parameter constraints](https://king.geek.nz/2015/07/23/ugly-validation/).

## **PowerShell Team Lightning Demos**

There was a lot of lightning demos, and I wrote a lot of notes. I'm not going to try to sort these into the more "conversational" style I've done above. Rather, here's a bullet point list:

* DSC
* No longer depends on WMI
* REST API!
* Prerelease support for PowerShellGet
* There are good text messages on the gallery site
* Find-Module -AllowPrerelease
* Save module doesn't put "prerelease" in folder name on disk
* Get-Module also doesn't indicate prerelease status, you have to looking under $_.PrivateDate.PSData
* PowerShell IoT Module
* This was demo'd on a Raspberry Pi, nice!
* Turned on a lamp by flipping a GPIO Pin
* Interfaced with a temperature sensor, able to easily create a weather station of sorts
* PSEditorServices
* Demo of debugging using VS Code
* Used the NotHotdog service
* Demo gods were looking down, but ended up getting there
* There's an awesome command in the PowerShell extension to open an issue on GitHub, it pre-populates a bunch of information for you!
* SHiPS remotely editing files
* Providers are cool (Snover was right!), it's an great was to "navigate" data.
* There are limitations when working between providers, copy doesn't work but you can get content from one and set content in another (because you're just passing through a string at that point really)
* Change Tracking in Azure
* Metadata and content changes, including a dif view
* They've thought a lot about the security, which is important as files are most sensitive part of a VM
* Azure PowerShell
* Azure commands have gotten a lot better, if you haven't used them recently give them another try
* Lots of (dynamic) tab completion that is relevant, e.g. US vs. China vs. Azure Stack
* They're working to make it a lot easier, you can now create a VM with 1 cmdlet rather than 10 (I'm not sure if that's hyperbole)
* Azure PowerShell on PowerShell Core is here but will be separate builds for now. Also the prefix (should be) changing to "AZ" soon
* Project Honolulu
* JEA with a GUI
* Going GA very soon
* Local Groups for RBAC
* Improvements to Help System
* You don't need to be admin to update help any more
* Help will be stored in the user profile if you're not admin
* You still need to be admin to update help for the entire system
* Markdown based help looks sexy!
* PowerShell ThreadJobs
* Find it on [GitHub](https://github.com/paulhigin/psthreadjob)
* Built in support to throttle jobs
* Viewable and manageable like a standard job
* They are lighter than standard jobs and return live objects rather than deserialized ones
* rModule
* Glad to see more of this
* It returns deserialized objects
* The module will be up on GitHub soon
* State Configuration (Preview) in Azure
* Currently Private Preview
* Looks like a great GUI to surface/monitor DSC compliance
* Handles composite configurations well, including filling out parameters

Also, I really apologize for not having names to attribute here!

## **Wrap Up**

That brings us to the end of the content from today, keep an eye out for my recap of day two when the breakout sessions start. As I write this, I still have 30 minutes for it to still go live on the same day. Wish me luck!

Also, remember that if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

