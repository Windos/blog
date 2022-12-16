+++
categories = ["Security", "Meta"]
date = 2017-08-28T12:00:00Z
description = ""
draft = false
thumbnail = "/2018/05/thumb-8.jpg"
slug = "war-on-passwords"
summary = "The NIST guidelines struck a chord with me. It seemed to be something that our users would embrace while addressing potential issues within any given workplace"
tags = ["Security", "Meta"]
title = "The War on Passwords"

+++


A little over a year ago, the United States National Institute for Standards and Technology ([NIST](https://www.nist.gov/)) released a draft of [SP 800-63 _Digital Identity Guidelines_](https://pages.nist.gov/800-63-3/). These guidelines were finalized in June. I first found out about NIST's guidelines via [Sophos](https://nakedsecurity.sophos.com/2016/08/18/nists-new-password-rules-what-you-need-to-know/) and it really captured my imagination.

(Yes, I'm including a few hyperlinks in this post. Consider them references and additional reading if you're want to find out more.)

Lately, I've seen several articles in the news on the topic, which has triggered me to consider it again.

## **That's a nice story, what're you getting at?**

The thing most people pulled out of the NIST guidelines were the recommendations for "memorized secrets" (also known as passwords, passphrases or even pins.) In short, these recommendations can be summarized as:

* Minimum length of 8 characters
* Maximum password length at least of 64 characters
* Accept all characters (including emoji)
* Ban known bad passwords
* Don't enforce complexity (one uppercase, one number, one symbol)
* Don't expire passwords without reason.

Chances are, all of this is making you think of an [old XKCD comic strip](https://xkcd.com/936/). Wow… the strip is from 2011, where has the time gone?

For the sake of clarity, I'll be referring to two types of "memorized secret" during this post:

* **Password**: Short, and with enforced complexity
* **Passphrase**: Long without mandatory complexity.

Like I said at the start, the NIST guidelines struck a chord with me. It seemed to be something that our users would embrace while addressing potential issues within any given workplace.

## **This seems like a big change from the status quo, what's the benefit?**

For me, it comes down to our users.

Strike up a conversation with your users about passwords, and I guarantee at least 90% of them will complain about having to change them every one to three months.

Strike up a conversation with your helpdesk/support team about passwords, and I guarantee they'll complain about accounts being locked out due to expired passwords.

The traditional wisdom about passwords expiring and needing to be changed regularly is that it limits the amount of time that a compromised account can be used before the password changes.

In reality, your users aren't picking a unique password every time. They're following a pattern; often incrementing the number at the end (that's only there because you told them they had to have one.) If a malicious third party has the password _Secret6_ and it stops working, you know what will probably start working for them? _Secret7_.

Rather than keeping your network secure, you're just serving to make users jump through hoops that frustrate them and, potentially, could lead them to committing an even greater sin that just incrementing the number: writing it on post-it and sticking it to their monitor. Okay, maybe it won't be that visible, but they could be inclined to record it somewhere.

{{< figure src="/2018/05/history.jpg" >}}

## **Wait, you said no complexity? That can't be right!**

It sure is!

I'll rip off the two examples from the previously mentioned comic to demonstrate the value of a longer passphrase vs traditions password (assuming a brute force attack at 1000 guesses per second):

* Tr0ub4dor&3 – Hard to remember, 3 days to crack.
* correct horse battery staple – Easy to remember, 550 years to crack.

Traditionally, we'd consider that first example to be a top-notch example of what we want. You're also likely to see this written on the underside this users' keyboard because it so hard to remember.

A longer passphrase takes much longer to brute force, so we should do what we can to encourage our users to use them… but they won't if we force them to sprinkle in numbers and symbols.

It's a balancing act. One I personally see being well worth it, when all I'm giving up is counter-productive complexity rules.

## **My users aren't touch typists, I don't think they'll take to longer passwords**

That's generally the first reaction I've had in my exploration of this topic. For me, and eight-character minimum isn't enough… so I went into this proposing a minimum of 12 characters.

I've talked to several users about this, for the most part these conversations went along the lines of:

"So, I'm thinking we increase our minimum password length to 12 characters, but you won't need to use any capitals, numbers or symbols if you don't want to."

"Ehh… I don't…"

"Also, you won't have to change your password all the time anymore."

"Sold! Where do I sign up?"

It really is an exercise in give and take. We want our users to have longer passwords, that they can easily remember so they don't write them down, and in return we make it so that they only have to come up with one good password.

## **NIST is American. My company doesn't care about what's happening in America**

And at the end of the day, neither does my organization… for the most part.

Chances are, those responsible for setting guidelines in your country are looking at the NIST guidelines and considering adjusting their own policies. So, find out where you should be looking locally (it helps to ask your auditors) and read through their relevant publications.

In New Zealand, we have the [Government Communications Security Bureau](https://www.gcsb.govt.nz/), who publish the [NZ Information Security Manual](https://www.gcsb.govt.nz/publications/the-nz-information-security-manual/). The NZISM contains policies that are part way in line with what NIST recommend:

* Minimum password length of 10 characters, with complexity, or
* Minimum password length of 16 characters, without complexity.
* Change passwords every 90 days.

I actually think having the two lengths is a good way to go about things, though for me personally I'd be looking at 8 characters for passwords and 12 characters for passphrases. This way you're allowing people to still use shorter passwords if they want to, but you're "rewarding" those who opt to go for a longer secret.

It's taken me a long time to come around to the idea, despite understanding and agreeing with the logic behind it, but I do think that not forcing password resets on a fixed timer is a good idea. We can still force a reset if we suspect a breach (someone loses their phone or laptop) and users can still change them of their own accord.

At the end of the day, these guidelines from NIST are still relatively new. Other regulatory bodies are going to take time assess them and adopt or tweak them. There's also decades of "best practice" that will be hard for many organizations to break away from.

The momentum is there, however, so if you think it's a good idea then it can't hurt to strike up the conversation within your workplace. You're either going to be the catalyst for a change that your users will love, or you'll be told "not until this or that regulation says it's ok."

## **One thing to keep in mind…**

If you have legacy systems on your network, you may have a limited upper ceiling for the length of your passwords. It wasn't that long ago that Windows passwords were capped at 15 characters, and if you've got something *really* old that could go as low as 7 characters.

Though, if you've got something that old you've got bigger problems to worry about.

## **Wrapping up**

This post isn't intended to tell you, "this is what you should be doing." Rather, it's just a heads-up about that's coming over the horizon. It's not exactly new (I did mention that XKCD comic is six years old, right?), but it's certainly becoming rapidly more mainstream.

I firmly believe in a few years' time, passphrases will be the industry standard. Until then, cogitate on them and start the conversation.

What are your thoughts about password policies. If you had free reign, what would your personal policy for your company be? Let me know in the comments below, or over on [twitter](https://twitter.com/WindosNZ).

