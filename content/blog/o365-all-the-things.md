+++
categories = ["PowerShell", "Office 365"]
date = 2017-08-03T12:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/thumb-6.jpg"
slug = "o365-all-the-things"
summary = "Now that we've figured out how to connect to each of the Office 365 services, it's time to tie a pretty (PowerShell) blue bow on it."
tags = ["PowerShell", "Office 365"]
title = "Office 365 & PowerShell: Connecting All the Pieces"

+++


Now that we've figured out [how to connect](https://king.geek.nz/2017/07/28/o365-connect/) to each of the Office 365 services, it's time to tie a pretty (PowerShell) blue bow on it. If you just want to skip the writeup and see the result, you can check out the [Gist](https://gist.github.com/Windos/5f96a9425b5b31c23df441035b478c5f). This Gist may get updates in the future.

## **Disclaimer**

The script created in this post is not a complete solution.

It is very much a starting point with no error handling. It is an all or nothing sort of thing, so if your Office 365 tenant does not have Skype for Business Online setup, you will probably get errors.

## **Initial housekeeping**

We know that there are a few requirements we need to meet to establish our connection(s) to Office 365, so we might as well put PowerShell to work. Using [#Requires](https://msdn.microsoft.com/en-us/powershell/reference/5.1/microsoft.powershell.core/about/about_requires) statements allows us to specify that we need PowerShell V3 (for those running Windows 7), and all of the installable modules. If the requirements aren't met, PowerShell will handle letting the user know.

```powershell
#Requires -Version 3.0
#Requires -Modules MSOnline, SkypeOnlineConnector, Microsoft.Online.SharePoint.PowerShell

```

Assuming we don't meet the requirements, I want to have download links for all the relevant installs readily available.

```powershell
<#
    Required downloads

    Microsoft Azure Active Directory Module for Windows PowerShell
        32-bit: http://aka.ms/fohrds
        64-bit: http://aka.ms/siqtee
    Skype for Business Online Connector: http://aka.ms/x3kyib
    SharePoint Online Management Shell: http://aka.ms/f04q5o

    Microsoft Online Services Sign-In Assistant (if Win7): http://aka.ms/vl42dg
#>

```

## **Connect-O365Services**

Now let's create our function for connecting to the various services. Start by defining the function and its parameters.

```powershell
function Connect-O365Services {
    param (
        [Parameter(Mandatory = $true,
                   Position = 0)]
        [ValidateNotNullOrEmpty()]
        [string]
        $Tenant,

        [Parameter(Mandatory = $true,
                   Position = 1)]
        [ValidateNotNullOrEmpty()]
        [System.Management.Automation.PSCredential]
        [System.Management.Automation.Credential()]
        $Credential
    )

```

Tenant is straight forward, it's just be the name that identifies your Office 365 instance. If you're using example.onmicrosoft.com, for example, you'd supply "example".

Credential is a little more interesting. You'll note that there are two possible object types that it'll accept allowing you can provide a fully formed credential object from `Get-Credential` or a username as a string, being prompted for a password at run-time.

We're making PowerShell work for us again by validating that the input for these parameters isn't null (or empty) to save us some error handling.

Next, we'll go through and actually get connected. I won't breakdown each one, as it's more or less identical to what we've [already covered](https://king.geek.nz/2017/07/28/o365-connect/).

```powershell
    # Connect to Office 365
    Connect-MsolService -Credential $Credential

    # Connect to Exchange Online
    $Script:ExSession = New-PSSession -Credential $Credential -ConfigurationName Microsoft.Exchange -ConnectionUri "https://outlook.office365.com/powershell-liveid/" -Authentication "Basic" -AllowRedirection -ErrorAction SilentlyContinue
    if ($Script:ExSession) {
        Import-PSSession $Script:ExSession
    }

    # Connect to SharePoint Online
    Connect-SPOService -Credential $Credential -Url "https://$Tenant-admin.sharepoint.com" -ErrorAction SilentlyContinue

    # Connect to Skype for Business Online
    $Script:S4BSession = New-CSOnlineSession -Credential $Credential -ErrorAction SilentlyContinue
    if ($Script:S4BSession) {
        Import-PSSession $Script:S4BSession
    }

```

The last, and most important, step for this function is to remember the closing curly brace.

```powershell
}

```

Oh, that variable notion of `$Script:Name` is being used here so that the variables are accessible to other functions within this script file… like the disconnect function below.

## **Disconnect-O365Services**

Like a good "tidy kiwi," we'll also be cleaning up the various remote sessions we opened with the previous function.

{{< figure src="__GHOST_URL__/content/images/2018/05/tidykiwi.jpg" >}}

You'll notice that there is no `Disconnect-MsolService` cmdlet. Unlike the other services, there is no actual PS session created, so just disconnect the others and close the PowerShell host.

```powershell
function Disconnect-O365Services {
    # Disconnect from Exchange Online
    if ($Script:ExSession) {
        Remove-PSSession $Script:ExSession -ErrorAction SilentlyContinue
    }

    # Disconnect from SharePoint Online
    Disconnect-SPOService -ErrorAction SilentlyContinue

    # Disconnect from Skype for Business Online
    if ($Script:S4BSession) {
        Remove-PSSession $Script:S4BSession -ErrorAction SilentlyContinue
    }
}

```

## **There's always more to do**

As I said in the disclaimer, this isn't a complete solution. It is a start, and will get the job done.

You could add things to this like checking if a service is provisioned on your tenant before trying to connect to it or checking to see if the credentials you're using even have permission within that service.

Have you created your own script for getting connected to Office 365, or did this post inspire you to make one? If so, send me a link, as I'd love to see your implantation.

