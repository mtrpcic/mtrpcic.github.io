---
layout: post
title:  "Chamber: Choosing a Stack"
date:   2016-01-14
tags:
- gamedev
- chamber
---
When I was 12, I was nearly addicted to an online game that was only played by about 100 people. It was a [MUD](https://en.wikipedia.org/wiki/MUD), a text-based online game where you would type in the actions you wanted to take. There was maybe 20 players online at a time. The more I played, the more I wanted to contribute to the game. It was just run by a few friends, and they invited me to create areas for the game, adding items, enemies, and weapons along the way. After a while, I wanted to take it to the next step by learning to program, so I could add even more advanced features. That was my first introduction to programming, and is the reason I do what I do today.

I've dabbled with game development over the years, but finally wanted to sit down and try to make something impressive. That's what this series is about, my experiments and goals for a substantial game.

### Introducing Hive
I needed something to name the github repository, and I chose "Hive". I figured, "Bees are pretty cool.", and just went with it. Hive aims to be a fairly advaned top-down 2D game engine, with a "2.5D" perspective camera.

<figure>
	<img src="{{ '/assets/img/gamedev/link_to_the_past.jpg' | prepend: site.baseurl }}" alt=""> 
	<figcaption>The "2.5D" perspective camera of <a href="https://en.wikipedia.org/wiki/The_Legend_of_Zelda:_A_Link_to_the_Past">The Legend of Zelda: A Link to the Past</a></figcaption>
</figure>

The engine should handle everything necessary to build a complete game. I'll be using the best tools (and libraries) for the job, but the engine logic, game loop, and interface structure will be built by hand. The engine will handle the following:

* **Asset Loading**: Including background imagery, spritemaps, iconography, custom fonts, data files, and anything else that might be needed during the course of development.
* **Game Loop**: Hive will handle the game loop, with various settings being configurable via a simple file. THis loop will handle updating game state, which will then be rendered by the rendering engine.
* **Rendering Engine**: The rendering engine (likely using [pixi](http://www.pixijs.com/)), will handle updating the screen on every tick of the game loop.  This is one of the most important and performance-optimized parts of the engine.
* **Audio**: Audio is an important part of games, and Hive will provide APIs for playing, pausing, and repeating background music and sound effects.
* **Menu Layout System**: A simple API for handling the generation of menus, along with the user interaction logic.

### The Core Stack
I've chosen to choose a core stack that I'm familiar with; one that is widely available, cross-platform, and just powerful enough to give me the tools I need, while forcing me to be clean and efficient with my engine design. I'll be using HTML/JS/CSS to build the entire thing, loading it into an [electron](https://github.com/atom/electron) shell. This has a lot of benefits for me:

* By using [babeljs](https://babeljs.io/), I'll be using the ES6 syntax, providing a lot of powerful features that will eventually be core components of the browser engine. This means I'll get speed improvements over time as the browser engine is optimized.
* I'll be using [Sass] to allow me to construct complex style content without needing to duplicate any specific rules or variables.
* During development, I can set up hotloading for the game. This will allow me to see changes to the code immediately in a running instance of the game. This will vastly improve development time.

### Extensibility
While Hive aims to do a lot for the game developer, the graphics are not particularly taxing, and the game logic is relatively simple. One of the great things about the Core Stack for the engine is the great possibility for easy extensibility into a more powerful "backend".  For the initial version, the game loop and all associated logic will be written in JavaScript. If things ever become too taxing, or we need some lower level features, we can easily migrate a large part of the engine into C++ using [Qt](https://www.qt.io/). We can leverage the [QtWebKit](https://doc.qt.io/qt-5/qtwebkit-index.html) module to pass data from our game loop to the rendering engine.

### Getting Started