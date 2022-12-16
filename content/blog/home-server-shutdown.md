+++
categories = ["PowerShell", "Nugget"]
date = 2016-07-03T12:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/Socket_5.png"
slug = "home-server-shutdown"
summary = "My home server, nestled behind the TV, is an old HP workstation. It's not exactly what I'd call... power efficient."
tag = ["PowerShell", "Nugget"]
title = "Automatically Shutting Down My Home Server, so I Don't Have To"

+++


My home server, nestled behind the TV, is an old HP workstation. It's not exactly what I'd call... power efficient.

In an effort to not consume too much power I try to shut it down every night, but if I wasn't actively using it before bed I would often forget about it.

The boxes main duty is to act as a [Plex](https://www.plex.tv/) server and over time I have come to realise that if I'm not watching something by about 8:30 at night then I won't be using it at all. So why not just let the machine shut itself down if Plex isn't in use?

Luckily, Plex has a nice [API](https://github.com/mjs7231/python-plexapi/wiki/Unofficial-Plex-API-Documentation) which PowerShell can easily consume. Looking down the list, I immediately latched onto /status/sessions which lists all currently playing items. This includes items played through the web, mobile and even to a Chromecast. If you pause what you're watching it still counts as 'playing' so I didn't have to worry about pausing a show only to have the server shut down on me.

There is a very handy count there, labelled MediaContainer which gives you and easy to test count on the number of items playing. This makes it pretty easy to query the API, see if anything is playing and then shutdown if not (and try again in 15 minutes if there is something playing.)

```powershell
$URL = 'http://localhost:32400/status/sessions'

while ($true)
{
	$PlexStatus = Invoke-RestMethod -Uri $URL

	if ($PlexStatus.MediaContainer.Size -eq 0)
	{
		Invoke-Expression -Command 'shutdown -s -t 0'
	}

	Start-Sleep -Seconds 900
}

```

You'll not that I'm invoking `shutdown.exe` rather than the native PowerShell cmdlet `Stop-Computer`. For whatever reason the cmdlet refused to work within a [Scheduled Job](https://blogs.technet.microsoft.com/heyscriptingguy/2014/05/12/introduction-to-powershell-scheduled-jobs/). Them's the breaks, I guess.

The final piece of the solution is to wrap the above PowerShell statement in a script block and register it as one of the previously mentioned Scheduled Job.

```powershell
$JobTrigger = New-JobTrigger -Daily -At '8:30 PM'
$JobOption = New-ScheduledJobOption -RunElevated
Register-ScheduledJob -Name 'PlexShutdown'
					  -Trigger $JobTrigger
					  -ScheduledJobOption $JobOption
					  -ScriptBlock $ScriptBlock

```

n.b. I did this on the server itself, so there is no remoting happening here.

Success! I now no longer need to even think about the server at night, even if I'm watching something.

You can see the full script over on [GitHub](https://github.com/Windos/powershell-depot/blob/master/General/PlexServerShutdown.ps1). I doubt it will ever get edited much in the future, but the latest version of it will always be there.

