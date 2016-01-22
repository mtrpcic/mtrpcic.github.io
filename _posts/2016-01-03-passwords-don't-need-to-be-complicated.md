---
layout: post
title:  "Passwords Don't Need to be Complicated"
date:   2016-01-03
---
The state of the password has changed a lot over the past several decades, and unfortunately, not for the better.  First, everyone had a few online accounts, and using the same password for the two or three accounts you might have had was probably pretty common. A few years go by and there are a whole slew of new services and sites, each with their own accounts and passwords. By now, it was common knowledge that using the same password for everything was Bad&#8482;, so we started using a new password for every service. Time ticked on, and we all had 50 accounts across the web, and Password Managers become the best way to manage a veritable novel of usersnames and passwords.

### Password Managers
Password managers are the current "right way" to handle passwords.  You let the tool handle generating, storing, and applying your passwords.  It keeps things safe and sound, but also introduces a single point of failure.  If you're using a service to handle things, that service can go away overnight (or be taken down temporarily by DDoS or similar attacks). If you're using an app on your phone, a bad night out mean that a stranger holds all of your keys in their hand. If somebody is able to guess your master password, your entire online life is an open book. I never liked the idea of password managers, because at their core, they violate the first thing we all learned about passwords when we were children: _"Don't write this down."_

### Something New
For 2016, I decided that it's time to fix the problem once and for all. It doesn't need to be complicated. I decided to treat the problem like I treat most of my problems: How would I solve this if I had to code my way through it?

#### Choose a Base
First, choose an easy to remember base for your password. It should be easy to remember, but still complex enough to avoid being guessed by a complete stranger, if it ever came down to it.  I opted to choose something that contained at least one upper-case letter and at least one number. I wanted this password to be accepted by almost any system I threw it at.  For the sake of argument, let's say my base is **Password1**.

_Note: It goes without saying, but you should keep the base a secret (Don't write it down either!)._

#### Customize Per Service
Now, for every account you sign up for (or are changing your passwords for), define an algorithm that converts everything about that service into a simple string. Here's a really easy one to get you started:

1. Use the domain name


#### Smush 'em Together
You've got the ingredients, now it's time to make a password. Just combine your base and your "service slug" and you're done.  Let's imagine I've got a few accounts, maybe Github, Netflix, and GMail.

|Service|Base|Domain|Slug|Password|
|-------|----|------|----|--------|
|Github|Password1|github.com|github|githubPassword1|
|Netflix|Password1|netflix.com|netflix|netflixPassword1|
|GMail|Password1|mail.google.com|google|googlePassword1|

Hmm, the passwords above aren't very strong...But that's because they're examples, just for a proof of concept.  Let's see what happens if we change things just  little bit:

1. Let's choose a more complex password base: `TyQgcx9K`
2. Let's choose a more complex algorithm for the "service slug": The first character of each syllable of the domain name (`google.com` => `ggdc`)
3. We'll put the password base **before** the slug.

|Service|Base|Domain|Slug|Password|
|-------|----|------|----|--------|
|Github|TyQgcx9K|github.com|ghdc|TyQgcx9Kghdc|
|Netflix|TyQgcx9K|netflix.com|nfdc|TyQgcx9Knfdc|
|GMail|TyQgcx9K|mail.google.com|ggdc|TyQgcx9Kggdc|

#### You're Done!
That's it. Your passwords are all unique to a single service, are all fairly secure, and there is no single point of failure.  There's no risk of your Password Manager getting compromised, lost, or simply disappearing overnight. By choosing a complex "algorithm" for your passwords, there should be no identifiable information that gives the pattern away. With this pattern, I can sign into any service just by knowing the domain.  No risk, all reward.