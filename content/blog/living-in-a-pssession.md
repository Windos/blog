+++
categories = ["PowerShell", "Nugget"]
date = 2020-02-28T20:04:08Z
description = ""
draft = false
thumbnail = "/2020/02/giu-vicente-FMArg2k3qOU-unsplash.jpg"
slug = "living-in-a-pssession"
summary = "PowerShell remoting is a truly powerful tool, but sometimes your scripts need a little help finding their way. How can you tell when you're in a PSSession and what info is available about the session itself?"
tags = ["PowerShell", "Nugget"]
title = "It's Likely We're Living in a PSSession"

+++


A [question](https://techcommunity.microsoft.com/t5/windows-powershell/invoke-command-on-multiple-sessions-accessing-session-specific/m-p/1196265) came up on the [PowerShell Microsoft Tech Community](https://techcommunity.microsoft.com/t5/powershell/ct-p/WindowsPowerShell) a couple of days ago that got me thinking.

How can we tell if our PowerShell code is running in a PSSession, and what info can we find about the connection itself.

The question itself was based around the fact that the remote sessions had access to a hash table with settings relevant to specific servers and this had to be queried by name.

The initial attempt involved to using `$env:COMPUTERNAME`, but there's a very real possibility that that won't match what was specified in the hash table and by extension the name used for the remote connection itself.

For example consider three different options you could use to identify a given server:

* Hostname: server1
* Fully Qualified Domain Name: server1.example.com
* IP Address: 192.168.10.50

So... what can we do?

## The Magic of Automatic Variables

It turns out that there is an automatic variable that only exists when you're inside a PSSession: [$PSSenderInfo](https://docs.microsoft.com/en-nz/powershell/module/microsoft.powershell.core/about/about_automatic_variables?view=powershell-5.1#pssenderinfo)

It contains a lot of information about the originator of the remote connection, including of all things the time zone of the source computer.

{{< figure src="/2020/02/image-6.png" caption="Example output from my lab" >}}

So in order to answer my first question, if the `$PSSenderInfo` variable exists you can be sure that you're in a PSSession.

As for the answer to the Tech Community question, the ConnectionString is my go to property. The connection string's host component mirrors the computer name specified when creating the PSSession.

It would be possible to do some string manipulation to isolate just the host from the entire string... but that is a lot of work.

Instead we'll turn it into a [System.Uri](https://docs.microsoft.com/en-us/dotnet/api/system.uri?view=netframework-4.8) object and make the system do the work for us:

```powershell
([Uri] $PSSenderInfo.ConnectionString).Host
```

```output
zannah
```

Now we know how the remote computer was identified on the originating computer, meaning you can create remote sessions in bulk and take programmatic actions based on this info.

I'm interested in knowing what other uses people may find for the info found inside this automatic variable, let me know in the comments or over on [Twitter](https://twitter.com/WindosNZ).

## Credit

Hero image by [Giu Vicente](https://unsplash.com/@giuvicente?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/virtual-reality?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

