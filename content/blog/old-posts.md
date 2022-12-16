+++
categories = ["Windows"]
date = 2016-11-21T10:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/REA_pie_from_oven.png"
slug = "old-posts"
summary = "I was recently looking through my Google Analytics and noticed there was an old blogspot address that I hadn't seen in years."
tags = ["Windows"]
title = "Digging Up Old Blog Posts"

+++


I was recently looking through my Google Analytics and noticed there was an old blogspot address that I hadn't seen in years.

The blog only had two posts on it, and judging by the title (and my job at the time) I was obviously looking at starting a Helpdesk orientated site.

Time sure has flown by since then.

Rather than just let the (short) posts die, I figure I'd repost them here (for the lulz.)

## Injecting drivers into offline WIM images

_Thursday, July 14, 2011_

> "The most likely way for the world to be destroyed, most experts agree, is by accident. That's where we come in; we're computer professionals. We cause accidents."
> ~[Nathaniel Borenstein](http://en.wikipedia.org/wiki/Nathaniel_Borenstein)

Historically, we have maintained two separate [WIM](http://en.wikipedia.org/wiki/Windows_Imaging_Format) images on our [Windows Deployment Services](http://en.wikipedia.org/wiki/Windows_Deployment_Services) server. These images have been tied to two different brands of PC, partly due to our automatic naming script but also due to the fact that both brands needed a different set of drivers.

With the introduction of a new batch of PCs (the same brand as we have previously been using, if you were wondering if they were a third brand,) we decided to experiment with creating a single, universal, image.

This has worked very well, with the exception of needing to manually rename the minority of PCs that are of the odd brand. In order to achieve the desired result we followed our usual process of creating a reference machine, [sysprepping](http://en.wikipedia.org/wiki/Sysprep) it and capturing the image.

Instead of uploading the image directly to our WDS server, it got mounted using [imagex](http://en.wikipedia.org/wiki/Imagex#ImageX) and the following command was run to add a directory filled to the brim with every driver we use on-site:

~~~
Dism /Image:C:\mount /Add-Driver /Driver:c:\drivers /Recurse /ForceUnsigned
~~~

This assumes the image was mounted to C:\mount\ and the drivers were stored in C:\drivers\

Now any machine we deploy the image to gets it's full suite of drivers, and the work involved in maintaing our deployment images has been halved.

## Fix Your HP LaserJet P2015

_Monday, August 1, 2011_

**Ingredients:**

* 1 Broken HP LaserJet P2015
* 1 Phillips Screw Driver
* Tin Foil

**Method:**

* Preheat oven to 180°C
* Remove side panel from your HP LaserJet
* Using you Phillips Screw Driver, remove the formatter board from your HP LaserJet
* Roll the Tin Foil into three Golf-Ball sized balls
* Place formatter board into the oven, component side down, on top of the Tin Foil balls
* Bake for 8 minutes
* Replace formatter board into your HP LaserJet
* Enjoy your fully-functional printe



