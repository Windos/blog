+++
categories = ["PowerShell", "BurntToast"]
date = 2020-08-07T17:54:01Z
description = ""
draft = false
thumbnail = "/2020/08/kate-hliznitsova-ZIGxIQ4gzBU-unsplash.jpg"
images = ["/2020/08/kate-hliznitsova-ZIGxIQ4gzBU-unsplash.jpg"]
slug = "actionable"
summary = "I don't know how to express a happy dance with text. If I could that's what would be here because I've finally removed a thorn from my side that's been there for five years."
tag = ["PowerShell", "BurntToast"]
title = "Actionable Toast Notifications, Finally Cracked Them!"
+++


I've been living and breathing toast notifications while building the [BurntToast](https://www.powershellgallery.com/packages/BurntToast) module for the past five years. Through this time, they've been great for delivering information... or annoying users.

But there has always been a lingering missing piece: "Can I _do_ something with these notifications?"

Sure, you could always put buttons on your toast notifications. But they were limited to snoozing and dismissing the notification itself or opening files or websites in their default applications.

Again, this is limiting us to just delivering information. What if you wanted to pop a notification telling the user they needed to reboot and providing them with a button which would carry out that reboot?

## Actions Via Protocol Handlers

One way of enabling actionable toast notifications is the use of [protocol handlers](https://docs.microsoft.com/en-us/windows/win32/search/-search-3x-wds-extidx-prot-implementing). These are a very important and valid method, but I feel like the barrier to entry here is a bit higher than it should be.

I will be documenting this method more in the future and writing a helper module to make using them easier. For now, [Adam Driscoll](https://twitter.com/adamdriscoll) did an excellent job of demonstrating their use with BurntToast during one of his Module Monday videos.

<iframe width="480" height="270" src="https://www.youtube.com/embed/TwZjr66yfc8?feature=oembed" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

These allow you to trigger scripts and take actions based on clicking directly on a toast notification or custom buttons that've been added to it.

But what about selection boxes? What if you want to take an action if the user dismissed the notification?

## Object Events

If you've been following my quest for actionable notifications over the years, you may have seen a screenshot like this.

{{< figure src="/2020/08/image.png" caption="Seeing this still irks me despite knowing where this post is going." >}}

Toasts are a Windows RT (WinRT) feature, and their associated events are WinRT events. PowerShell cannot subscribe to those events... until now (sort of, but we're not peaking too far behind the curtains today.)

Before we dive into this, I need to dash some hopes that may be building. What follows is only possible using [BurntToast v0.7.2](https://github.com/Windos/BurntToast/releases/tag/v0.7.2) with [PowerShell](https://github.com/PowerShell/PowerShell) 7.1 (from Preview 4 and above).

But PowerShell 7 is great and once you've made the leap to that then following the updates is relatively painless.

Now to build hopes back up, assuming you're running the required versions, here's a taste of what you can now do:

{{< figure src="/2020/08/2020-08-06_23-03-10.gif" >}}

That right there is a working selection box. A second toast is triggered by the activation (button click) of the first toast, based on the selected item.

It may seem like such a simple thing, but this unlocks **limitless potential**.

That may sound corny, but it's true. If you can think of some action you want to take based on a toast, you can do it now. Whether it be as simple as acting based on whether a toast of clicked or not, or what a user selected from a selection list, or the text they entered into a text input box.

## Do It Yourself

I'm going to blast through a lot of the cmdlets being used here, as their use is described in [The Anatomy of Toast](__GHOST_URL__/2020/07/29/anatomy-of-toast/), and mainly focus on the new stuff.

Start with the text, emails, and audio for your toast, then put those into both a binding and a visual element.

```powershell
$Text1 = New-BTText -Content 'New Batch of GIFs'
$ImagePath = 'C:\Program Files\PowerShell\7-preview\assets\Powershell_av_colors.ico'
$Image1 = New-BTImage -Source $ImagePath -AppLogoOverride -Crop None
$Audio1 = New-BTAudio -Source 'ms-winsoundevent:Notification.Default'

$Binding1 = New-BTBinding -Children $Text1 -AppLogoOverride $Image1
$Visual1 = New-BTVisual -BindingGeneric $Binding1
```

Now for something new, create each of the items for your dropdown selection box. Do this with the `New-BTSelectionBoxItem` cmdlet, supplying a unique ID for each. The content parameter is what will be shown to the user.

With all of the items created, add them to an "input" object with the `New-BTInput` cmdlet. Providing an ID is mandatory, but the title is optional and will be displayed above the selection box if specified.

You specify each of your selection box items against the item parameter, the order you provide them will be the order that they're displayed. Finally, you can optionally supply the ID of the item you want selected by default.

```powershell
$Item1 = New-BTSelectionBoxItem -Id 'Item1' -Content 'Picard Facepalm'
$Item2 = New-BTSelectionBoxItem -Id 'Item2' -Content 'Get It Girl'
$Item3 = New-BTSelectionBoxItem -Id 'Item3' -Content 'Seinfeld - Oh, right right right'
$Item4 = New-BTSelectionBoxItem -Id 'Item4' -Content 'Bob Ross?'

$InputSplat = @{
    Id    = 'Selection001'
    Title = 'Select a GIF to view now'
    DefaultSelectionBoxItemId = 'Item1'
    Items = $Item1, $Item2, $Item3, $Item4
}
$BTInput = New-BTInput @InputSplat
```

Next we need a pair of buttons. One is a standard dismiss button and the other is a submit button. What makes it a "submit" button? The fact that it shows Submit on the button itself. The argument is required, but in this case it doesn't matter what it is set to.

With our two buttons created, we add them to an "action" object alongside the previously created input object.

And then everything we've created so far is bundled up into a "content" object.

```powershell
$Submit = New-BTButton -Content 'Submit' -Arguments 'SubmitButton' -ActivationType Foreground
$Dismiss = New-BTButton -Dismiss

$Actions1 = New-BTAction -Inputs $Input1 -Buttons $Submit, $Dismiss

$Content1 = New-BTContent -Visual $Visual1 -Audio $Audio1 -Actions $Actions1
```

Because things are in limbo at the moment, we have to take some manual action to pop our notification. This is because we need to get access to the raw ToastNotification object which we normally don't interact with if using `Submit-BTNotification`.

This means we have to manually "clean up" the XML that describes our toast before creating the toast object itself with it.

```powershell
$CleanContent = $Content1.GetContent().Replace('<text>{', '<text>')
$CleanContent = $CleanContent.Replace('}</text>', '</text>')
$CleanContent = $CleanContent.Replace('="{', '="')
$CleanContent = $CleanContent.Replace('}" ', '" ')

$AppId = 'Microsoft.AutoGenerated.{CD9EFC53-1ACE-EA00-530A-3EC179D1971C}'

$ToastXml = [Windows.Data.Xml.Dom.XmlDocument]::new()

$ToastXml.LoadXml($CleanContent)

$Toast = [Windows.UI.Notifications.ToastNotification]::new($ToastXml)
```

Now, armed with a toast object we can get to work on making it actionable. You can see what events are available as follows:

```powershell
$Toast | Get-Member -MemberType Event
```

```output
   TypeName: Windows.UI.Notifications.ToastNotification

Name      MemberType Definition
----      ---------- ----------
Activated Event      Windows.Foundation.TypedEventHandler`2[Windows.UI.Notifications.ToastNotification,System…
Dismissed Event      Windows.Foundation.TypedEventHandler`2[Windows.UI.Notifications.ToastNotification,Window…
Failed    Event      Windows.Foundation.TypedEventHandler`2[Windows.UI.Notifications.ToastNotification,Window…
```

Today we're interested in the Activated event, but you may want to experiment with Dismissed and Failed events depending on what you're wanting to achieve.

We define what will happen when a given event happens using `Register-ObjectEvent`.

For this demo we're creating a whole new toast when the first toast is activated. I'm not going to talk that through section by section. You'll notice a switch statment at the top that drills into an `$Event` variable, this is created automatically and gives you information about how the event was triggered. Specifically here we're checking to see which item in our selection box was selected when the toast was activated.

```powershell
Register-ObjectEvent -InputObject $Toast -EventName Activated -Action {
    switch ($Event.SourceArgs[1].UserInput.value) {
        Item1 {
            $Text1 = New-BTText -Content 'GIF: Picard Facepalm'
            $ImagePath = 'C:\temp\gifs\facepalm.gif'
        }
        Item2 {
            $Text1 = New-BTText -Content 'GIF: Get It Girl'
            $ImagePath = 'C:\temp\gifs\get-it-girl.gif'
        }
        Item3 {
            $Text1 = New-BTText -Content 'GIF: Seinfeld - Oh, right right right'
            $ImagePath = 'C:\temp\gifs\oh-right.gif'
        }
        Item4 {
            $Text1 = New-BTText -Content 'GIF: Bob Ross?'
            $ImagePath = 'C:\temp\gifs\bob-ross.gif'
        }
    }

    $Image1 = New-BTImage -Source $ImagePath -HeroImage

    $ImagePath2 = 'C:\Program Files\PowerShell\7-preview\assets\Powershell_av_colors.ico'
    $Image2 = New-BTImage -Source $ImagePath2 -AppLogoOverride -Crop None

    $Audio1 = New-BTAudio -Source 'ms-winsoundevent:Notification.Default'

    $Binding1 = New-BTBinding -Children $Text1 -HeroImage $Image1 -AppLogoOverride $Image2
    $Visual1 = New-BTVisual -BindingGeneric $Binding1

    $Content1 = New-BTContent -Visual $Visual1 -Audio $Audio1 -Duration Long

    $CleanContent = $Content1.GetContent().Replace('<text>{', '<text>')
    $CleanContent = $CleanContent.Replace('}</text>', '</text>')
    $CleanContent = $CleanContent.Replace('="{', '="')
    $CleanContent = $CleanContent.Replace('}" ', '" ')


    $AppId = 'Microsoft.AutoGenerated.{CD9EFC53-1ACE-EA00-530A-3EC179D1971C}'

    $ToastXml = [Windows.Data.Xml.Dom.XmlDocument]::new()

    $ToastXml.LoadXml($CleanContent)

    $Toast = [Windows.UI.Notifications.ToastNotification]::new($ToastXml)

    [Windows.UI.Notifications.ToastNotificationManager]::CreateToastNotifier($AppId).Show($Toast)
}
```

Finally, we pop our first toast. This will look familiar if you read through the event registration above as that too ends with popping the second toast.

```powershell
[Windows.UI.Notifications.ToastNotificationManager]::CreateToastNotifier($AppId).Show($Toast)
```

And with that, your toast will pop up and you'll be able to select what shows on the second. Do note that you'll have to supply your own gifs and point to them inside the event's switch statement.

## Exploring the $Event Variable

In the previous example I pinpointed the info that we needed from the $Event variable. What do you do if you want to poke around that variable yourself to see what it contains?

I like to register an event that just assigns that variable to a globally scoped variable which I can use from anywhere.

```powershell
Register-ObjectEvent -InputObject $Toast -EventName Activated -Action {
    $global:Testing = $Event
}
```

You can now interrogate `$Testing` at your leisure, though to narrow things down a bit the interesting stuff is all under the SourceArgs property.

In all of my experiments so far, `$Testing.SourceArgs` has contained two objects. The first is the toast object, allowing you to inspect the XML and other information about the toast. The second object, however, contains any arguments (from the button pressed) and user input (from text boxes or selection boxes.)

This is where you can interrogate any input you may need to make decisions on what specific action to take based on feedback from the user.

## Next Steps and Unknowns

I'm currently planning out what needs to be done for the next release of BurntToast. Regarding making actionable toasts easier to work with I'm planning to add some way of using specifying code to trigger on events via both `New-BurntToastNotification` and `Submit-BTNotification`.

This will probably take the form of ActivatedEvent and DismissedEvent parameters that accept a script block, though I'm open to suggestions on that front. I'm wondering about the failed event too; this may only be exposed against `Submit-BTNotification`.

I still have a few unknowns too. This has so far only been tested in a session on my local computer and I'm not sure what the interaction would be regarding remote sessions. Remembering the remote endpoint would need to be on PowerShell 7.1, it will depend on how long the session stays open. If it closes immediately after the toast is sent, then the event registration will be lost, and nothing will trigger.

Regardless, this is a very exciting time in the work of toast notifications. All those use cases I've been shelving for half a decade can be revisited and there's more testing needed than there is time in the day.

If you're interested in seeing some of these use cases in action, make sure you tune into [PSPowerHour](https://www.twitch.tv/devopsorg) towards the end of September (keep an eye on [Twitter](https://twitter.com/WindosNZ) for details) where I'll be presenting "[Croque Monsieur: Step up Your Toast Notification Game](https://sessionize.com/s/windos/croque_monsieur_step_up_your_toast_/34007)".

If you've got any questions about these actionable toasts that might need a bit of depth, please do feel free to open a new [discussion over on GitHub](https://github.com/Windos/BurntToast/discussions).

## Credit

Hero image by [Kate Hliznitsova](https://unsplash.com/@kate_gliz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/cracked-egg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

