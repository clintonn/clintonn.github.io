---
layout: post
title: "Vues from the 6"
subtitle: "What I Made with Vue in an Hour of Fooling Around"
date: 2017-01-20-12:13:00
categories: [code]
---

![does-javascript-once](http://i.imgur.com/sK2RJHL.png)

# I want to make a todo list.

For the amount of programming we've learned, we can go about this in a hundred different ways — a Rails app that tracks users and items, a Sinatra app that does the same thing with far less baggage. Hell, we can set up an HTTP server that sets up a single page and watches for various POST requests and adds and removes items as they're done.

But instead of setting up databases, what if we just set up a single page app?

As a sort of exercise, I'm going to go through like five different ways of doing it in JavaScript, because I think everyone, myself included, has developed a weird codependent relationship with the language over its 22-year history.

## Vanilla JS

#### HTML

```HTML
<div id="todo">
  <h1>TO DO</h1>
  <ul id="todo-list">
  </ul>
</div>
<form id="add-todo">
  <label for="add-todo-item">Add To—Do Item: </label><input type="text" id="add-todo-item" />
  <input type="submit" />
</form>
```

#### JS

```JavaScript
window.onsubmit = function(event) {

 event.preventDefault()
 var val = document.getElementById('add-todo-item')
 var todoItem = document.createElement('li')
 todoItem.innerHTML= val.value

  todoItem.onclick = function() {
    this.parentNode.removeChild(this)
  }

  document.getElementById('todo-list').appendChild(todoItem)
  val.value = ''
}
```

[JSFIDDLE](https://jsfiddle.net/clintonn/jywy9qmw/)

Obviously vanilla JavaScript is a lot more verbose when it comes to selecting and appending nodes to the DOM tree. This wasn't terribly difficult, but I could do with something that's a little more low-language.

**FUN FACTOR**
![screaming-inside](https://thumb1.shutterstock.com/display_pic_with_logo/64260/548279518/stock-photo-healthcare-pain-stress-age-and-people-concept-senior-man-suffering-from-headache-at-home-548279518.jpg)

## JQUERY

#### Javascript

```JavaScript
$('form#add-todo').submit((event) => {
  event.preventDefault()
  let $val = $('#add-todo-item').val()
  let $node = $('#todo-list').append(
    `<li>${$val}<button class="remove">Remove</button></li>`
  )
  $('#add-todo-item').val('')
  $('button').click( function(event) {
    $(this).parent().remove()
  })
})
```

#### HTML, but with nested remove buttons

```HTML
<div id="todo">
  <h1>TO DO</h1>
  <ul id="todo-list">
  </ul>
</div>
<form id="add-todo">
  <label for="add-todo-item">Add To—Do Item: </label>
  <input type="text" id="add-todo-item" />
  <input type="submit" />
</form>
```

[JSFIDDLE](https://jsfiddle.net/clintonn/Lxmnc6j6/)

Selectors did me some good here, and the jQuery's slightly more forgiving `.append()` method made adding nodes to the DOM tree much easier.

**FUN FACTOR**
![still-not-there](https://thumb10.shutterstock.com/display_pic_with_logo/76219/299361773/stock-photo-confused-businessman-looking-at-laptop-computer-in-office-299361773.jpg)

### Vue

Vue.js an altogether different beast and something that I'd been looking at for a while as a sort of soft entry into React.

Vue is a framework that allows you to make extensible components that will compile back into valid HTML objects. In Vue, you specifically create Vue objects to encapsulate data and methods, much like React's component extensions:

```JavaScript
var vueable = new Vue({
  el: '#app',
  data: {
    message: 'Hello world!'
  }
})
```

And in a page, when you hook that Vue object onto the `el` selector you've set in your JS — in this case `#app` — you'll have access to the data you set for that object in templating.

```HTML
<div id="app">
  \{ { message } \}
  <!-- spits back "Hello world!" onto the DOM when you remove forward slashes -->
</div>
```

Vue also bundles in a LOT of great basic functions that aren't as syntactically sweet in vanilla JS or jQuery. For instance, there's a `v-for` directive that you can call on a component with an enumerable object in its data to, say, render each item in the view.

For instance, let's make a grocery list:

```JavaScript
var groceries = new Vue({
  el: '#grocery-list',
  data: {
    cart: [
      { productName: "apple" },
      { productName: "bananas" },
      { productName: "vegemite" }
    ]
  }
})
```

You can iterate over the object in the view like so in HTML:

```HTML
<ul id="grocery-list">
  <li v-for="item in cart">\{ { item.productName } \}</li>
</ul>
```

Kinda like rails!

#### JAVASCRIPT

```JavaScript
var todo = new Vue({
  el: '#todo-app-wrapper',
  methods: {
  addToDo: function() {
  	let val = this.newToDo.trim()
    if (!val) {
    	return
    } else {
    	this.todos.push({text: val})
    }
  },
  deleteToDo: function(item) {
    this.todos.splice(this.todos.indexOf(item), 1)
  }
  },
  data: {
    newToDo: '',
  	todos: [
    	{text: "Test todo"},
      {text: "Test todo 2"}
    ]
  }
})
```

#### HTML

```HTML
<div id="todo-app-wrapper">
  <ul>
    <li class="todo-item" v-for="todo in todos" v-on:click="deleteToDo(todo)">
      {{ todo.text }}
    </li>
  </ul>
  <label for="add-todo-field">Add todo: </label>
<input type="text" v-model="newToDo" v-on:keyup.enter="addToDo" id="add-todo-field" />
</div>
```

[FIDDLE](https://jsfiddle.net/clintonn/ahejLk9k/2/)

One of the hardest things in this lab was actually selecting the nodes I needed

**FUN FACTOR**
![thinking-emoji](http://i.imgur.com/FP5ZB2A.jpg)


### A little more about what Vue and similar MVVM JavaScript frameworks are for

Vue is what's called a Model–view–viewmodel (MVVM) JavaScript framework. The biggest difference between the MVVM and MVC paradigm is that MVC separates most of its concerns about an object's state to a server-side model — the controller runs logic on the model, retrieves information from that model, and serves it to the view.

For instance, if you were editing a blog post and hit update, the model handles the blog post's "state", then re-renders the page showing the updated state of the post.

But what if you had a single page app that didn't need any of that?

In MVVM, much of the logic handled between the model and the view is relegated to the viewmodel — the VM.

The viewmodel is, in short, responsible for maintaining a continuous connection between the JavaScript-defined models and the view, and changing the view when the state of an object or component changes.

This changes components in the view if the component changes, based on what the state of the component. The most basic example of a state-changing component is a timer, or a clock. If you look at the code for a simple clock built in InfernoJS (React's purportedly leaner, faster cousin), you'll see the following:

```JavaScript
class Clock extends Component {
  constructor() {
    super();
    // set initial time:
    this.state = {
      time: Date.now()
    };
  }

  componentDidMount() {
    // update time every second
    this.timer = setInterval(() => {
      this.setState({ time: Date.now() });
    }, 1000);
  }

  componentWillUnmount() {
    // stop when not renderable
    clearInterval(this.timer);
  }

  render() {
    let time = new Date(this.state.time).toLocaleTimeString();
  }
}
```

Once a clock is mounted into the view, the `componentDidMount()` method will fire. This sets a timer that **changes the state** — via the `this.setState()` method — every second (1000 milliseconds).

State is important! Especially when we want to make any apps that aren't super database-reliant. Correct state usage cuts down on server overhead, makes for a better user experience, and makes for a much more robust app.

---

## More reading:

**React State** — <https://medium.com/react-tutorials/react-state-14a6d4f736f5#.l9purwm1h>  
**Get Started on Vue.js** — <https://vuejs.org/v2/guide/>  
**Vue JS in Less Than 30 Minutes for Beginners** — <https://www.youtube.com/watch?v=VPUdtEf3oXI>
