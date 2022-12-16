+++
categories = ["Meta"]
date = 2018-08-12T22:21:01Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/08/jason-rosewell-60014-unsplash.jpg"
slug = "echo-echo"
summary = "I don't know if this will help anyone else, and it's more for future me, but I hope it helps someone out there!"
tags = ["Meta"]
title = "Echo, Echo: Combine System Sounds with your Microphone for Skype for Business Presentations"

+++


As with a lot of things I blog about, I don't know how many other people have found themselves looking for solutions to this particular problem.

One of my main PowerShell projects is [BurntToast](https://powershellgallery.com/packages/BurntToast), a module for generating toast notifications. A large part of toasts are their sounds and I've been struggling with this when doing demos via Skype for Business.

The first time I realised this, I was in the middle of an "alarm clock" demo and I was making a joke about subjecting the audience to an annoying ringing noise... only to be told in chat that they couldn't hear it.

Oops, yeah. Skype for Business only takes your microphone and ignores the other system sounds on your computer. This makes sense, if it did transmit your system sounds and someone else talked you'd end up with terrible echo and feedback.

But what if you **do** want, or even need, to let others here system sounds?

## **Grab a (Virtual) Audio Cable**

The solution, or at least the one I ended up settling on, is a virtual audio cable. In effect this is an audio device driver which allows you to "plug" sources in to an input and then have your target software listen to the matching output.

I settled on [VB-CABLE](https://www.vb-audio.com/Cable/), which is "donationware" and did it's job amazingly well.

The install is straight forward, and I won't bog you down with screenshots of it. Just download and unzip the archive then run the relevant setup file for your system (as an administrator.)

Once the install is done, reboot your PC. It's a new Windows driver, don't argue about the reboot...

After the reboot, head to "Sound" in the Control Panel.

{{< figure src="__GHOST_URL__/content/images/2018/08/SoundStartMenu.png" >}}

On the playback tab, you should see "CABLE Input". In my case this had automatically become my default playback device. If this didn't happen for you, go ahead and right click it, making it the default for both playback and communication.

{{< figure src="__GHOST_URL__/content/images/2018/08/Default.png" >}}

Now head to the recording tab and open the settings for your microphone. Head to the listen tab and check the "Listen to this device" option and select "CABLE Input" from the drop down list.

{{< figure src="__GHOST_URL__/content/images/2018/08/ListenMic.png" >}}

At this point, you may have noticed that you can't hear your system sounds. If you do want to hear system sounds, open up the properties for "CABLE Output" and change the options on the listen tab to specify your speakers or headphones... just be careful as you could end up with feedback or looped sound if your mic picks up the output.

You can tell that this is working when you talk into your mic and see the level changing for both the microphone itself **AND** CABLE Output, as shown in this GIF:

{{< figure src="__GHOST_URL__/content/images/2018/08/MIC.gif" >}}

Further, if you play sound on your PC but don't talk into your mic, you should see the level changing on just CABLE Output:

{{< figure src="__GHOST_URL__/content/images/2018/08/DesktopSound.gif" >}}

Now we need to configure Skype for Business. In the client, click the icon down the bottom left and choose "Audio Device Settings."

{{< figure src="__GHOST_URL__/content/images/2018/08/SkypeForBusinessAudioDeviceSettings.png" >}}

Choosing "Custom Device" allows you to pick different devices for your speaker and microphone. In this case you want your speaker to be your headset (not desktop speaker, you really don't want this being picked up by your mic.) Now set your microphone to CABLE Output.

{{< figure src="__GHOST_URL__/content/images/2018/08/S4B-CustomDevice.png" >}}

With all of this set, Skype for Business is now accepting your system sound mixed with your microphone as input but it's outputting to your headset, which is separate from system sound in this case.

This means you can hear other people in the call, other people in the call can hear you and any sounds on your PC (in my case, this is annoying toast noises), but the other people don't hear themselves being echoed back at them.

**You** won't be able to hear your own system sounds. I'm sure you could tweak things to still hear them, but for my use case I didn't worry about it. When I was done with the call, I uninstall the virtual audio cable driver and all of my audio settings go back to normal until the next time I find myself in this situation.

The final thing to do is get a friend and do a test call. You're not going to know for sure that this has worked until you've jumped into a meeting and asked someone at the other end if they can hear your "dinging" as you adjust volume up and down.

That's it for this post. It's just as much for me so I remember how to set this up next time I'm demoing BurntToast, but I sincerely hopes it helps someone else.

