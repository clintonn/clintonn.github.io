---
layout: post
title: "Where Vue and React divide on templating"
subtitle: ""
date: 2017-02-02-23:25:00
categories: [code]
---

![react-logo](https://facebook.github.io/react/img/logo_og.png)

After spending a few days learning about React components and state, I've come to respect JSX for its flexibility, and how much more readable it is than reading `React.createComponent` until I lie down and die.

Compared to PHP, JSX feels like a breath of fresh air — it's almost HTML (XML more precisely), with JavaScript mixed in between.

In a similar templating language in Ruby, ERB, you'd wrap rendered Ruby expressions in `<%= %>` tags. In JSX, everything compiles down to JavaScript. Anything in curly braces `{}` are interpreted in plain JavaScript, which makes it easy to, say, render or exclude components based on evaluated logic in JS.

For instance, in the contrived example below, we're setting a `welcomeNav` component that displays a login form if you're logged out, and a greeting with a logout button if you're logged in.

```JavaScript
import LoginButton from 'LoginButton'
class WelcomeScreen extends React.Component {

  render() {

    var welcomeNav = loggedIn ?
    (<div>Welcome! <LoginButton onClick={this.doLogout} /></div>) :
      (<div><input type="text" />
      <input type="password" />
      <LoginButton onClick={this.doLogin} />
      </div>)

    return (
       { welcomeNav }
    )
  }
}
```

I've also seen how messy it can get when placing any logic in the `render`.

Despite looking uncannily like HTML, you can pass in **any** amount of logic into the component's props, making the task of divorcing logic from the presentation less and less easy to avoid.

For example, in a recent exercise, I had to map an array of `Pet` components, each of which have an `isAdopted` prop that evaluates true or false based on the pet's inclusion in an `adoptedPets` array.

The code for that looks something (exactly) like this:

```JavaScript
mapPets() {
  let pets = this.props.pets
  let adoptedPets = this.props.adoptedPets

  return pets.map(petProps => {
    return <Pet pet={petProps}
    isAdopted={adoptedPets.includes(petProps.id)}
    onAdoptPet={this.props.onAdoptPet}
    key={pets.indexOf(petProps)} />
  })
}
```

There's also an intense amount of setup to actually get to where we're at now:

1. You have to import React into all component dependencies.
2. To serve components onto a webpage, you have to bundle your component files, feed `.babelrc` your ES2015 and React presets to have them actually transpile your JSX into grokkable JavaScript.
3. You have to create a JavaScript runner file that imports react-dom, react, and all components to render them onto the DOM.

If I hadn't been given a runthrough of the setup, it would have taken me at least a day or two to figure out how to configure Webpack and Babel, simply for the pleasure of making a simple hello world app.

## But, like, what about Vue?

If it wasn't apparent from my [previous post](/code/2017/01/20/javascript.html), I am a huge Vue stan.

Apart from being extremely forgiving to developers who want to toss in a script file and hit the ground running, it does a lot of the same things React does — provide composable, state-aware UI components.

Vue works through Vue instances, which mount onto elements through a selector specified in `el`. Vue uses the variable `vm` to denote viewmodels — the actual containers holding component parts.

```
// HTML

<div id="helloWorld">｛｛ message ｝｝</div>

// JS

var vm = new Vue({
  el: '#helloWorld',
  data: {
    message: 'Hello World!'
  }
})
```

<script async src="//jsfiddle.net/clintonn/0q6vw6p1/1/embed/"></script>

You can also create components in the same way, pass in expected props in a `props` array on the child.

Vue instances' `data` objects operate in a similar fashion to React components' state — you can pass parent states and callback functions across child components through props, and even use those props in rendering.

These components are placed on the DOM through a template key, which is an HTML string, e.g.

```JavaScript
Vue.component('custom_component', {template: '<div>My custom component</div>'})
```

After you register the component, whether globally or within the scope of a Vue instance, you can call the component by quite literally putting it in the HTML file:

```HTML
<div id="app">
  <custom_component></custom_component>
</div>

<!-- rendered as <div>My custom component</div> -->
```

<script async src="//jsfiddle.net/clintonn/8kndhhLz/2/embed/"></script>

A good note here is that you have the option of literally mounting the components you want straight into the HTML file, whereas with React, you'd have to mount the components through react-dom or react-router.

If you're a little more married to the idea of separating JS and HTML, Vue's component system makes it easy to work straight with the HTML file instead of mounting to specific el IDs.
