+++
categories = ["BurntToast", "PowerShell"]
date = 2019-07-30T23:09:29Z
description = ""
draft = false
thumbnail = "/2019/07/joseph-gonzalez-zcUgjyqEwe8-unsplash.jpg"
slug = "crouton-12"
summary = "Toast notification data bindings have a number of uses. We've already covered updating toasts, now let's teach our notifications a second language."
tag = ["BurntToast", "PowerShell"]
title = "Crouton #12 - Toasting in Many Tongues (Tōhi o nga reo maha)"

+++


[Last time we talked](__GHOST_URL__/2019/07/17/crouton-11/) toast notifications, we covered how you can gracefully update their content. This was achieved through a process called data binding where you specify placeholder "anchors" in your notification in place of the actual text which then maps through to the text to be displayed in a hashtable.

There's another use case for this data binding mechanism which I'm very excited about: localization.

## First, an Apology

I'll be using [Māori](https://en.wikipedia.org/wiki/M%C4%81ori_language) as a non-English language in this post, but I'm ashamed to say that my te reo isn't too sharp. So if I stuff up a phrase, word, or sentence construction - I'm sorry!

I'm leaning on an online [Māori dictionary](https://maoridictionary.co.nz) pretty heavily here, and it's a great resource for looking up the meaning of individual words or short phrases.

## Picking up Another Language

So let's get to the point and teach our notifications to speak another language. We'll start by defining two hashtables, one for each language. If you wanted to support more languages than this, you'd need to set up one hashtable for each (though you should probably consider a different solution if you're trying to support the entire world!)

```powershell
$English = @{
    Header = 'Morning proverb'
    Body   = 'Unlike a canoe rope, a human bond cannot be severed'
}

$Maori = @{
    Header = 'Whakataki i nga ata'
    Body   = 'He hono tangata e kore e motu; ka pa he taura waka e motu'
}
```

With those defined, we can go ahead and create our toast notification specifying the language we want to use against the `-DataBinding` parameter.

_As a reminder, the text elements must be the same as the keys you want to match against in your hashtable(s). That's `Header` and `Body` in this case._

```powershell
New-BurntToastNotification -Text 'Header', 'Body' -DataBinding $English
```

{{< figure src="/2019/07/English.png" caption="Example Toast in English" >}}

```powershell
New-BurntToastNotification -Text 'Header', 'Body' -DataBinding $Maori
```

{{< figure src="/2019/07/Maori.png" caption="Example Toast in Māori" >}}

## Scaling Out

Sure, specifying the language manually works, but sort of defeats the purpose of this post and automation in general. If we're going to be manually selecting a hashtable we might as well just be putting the desired text directly into the function call.

Luckily, we can work smarter.

Start as we did before, with our language specific hashtables.

```powershell
$English = @{
    Header = 'Morning proverb'
    Body   = 'Seek after learning for the sake of your wellbeing'
}

$Maori = @{
    Header = 'Whakataki i nga ata'
    Body   = 'Whāia te mātauranga hei oranga mō koutou'
}
```

For good measure, we'll need a third hashtable that we'll use for [splatting](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_splatting?view=powershell-6) when it comes time to create our notification.

```powershell
$ToastSplat = @{Text = 'Header', 'Body'}
```

Now we can figure out what out "[culture](https://docs.microsoft.com/en-nz/openspecs/windows_protocols/ms-lcid/a9eac961-e77d-41a6-90a5-ce1a8b0cdb9c)" Windows is currently using. We'll store this info in a variable so that it can be re-used.

```powershell
$Culture = Get-Culture
```

On my machine, my culture info looks like this:

```output
LCID             Name             DisplayName
----             ----             -----------
5129             en-NZ            English (New Zealand)
```

If my machine was set to Māori it would look like this:

```output
LCID             Name             DisplayName
----             ----             -----------
1153             mi-NZ            Maori (New Zealand)
```

_There's also a default variable that shortcuts what we just did (_`$PSCulture`_), but I wanted to show the extra metadata available in case it's useful to anyone._

It's true that this example, using only two languages, is simple enough that we could just use a couple of `if` statements to get this job done. Let's not paint ourselves into a corner though, and instead I'll use a `switch` statement which can be easily expanded to accommodate additional languages in the future.

```powershell
switch -Regex ($Culture.Name) {
    '(^en)' {
        $ToastSplat['DataBinding'] = $English
        continue
    }
    '(^mi)' {
        $ToastSplat['DataBinding'] = $Maori
        continue
    }
    default {
        $ToastSplat['DataBinding'] = $English
    }
}
```

That may look a little intimidating, but in short that we're doing here is checking the name of our culture (e.g. en-NZ) and matching it against a Regex expression.

In this example, `^en` will match any culture that starts with and "en" including "en-NZ" and "en-US"

You could add another language to the mix by adding a new expressing anywhere in the list as long as it's above the default. Say you wanted to add French, the expression would be `'(^fr)'`.

As for that default, this is our catch all. If none of the expressions match, we're going to assume English will be the most helpful to the bulk of the audience of this script.

Inside the curly braces, we're adding the appropriate data binding hashtable to our splatting variable. Oh, and the continue statement is there simply to stop future matches from trying to occur. It's not to much of an issue with only two languages, but as you scale this not doing this could cause a small slow down.

And that's it, use your splatting variable to create your new toast after running through the switch statement and the correct language will be output.

```powershell
New-BurntToastNotification @ToastSplat
```

## Wrap Up

Yes, this was a very simple example, but the ground work is there to scale out to any number of languages. You can use the data bindings almost anywhere text is shown to the end user, including buttons, progress bars, and other adaptive elements.

Let me know if you find this feature useful in your line of work.

Heoi anō tāku mō nāianei.

