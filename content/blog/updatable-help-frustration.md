+++
categories = ["PowerShell"]
date = 2016-11-07T10:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/thumb-1.png"
slug = "updatable-help-frustration"
summary = "AAAAAAAAAAAAARRGH!"
tags = ["PowerShell"]
title = "PowerShell Updateable Help Frustration"

+++


AAAAAAAAAAAAARRGH!

I've been trying to brute force my way through this for a little over 24 hours now, and I'm afraid I'll have to admit defeat.

I was playing around with the [platyPS](https://github.com/PowerShell/platyPS) module to generate markdown and external help for my [BurntToast](https://github.com/Windos/BurntToast) module. It's a very short leap from there to updateable help.

And honestly, once you have external help files it is actually a very simple process to get updatable help in place and working. There's actually some very good articles out there on the topic, including those from [June Blender](http://info.sapien.com/index.php/scripting/scripting-help/helpuri-v-helpinfouri) and the [series on MSDN](https://msdn.microsoft.com/en-us/library/hh852754(v=vs.85).aspx)

I mentioned it's simple, so why the frustration?

As always, I needed to throw a self-imposed spanner in the works: the module is developed via GitHub, surely I can serve up the help content from there as well?

## **The things I've learnt**

First up, I'll list off the silly little things I picked up on that could save you some time:

* The HelpInfoURI in your PSD1 file needs to point to the root location containing your HelpInfo.xml file, **not** the xml file itself.
* Similarly, the HelpContentURI in your HelpInfo.xml file needs to point to the root location of your .cab file, **not** directly to it.
* Both of these URIs (seem) to need to end in a '/', otherwise PowerShell will complain that the URI does not resolve to a container.
* These URIs can point to different locations, but they don't need to.
* The web server hosting your xml file and help content don't need to have directory browsing enabled.
* HelpInfoURI cannot start with https. Not sure about HelpContentURI, as I've been using the same link for both.

## **Sounds simple, what did you do wrong?**

_Note: a lot, if not all, of the links shown below are no longer valid as I have cleaned up the repo(s)_

My initial attempt was to put all of the required files into a directory in the BurntToast repo. But... what location do I link to?

I tried grabbing the download link for the [cab file](https://github.com/Windos/BurntToast/raw/master/Help/Updateable/BurntToast_751a2aeb-a68f-422e-a2ea-376bdd81612a_en-US_helpcontent.cab) and stripping it down to the 'directory':

[https://github.com/Windos/BurntToast/raw/master/Help/Updateable/](https://github.com/Windos/BurntToast/raw/master/Help/Updateable/)

That should work, right?

{{< figure src="__GHOST_URL__/content/images/2018/05/nope1.png" >}}

Ok, fine, maybe a repo isn't going to work. But wait! This blog runs out of GitHub Pages, perhaps I can serve these files out of a directory on there? Let's try:

[http://king.geek.nz/PSHelpContent/](http://king.geek.nz/PSHelpContent/)

Same result.

What is interesting is that when you try to run `Save-Help` on the module, it creates an empty, correctly named, cab file in the target directory.

{{< figure src="__GHOST_URL__/content/images/2018/05/folder-1.png" >}}

This is actually different than if you remove the files from the remote location (or just point the URI at the entirely wrong place.) When this happens I only get one of the two error messages shown above and the empty cab file isn't created.

For reference, the expected result of running `Save-Help` is a copy of both the cab and xml files.

## **What did you end up doing?**

In order to move on from this and get to work on actually finishing the next release of BurntToast, I've installed IIS on my home lab server and dumped the files on there.

Using a URL that points to that, the updateable help is working flawlessly. Unfortunately, I [don't normally](https://king.geek.nz/2016/07/04/home-server-shutdown/) leave this server running 24/7, so I'll need to look for a cheap hosted solution at some stage.

There may even be a demand for a community service around this?

I wonder if OneDrive or some sort of cloud storage will work?

On second thought, these are not rabbit holes I should be exploring right now.

