---
title: Unwave
date: 2017-05-02 09:44:50 Z
categories:
- code
layout: post
subtitle: Making the one small project I've been meaning to do.
---

![unsplash hero](https://images.unsplash.com/photo-1486758206125-94d07f414b1c?dpr=1&auto=format&fit=crop&w=1200&h=800&q=80&cs=tinysrgb&crop=&bg=)

The past few weeks I've been mulling smaller, more actionable project ideas to hock code at. The idea — coming out of coming out of a coding program, or I guess any CS degree — is that you should have something small, working, and preferably fun to play around with.

Like well-informed listicles or shortform articles, they have their purpose — to be readily digestible and be a tangible for your technical ability.

That's all leading me to say I made a small thing and I finally made something that 1) works, and 2) isn't something I need to prep a 30-second pitch for; it explains itself. It's called **Unwave**, and you can see [a working demo of it here](http://unwave.surge.sh).

## The prompt

* Use Flickr, or a similar photo API to display photos, 10 at a time
* Paginate them
* When the user clicks the photo, show an overlay with a larger view that they can click out of

## Which API?

As it turns out, there are a lot of photo APIs out there to pick from with varying levels of access and power.

Being a legacy company by now, Flickr's API [is obviously expansive](https://www.flickr.com/services/api/). Not to say I wasn't down to find the right endpoints, but it's a little top-heavy for our relatively simple needs. There's also no real guarantee on the quality of the content we're getting.

Pixabay, a free stock photo service I used heavily when I was doing reporting, [also provides an API](https://pixabay.com/api/docs/). Their default endpoint is search by nature, and if you omit the search query it'll spit back *all the photos*. There's an option for editor's choice, too, and their rate limits are set at a production-friendly 5,000 requests an hour.

Pixabay, however, has an interesting directive on requests:

> To keep the Pixabay API fast for everyone, requests must be cached for 24 hours.

Makes total sense for an API that doesn't want to get spammed, but I didn't really want to set up caching for a project so small. I'll delve [into that rabbit hole](http://guides.rubyonrails.org/caching_with_rails.html) when it seems appropriate.

I settled on [Unsplash](https://unsplash.com), a similarly free photo service. [Their API](https://unsplash.com/documentation) is slim, their photos are always crisp and serve no agenda other than looking Instagram-ready, and their documentation was a lot easier to parse (contents ordered by model, then what data you want from that model).

The only catch: 50 requests an hour for dev builds. So no fooling around.

## Getting started

I decided on [Preact](https://preactjs.com), because building modular components is dead easy with React, and encapsulating data through props and state makes sense. Preact gives me that functionality in a smaller package.

I used a [boilerplate](https://github.com/developit/preact-boilerplate) which ended up having a lot of good things — a basic unnested router, a perfectly serviceable webpack config, and PWA configs (cool but not needed). But more than that it defined a component-centric file structure:

![Component file tree, with js, less, and dependent assets in their respective component folders](http://i.imgur.com/nMTxBb0.png)

I decided to make three containers for three of Unsplash's API endpoints: `Home` for Unsplash's daily stream (`/photos`), `Random` for their random photos (`/photos/random`), and `Curated` for their curated sets (`/photos/curated`).

## Making a barebones forwarding API

One of the more fun problems I had was figuring out how to route API calls to Unsplash, because exposing keys of any sort on the client is a big no-no.

For the listening server, I could have used a Sinatra server to keep it lean, but because Heroku's Rails deployment is so streamlined, I just generated a new Rails API without ActiveRecord or database configs:

```
$ rails new unwave_api --no-test-framework --O
```

The API simply listens for incoming requests and passes it off to a `PhotosController` action to forward to Unsplash's API. The action strings together the URL and calls the appropriate endpoint with supplied params, and forwards the response back:

```ruby
def unsplash_call
  @caller = HTTP.auth("Client-ID #{ENV['UNSPLASH_CLIENT_ID']}")
  @queries = params[:queries]
  @endpoint = params[:endpoint]
  @queries = @queries.map { |k, v| &#{k}=#{v} }
  @res = @caller.get("https://api.unsplash.com/#{@endpoint}?#{@queries}")
  render json: JSON.parse(@res)
end
```

Most complex apps work pretty similarly by slinging together data between multiple APIs. This API just forwards requests — a necessary step since we don't want API keys on the frontend at all.

The forwarding fortunately makes no noticeable difference on the client side. Just as fast as using an internal API.

## Components

Now that we have a forwarding API, I laid out basic guidelines for what my components should do:

1. A `<PhotoCard>` component will take a single photo object Unsplash's JSON response and create a photo card containing all the information about the photo. It'll link to the photographer and have access to the full view of the photo for the overlay.
2. The container components, `<Home>`, `<Random>`, and `<Curated>`, should make the appropriate API calls to load the initial set of photos.
3. In every container component, there should be a "load more" button to load more content because it's 2017, man. The button sends an API call with a page query (`&page=1`, `&page=2`) that increments with every call.

Much of this was rote JSX and CSS work, but here's one nice observation that came out of doing the load more button.

The load more button was necessary to keep to the 50 reqs/hr limit, but under more lenient APIs you can easily imagine setting up an **infinite scroll feature** to request more pages on scroll.

So instead of:

```js
// jsx
<button class={`${style.loadMore__button}`} onclick={this.loadMore.bind(this)}>Load More</button>

// onclick cb
async loadMore() {
  this.setState({...this.state, loading: true})
  let photos = await axiosClient.get(`photos/more`, {
    ...params
  })
  this.setState({
    ...this.state,
    photos: [...this.state.photos, ...photos.data],
    // etc
  })
}
```

You can do:

```js
componentWillMount() {
  window.addEventListener("scroll", (e) => {
    // if scrolltop hits 90% of page height, fire cb
    if (document.body.scrollTop + window.innerHeight > document.body.scrollHeight * .9) {
      this.loadMore()
    }
  }).bind(this)
}

async loadMore() {
  // ditto above
}
```

Very neat.

## What I learned

Some of the differences I found when working in Preact versus React:
- React components need `React` in scope to transform from JSX to JS — entirely syntactic sugar for `React.createElement()`. Preact has a version of that transformer called `h` — short for Preact's flavor of [hyperscript](https://github.com/hyperhype/hyperscript). The only difference I saw was that in Preact's JSX, `class` literally means the HTML `class` attribute. No more `className`!
- No SyntheticEvents that you're used to in React — use vanilla `createEventHandler` and bind it correctly.
- `render()` takes in a node as a third arg that the component replaces, rather than appends on.

Deployment-wise, it was fun and easy to build this thing and see the bundle size actually end up a fraction of a React production bundle. For 39KB over the wire, we're getting a lot, and much of it is extensible and modular! And if hosting static projects on Github weren't easy enough, deploying on [Surge](https://surge.sh) was stupidly easy. 10/10

And while on the surface this photo viewer's just a presentational layer, it actually provides a view that Unsplash itself doesn't give users simply visiting the site. You don't have easy, no-signup access to a "random" photo view, and their curated collection is tucked away across several pages.

And yet we have endpoints for them and more. Much thanks to the devs for making that available, and for giving me a pretty sweet project to build out over a day. ✌️
