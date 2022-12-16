+++
categories = ["PowerShell"]
date = 2020-03-10T22:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2020/01/valentin-lacoste-jNSJE8dMro0-unsplash.jpg"
slug = "ps7now-null-conditional"
summary = "How do you deal with potentially getting nothing back from a cmdlet or function? PowerShell 7 introduced a set of new operators to make working with null values easier."
tags = ["PowerShell"]
title = "Exploring... Nothing? PowerShell 7's Null Conditional Operators - #PS7Now"

+++


<p class="note"><em>This post is part of the <a href="https://twitter.com/search?q=%23PS7Now" target="_blank">#PS7Now</a> PowerShell Blog Week celebrating the general availability launch of <a href="https://docs.microsoft.com/en-us/powershell/" target="_blank">PowerShell 7.</a></em><br /><br />
<em>There's a lot of great content coming out this week from a number of different authors, so make sure you keep an eye on the <a href="https://twitter.com/search?q=%23PS7Now" target="_blank">hashtag</a> over on Twitter or checkout the table at the bottom of this post for direct links!</em></p>

Have you ever found yourself with a variable that may or may not actually exist? Maybe you were assigning the output of a cmdlet to it, but it's possible that the resource you were looking for doesn't exist so the cmdlet didn't return anything.

In this situation, the variable is "null" or empty/non-existent. For a deep dive on the general concept of null in PowerShell I highly recommend checking out [Kevin Marquette](https://twitter.com/KevinMarquette)'s "[Everything You Wanted to Know](https://powershellexplained.com/2018-12-23-Powershell-null-everything-you-wanted-to-know)" post.

But back to the original question. Traditionally if you wanted to check if your variable was null or not, you might include something like this:

```powershell
$TheThing = $null

if ($null -eq $TheThing) {
	"Nope, it wasn't there!"
} else {
	$TheThing
}

```

In this example, we first assign null to the `$TheThing` variable, we're doing this for the sake of the demo to ensure you get the expected result when you try it at home.

We then check to see if that variable is null. If it is null then we take some action (in this case outputting a string) and if it isn't then we return the value it contains.

You could simplify your if statement from `($null -eq $TheThing)` to `(-not $TheThing)`, but regardless of how you go about the comparison you're still using a full if/else statement.

## Null Coalescing Operator

The newly released PowerShell 7 introduces a new option for us on this front. We now have a `??` null coalescing operator, which is a feature of more "traditional" programming languages.

The best way to describe this is that your output will be the value on the left hand side of the operator if it isn't null, but if it **is** null then it will output whatever is on the right of the operator.

```powershell
<left> ?? <right>
```

Our previous example re-written to use the null coalescing operator looks like this:

```powershell
$TheThing = $null

$TheThing ?? "Nope, it wasn't there!"
```

That's dramatically shorter isn't it?

### Try It Yourself

If you haven't already, let's run through those examples and see the output from them. The first "old" example was this:

```powershell
$Thing = $null

if ($null -eq $Thing) {
    'The thing is null'
} else {
    $Thing
}
```

This will output:

```output
The thing is null
```

Now let's give `$Thing` a value:

```powershell
$Thing = 'The thing has a value!'

if ($null -eq $Thing) {
    'The thing is null'
} else {
    $Thing
}
```

The output now contains the value we assigned to `$Thing`:

```output
The thing has a value!
```

Now try those two examples again, but using the null coalescing operator.

```powershell
$Thing = $null

$Thing ?? 'The thing is null'
```

```powershell
$Thing = 'The thing has a value!'

$Thing ?? 'The thing is null'
```

### Stretching Things

It's not just strings that you can put on either side of this operator. You can actually run commands. A caveat here, as far as I'm aware from my experiments, is that you're limited to one pipeline (meaning you can use `|` characters, but not `;`) and in my testing the expression needs to be wrapped in brackets.

```powershell
$Thing ?? (Get-Date | Select *)
```

_You can even put an expression like this on the left. Just be aware that the expression on the right will **only** be evaluated if the left side was null. That's a very specific condition, remember that things that return a Boolean value aren't returning null (`$false` is a value, therefore it's not null.)_

This is all well and good, but what if we want to actually assign a value to our variable if it's null?

## Null Conditional Assignment Operator

A very near relative to the `??` coalescing operator is the `??=` conditional assignment operator.

With this operator, the variable on the left will keep its current value if it is not null, but will be assigned the value on the right if it is null.

Let's walk through a full example of this using just the new operators from PowerShell 7 (no need for if/else here!)

First we'll make sure our variable is null:

```powershell
$Thing = $null
```

Then we'll make sure it's actually null with the coalescing operator:

```powershell
$Thing ?? "Yup, it's null!"
```

```output
Yup, it's null!
```

Now, let's use the conditional assignment operator to give our variable a value:

```powershell
$Thing ??= 'This is a value!'
```

There's no output on this. To see that something has happened, we'll use the coalescing operator again:

```powershell
$Thing ?? "Yup, it's null!"
```

```output
This is a value!
```

Excellent, our value was assigned. For a little homework, try running the following commands to double check that the conditional assignment operator won't overwrite and existing value:

```powershell
$Thing ??= 'Yet another value...'
$Thing
```

## Bonus: Accessing Properties and Methods on Null Variables

One of the main reasons I actually find myself checking whether a variable is null or not is to avoid errors when trying to dig into it, such as pulling out properties, calling methods, or indexing an item in a collection.

Doing any of these actions on a null variable will result in errors. We can avoid these errors by throwing question marks at the problem (don't worry, this statement will make sense in a moment.)

_Note: This is an experimental feature, and will need to be enabled prior to use._

```powershell
Enable-ExperimentalFeature -Name PSNullConditionalOperators
```

### Methods and Properties

Let's say we're wanting to stop the BITS service using its `Stop()` method, but we've forgotten to actually get the service in a variable prior to trying.

```powershell
$BitsService.Stop()
```

{{< figure src="__GHOST_URL__/content/images/2020/02/image-2.png" >}}

In PowerShell 7 we can instead do the following:

```powershell
${BitsService}?.Stop()
```

{{< figure src="__GHOST_URL__/content/images/2020/02/image-3.png" >}}

What you'll note here is that the name of the variable has been wrapped in curly braces, this is needed because variable names can end with a question mark and that happens to be what we need to add before the period when calling a method or property.

The previous example didn't actually _do_ anything because that variable was still null. If it did contain a service object, the service would have been stopped. Adding the question mark specifies that you're expecting that the variable _could_ be null and that that is OK... for whatever reason.

_Note: Trying to get properties from a null variable won't actually generate an error, even before PowerShell 7. Using this syntax just makes then intentions of your script clearer._

### Indexing Collections

Let's say you're getting a list of users from Active Directory, but the Organizational Unit you're using may or may not actually contain users. You want to index into the potential collection of users, but if none were returned your variable will be null... and you'll get an error

```powershell
$Users[0]
```

{{< figure src="__GHOST_URL__/content/images/2020/02/image-4.png" >}}

Once again, we can add wrap the variable's name in curly braces and add a question mark to avoid the error:

```powershell
${Users}?[0]
```

{{< figure src="__GHOST_URL__/content/images/2020/02/image-5.png" >}}

These can be chained together, so you can index into a collection and also call a method or get a property, you just need to continue to use question marks.

```powershell
${Users}?[0]?.SamAccountName
```

## Final Thoughts

It's important to remember, as with most of the new PowerShell 7 features, that using them automatically makes your scripts incompatible with older PowerShell versions.

There's nothing wrong with this, just consider the audience of your scripts and whether you want to force them to upgrade (even though we all know that they should!)

Personally, I'll be using some of these new operators when working via an interactive shell, but not in scripts that I'm saving for later re-use. This is the same stance I take on [aliases](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_aliases?view=powershell-7).

The main value I see here is breaking down a barrier that may have previously stopped existing developers from adding PowerShell to their toolbox.

Now that you've learnt a little bit about nothing (ha, null jokes!), please go check out the other [#PS7Now](https://twitter.com/search?q=%23PS7Now) authors:

| Author         | Twitter                                               | Blog                                                                   |
|----------------|-------------------------------------------------------|------------------------------------------------------------------------|
| Adam Bertram   | [@adbertram](https://twitter.com/adbertram)           | [https://adamtheautomator.com](https://adamtheautomator.com)           |
| Dan Franciscus | [@dan_franciscus](https://twitter.com/dan_franciscus) | [https://winsysblog.com](https://winsysblog.com)                       |
| Dave Carroll   | [@thedavecarroll](https://twitter.com/thedavecarroll) | [https://powershell.anovelidea.org](https://powershell.anovelidea.org) |
| Jeff Hicks     | [@JeffHicks](https://twitter.com/JeffHicks)           | [https://jdhitsolutions.com](https://jdhitsolutions.com)               |
| Jonathan Medd  | [@jonathanmedd](https://twitter.com/jonathanmedd)     | [https://www.jonathanmedd.net](https://www.jonathanmedd.net)           |
| Josh Duffney   | [@joshduffney](https://twitter.com/joshduffney)       | [http://duffney.io](http://duffney.io)                                 |
| Mike Kanakos   | [@MikeKanakos](https://twitter.com/MikeKanakos)       | [https://www.commandline.ninja](https://www.commandline.ninja)                 |
| Prateek Singh  | [@singhprateik](https://twitter.com/singhprateik)     | [https://ridicurious.com](https://ridicurious.com)                     |
| Thomas Lee     | [@doctordns](https://twitter.com/doctordns)           | [https://tfl09.blogspot.com](https://tfl09.blogspot.com)               |

## Credit

Hero image by [Valentin Lacoste](https://unsplash.com/@valentinlacoste?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

