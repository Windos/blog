+++
categories = ["Meta"]
date = 2018-05-25T06:52:00Z
description = ""
draft = false
thumbnail = "/2018/05/james-pond-191811-unsplash.jpg"
slug = "blog-makeover"
summary = "Didn't I literally just post about my blogging workflow on GitHub Pages? Welp, I'm not there anymore!"
tags = ["Meta"]
title = "Blog Makeover - Why, Where, How?"

+++


It feels odd writing this post so soon after documenting my [blogging workflow](https://king.geek.nz/2018/04/26/blog-workflow/). Over the past two days this blog has migrated from [GitHub Pages](https://pages.github.com/) to a self hosted [Ghost](https://ghost.org/) install.

## A Little History

This blog, or at least the earliest posts on it, started on Ghost. This was back when the cost of their hosted services was *very* cheap. If memory serves, it was around the $5/month mark.

I loved the platform them, and still love it now. It strikes a nice balance between simplicity and features.

My move to GitHub Pages was a cost saving measure at the time. I wasn't blogging often, and when the cost of a hosted Ghost blog went up I couldn't justify the cost. For what I was doing, I didn't need scheduling, drafts, etc. and so static pages on a GitHub repo suited me fine.

And so now I find myself back where this adventure started.

## Why?

I have never been 100% satisfied with the theme I was using on Pages. Despite not being the topic of the conversation, talking with [Andrew](https://twitter.com/plaandrew22) got me thinking that I should look for a new theme.

I had already conceded that I may end up having to pay for a theme and I eventually found one that I was pretty keen on. In doing some research I ended up finding out that the theme I had earmarked was available for Jekyll based sites **and** Ghost.

Long story short, this started me off on a night's worth of research into what is involved in standing up an instance of Ghost on a cheap VPS.

Other than wanting to switch up the theme, I have also found myself wanting to be able to schedule posts. It was to the point where I was looking at scheduling merges on GitHub.

Finally, the metadata that social media sites use wasn't entirely correct.

## Where?

Ghost doesn't need a lot of [resources](https://docs.ghost.org/docs/hosting), they only warn that 1GB memory is the minimum and if you go lower than that you must configure swap. This blog is hosted on a VPS over on [DigitalOcean](https://www.digitalocean.com/), on their smallest sized Droplet. If it ends up needing more juice in the future I can always bump it up to a larger one.

I stuck with the recommendations, so the OS is Ubuntu 16.04.

As with the Pages blog, this is sitting behind Cloudflare for caching/CDN and DDoS protection. SSL wasn't as big of a reason for sticking with Cloudflare, as Ghost uses [Let's Encrypt](https://letsencrypt.org/) to get its own certificates. I'm using **both** certs, you see the one from Cloudflare and they see the one from the blog itself, all nice and enforced.

## How?

There's two videos working their way through the [TechSnips](https://www.techsnips.io/) editing pipeline, about setting up a DigitalOcean Droplet and then installing Ghost on it.

If you can't wait for that, the [Ghost docs](https://docs.ghost.org/docs/install) are really good!

What I will say is that it pays to point your domain name at your VPS before you start the install. The resolution needs to work in order for Let's Encrypt to generate the SSL cert.

I'd originally setup my install using an IP address and no SSL. The documentation says you should be able to update the address and setup SSL after the fact... but it didn't work for me.

This failure was *after* a day of setup and transitioning over blog posts (which unfortunately wasn't just dropping in my existing markdown files).

I ended up having to stand up a second VPS, complete the setup and then figure out how to move the content between the two. The luckily worked flawlessly.

## The Theme

Given that this was the catalyst for the move, I figure the theme deserves its own section.

I'm using [Mapache](https://github.com/godofredoninja/Mapache), which I've been tweaking a little (and probably will continue to tinker on for a while.)

This theme is simply sexy, [imho](https://www.urbandictionary.com/define.php?term=IMHO).

One feature I want to call out is how it handles embedded videos. It treats them like a featured image at the top of the page. To test this out I added an embedded video to my [Secret Santa](https://king.geek.nz/2017/12/12/kovertkringle/) post!

## Wrap Up

If there's anything you want to know about the setup of this blog, please do ask in the comments below.

Remember that [#PSTweetChat](https://twitter.com/search?f=tweets&vertical=default&q=%23pstweetchat) is next Friday (1 - 2 PM Eastern Time). Also, remember that if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)



