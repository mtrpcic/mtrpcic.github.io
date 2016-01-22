---
layout: post
title:  "Project, Process, Profit"
date:   2013-02-20
redirect_from: /2013/02/project-process-profit/
---
For the past several weeks, there has been a lot of discussion both online and in my office about the "ideal" practices when it comes to software development.  What steps should be taken before you start coding?  How much planning should you do?  How do you eliminated scope creep?  Around the same time we were holding these discussions at work, [someone asked on Reddit](https://www.reddit.com/r/learnprogramming/comments/17mwsb/can_a_professional_programmer_explain_the_thought/).  While I was a little bit late to the game, my comment received a fair amount of traction, and even generated some direct messages with questions and comments.  I thought the ideas worked well, and decided to post them here as well.

The following are the steps that I try to follow when starting up a new project, whether it's going to be a web application, a useful library, or anything in between.

#### 1. Basic Idea
I usually start by getting some idea stuck in my head. It'll come to me while on the train, maybe laying in bed, or while watching TV. If it's a good idea, I'll think about it a bit more, and start to think about features. At this point, it's all in my head.

#### 2. Features and User Stories
Once I've rolled an idea around in my head for a while, it starts to evolve and get uniquely defined features. At this point, I'll start by writing the features out in a notebook somewhere (for me, hard copy helps my ideas flow better than using a computer). After a rough point-form list of features is done, I make them more details as "User Stories", which look like this:

```
As A User:
  I want to be able to register for an account by entering my email
  and password.

  I want to be able to post notifications and have them automatically
  be emailed to all of my subscribers

As A Subscriber:
  I want to automatically receive emails whenever one of the users
  I follow submits a notification.
```

You'll notice these are basically lists of "Wants". Also notice that the list is fairly fine grained, and can be quite long. Once you know what you want, you can go a step further.

#### 3. Mockups
If my application is going to have a user interface (like a desktop or web application), I'll draw a few sketches in a notebook. I am not a designer, artist, or User Experience professional, so these sketches always look terrible and never stick, but they help me to find the rough "look and feel" that I'm going for.

If you're more technically inclined, you can get [Balsamiq Mockups](https://balsamiq.com/products/mockups/) and create interactive mockups on the computer. I highly recommend this product.

#### 4. Deciding on Tools
Ok, so now I know exactly what I want to build, from features to (rough) design. The next step is to decide on my toolset. I'm familiar with many different languages, libraries, and frameworks, so I sit down and do a bit of research to find the best one(s) for this project.

For example, if I'm creating a real-time, auto-updating web application, I'll do some research to find out which web servers I'm familiar with have the best support for WebSockets, and might find out that [I can integrate my HTTP and WebSocket server if I use Tornado and SockJS](https://github.com/mrjoes/sockjs-tornado/).

#### 5. Planning
Once the tools are decided upon, I plan the project out. I rarely need to come up with my own directory structure, as whatever framework I'm using will dictate one for me, whether it's [Ruby on Rails](http://rubyonrails.org/) or [Django](https://www.djangoproject.com/).

To start my planning, I create a new board on [Trello](https://trello.com/), and give it four columns, labelled "To Do", "In Process", "To Verify", and "Done". I convert each user story from Step 2 into a task in my "To Do" list. I also add a few other tasks that relate to the general "start up" process for a project, such as setting up a [Github](https://github.com/) repository, etc.

#### 6. Development
Ok, I know what all my features are. I know what tools I will use to build the site. I know what all my tasks are. I'm ready to start. I go through the tasks in whatever order I feel makes sense (keep in mind, some tasks will require others to be completed first), and add those features to my program. When I start a task, I move it to the "In Process" list in Trello, and when I'm done it, it goes into "To Verify". **It does not go in "Done"!**

After about a week has passed, I'll go back to the "To Verify" task in Trello and read the user story I put together. I then try to do what it says in my application, and ensure it works. If you have two or more developers on a team, it is good practice to only verify someone elses work. The reason I wait a week is so that I "forget" some of the "quick tricks" you learn while repeatedly trying/testing during development. Being stuck in this routine while verifying can cause you to miss things.

Once a task is verified, it goes into the "Done" column. Once all tasks are in the Done column, the project is "complete". Many times, I'll do a project over multiple iterations, so this set of steps will happen once or twice, with a different starting point each time.