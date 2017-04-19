---
title: A Basic Redux Workflow in React
date: 2017-04-02 00:35:48 Z
categories:
- code
layout: post
subtitle: Store, action, action creators, reducers, and adapters
---

To get a better mental map of how Redux works in conjunction with a React app, it's necessary to lay out some groundwork on why you might want Redux in the first place.

First, the store. Redux relies on a single, global store — a plain JavaScript object — that acts as a container for all the data that needs to be kept track of between components in the app.

Without this, you'd need to rely on passing props from the top down to the child components that need that data. Not only does this end up being inconducive when you hand off the work to another dev on your team, but it's prone to errors if you're not keeping close track of the props coming in and out.

It's much easier, then, to relegate component state to keep track of basic data, and then subscribe components to changes in the store.
