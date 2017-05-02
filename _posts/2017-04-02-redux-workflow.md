---
title: A basic Redux workflow with React
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

The basic Redux flow, when paired with React, works like this.

### Store

The store itself is a JavaScript object with keys relating to different parts of your program. The store is created with `createStore`, [a method][createStore] that takes in a single reducer, and optionally takes in a preloaded initial state and an enhancer (middleware, time-travel, etc).

It's important to note that while the store requires a single reducer, you're actually better off separating the concerns of multiple parts of the store to their own reducers (e.g. usersReducer, postsReducer). Redux can combine these reducers by [a function][combineReducers] called `combineReducers`, which takes in an object containing destructured reducers and optionally which keys in store they correspond to.

**Example**:

```js
import { combineReducers } from 'redux'
import usersReducer from '../reducers/usersReducer'
import postsReducer from '../reducers/postsReducer'

const rootReducer = combineReducers({
  usersReducer,
  postsReducer
})

export default rootReducer
```

### Actions and Action Creators

Actions are both the timestamp and the payload for different user actions that will change the state of the app. When a user registers, it makes sense for the app to know on a top level who the current user is — it's the user that just signed up.

You can encapsulate this logic in the usersReducer as such:

```js
// usersReducer.js
const initialState = {
  users: [],
  currentUser: undefined
}
const usersReducer = (users=initialState, action) => {
  switch(action.type) {
    case "LOGIN_USER":
      return { ...users, currentUser: action.payload }
      // ...
    default:
      return users
  }
}
```

If you'll notice, the reducer takes an action, which describes which switch action the reducer should refer to. The action will have a payload, which is expected to be a user object.

Here's what the actual action looks like, from an app I'm working on:

```js
export const createUser = params => {
  let userObj = userAdapter.createUser(params)
  return {
    type: "CREATE_USER",
    payload: userObj
  }
}
```

Here, `createUser` is itself an action creator, and its return value is the action. `createUser` simply fires off a method from an adapter, and assigns that response to the payload. When the Promise resolves, the payload gets properly passed to the reducer with the user object.

Actions need not only have payloads — they can have other keys that you can assign to change part of your app. Like if you logged in, you might want to return a flag that tells your app to send a welcome flash message.

### Reducers

Reducers basically take actions and their payloads and figure out the next state object based on that. In our reducer above, the reducer takes in a state, which can be assigned a default value. Here I gave it some basic structure.

The reducer is a pivotal part of why Redux is an awesome example of functional programming. Instead of mutating the state and introducing possibly unpredictable side effects app-wide, the reducer returns an entirely new object. This makes it possible for Redux to do things like [time travel][timetravel] and undo actions.

### Provider

The Provider is a parent container that ties everything together. It's a react-redux binding, takes in a store, and makes it globally available to any component that's mapped or connected to it.

```js
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import rootReducer from '../reducers/index'
import ReactDOM from 'react-dom'
import React from 'react'
import routes from './routes.js'
const store = createStore(rootReducer)

ReactDOM.render(<Provider store={store}>{ routes }</Provider>)
```

### Making Components Aware of the Store

Without Redux, React components may only be aware of state through props chained downward through parents. This can not only get messy quickly, but it also pollutes the prop space for components where the state might not even be relevant.

React Redux does away with this by providing a way for components to connect to the store: the `connect` function.

The `connect` [function][usage-w-redux] takes in four arguments, but we're mostly concerned with the first two: `mapStateToProps` and `mapDispatchToProps`.

`mapStateToProps` is a function that returns an object defining how a part of the store should be mapped to the prop. If a component, for instance, has need of the user key in store, you could do something like:

```js
const mapStateToProps = store => {
  return {
    users: store.users,
    currentUser: store.users.currentUser
  }
}
```

And once the component is `connect`ed you'll be use `this.props.users` as if you had passed down props normally down the chain.

`mapDispatchToProps` is expected to be a single action creator or an object containing action creators. Using `mapDispatchToProps`, you can make dispatches available to the component via its props.

A good use case for this might be to dispatch a `setCurrentUser` action for a register form. In the form event handler, you can structure the parameters, and then fire off the dispatch function with those parameters.

A handy function for binding the action creators is simply `bindActionCreators` from React Redux.

```js
import { bindActionCreators } from 'react-redux'
import { loginUser } from '../actions/userActions'

const mapDispatchToProps = dispatch => { return bindActionCreators({ loginUser }, dispatch) }
```

After creating your `mapDispatchToProps` and `mapStateToProps`, you can connect the two to your component via `connect`, and you can export it in one go.

```js
export default connect(mapStateToProps, mapDispatchToProps)(MyComponent)
```

### Conclusions

That was a whole lot. But the beauty of this workflow is that it makes it easy for components to cherry-pick what parts of state they need to be aware of, and it gives devs finely tuned control over what dispatches components can have access to.

this beats chaining down props and callback functions by a mile, IMO.

[combineReducers]: http://redux.js.org/docs/api/combineReducers.html
[createStore]: http://redux.js.org/docs/api/createStore.html
[timetravel]: https://onsen.io/blog/react-redux-devtools-with-time-travel/
[usage-w-redux]: http://redux.js.org/docs/basics/UsageWithReact.html
