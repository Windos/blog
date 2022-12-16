+++
categories = ["PowerShell", "BurntToast"]
date = 2018-03-02T15:30:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-20.jpg"
slug = "burnttoast-wix"
summary = "This time around, I'll walk you through how to create your own installer."
tags = ["PowerShell", "BurntToast"]
title = "DIY AppId and WiX, for Tasty Toast"

+++


It has certainly taken a lot longer to get this post out than I'd hoped. Better late than never, I guess?

My [last post](https://king.geek.nz/2018/02/04/burnttoast-appid-installer/) showed off the results I had using the [WiX Toolset](http://wixtoolset.org/) to create a Start Menu shortcut with the necessary metadata embedded to get toasts working properly in newer versions of Windows 10.

This time around, I'll walk you through how to create your own installer.

## **Get WiX**

Given we'll be using it very soon, the first thing you should do is grab the latest stable release of the [WiX Toolset](http://wixtoolset.org/releases/) (they'll link you off to the GitHub repo, download the exe file).

You'll also want to ensure that WiX's command line tools are in your ["Path" environment variable](https://msdn.microsoft.com/en-us/library/gg513936.aspx).

## **Get XML**

Technically it's a "**.wxs**" file, but the content of it is XML.

The **.wxs** file is what defines everything WiX is about to do for us. It's where we're going to do a bit of work, and the rest of this process afterwards is pretty straight forward.

You can see the file used for the BurntToast installer [on GitHub](https://github.com/Windos/BurntToast/blob/master/Installer/src/BurntToast.wxs). I've templated it to an extent, and will include it all here, but you can find a [Gist](https://gist.github.com/Windos/f01ff665adbd5bc46fcb9abcba22490c) of it too.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
    <Product Id="*" UpgradeCode="__GUID1__" Version="1.0.0.0" Language="1033" Name="__NAME__" Manufacturer="__MANUFACTURER__">
        <Package InstallerVersion="300" Compressed="yes"/>
        <Media Id="1" Cabinet="__NAME__.cab" EmbedCab="yes" />

        <Directory Id="TARGETDIR" Name="SourceDir">
            <Directory Id="ProgramFilesFolder">
                <Directory Id="APPLICATIONROOTDIRECTORY" Name="__NAME__"/>
            </Directory>
            <Directory Id="ProgramMenuFolder">
                <Directory Id="ApplicationProgramsFolder" Name="__NAME__"/>
            </Directory>
        </Directory>

        <DirectoryRef Id="APPLICATIONROOTDIRECTORY">
            <Component Id="__NAME__.png" Guid="__GUID2__">
                <File Id="__NAME__.png" Source="__NAME__.png" KeyPath="yes" Checksum="yes"/>
            </Component>
        </DirectoryRef>

        <Icon Id="__NAME__.ico" SourceFile="__NAME__.ico" />
        <DirectoryRef Id="ApplicationProgramsFolder">
            <Component Id="ApplicationShortcut" Guid="__GUID3__">
                <Shortcut Id="ApplicationStartMenuShortcut"
                    Name="__NAME__"
                    Description="Shortcut that enables Toast Notifications"
                    Target="[#__NAME__.png]"
                    WorkingDirectory="APPLICATIONROOTDIRECTORY"
                    Icon="__NAME__.ico">
                    <ShortcutProperty Key="System.AppUserModel.ID" Value="__MANUFACTURER__!__NAME__" />
                    <ShortcutProperty Key="System.AppUserModel.ToastActivatorCLSID" Value="{__GUID4__}"></ShortcutProperty>
		        </Shortcut>
                <RemoveFolder Id="ApplicationProgramsFolder" On="uninstall"/>

                <RegistryValue Root="HKCU" Key="Software\Microsoft\__NAME__" Name="installed" Type="integer" Value="1" KeyPath="yes"/>
           </Component>
        </DirectoryRef>

        <Feature Id="MainApplication" Title="Main Application" Level="1">
            <ComponentRef Id="__NAME__.png" />
            <ComponentRef Id="ApplicationShortcut" />
        </Feature>
    </Product>
</Wix>

```

You'll notice four GUID values in there, they need to be unique, but you don't need to put much thought at all into them. Just run `New-Guid` four times and use the output to replace:

* __GUID1__ - Line 3
* __GUID2__ - Line 18
* __GUID3__ - Line 26
* __GUID4__ - Line 24

Next, replace all instances of __NAME__ with the name you want associated your AppId. This will be visible on your toasts and in the Action Center.

Finally replace all instances of __MANUFACTURER__ with… something. I used my blog URI (king.geek.nz). This will form part of the AppId but won't be visible to end users, unless they go looking through the Program Files directory.

## **Get Visual**

Before we get WiX to do its thing, we need two images.

One is going to be placed in Program File and will be the target of our Start Menu shortcut. If someone happens to click our shortcut, the picture will be opened.

The other image is an icon (**.ico**) and will be shown in the Action Center. Transparent backgrounds tend to work best in my experience and for me I already had an icon ready to go for BurntToast. You may just end up converting the image I mentioned above.

Put these images in the same directory as your **.wxs** file and give them the same name as what you used to replace __NAME__ in the template. If your first image is not a **.png**, you'll have to update that in the **.wxs** file too.

## **Light the Candle**

Time to put WiX to work! Fire up a terminal (CMD or PowerShell), navigate to the directory into which you've saved your files and run the following commands. Remember to change the example file names to match what you've actually used.

```
candle.exe ToastExample.wxs
light.exe -ext WixUIExtension ToastExample.wixobj

```

Note that the file extension changed between the two commands. The result will be a **.msi** installer

## **Run the Installer**

Now run your installer and find that you've got a new item in your Start Menu. Double check the AppId by running: `Get-StartApps`

If your aim was to use this for toasts via the [BurntToast module](https://www.powershellgallery.com/packages/BurntToast), pop that AppId into the module's config.json file and you're set!

## **Did I Miss Anything?**

If I missed anything you were hoping to know, glossed over anything too fast, or just didn't make any sense, please do let me know via a comment or on [Twitter](https://twitter.com/WindosNZ).

