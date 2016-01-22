---
layout: post
title:  "PathJS and HTML History - A Match Made in Heaven"
date:   2011-07-19
redirect_from: /2011/07/pathjs-and-html5-history-a-match-made-in-heaven/
---
Nobody will argue that the [HTML5 History API](http://diveintohtml5.info/history.html) was a step in the right direction in regards to Web Applications.  Unfortunately, there was no simple, consolidated way that provided a routing mechanic that was both versatile and easy to manage.  As of today, the simple routing mechanic of PathJS is fully compatible with the HTML5 History API.  You get the power of a manual, with the simplicity of an automatic.  Here's how it works:

```js
// Define all your routes as you usually would with PathJS - Simply omit the "#"
Path.map("/home").to(function(){
    console.log("Welcome home!");
});
 
Path.map("/users").to(function(){
    console.log("Users, users, users.");
});
 
Path.map("/contact").to(function(){
    console.log("Feel free to contact me via Github.");
});
 
// Now override the click events for any of your links, and call
// the Path.history.pushState method to invoke the PathJS router.
 
// This example uses jQuery.
$("a").live("click", function(event){
    event.preventDefault();
    Path.history.pushState({}, "", $(this).attr("href"));
});
```

It's simple as that.  The PathJS implementation of the HTML5 History API supports the same features as regular PathJS, including:  Before-filter chaining, enter/exit methods, parameterized routes, and optional route parameters.  You can read more about it's features and limitations on the [Github Page](https://github.com/mtrpcic/pathjs).