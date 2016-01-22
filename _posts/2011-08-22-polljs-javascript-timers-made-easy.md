---
layout: post
title:  "Introducing PollJS: Javascript timers made easy"
date:   2011-08-22
redirect_from: /2011/08/introducing-polljs-javascript-timers-made-easy/
---

One thing that has always caused issue when writing web applications is the issue of querying the server for long-running background tasks (generating thumbnails or PDF's, sending batch emails, etc).  Often times these actions are too long-running to be contained in an HTTP request, so a short request is made and an interval is set up to begin querying for the eventual finish.  This isn't a terrible way to do things, but the barebones methods that the browser provides are somewhat lackluster. For example, to kill an interval you need to assign the returned value of your `setInterval` call to a variable, and clear it out with `clearInterval`. This means you need to keep reference to the Interval ID, and that reference must live there. If your user is going to be using the application during this time, you'll need to store this variable somewhere high up the ladder (possibly even as a global!), simply so you can clear it later.

But what if that long running job never gets executed during this particular session?  And what if you have many different operations that need this type of implementation?  You've now got your global namespace polluted with oft-unused variables.  That's where PollJS steps in.

PollJS is a convenience wrapper for the native "setTimeout", "setInterval", "clearTimeout", and "clearInterval" methods.  It wraps them with a simple definition method, and extends their functionality with additional options:

```js
// A Basic Example
Poll.start({
    "update_users",
    interval: 1500,
    action: function(){
        alert("Updated!");
    }
});

Poll.stop("update_users");
```

You can read more about the project, and download the source code on the [Github page](https://github.com/mtrpcic/polljs).