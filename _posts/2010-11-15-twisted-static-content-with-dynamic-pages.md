---
layout: post
title:  "Twisted: Static Content with Dynamic Pages"
date:   2011-01-04
redirect_from: /2010/11/twisted-static-content-with-dynamic-pages/
---
When I was first learning about the twisted.web server framework, I made the mistake of trying to include an external stylesheet from my server. Much to my dismay, it simply wouldn't load. Eventually I figured out that I wasn't telling Twisted to server those files, and since I wasn't going through any kind of encompassing server (nginx, Apache, etc), it made perfect sense that those files remained a mystery to the browser. The solution is simpler than you might assume. Let's define a nice, basic web page:

```python
from twisted.web import resource

class Home(resource.Resource):
    isLeaf = False
 
    def getChild(self, name, request):
        if name == '':
            return self
        return resource.Resource.geChild(self, name, request)
 
    def render_GET(self, request):
        return "<html>Hello, world!</html>"
```

Run the above script in your main method via something similar to the following:

```python
if __name__ == "__main__":
    from twisted.web import server
    from twisted.internet import reactor
    root = Home()
    site = server.Site(root)
    reactor.listenTCP(8080, site)
    reactor.run()
```

Now if you run your script and access localhost:8080 in the browser, you'll see our nice "Hello, world!" message.  If we wanted to have that method return a substantial chunk of HTML, rather than a simple string, there's a good chance that that HTML would point to external scripts and styles.  To set up an external directory to server your files is as easy as changing your main method from the piece of code above, to the following:

```python
if __name__ == "__main__":
    from twisted.web import server, static
    from twisted.internet import reactor
    root = Home()
 
    # We are adding static directories to our server
    root.putChild('styles', static.File("./styles"))
    root.putChild('scripts', static.File("./scripts"))
    site = server.Site(root)
    reactor.listenTCP(8080, site)
    reactor.run()
```

You can tell Twisted to serve any directory on your filesystem (assuming permissions are correct), and can tell it to name it whatever you like.  This makes it easy to host static content (HTML, CSS, and JavaScript), while integrating it with your Python scripts.