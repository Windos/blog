+++
categories = ["PowerShell", "Office 365", "Nugget"]
date = 2018-08-23T22:22:00Z
description = ""
draft = false
thumbnail = "/2018/08/rawpixel-777268-unsplash.jpg"
slug = "orphaned-shared-mailboxes"
summary = "Time for a bit of Exchange flavoured spring cleaning? Let's find some un-needed shared mailboxes!"
tag = ["PowerShell", "Office 365", "Nugget"]
title = "Pre-Migration Housekeeping: Finding Orphaned Shared Mailboxes"

+++


You might be migrating your on-premises Exchange server to the cloud. Maybe you're migrating to a newer version of Exchange. Or maybe you just feel like a bit of a spring clean.

Regardless of why, or if, you want to clean up orphaned shared mailboxes, it is an interesting problem to tackle.

First, in the interest of avoiding scope creep, let's define what we mean by "orphaned shared mailbox":

1. It **is** a shared mailbox, not a user mailbox that you've shared.
2. No users have been given rights, full access or otherwise, to the mailbox.
3. No users have been given rights to the mailbox's calendar.

For this exercise, these three points are enough to consider a mailbox as a candidate to be deleted. I'd probably check to see if they'd been receiving mail, even though no one has been reading it, before doing so. But this is a good starting point.

## **Building out the Script**

I'm just going to be going over the highlights of this script, showing the key points. If you're looking for the complete script, there will be a Gist link at the end.

First, as ever, let's connect to our Exchange server.

```powershell
$ExSplat = @{
    ConfigurationName = 'Microsoft.Exchange'
    ConnectionUri     = 'http://exchange.example.com/powershell'
    Authentication    = 'Kerberos'
}
$ExSession = New-PSSession @ExSplat

# *>$null suppresses the output from Import-PSSession
Import-PSSession -Session $ExSession *>$null

```

Next, we'll collect all of our shared mailboxes. This fulfills our first criteria listed above in the intro.

```powershell
$SharedMailboxes = Get-Mailbox -RecipientTypeDetails SharedMailbox

```

For each mailbox, we'll then get the permissions set on it. We'll also need to rule out system or service accounts that you expect to find on a mailbox.

```powershell
foreach ($SharedMailbox in $SharedMailboxes) {
    $Permissions = $SharedMailbox | Get-MailboxPermission | Where-Object {
        $_.IsInherited -eq $false -and $_.User -like 'EXAMPLE\*' -and $_.User -ne 'EXAMPLE\serviceaccount'
    }

    {#Snipped}
}

```

Assuming you find no permissions on the mailbox, we've met our second criteria. We now need to check the permissions on the calendar folder specifically.

```powershell
if ($null -eq $Permissions) {
    $CalendarIdentitiy = '{0}:\Calendar' -f $SharedMailbox.PrimarySmtpAddress
    $CalendarPermissions = Get-MailboxFolderPermission -Identity $CalendarIdentitiy | Where-Object {
        # Yes, these are redundant, but I've listed them like this for easier expansion.
	    $_.User -notin @('Default', 'Anonymous') -and $_.User -like 'EXAMPLE\*'
    }

    {#Snipped}
}

```

If you've got to this point and found that there are no (expected) permissions on the calendar, then we've found an orphaned shared mailbox so we should output the name and address so that we can then carry out further investigation... or just delete it.

```powershell
if ($null -eq $CalendarPermissions) {
	[PSCustomObject]@{
		Mailbox = $SharedMailbox.Name
		SMTPAddress = $SharedMailbox.PrimarySmtpAddress
	}
}

```

Finally, just in case there is any weird scoping issue, I like to clear out my two permissions variables so that I can be sure that my next iteration will start with both being NULL.

```powershell
Clear-Variable -Name '*Permissions'

```

## **Wrap Up**

Head over to [Gist](https://gist.github.com/Windos/bb663a8b025de03f922a4593de79fcf9) if you want to see a full script.

Obviously, all the normal disclaimers apply. This isn't polished, and we may not have covered all of the things that would constitute "orphaned" in your environment.

Remember, if you're looking for [PowerShell help](https://king.geek.nz/2018/03/20/pshelp-twitter/), you can find it on Twitter using [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23pshelp&src=typd)!

