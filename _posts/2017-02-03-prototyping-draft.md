---
layout: post
title: "Why developers should learn Sketch"
subtitle: ""
date: 2017-03-02-18:25:00
categories: [ephemera]
---

![sketch-icon](http://radishlab.com/wp-content/uploads/2015/08/sketch-app-icon-e1439400898972.png)

Back when I was working on my first Rails app, my team managed all its tasks through a single, chaotic Trello board.

There were times when we lost track of how the end product should have looked and behaved. It wasn't that our goals and endgame functionality weren't explicitly — there were plenty of discussions and whiteboarding and boxes and wireframes.

But as developers and not designers, we tended to make design of secondary importance. The reality, after hours of hacking together stylesheets, was that not having a visual prototype really set us back.

After reading about implementing a [Three Level Design Approach](https://blog.prototypr.io/three-level-design-approach-73602a99bc68#.jvzmjkt0x) in Sketch, I've started thinking about projects through the eyes of three different project members: the designer, the developer, and the product manager.

Iterating on [a music project](http://clinton.so/longbox/) I made in college, I'm planning to make a magazine-style publication, just for carefully curated playlists. Here's what the front page desktop prototype looks like:

![longbox-frontpage-proto](http://i.imgur.com/to4AXB6.png?1)

The visual representation for all the frontpage components are all there, but that's just a third of the job — the designer's, specifically. Now onto the developer's view.

![longbox-mobile-dev](http://i.imgur.com/b0WuLqU.png)

After making a larger container artboard to contain both the "view" artboard and any notes I had on it, I started to describe some of the user actions and behaviors that would trigger component functions. I also kept track of some of the state the app would keep, e.g. `currentTrack`, flags, and so on.

I also drafted out the shape of some of the data certain components would keep. In the future, I'd add more notes on routing, where links lead off to, and briefly describe some of the handler functions for various user interactions (streaming and fetching metadata for instance).

In the end, these are honestly all just liner notes — visual pseudocoding — but in the end it makes the whole project much easier to grok.

Even on the designer's side, Sketch has a couple tools that allow designers to break down components to reusable parts in the way developers tend to their elements. Those components for Sketch are called [Symbols](https://www.sketchapp.com/learn/documentation/symbols/). Symbols not only allow the designer to create reusable UI elements, but they also allow for nesting, which makes it trivially easy for you to organize different visual states under single components. For instance, you could have highlighted and unselected tracks for this playlist project hidden under the `tracks/` symbol folder.

The last part of the process is making sure a product manager can quickly understand what's going on when you interact with the prototype. For Sketch, I found that you can export the artboards to [Invision](https://invisionapp.com), which allows you to link different "hotspots," or prototype links, to different screens.

So if you had to quickly mock up where links take you on a site, you could draft out the different screens — user profile, login, register, and so forth — and export them to InVision, where you can "link" them together.

![invision-longbox](http://i.imgur.com/iuBF2ie.png)

At the end of the day, I found that even just making a one-hour wireframe that describes what the app or page does makes project goals that much clearer. None of it has to even be this painstakingly developed, and Sketch has Bootstrap-like display templates and symbols for forms, buttons, and frequently used UI elements.

So even if you're a developer who's never used a vector editor before, you too can slap together a working visual prototype in minutes flat.
