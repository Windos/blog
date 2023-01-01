+++
categories = ["PowerShell", "BurntToast"]
date = 2020-07-28T14:05:26Z
description = ""
draft = false
thumbnail = "/2020/07/joyce-mccown-LAEPsbXOhIM-unsplash.jpg"
images = ["/2020/07/joyce-mccown-LAEPsbXOhIM-unsplash.jpg"]
slug = "anatomy-of-toast"
summary = "BurntToast just turned five, and I've only now realized that I've never written about how all the module's \"advanced\" functions work together to create a toast notification."
tag = ["PowerShell", "BurntToast"]
title = "The Anatomy of Toast"
aliases = [
    "/2020/07/29/anatomy-of-toast/amp/",
    "/2020/07/29/anatomy-of-toast/",
    "/2020/07/29/anatomy-of-toast"
]
+++


Last week, [Jeff Hicks](https://twitter.com/JeffHicks) asked a [question](https://github.com/Windos/BurntToast/discussions/107) on the [BurntToast repo](https://github.com/Windos/BurntToast) that made me realize I've got a lot to work to do documenting the module. I mean, I already _knew_ this, but it was the first time someone had said "I'm trying to figure this out using only the built-in help, and it's not working!"

One of the main things this highlighted, and a good place to start, was that I've never written about the how the module's "advanced" functions map to the structure of the resulting XML that Windows uses to display toast notifications. Nor how they all interact in their seemingly random order.

## Wait, XML?

Right, let's start at the beginning—or rather the end—of the toast notification construction journey.

The layout and content of a toast notification, ignoring [data bindings](https://docs.microsoft.com/en-us/windows/uwp/design/shell/tiles-and-notifications/toast-progress-bar#using-data-binding-to-update-a-toast) (which need more documentation too) and other metadata, is defined in an XML document. For a default notification created with BurntToast, that XML is:

```xml
<?xml version="1.0" encoding="utf-8"?>
<toast>
  <visual>
    <binding template="ToastGeneric">
      <text>Default Notification</text>
      <image src="" placement="appLogoOverride" hint-crop="circle" />
    </binding>
  </visual>
  <audio src="ms-winsoundevent:Notification.Default" />
</toast>
```

If you knew the [XML schema](https://docs.microsoft.com/en-us/windows/uwp/design/shell/tiles-and-notifications/toast-xml-schema) well enough, you could work directly with it.

<p class="note">Up until the release of BurntToast <a target="_blank" href="https://www.powershellgallery.com/packages/BurntToast/0.5.0">v0.5.0</a> in 2016, the module was entirely based on direct XML manipulation.<br /><br />
I'm <b>so</b> glad those days are in the past.</p>

But no one wants to work directly with XML (and if _you_ do, we can't be friends!)

The rest of this post is going to focus on the individual elements of a toast notification's XML document and how BurntToast works to, hopefully, make building it easier.

If you happen to want to forge your own path, without dealing with raw XML, check out the [Microsoft Community Toolkit](https://docs.microsoft.com/en-us/windows/communitytoolkit/getting-started). This is what BurntToast uses, so that I too don't have to spend my life seeing XML!

## A Hefty Example

In order to have something meaty to bite into, let's start with a more complex example.

<p class="note">This example is inspired by the Restaurant.xml demo in the <a target="_blank" href="https://www.microsoft.com/store/productId/9NBLGGH5XSL1">Notifications Visualizer</a> app. It's a great tool for testing toast notifications and is available from the Microsoft Store.</p>

This is the toast that we'll be dissecting today:

{{< figure src="/2020/07/ToastExample.png" >}}

And the XML behind this is:

```xml
<?xml version="1.0" encoding="utf-8"?>
<toast>
  <visual>
    <binding template="ToastGeneric">
	  <text>Upcoming Reservation</text>
	  <text>You have a booking at Wildfire tonight, make sure you know how to get there!</text>
	  <image src="C:\Demos\Map.png" hint-removeMargin="false" />
	  <image src="C:\Demos\WildFire.jpg" placement="hero" />
	  <image src="C:\Demos\ChefIcon.png" placement="appLogoOverride" hint-crop="circle" />
	</binding>
  </visual>
  <actions>
    <action content="Google Maps" arguments="https://www.google.com/maps/SNIP" activationType="protocol" />
	<action content="Bing Maps" arguments="https://www.bing.com/maps?SNIP" activationType="protocol" />
  </actions>
</toast>
```

But how the heck did we get there?

## Growing All Organic Toast

When creating toast notifications using BurntToast's advanced notifications, I begin with any images and text that'll be displayed on it. Let's start with the images.

```powershell
$AppLogo = New-BTImage -Source 'C:\Demos\ChefIcon.png' -AppLogoOverride -Crop Circle
$HeroImage = New-BTImage -Source 'C:\Demos\WildFire.jpg' -HeroImage
$MapImage = New-BTImage -Source 'C:\Demos\Map.png'
```

There are three different types images here.

* `$AppLogo` is the icon that is displayed to the left of the main content on your toast. It is otherwise known as the "App Logo Override", as it replaces the logo that would normally be associated with the application popping the notification (PowerShell in this case.)
* `$HeroImage` is the large image displayed across the very top of the notification, above all other content and stretching from edge to edge.
* `$MapImage` is a normal image which displays under any text.

Text is next. There's a little less to worry about with text, as you're just specifying the string to display.

```powershell
$TextHeading = New-BTText -Text 'Upcoming Reservation'
$TextBody = New-BTText -Text 'You have a booking at Wildfire tonight, make sure you know how to get there!'
```

But then, what do we do with all of these objects that we're creating? We "bind" then into a "binding" object.

```powershell
$Binding = New-BTBinding -Children $TextHeading, $TextBody, $MapImage -AppLogoOverride $AppLogo -HeroImage $HeroImage
```

The key thing here is the order in which you add your text objects. The first one supplied is considered to be the heading and will display first and bold. There's no other way to designate this, other than the order in which they're provided.

This maps through to our XML as shown in this splatter of arrows.

{{< figure src="/2020/07/image-16.png" >}}

What next? We have our binding element; this now needs to go under a visual element. This is a touch boring in our use case today, as we're not using any features enabled through the visual element, so we'll just assign our binding to it.

```powershell
$Visual = New-BTVisual -BindingGeneric $Binding
```

Looking one more layer up in our XML we can see that the next (and final) element is the toast element, but we're not ready for that yet. You'll notice that there's another element we need to prepare first.

{{< figure src="/2020/07/image-17.png" >}}

This last section is our buttons. In the XML schema they're called an action, and are assigned to an actions element. Like your text and images, let's create the buttons first.

```powershell
$GoogleButton = New-BTButton -Content 'Google Maps' -Arguments 'https://www.google.com/maps/place/Wildfire+Auckland/@-36.842,174.76443,15z/data=!4m5!3m4!1s0x0:0xd5bbf397c4772716!8m2!3d-36.842!4d174.76443?hl=en-GB'
$BingButton = New-BTButton -Content 'Bing Maps' -Arguments 'https://www.bing.com/maps?osid=24c08897-bb68-48e5-95a8-fe2f70c5d53e&cp=-36.8421~174.762899&lvl=17&imgid=51ef1b68-a3e7-4fd8-a1e9-d04940a98398&v=2&sV=2&form=S00027'
```

Here, the content is the text to display on the button and the argument is the url to be opened when you click on a given button.

Now add the buttons to an action object.

```powershell
$Actions = New-BTAction -Buttons $GoogleButton, $BingButton
```

Again, the order here is important. The first button specified will be on the left-hand side of the toast followed by any others in order.

{{< figure src="/2020/07/image-18.png" >}}

Now we have all the components we need to wrap up all the content that describes our toast notification.

```powershell
$Content = New-BTContent -Visual $Visual -Actions $Actions
```

It's at this point that you can take your XML document and redistribute it, as described in a [previous post](__GHOST_URL__/2018/08/21/crouton-8-i-like-your-code-but-i-dont-want-it-on-all-my-computers/). Alternatively, if you want to pop your notification go ahead and submit it.

```powershell
Submit-BTNotification -Content $Content
```

## Anatomy Recap

My original intention was to end with an image of the PowerShell code next to the XML and arrows connecting all the pieces. That ended up looking for too much like spaghetti.

Instead we'll end with the script in its entirety, annotated with how I think through the order of things.

```powershell
# First, create all the component pieces

$AppLogo = New-BTImage -Source 'C:\Demos\ChefIcon.png' -AppLogoOverride -Crop Circle
$HeroImage = New-BTImage -Source 'C:\Demos\WildFire.jpg' -HeroImage
$MapImage = New-BTImage -Source 'C:\Demos\Map.png'

$TextHeading = New-BTText -Text 'Upcoming Reservation'
$TextBody = New-BTText -Text 'You have a booking at Wildfire tonight, make sure you know how to get there!'

# Then bind them together

$Binding = New-BTBinding -Children $TextHeading, $TextBody, $MapImage -AppLogoOverride $AppLogo -HeroImage $HeroImage

# And remember that these components are visual, but not actionable

$Visual = New-BTVisual -BindingGeneric $Binding

# Speaking of actionable, we're using actions right?

$GoogleButton = New-BTButton -Content 'Google Maps' -Arguments 'https://www.google.com/maps/SNIP'
$BingButton = New-BTButton -Content 'Bing Maps' -Arguments 'https://www.bing.com/maps?SNIP'

# Don't forget that an action by itself is useless, even a single button needs to become plural

$Actions = New-BTAction -Buttons $GoogleButton, $BingButton

# Now all of the content is together...

$Content = New-BTContent -Visual $Visual -Actions $Actions

# We can submit it to the Operating System

Submit-BTNotification -Content $Content
```

## Wrap Up

I hope all of that made sense. I realize there's a lot to BurntToast, and toast notifications in general, that need more and better documentation.

This is going to be a big focus of mine over the coming months, but it's hard to know what's needed when you're so close to the topic. Getting feedback in issues and discussions on [GitHub](https://github.com/Windos/BurntToast) is a great way of steering where attention should be directed.

If you've got any specific requests for documentation that should be accelerated, please do get in touch!

## Credit

You can get all images and code from this posts example [here](https://1drv.ms/u/s!AmsZe_r-94-XgZdKsmCOYgHFx4Nn0A?e=nO8KoH).

* Hero image by [Joyce McCown](https://unsplash.com/@moonshadowpress?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/anatomy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
* WildFire.jpg belongs to [Wildfire Churrascaria](https://www.wildfirerestaurant.co.nz/)
* ChefIcon.png made by [xnimrodx](https://www.flaticon.com/free-icon/location_3232769?term=chef%20hat&page=1&position=27) from  [www.flaticon.com](https://www.flaticon.com/)

