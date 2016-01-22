---
layout: post
title:  "Hashbang URIs - They're Not That Bad; Really"
date:   2011-02-16
redirect_from: /2011/02/fragment-uris-theyre-not-as-bad-as-you-think-really/
---
The internet has been abuzz since the suite of [Gawker](http://gawker.com/) sites switched from regular old links, to [Fragment URIs](https://en.wikipedia.org/wiki/Fragment_identifier). Long story short, a JavaScript bug caused all content, on all of their sites, to be inaccessible to the entire world. Ouch, I know. But just because one developer made a farce of a piece of technology doesn't make it as horrendous as everyone's saying it is.

### A Brief Introduction
So what's the big guffaw all about?  Wikipedia uses Fragment Identifiers to jump around the page, don't they?  Well yeah, but that's just static content.  The big dilemma is over the loading of remote content when a new fragment identifier is loaded.  It's a nice way to load remote content, while still allowing the user to use the "Back" button properly.  Why would anyone want to do this, you might ask?  Well, let me tell you.

### Some Numbers
When loading any website, there's two things you pull from the server.  The Markup (HTML), which structures the page, and your helpers (CSS and JavaScript) to pretty the beast up, and make him shine.  I took a look at 4 sites that are kind of a big deal, to see what was being loaded every time I hit their homepage:

|Site|HTML|CSS|JS|Total|Content|
|---|---|---|---|---|---|
|Reddit|37.6KB|20.2KB|47.7KB|105.5KB|35.6% Content|
|Google|12.2KB|0KB (Inlined)|73.1KB|85.3KB|14.3% Content|
|YouTube|20.9KB|1.3KB|17.1KB|39.3KB|53.2% Content|
|Github|7.3KB|61.5KB|127.9KB|196.2KB|3.7% Content|

It looks like nearly every site is loading a lot of stuff other than content, especially Github.  Every time a request hits the server, it has to handle this additional content if it's not cached. If that's the case, taking away the constant loading of the JavaScript and CSS can significantly decrease bandwidth and server load. In addition to not needing to fetch the external resources, you also cut down on the amount of HTML required to be loaded with every request, as the DOM structure is only loaded once, and only the content needs to be loaded with every request. In the case of Reddit.com, the majority of HTML loaded is DOM structure and static content, with only 5.9 KB of the 37.6 KB being real, interesting content.  The example below is a rudimentary way to accomplish this, with very little overhead in cost, time, or complexity.

### Server Side

There are three things you need to do to make this work, and that's it.  First, make all internal links begin with the root of your site, sans the domain name.  We want things that "/look/like/this".  Next, modify all of your content and views to host the majority of your markup in a Layout of some sort.  Basically, your controller action should only return a block of content that will be placed into the body tag of the layout.  Finally, modify your controller actions to return the content sans layout when AJAX comes calling.  In rails, it would be as simple as:

```ruby
class WidgetController < ApplicationController
  def index
    @widgets = Widget.all
    respond_to do |wants|
      wants.html {}
      wants.js {render :layout => false}
    end
  end
end
```

That's it.  Now, when AJAX hits any of your existing controller actions, they'll return the content without the layout.

### Client Side

There are two things to do here.  First, you're going to intercept the click of every "a" tag on your site.  This is easy with jQuery:

```js
$("a").live("click", function(event){
    var href = $(this).attr("href");
    if(href[0] == "/"){
        event.preventDefault();
        window.location.hash = "#!" + href;
    }
});
```

The code block above checks to see if the link goes to a local resource, and if so, intercepts it, stops the link from acting, and changes the Resource Identifier instead.  Now, using a library like PathJS, listen for route changes and act accordingly.  With pathJS, it's as simple as:

```js
Path.default(function(){
    $.get(window.location.hash.replace("#!", ""), function(data){
        $("#contents").html(data);
    });
});
 
Path.listen();
```

Now, whenever a local resource link is clicked, it will be picked up and routed to the default function.  This function does an AJAX call to get new content, which is returned without the layout thanks to our controller action changes, and injects it into the DOM.  Fast and easy.  If a user hits your site with JavaScript disabled, the links will never be intercepted, and will go to your regular routes, returned with the layout intact.

### But what about...
There are a few things people complain about when confronted with as simple a solution as this:

**It doesn't make use of the HTML5 History API!**  
The example above does not, but the onus is on you as the application developer to build in support for that.  As time progresses, libraries like PathJS will be updated  to integrate with new technologies, but the HTML5 spec is ever changing, and the majority of browser market share doesn't support a lot of these new technologies.

**If I have JavaScript enabled, and send my friend (who has JavaScript disabled) a "Hashbang" URI, he won't load the content!**  
You're right.  This is the primary drawback of an approach like this, and you'll need to figure out if that's acceptable for you.  Are you building a website, or a web application?  Will users ever share links?  What if you're developing an Admin Panel for an internal company tool, an authentication-locked wiki system, or any application that relies on JavaScript to function (image editing, data plotting, etc)?  In those cases, users won't be sharing links, and it's a moot point.  On the other end of the spectrum, you could be building a website, not an application, where users can often share links.  Well, what is your target audience?  Are they likely to have JavaScript disabled?  Is the edge-case of a broken shared link worth the benefits an approach like this provides?  These are all questions that you and your development team need to answer before making a decision.

**Won't browser caching reduce server load already?**  
Yes and no.  In a perfect world, that would be the case.  However, there are a lot of things that can cause the server to send the additional contents on every request, such as  improperly set Headers, browsers with caching turned off/unsupported, clients that don't respect server headers, or any of a multitude of other possible scenarios.  In cases like these, a solution like "Hashbang URIs" makes sense.

_Edit: In addition to the cases above, which are unlikely in the best of scenarios (as was pointed out to me by friends and strangers alike), some browsers have a very low cache limit, namely the mobile browser market._

### Conclusion
No, it's not perfect.  No, it's not a solution for everyone. Yes, it has drawbacks and downsides.  So what?  It's pushing the boundaries of the browser, and treating the web as an Application Platform, rather than a dumb terminal for reading content and data entry.  The "Hashbang URI" makes your application feel like an application, instead of a website.  The drawbacks are things that need to be weighed against the benefits in your unique case, and the decision will be different for everyone.

Just because it doesn't make sense for you to use it, doesn't mean it's a stupid idea.  You are not the center of the Universe.