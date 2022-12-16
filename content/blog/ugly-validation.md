+++
categories = ["PowerShell"]
date = 2015-07-22T12:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/3224581771_5917d64f99_b.jpg"
slug = "ugly-validation"
summary = "Figure out what you did wrong and don't do it again. Good luck!"
tag = ["PowerShell"]
title = "Giving ValidateScript a Makeover"

+++


## **I Feel Validated**

One of the awesome things you get when adding the [CmdletBinding attribute](https://technet.microsoft.com/en-us/library/hh847872.aspx) to you functions (you are including CmdletBinding, right?) is the ability to use [Parameter Validation Atributes](https://technet.microsoft.com/en-us/library/Hh847743.aspx). These atributes include nifty options like allowing or disallowing null values, specifying a valid amount of items that an aray parameter can accept, specifying a set of pre-determined valid values.

All of these are awesome, and save you a fair amount of work in manually validating user input before using it within the rest of the function. They are checked before your code even runs and gives the end user nice feed back on what was wrong and what to do to fix it.

```powershell
function Test
{
    [CmdletBinding()]
    Param
    (
        [ValidateSet('John','Jane')]
        [string] $Name
    )
}

```

This test fucntion has a name parameter, and the `ValidateSet` attribute will only allow John or Jane as valid values. If I try to pass in 'Windos', the function doesn't run, and PowerShell lets me know why.

{{< figure src="/2018/05/ValidateSet.PNG" >}}

## **Very Flexible, Yet So Ugly**

I would argue that the most flexible, and possibly useful, validation atribute is `ValidateScript`. It allows you to specify a script which is run against the user's supplied value.  _(Note that the value being validated is assigned to the `$_` variable.)_

Given that you're specifying your own script, you're free to test literally anything.

* The supplied date should be in the future? Done.
* The supplied date should be in the past? Done.
* A supplied path should have two parent directories, should contain the letter 'a' and be hidden? Done.

With this flexibility, however, comes (subjectivly) ugly and less than helpful feedback for the user.

```powershell
function Test-PC
{
    [CmdletBinding()]
    Param
    (
        [ValidateScript({ Test-Connection $_ -BufferSize 16 -Count 1 })]
        [string] $ComputerName
    )
}

```

This function takes a Computer Name, but will only run if Test-Connection runs against the supplied name successfully. If I pass in a name I know doesn't exist the validation fails.

{{< figure src="/2018/05/ValidateScript.PNG" >}}

When I read the resulting message, which just blurts out the full validation script, I see: 'Figure out what you did wrong and don't do it again. Good luck!'

## **A Better Option**

You're in no way limited to using existing cmdlets in your ValidateScript atribute. You can even specify you're own functions.

Whenever I use ValidateScript I always like to create my own `Validate-*` functions, in which I can do as complex a test as I want and also control the error message that is returned to the user. Your function should return `$true` if the parameter value is valid, and throw an error message if it is not valid.

```powershell
function Validate-ComputerName
{
    Param
    (
        [string] $ComputerName
    )

    if (Test-Connection $ComputerName -BufferSize 16 -Count 1)
    {
        $true
    }
    else
    {
        throw "The computer $ComputerName is either offline or does not " +
            'exist. Please check that the value is correct and try again.'
    }
}

function Test-PC
{
    [CmdletBinding()]
    Param
    (
        [ValidateScript({ Validate-ComputerName $_ })]
        [string] $ComputerName
    )
}

```

{{< figure src="/2018/05/Custom.PNG" >}}

As you can see, the text we specified after the `throw` statement is presented to the user and is a lot easier to understand what the issue is and what needs to be done to correct the situation.

## **Closing Notes/ TL;DR**

* Use the CmdletBinding atribute
* Parameter Validation Atributes save you from having to manually test user input
* ValidateScript is the most flexible atribute
* But man is that output ugly
* Validation functions, which throw on invalid values allow you to present much nicer feedback to the user.

