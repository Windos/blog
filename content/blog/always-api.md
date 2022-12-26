+++
categories = ["PowerShell"]
date = 2017-04-24T12:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-2.jpg"
images = ["/2018/05/thumb-2.jpg"]
slug = "always-api"
summary = "Your business uses two disparate cloud services and needs them to share data, but they don't integrate natively. You really don't need to make this harder than it needs to be."
tag = ["PowerShell"]
title = "Always Ask for the API"
+++


Take this hypothetical, totally-didn't-recently-come-up-at-work, scenario: your business uses two disparate cloud services and needs them to share data, but they don't integrate natively.

You, the scripting magician that you are, can take an export from the first service, manipulate it however it needs to be manipulated and then import it to the other service. You could do this in reverse. You could do this all day, every day; it's a task right up your alley.

But you really don't need to make this harder than it needs to be.

## **Hard? This sounds like a piece of cake!**

Yeah sure. At first look it really is a simple task.

You read in a CSV, make some changes to the resulting objects, maybe pull in some auxiliary info from an internal system and finally spit out a new CSV that can be uploaded to the second service.

But take a step back and consider: how are you getting that initial CSV?

Chances are that you may not have your own access to the service in question, or you may have limited access. This is especially true if it's “owned” by another business unit outside of IT, like Human Resources. This means that you likely won't be able to export a report as a CSV and you'll need someone else to do it for you and probably email it to you.

Now you've got to figure out how to:

* Check for to see if the email is in your inbox.
* Save the attachment somewhere.
* Read the file in and do all the processing mentioned above.
* Clean up the file.

You've also got to worry about the staff member you've roped into this scheme remembering to do it. It'll also never be sent at the same time, so you're script/process will need to run in a loop until the email arrives.

Also, the whole point of automation is to rule out (or limit/mitigate) human error. Are you 100% sure that there is no chance that the person running the report won't accidentally generate the wrong one. The report you get could end up with missing columns, extra columns. Maybe it has the right info, but in the “wrong” order.

## **Well, that escalated quickly**

It sure did! But fear not, there is (usually) and easier way.

Firstly, if this is a bigger cloud provider, chances are they already have an accessible and well documented API. In which case, just use that… and why were you even considering doing any of the above?!

However, if this is more of a bespoke service, then instead of roping someone from the business unit into running a report all the time for you, ask them if they have a technical contact or account manager with the company providing the cloud service.

With contact details in hand, flick them an email. Include the person you've been talking to within your own company so they are aware of what you're asking for and can authorize it if need be.

Keep the email short and simple, explain that you're attempting to get data out of system x using an API (maybe mention a preference for JSON if you're on that PowerShell train). Ask if it's possible and what the authorization and development process is.

You'll likely get a “sure, no problem!” type response and details on the specific info the company needs. This will likely include the specific info you want exposed and how often you'll be polling it.

When I've done this in the past, after specifying the info I'm after they will generate an example of the result as a CSV so you can eye ball that the result is what you're looking for and then they'll hand it off to their developers to set it all up.

When the developers have done their thing, you should get some instructions, a URL and an authorization key. Time to start `Invoke-RestMethod`ing!

## **So I can have my cake and eat it too?**

Getting access to the API will make your job dramatically easier and the end product more robust and reliable.

But it isn't free (unless you have a generous provider, a great support contract or negotiate it when procuring the service.)

In every case when I have been through this process we've been changed an hourly fee for two hours of development time. It's also possible that your provider may do the setup for free but then charge per request against the API.

It's pretty easy to justify this cost, assuming your provider doesn't absolutely gouge you, when you consider the amount of time that it'll take you to develop the process that an API eliminates. Also consider the time you'll have to give up when something goes wrong; an extra column causing the script to fail? That's more of your time that would have been freed up with the API being in place.

As an aside if you're having trouble working your hours into a cost/benefit analysis keep in mind your time does have a cost to the business. Chances are it's actually higher than the hourly rate that you're paid. Some managers would happily give you a number you can use in a business case, others may have never thought about it before.

In any case, it can't hurt to at least ask for the API, that much costs nothing.

