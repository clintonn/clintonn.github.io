---
layout: post
title: "Making a Learning Platform on React"
subtitle: ""
date: 2017-02-27-15:25:00
categories: [code]
---

![Maestro hero](http://i.imgur.com/XQEku9F.png?1)

The last project I worked on at Flatiron School was [Maestro](https://learn-with-maestro.herokuapp.com), a learning platform that allows users to post "trails" of educational resources that guide you toward learning technical skills better and faster.

The conceit is simple and based off of something I experienced many, many times while learning to code.

If I, say, were looking to learn JavaScript, Google PageRank's first few thoughts would be to send you off to Codecademy and W3Schools. While that's all well and good (and no offense to Codecademy or W3Schools!) there are better resources out there if you're planning on making your learning into your living at some point. When the gloves come off, you'll want to understand code better than you can copy and paste it in a live editor.

![JavaScript search results on Google](http://i.imgur.com/83kSAoq.png)

But if you really wanted to learn something right and well, you'd have to sift through the cruft of Google search results, subreddits, and potentially outdated guides. Not really all that encouraging if you're a beginner and want to be led to the right places. The road to learning the right stuff curves every year.

That's why I made Maestro with my partner [Jason Decker](https://github.com/curiositypaths). The platform allows you to register and add your own "trails," which could be anything as simple as "Beginner JavaScript" to "Essential Cooking Skills Everyone Needs."

After a couple days of working on it, the project is still in its infancy. The frontend runs static files and webpack bundled React and Sass with components routed via React Router. And the backend is just a simple RESTful Rails API that responds to get and post requests for user models and resources.

Despite the basic operations happening on both sides, React's component rendering makes the user experience really shine. With the exception of a potentially burdensome bundle JS file (next todo: ejecting our boilerplate React config and figuring out how to [compress and gzip](https://medium.com/@rajaraodv/two-quick-ways-to-reduce-react-apps-size-in-production-82226605771a) production files!), the client only has to click on things to make simple XHR requests to the API. The components then change state and re-render based on the response, and the experience ideally goes off a single page reload.

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

There isn't anything fancy about the way the server spits back data, nor is there anything remotely crazy being done with the React components on the frontend dispatching XHR requests to the server.

But its simplicity is magical considering this is how most of the modern internet: not by hocking back lots of unnecessary data on every link clicked, but by allowing clients to request just enough information without unnecessary reloads or heavy DOM manipulation.

All I can say is we've gone a long way from AJAX's renaissance ca. 2012. There are plenty of bugs to pick out from our project and actual fun social features to stub out (Following! Voting!).

And even though our project's nowhere near perfect, we learned a lot about the power of React and how efficiently it handles data in and out. 
