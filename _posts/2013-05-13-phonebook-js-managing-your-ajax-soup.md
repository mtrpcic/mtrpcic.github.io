---
layout: post
title:  "Phonebook.js - Managing Your AJAX Soup"
date:   2013-05-13
redirect_from: /2013/05/phonebook-js-managing-your-jquery-ajax-soup/
---

With the onset of modern web technologies and the advent of the MVC/VM wars, it was pretty much forgotten that a lot of projects aren't in the right place to use these new "auto-ajax" toolsets. Perhaps they're legacy projects, or maybe they're being built by people who either don't understand or like the MV* approach.  In either case, projects that are just using a "legacy" approach to AJAX can quickly devolve into a bowl of soup.  The code becomes bloated and hard to follow, and it's not immediately apparent what's happening when you look at it.

Phonebook.js fixes this by providing a lightweight (1.33kb minified) wrapper around your AJAX calls, allowing you to define a manageable "API consumer" which you can use throughout your project.

```js
// Turn this:
var request = $.ajax({
    url: "/api/users/all",
    type: "get",
    dataType: "json",
    data: {
        "active": true
    }
});
 
// Into this:
var request = API.users.all();
```

You can view the project on [github](https://github.com/mtrpcic/phonebook).