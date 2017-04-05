---
title: Making a Learning Platform on React
date: 2017-02-27 15:25:00 Z
categories:
- code
layout: post
subtitle: ''
---

![Maestro hero](http://i.imgur.com/XQEku9F.png?1)

The last project I worked on at Flatiron School was [Maestro](https://learn-with-maestro.herokuapp.com), a learning platform as a single-page application. Maestro allows users to post "trails" of high-quality educational resources that guide others toward learning technical skills faster and more efficiently.

The conceit is simple and based off of a problem I experienced many, many times while learning to code.

If say, I were looking to learn JavaScript, Google's first few results would send you off to Codecademy or W3Schools. While that's all well and good (and no offense to Codecademy or W3Schools!) there are better resources out there if you're planning on making your learning into your living. When the gloves come off, you'll want to understand code better than you can copy and paste it in a live editor.

![JavaScript search results on Google](http://i.imgur.com/83kSAoq.png)

That's why I made Maestro with my partner [Jason Decker](https://github.com/curiositypaths). The platform allows you to register and add your own "trails," which could be anything as simple as "Beginner JavaScript" to "Essential Cooking Skills Everyone Needs."

After a couple days of working on it, the app is working but in its infancy. The frontend runs static files and webpack bundled React and Sass with components routed via React Router. And the backend is just a simple RESTful Rails API that responds to get and post requests for user models and resources.

Despite the basic operations happening on both sides, React's component rendering makes the user experience shine. With the exception of a potentially burdensome bundle JS file (next todo: ejecting our boilerplate React config and figuring out how to [compress and gzip](https://medium.com/@rajaraodv/two-quick-ways-to-reduce-react-apps-size-in-production-82226605771a) production files!), the client only has to click on things to make simple XHR requests to the API. The components then change state and re-render based on the response.

Let's dive under the hood for a sec. Here's a navbar component that renders controls if you're logged in, login/register options if you're not:

![Maestro navbar](http://i.imgur.com/SYIje9x.png)

```JavaScript
// navbar.js render
render() {
  let logInAndOutOutOptions
  if (this.currentUserIsSet()) {
    logInAndOutOutOptions =
    <div className="navbar__buttons__container">
      <a href={`/trails/new`}>Post a new trail</a>
      <a href={`/users/${this.props.users.currentUser.id}`}>Profile</a>
      <a href="" onClick={ this.handleLogout }>Logout</a>
    </div>
  } else {
    logInAndOutOutOptions =
    <div className="navbar__buttons__container">
      <a href='/login'>Login</a><a href='/register'>Register</a>
    </div>
  }
  return (
  <nav className="navbar container">
  // ...
      {logInAndOutOutOptions}
  // ...
  </nav>
  )
}
```

The profile links are simple anchor tags that link to `/users/:id`, and your user ID is fetched and stored in a Redux store upon the first login.

On the Router, we have container components (`App`, `Homepage`, `UserProfile`, etc) to be rendered whenever a user visits its corresponding URL pattern.

```JavaScript
// routes.js
export default (
  <Route path='/' component={App}>
    <IndexRoute component={Homepage} />
    <Route path='/register' component={UserSignUp}/>
    <Route path='/login' component={UserLogin} />
    // ...
    <Route path='/users/:id' component={UserProfile} />
    // ...
  </Route>
)
```

React Router works by altering the URL scheme, but never actually refreshing or navigating from the `index.html` page. It's a bonafide [SPA](https://en.wikipedia.org/wiki/Single-page_application).

Whenever you'd visit another page, say your user profile, the app would simply unmount unneeded components from the "previous page" and then mount the components corresponding to the Router's URL patterns. So for `/users/:id`, `UserProfile` gets rendered.

UserProfile in turn fetches user information on mount via a dispatch, which makes an XHR request to our API.

```JavaScript
// Userprofile.js
componentDidMount() {
  this.props.fetchUser(this.props.params.id)
}
// userAdapter.js
fetchUser = id => axios.get(`/users/${id}`).then( response => response.data )
// usersReducer.js
case "FETCH_USER":
  return { ...users, currentlyViewedUser: action.payload }
```

The users reducer here returns a new state based on the API's JSON response. The `UserProfile` component then uses that to populate a user profile.

```JavaScript
// GET `/users/1`
{
  "id": 1,
  "email": "clinton.ngyn@gmail.com",
  "first_name": "Clinton",
  "last_name": "Nguyen",
  "trails": [ /* 2 trail objects here... */ ]
  // ...
}
```
👆 gets used to populate 👇

![Maestro user profile](http://i.imgur.com/a1AMlPP.png)

There isn't anything fancy about the two-way communications between frontend and backend — this paradigm has been kicking for years.

But the way we solve the two-way communication gap between client and server is getting more and more graceful. Less unnecessary re-renders, heavy DOM manipulation, and iframes, and more DOM diffing magic.

All I can say is we've gone a long way from AJAX's renaissance ca. 2012 when reload-free blogs were magical.
