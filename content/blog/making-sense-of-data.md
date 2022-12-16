+++
categories = ["PowerShell"]
date = 2019-12-10T20:39:17Z
description = ""
draft = false
thumbnail = "/2019/12/josh-riemer-flRN6KYpl1A-unsplash.jpg"
slug = "making-sense-of-data"
summary = "\"There's way too much information to decode the Matrix. You get used to it, though. Your brain does the translating. I don't even see the code.\""
tag = ["PowerShell"]
title = "Making Sense of Data: The Matrix"

+++


PowerShell is a, well, powerhouse when it comes to collecting data. Sometimes however it can feel like you're swimming in said data and you can't quite find the right way to understand that data yourself, let alone hand that data off to someone else and it actually be useful.

## Enter the Matrix

One of the methods I've been using lately is a matrix. These matrixes are a spreadsheet with one set of "things" along the top, another set down the left, and some sort of mark in the middle to show when they intersect.

That description was hard to write, I assume it's hard to read too. Instead let's try a picture:

{{< figure src="/2019/12/Matrix-Example.png" >}}

In this example, each row is a user from Active Directory. The first couple of columns are info about these users, specifically their user name and which Organizational Unit they're in. Most columns are hidden as they didn't have much interesting data, but from column D onward it lists every group in the domain.

Where a user is a member of a group, you'll see the the word "Member" in the group's column. This means that at a glance you can see that all user's are "Domain Users" and only the Administrator is a "Domain Admin. Vincent is the sole IT person and for whatever reason Melissa isn't in and group other than the default.

This sort of layout has helped with Role Based Access Control projects, as it allows you to quickly spot patterns of group memberships between different people, teams, and departments.

It's also been used for auditing everything from remote desktop access to web proxy policy groups.

A disclaimer before we get into any code: we aren't going to be outputting an Excel spreadsheet with the fancy angled headings. Instead you'll end up with a CSV file and the formatting will be up to you.  _One day I'll figure out how to do that with the [ImportExcel](https://www.powershellgallery.com/packages/ImportExcel) module and amend this script._

## The Code

The first thing we need to do in order to generate this group membership matrix is to get our users and all groups from Active Directory.

```powershell
$Users = Get-ADUser -Filter 'Enabled -eq $true'
$GroupNames = (Get-ADGroup -Filter * |
    Where-Object {$_.DistinguishedName -notlike '*CN=Builtin,*'}).Name
```

For the users, I'm only get those that are currently enabled. For the groups, I'm not getting any that live in the Builtin location, and only storing their names.

You can adjust the filters on these as much as you like to match your use case.

Next we'll start looping through each user, and assign the output of this loop to a `$Report` variable. The first step in this loop is to get all of the all of the groups that the user is a member of.

```powershell
$Report = foreach ($User in $Users) {
    $Groups = Get-ADPrincipalGroupMembership -Identity $User.DistinguishedName
```

Now we start building up the properties which will represent each row of our resulting CSV file.

```powershell
    $ReportProp = [Ordered] @{
        'Name'     = $User.DisplayName
        'Username' = $User.SamAccountName
        'OU'       = $User.DistinguishedName -replace '(CN=[\w\s-]+,){1}' , ''
    }
```

These are the three properties we capture for each user regardless of their group memberships.

You'll notice that I'm using a regular expression to remove the user's common name from their distinguished name. This leaves me with the distinguished name of their organizational unit (this could be handy if I want to see that all in the IT OU are also in IT specific groups, for example.)

Next we'll loop through our collection of group names (the domain wide list). If the user is in the group we're testing, we add 'Member' to that property and if not we add an empty string.

```powershell
    foreach ($Group in $GroupNames) {
        $Present = $Groups | Where-Object {$_.Name -eq $Group}

        if ($Present) {
            $ReportProp[$Group] = 'Member'
        } else {
            $ReportProp[$Group] = ''
        }
    }
```

This brings us to the end of looping through our collection of users. The last thing we need to do is use our properties to create a custom object. This is automatically added to the `$Report` variable.

```powershell
    [PSCustomObject] $ReportProp
}
```

Finally, we have all the information about our users and we need to do something with that info.

For the sake of this post, we'll be exporting this to a CSV file. From there you can do whatever you want to pretty it up, filter it, and otherwise interrogate it to answer your businesses questions.

```powershell
$Report | Export-Csv -Path 'C:\Support\GroupMembershipMatrix.csv' -NoTypeInformation
```

Rather than trying stitch all those example pieces back together, check out [this Gist](https://gist.github.com/Windos/eaaf1298bd30845fb6e6d9423394d4f9) for the complete script.

## Many Methods

As ever, there are so many way to accomplish all tasks. I've been on a bit of an Excel kick lately and figuring out the best way to convey data to others. I wouldn't be surprised if you see some Excel (or specifically ImportExcel) posts in the future.

What's your favorite method for presenting information? Let me know in the comments below or over on [Twitter](https://twitter.com/WindosNZ).

## Credit

Hero image by [Josh Riemer](https://unsplash.com/@joshriemer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

