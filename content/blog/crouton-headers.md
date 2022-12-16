+++
categories = ["PowerShell", "BurntToast"]
date = 2017-05-15T12:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-4.jpg"
slug = "crouton-headers"
summary = "Headers, introduced in the Creators Update, can be used to organize you toasts."
tag = ["PowerShell", "BurntToast"]
title = "Crouton #4 – Getting Your Head Around Headers"

+++


Headers are a new option for toast notifications introduced in the Creators Update for Windows 10. They are used for the organization of toasts, and are supported from version 0.6.0 of the [BurntToast](https://www.powershellgallery.com/packages/BurntToast) PowerShell module and beyond.

## **Creating a Header**

Unlike the [clickable toasts](https://king.geek.nz/2017/05/08/crouton-clickable/) that we discussed in the last [crouton](https://king.geek.nz/tags/index.html#crouton) post, you can use the “basic” `New-BurntToastNotification` function for toasts with headers, however you will need to use the `New-BtHeader` function to create the header object (which you can then re-use for multiple toasts.)

First, create your header:

```powershell
$ToastHeader = New-BTHeader -Id '001' -Title 'Stack Overflow Questions'

```

Of note here, the Id needs to be unique. The title is the display text and can be anything.

Now, create a toast using this header:

```powershell
New-BurntToastNotification -Text 'New Stack Overflow Question!', 'More details!' -Header $ToastHeader

```

## **Check out Those Results**

There are two places you will see these headers manifest, on the toast themselves and in Windows’ Action Center.

On the toast, the header appears, as you would expect, as a headline:

{{< figure src="/2018/05/toast.png" >}}

In the Action Center, a header will appear once and every toast with that header will be neatly sorted underneath.

{{< figure src="/2018/05/actioncenter.png" >}}

## **Bugger! I Messed up My Header!**

Don’t worry, you can easily update the display string. Remember that Id I mentioned above? Behind the scenes, the Id is the glue that holds this together. It tells the Action Center which toast(s) belong under which header(s).

If you create a new header with the same Id as has been used previously, but with a different Title, the display text will be updated which you submit your next toast.

## **So Why Would I Need This?**

Generally speaking, headers give you a second way of organizing your toasts. The first of which being the AppId (which defaults to BurntToast when using the module.)

In general, given that these toasts are created using PowerShell, I consider the AppId the perfect way of defining which script or process generated the toast. Headers give you a way of separating toasts generated via different “channels” within a script or process.

As a practical example of this, consider a script that [checks for new questions on Stack Overflow.](https://king.geek.nz/2017/03/20/crouton-stackwatch/) Unlike the previous example of this, let’s assume that you want to keep track of more than one tag. Headers will allow you to keep these questions organized.

{{< figure src="/2018/05/stackwatcher.png" >}}

That's all there is to headers (for now.) This will likely be the last crouton post for a short while. My next post should be something a little more... normal.

