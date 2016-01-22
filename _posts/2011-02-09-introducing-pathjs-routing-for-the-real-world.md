---
layout: post
title:  "Introducing PathJS - Routing for the Real World"
date:   2011-02-09
redirect_from: /2011/02/introducing-pathjs-routing-for-the-real-world/
---
As the web experience continues to evolve, more and more people are coming to expect "Web Applications" instead of "Web Sites".  HTML5, CSS3, AJAX, and a ton more acronyms all help us reach the goal of turning our website into something more.  Now, PathJS is here to help.

PathJS lets you route URL hashes to functions in javascript.  These functions can (and likely will) perform AJAX request to populate your shiny Web Application with data and DOM Structures galore.  You can find the code, tests, and eventually examples on the [github page](https://github.com/mtrpcic/pathjs).

Now, a brief example:

```js
$(document).ready(function(){
    Path.map("#/home").to(function(){
        document.write("Welcome Home");
    });
 
    Path.map("#/contact").to(function(){
        document.write("Contact Us");
    });
 
    Path.map("#/download").to(function(){
        document.write("Downloads coming soon");
    });
 
    Path.root("#/home");
    Path.listen();
});
```