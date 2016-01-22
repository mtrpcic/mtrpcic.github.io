---
layout: post
title:  "Documentation - The Nectar of the Gods"
date:   2011-07-25
redirect_from: /2011/07/documentation-the-nectar-of-the-gods/
---
Since day one of college, documentation was toted as being of the utmost importance.  It was necessary, at all times and in every situation.  "By god, what does the variable "i" mean in this "for" loop!?" I could imagine my professors mumbling as they read through my code.  Of course, when I was writing that code, it was painfully obvious what "i" was doing.  It's **always** obvious what "i" is doing - isn't it?

Of course, my example above is a bit simplified, but it proves the point none the same.  No matter how complex the code that you're currently working on may seem, it's always within the realm of understandability.  Step back for several weeks, and walk into the hellish nightmare that is **The Spaghetti Abyss**, and you'll spend five minutes wondering, "What the hell was this fool doing?" followed by a brief flash of recollection and embarrassment - "Gasp! I am that fool!"

And therein lies the rub.

As soon as I finished my time in school, documentation seemed much less important.  You see, up until this point, students had been documenting merely to get the marks for it.  We documented our code because our grades depended on it.  As soon as I stepped out those doors for the final time, I remember thinking I was free of that nightmare.  I was a good programmer, I could look at code and understand what was happening under the hood; I didn't need an explanation.

Fast forward a couple of months, and I'm at work, trying to solve a particularly bad problem - a problem I inadvertently produced several weeks back, in a module that had since been left to stew in the bowels of a great beast of a project so terrifying, so insidious, it was Beelzebub incarnate (in code).  I open that file, thinking "This will be an easy fix - after all, I wrote it."  Never have I been more wrong in my life.  My undocumented code put a bucket over my head and slammed it with a baseball bat.  It took me several days to fix a bug that was incredibly simple, but my lack of documentation fanned the flames until my ego was burned to the ground.  I wasn't a good programmer anymore; a good programmer wouldn't have run into this problem.

Fast forward yet again.  I need to delve deep within the source code of [ActiveSupport](https://github.com/rails/rails/tree/master/activesupport).  I'm looking at code written by the deities of code, heavy hitters within the Ruby community. These men and women know Ruby from top to bottom, inside out, and to a peasant like me, it looked like they were making use of every trick in the book to get things done.  But, if that was the case, why was it so easy for me to understand everything?  Why was I able to follow along at a nice leasurely pace, instead of having my eyes melt off my face? Oh, I see now...

This code had documentation.  This code had a dictionary beside it, and I could find out what everything was doing every step of the way.  This code was **easy**.

It takes a situation like the one described above to let you know how important something really is.  You can be an amazing problem solver, and a prolific programmer, but you won't ever be considered one of the best until your code can be understood by thousands of people at a glance.  Now that I've got my own projects, I make sure that I do my best to document every code change.  I try to act like one of the best, because I'd like to be one of the best.
