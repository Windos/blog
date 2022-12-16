+++
categories = ["PowerShell"]
date = 2019-11-27T20:18:22Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2019/11/anthony-martino-6AtQNsjMoJo-unsplash.jpg"
slug = "like-operator-chains"
summary = "RegEx continue to prove itself as a valuable tool in my tool belt when scripting. Sometimes it can save you a lot of time and many lines of code."
tags = ["PowerShell"]
title = "Tired of Massive \"like\" Operator Chains? Rub a Little RegEx on It"

+++


I was recently writing a PowerShell script and found myself needing to check an input value against a laundry list of criteria. That criteria could have been anywhere in the input value, so out of habit I started writing something that looked like this:

```powershell
$Input = 'This is an example string. It demonstrates "stuff."'

if ($Input -like '*example*' -or
    $Input -like '*demo*' -or
    $Input -like '*more criteria*' -or
    $Input -like '*when will this end?*') {
    'A criteria matched, do the thing!'
}
```

This works, but it doesn't scale. This example matches against four criteria, and it's already getting a little long for my liking.

What if I had a list of 10 criteria, or 100? Yeah... that's not going to work out.

## There's Got to Be a Better Way

In the above example, I used the "like" operator, which allows you to match against a string using wildcards.

There are a lot of other [comparison operators](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_comparison_operators?view=powershell-6) available for us to use.

A favorite when looking for a a full string in a large group of criteria, is the "in" (or "contains") operator.

```powershell
$Input = 'josh'

$Criteria = 'adam', 'barry', 'josh', 'steve'

if ($Input -in $Criteria) {
    'A criteria matched, do the thing!'
}
```

What we really need is a way combine both the "like" and "in".

## RegEx to the Rescue!

It turns out that RegEx is a very handy tool to have in your tool belt, as I've been discovering since I last used it in a [blog post](__GHOST_URL__/2019/07/25/normalizing-phone-numbers-easier-with-regex/). It has the ability to both look for chunks of text inside a larger input string, and also chaining together those chunks that we're looking for.

From now on, we'll make use of the "match" operator.

For starters, searching inside a string is even easier than with the "like" operator:

```powershell
$Input = 'This is an example string. It demonstrates "stuff."'

$Input -match 'example'
```

Next we layer in matching against multiple criteria. We do this with the `|` character, which in RegEx is technically called an "alternation", just think of it as an "or".

```powershell
$Input = 'This is an example string. It demonstrates "stuff."'

$Input -match 'example|unique|test'
```

This is going to return true if any of the words "example", "unique", or "test" are found in my input string. It's important to know that you're no limited to words here, you can have full on sentences if needed.

_Note: if you're a performance fiend, the words are searched for in order, so if you're expecting one to match most often, put it towards the left of your list of criteria._

Now we need to make this even more expandable. Ideally we could pull a list from some system and dynamically build our RegEx string to match against all of them. An example might be pulling a list of common passwords.

```powershell
$Input = 'This is an example string. It demonstrates "stuff."'

$Criteria = 'example', 'unique', 'test', 'a lot', 'of', 'entries'

$Input -match ($Criteria -join '|')
```

Here we load up our criteria into a list (that's fairly easy to maintain), and then use the 'join' operator to put a pipe character between each of them. We then use the joined string in our match.

We've now got a working method for searching for long lists of criteria in a given string without having to tediously build chains of like statements!

## My Real World Example

The script I alluded to at the start of this post involved pulling user account information from Active Directory. One of my main design drivers was making the script as generic as possible and also friendly to use by people that don't live and breath PowerShell.

With that in mind, I had made it so that the user could specify a base Organisational Unit (OU) to search from but I also needed a way of allowing the user to exclude specific OUs that may live under that base.

There's many ways to solve this problem, but the way I opted to do it was to take the user's distinguished name property (which includes the OU path the live under), and check it against a list of exclusions.

For user input, you might have data like this:

```powershell
$BaseOU = 'OU=Users,OU=Corp,DC=ToastIT,DC=DEV'
$ExclusionOUs = 'OU=HR,OU=Users,OU=Corp,DC=ToastIT,DC=DEV',
                'OU=Finance,OU=Users,OU=Corp,DC=ToastIT,DC=DEV',
                'OU=Executive,OU=Users,OU=Corp,DC=ToastIT,DC=DEV'
```

I've kept the list of exclusions short for the sake of demonstration, but as a script I was going to be handing off I have no idea how long (or short) it could end up being.

Later on, I need to pull user objects from AD, but I don't want to end up with users inside those that were excluded.

```powershell
$Users = Get-ADUser -Filter 'Enabled -eq $true' -SearchBase $BaseOU |
    Where-Object {$_.DistinguishedName -notmatch ($ExclusionOUs -join '|')}
```

Job done!

## Proceed with Caution

Generally, RegEx is case sensitive, however PowerShell isn't. The "match" operator will ignore case, so it'll match "Example" with "example".

If this is going to be an issue in your case, use the "cmatch" operator. There's actually a number of these case sensitive operators in PowerShell, including the "clike" and "cin" versions of the operators we looked at earlier.

Another thing to be aware of is special RegEx characters. For example, a period (".") is one of the wild card characters available so you will probably want to avoid including them in your list of criteria (or work out escaping them.)

If you're interested in learning about other edge cases that may cause issues, or find learn some more things you can do with RegEx, check out the reference on [regexr.com](https://regexr.com/)

