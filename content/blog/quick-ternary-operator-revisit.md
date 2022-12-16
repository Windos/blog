+++
categories = ["Nugget", "PowerShell"]
date = 2019-10-04T15:41:49Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2019/10/jens-lelie-u0vgcIOQG08-unsplash.jpg"
slug = "quick-ternary-operator-revisit"
summary = "I was thinking about the new Ternary Operator and had a random thought... can it handle elseif logic?"
tags = ["Nugget", "PowerShell"]
title = "Quick Ternary Operator Revisit: Can It Elseif?"

+++


It was a little over a week ago that I first started looking into the [Ternary Operator](__GHOST_URL__/2019/09/25/ternary-operator-powershell-7/), which is being added in [PowerShell 7](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.0-preview.4). I came away from my initial experimentation looking forward to being able to use it for trivial conditional logic when I can guarantee that the code will only need to run on the new version of PowerShell.

Why trivial? As someone that's not used the ternary operator in the past, reading it doesn't come naturally. As soon as it needs to run across multiple lines, which the operator does support, I found it harder to read at a glance than the more verbose `if`/`else` syntax.

## But, Does It Do Elseif?

I was thinking more about this operator since writing that first post and a random thought came to mind... I wonder if you can representing and `elseif` statement using the ternary operator?

Let's start with the normal syntax:

```powershell
$Num = Get-Random -Minimum 1 -Maximum 20

if ($Num -gt 10) {
    '> 10'
} elseif ($Num -lt 10) {
    '< 10'
} else {
    '== 10'
}
```

As a ternary operator, you can represent this as:

```powershell
$Num = Get-Random -Minimum 1 -Maximum 20
$Num -gt 10 ? '> 10' : $Num -lt 10 ? '< 10' : '== 10'
```

And what would you know? It works!

{{< figure src="__GHOST_URL__/content/images/2019/10/image.png" caption="Example output in PowerShell 7" >}}

Unsurprisingly, I'm not a fan of this but it's a really cool option. If I were to use this myself, I'd probably opt to wrap the `elseif` component in brackets to help me parse it quickly:

```powershell
$Num -gt 10 ? '> 10' : ($Num -lt 10 ? '< 10' : '== 10')
```

## Bonus: Nesting

All we're really doing here is nesting `if` expressions. Strictly speaking what our previous ternary operator is actually doing looks a little like this:

```powershell
if ($Num -gt 10) {
    '> 10'
} else {
    if ($Num -lt 10) {
        '< 10'
    } else {
        '== 10'
    }
}
```

This is because the ternary operator has two (and only two) branches.

You can also nest within the 'true' section between the `?` and `:` as well.

## Verdict

Unlike the ternary operator itself, which was added to my toolbox... I won't be using this nested syntax myself. It very quickly becomes an unreadable mess, at least to my eye.

Even if I had a good grasp on them while writing them, there's no way I could guarantee that I'd be able to make sense of them in a few months time.

### Credit

Hero image by [Jens Lelie](https://unsplash.com/@leliejens?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

