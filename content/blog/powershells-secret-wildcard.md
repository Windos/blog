+++
categories = ["PowerShell", "Nugget"]
date = 2020-01-08T21:07:33Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2020/01/stefan-steinbauer-HK8IoD-5zpg-unsplash.jpg"
slug = "powershells-secret-wildcard"
summary = "It's funny how you can be a daily PowerShell user for years and completely miss something about a feature you regularly use... such as the \"like\" operators accepting more than two different wildcards."
tags = ["PowerShell", "Nugget"]
title = "PowerShell's Secret Wildcard"

+++


A couple of days ago I found myself needing to match text entries between two systems. The entries had a bunch of meta data, but the only thing I could use for the match was the text itself.

In one system, the text might read:

```
"This text is super important."
```

The second system had that same text, but with a little extra added to it but not in a predictable format:

```
"Urgent: This text is super important."
```

Simple enough, right? Just throw some wildcards around the text from the first system and check that what's coming from the second matches:

```powershell
$One = "This text is super important."
$Two = "Urgent: This text is super important."

$Two -like "*$One*"
```

Sorted! Time to process a few thousand records.

## Trouble

It wasn't long until I started seeing red errors flashing up in my console.

```
The specified wildcard character pattern is not valid
```

You... what?

The only wildcard I'm supplying is the asterisk (`*`), which matches everything including nothing! The only other wildcard that could be showing up in my input is the question mark.

Those are the only two wildcards that the like operator works with, and the worst that might happen if I have a few extra of those sprinkled in is accidentally being too promiscuous when matching. It certainly shouldn't fail!

Let's be doubly sure and check the [documentation](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_comparison_operators).

> The like operators (**-like** and **-notlike**) find elements that match or do not match a specified pattern using wildcard expressions.

Ok, but what is a wildcard expression?

> **-like**Description: Match using the wildcard character (*).

That's not all that helpful, it doesn't even mention the question mark! Luckily there's [documentation on wildcards](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_wildcards).

```
Wildcard	Description								Example		Match
*			Match zero or more characters			a*			aA, ag, Apple
?			Match one character in that position	?n			an, in, on
[ ]			Match a range of characters				[a-l]ook	book, cook, look
[ ]			Match specific characters				[bc]ook		book, cook
```

No! There is no way that square brackets have special meaning to the like operators! That is **_way_** too regex-y.

## Double Checking

After a little searching through my datasets I found that there was indeed plenty of square brackets.

These could easily cause a comparison to fail when I was expecting it to match. For example a `[username]` would have been considered as a single character with multiple potential variants rather than a word wrapped in brackets.

Worse still, it's very easy to put an invalid set inside of those brackets. The easiest one to hit is... nothing.

```powershell
$One = "Found problem users: []"
$Two = "Log: Found problem users: []"

$Two -like "*$One*"
```

This will result in invalid pattern error:

```
The specified wildcard character pattern is not valid: *Found problem users: []*
At line:4 char:1
+ $Two -like "*$One*"
+ ~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], WildcardPatternException
    + FullyQualifiedErrorId : RuntimeException
```

I'd legitimately never ran into this before. I've spent years blissfully thinking like only accepted two wildcards. This is huge, and makes these operators so much more powerful... but I still had a task to complete.

## Stripping Away Special Meaning

When you want to stop something having a special meaning in PowerShell you're going to want to try the back tick (```) character first, and that happens to be exactly what we need to get this job done.

I ended up using the `Replace` method twice. The first is to escape the left square bracket and the second for the right.

To illustrate this, let's revisit the previous example that failed.

```powershell
$One = "Found problem users: []"
$Two = "Log: Found problem users: []"

$Two -like "*$($One.Replace('[','`[').Replace(']','`]'))*"
```

When the comparison runs, the string in the `$One` variable will actually be:

```
"Found problem users: `[`]"
```

And the output of this example is the `$true` I was expecting all along.

## Credit

Hero image by [Stefan Steinbauer](https://unsplash.com/@usinglight?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/secret?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

