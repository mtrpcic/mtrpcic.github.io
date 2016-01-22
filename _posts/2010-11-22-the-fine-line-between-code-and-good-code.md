---
layout: post
title:  "The fine line between 'Code' and 'Good Code'"
date:   2010-11-22
redirect_from: /2010/11/the-fine-line-between-code-and-good-code/
---

I've been spending a fair bit of my spare time working on my [current project](https://github.com/mtrpcic/WebIRC), and one of the major issues I'm having right now is drawing the fine line between writing code, and writing good code.

How many times have you sat down with a good, solid idea, only to sit and stare at your monitor for 30 minutes because you're trying to think of 30 different ways to accomplish the same goal. You end up asking yourself 10 different questions, each with 10 pros and 10 cons, and you wind up with a very solid understanding of what the end product will do, but you've made no progress towards it.

> "Is this going to be DRY enough?"

> "Should I use List Comprehensions here?"

> "Should these be params to the method, or passed in when the object is initialized?"

I find myself running into this problem quite often, and always come to the same conclusion.  The conclusion that should be at the heart of as much as your logic as possible; **Keep it simple, stupid.**

When you're confronted with the "Mile-a-Minute Mind" scenario, just sit back and write whatever code comes to mind that gets the job done.  You're not writing **good code**, you're just writing **code**. Wow, that was easy?  It's ugly, but it was easy.  Alright, what's next?  Well, you could (and should) write any pertinent Unit Tests for the code you just wrote.  Make them as solid as you can. Now you're cooking with fire.

#### Congratulations, you fought through your own indecision, and wrote some code.

Now we're excited.  What next!?  Well, you wrote some code, and you wrote tests for your code.  Now comes the fun part.  Now you can get rid of that code, and replace it with what we've wanted all along; good code.  Thanks to our wonderful Unit Tests, you'll know if your new code is up to the same dastardly deeds as your old code.  Refactor your work of art to your hearts content, until it's as good as it can possibly be.

The moral of the story is that indecision is the bane of productivity.  Don't worry about writing concise code. Don't worry about writing optimized code. Just worry about writing working code, then take it from there. As we say in the world of Test Driven Development, "Red, Green, Refactor."