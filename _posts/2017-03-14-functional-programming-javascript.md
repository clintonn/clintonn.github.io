---
layout: post
title: "Compress like your conversion rate depends on it"
date: 2017-03-14-12:30:00
categories: [code]
---

![compression example](http://www.exposureguide.com/images/file-formats/jpeg-compression.jpg)

You'd think that years and years of 3G networks and the [shamelessly slow growth](https://en.wikipedia.org/wiki/List_of_countries_by_Internet_connection_speeds#Akamai_Q4_2015_global_average_connection_speeds_rankings) of broadband speeds may have started developers on the right foot: building pages for the neediest user. After all, most of us have had to consider the oft-mentioned but rare rural visitor rolling in with IE8 and below.

Here's what those users generally need: **fast, lean websites that serve up a workable user experience** in a few seconds flat, if not less. Not only will developing for these users only better the experience all visitors have on the whole, but it'll ensure that fewer and fewer users wait seconds for a DOM-dependent script to fire off. I've been there, you've been there — you've definitely seen how frustrating one-bar web browsing on mobile can be. Let's fix that.

I found that the key towards building towards faster loading without compromising user experience is in lazy loading, enabling gzip compression, and offloading dependency scripts to CDNs that likely have better uptime and speed than your personal server.

I'll go over each of them briefly.

### Lazy loading

Lazy loading in web development is the practice of only loading assets when they're ready to be displayed, and in their place, offering lower-resolution versions until they do. This'll make it easier for larger, image-heavy sites to load images progressively, making for a better infinite scroll experience.

There are a number of plugins and packages developed just for this purpose, including the [jQuery lazy load plugin](https://github.com/tuupola/jquery_lazyload), but my personal favorite is [lazysizes](https://github.com/aFarkas/lazysizes/blob/gh-pages/README.md), which doesn't require jQuery as a dependency.

Here's a demo of lazysizes in action:

<iframe name='quickcast' src='http://quick.as/embed/9gryh940b' scrolling='no' frameborder='0' width='100%' allowfullscreen></iframe><script src='http://quick.as/embed/script/0.87'></script>

### Enabling gzip compression

If you operate your own server, you can easily set gzip compression. When users visit your website, and their client has an `Accept-Encoding` [header value](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Encoding) that includes gzip, you can serve them compressed assets — script files, CSS, typefaces, images — anything, even html files. It's a step above and beyond just serving minified assets, but there are plenty of tutorials for enabling gzip compression out there, usually by declaring it in a `.htaccess` file in your root site directory.

[Here's one for Apache](https://css-tricks.com/snippets/htaccess/active-gzip-compression/) and here's a much longer one for [nginx](https://www.digitalocean.com/community/tutorials/how-to-add-the-gzip-module-to-nginx-on-ubuntu-14-04).

### Using CDNs

CDN are a viable way of serving script and css files, and most come gzipped and compressed by default. Most CDNs also give you the option of linking to component parts of libraries (for instance, Bootstrap's grid module) rather than linking to the core. On [my own site](https://clinton.so), I used cdnjs to serve a 128KB minified and gzipped version of three.

![three js minified by cloudflare](http://i.imgur.com/eIXGjfe.png)

### The future

Much like `<video>` tags introduced in HTML5 that serve up multiple mimetypes depending on your user-agent, browsers are adding support for [a new HTML tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture) called `<picture>`. The tag allows you to specify a `<source>` tag that defines the media type or user-agent to serve an image or svg to, in the `srcset` attribute. The element uses a child `<img>` tag as a fallback for when all `<source>` conditions fail.

So for instance, you might imagine a smaller image being served up to a mobile device, based on smaller width breakpoint:

```html
<picture>
  <source srcset="example-logo-sm.jpg" media="(max-width: 480px)">
  <source srcset="example-logo-md.jpg" media="(max-width: 720px)">
  <source srcset="example-logo-lg.jpg" media="(max-width: 1280px)">
  <img src="example-logo-fallback.jpg" alt="original image fallback" />
</picture>
```

Easy, and you don't have to jerry-rig breakpoint scripts in your JS files anymore.
