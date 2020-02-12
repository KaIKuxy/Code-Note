---
description: 'React Router https://reacttraining.com/react-router/core/guides/philosophy'
---

# Routing in Single-Page-App

## How to do multiple routing in a single page app?

![Routing is done by React in a SPA](../.gitbook/assets/image%20%2814%29.png)

Two packages for routing: **react-router** & **react-router-dom** \(react-router-dom has dependency on react-router so just install react-router-dom\)

Wrap the whole App with `BrowserRouter` from react-router-dom

```jsx
import React, { Component } from 'react';
import { BrowserRouter } from 'react-router-dom';

class App extends Component {
  render() {
    return (
      <BrowserRouter>
        <div className="App">
          // ...
        </div>
      </BrowserRouter>
    );
  }
}

export default App;
```

Render different component using `Route` component from react-router-dom

## Two ways of routing component

### Use component prop

```jsx
<Route path="/" exact component={RoutedComponent} />
```

The component will get the props from Route component \(`match`, `location` and `history`\)

With component prop, we cannot send props to the routed component.

### Use render prop

```jsx
<Route path="/" exact render={() => <h1>Home</h1>} />
// The Route component will match the path as a prefix with the current url
// Use exact to render the route only match exactly with the path
```

Wrap switch component by `Switch` from `react-router-dom` to only render one route-related component.

## Not &lt;a&gt; but &lt;Link&gt; / &lt;NavLink&gt;

### Who does the routing job?

React App knows the routes, not the server, so without settting up, the server will return 404 error. Server have to always forward any request from the client back to the client and returns the index page then allows React to work with the url.

If the url is example.com/my-app, then set the basename prop of the component BrowserRouter to `my-app`.

![](../.gitbook/assets/image%20%285%29.png)

If the link is `<a>`, then the page is reloaded again and again, and the state is lost.

### Use `Link` component from react-router-dom

```jsx
// Either a string or an object
<Link to="/">Link</Link>
<Link to={{
    pathname: '/new-post',
    hash: '#submit',
    search: '?quick-submit=true'
}}>Link</Link>
```

`Link` component prevents from sending new request.

### How to get the routing props

Only the component that is specified in a `Route` component has the routing props. Otherwise, to get the routing information:

1. Use `withRouter` from react-router-dom to wrap the component and add route-related props from the descendent component, the closest one that has routing information.

```jsx
export default withRouter(post);
```

2. Pass the props to the inner component.

### Use NavLink to set styles

Use `NavLink` component from react-router-dom. If not specified by prop `activeClassName`, the NavLink will get a active class .active when it matches the current url.

Remember to set exact to `NavLink` component to prevent multiple active class.

```jsx
<NavLink
    exact
    to="/"
    activeClassName="my-active"
>Home</NavLink>
```

Setting `activeStyle` to set up inline active style.

```jsx
<NavLink
    exact
    to="/"
    activeClassName="my-active"
    activeStyle={{
        color: '#fa923f',
        textDecoration: 'underline'
    }}
>Home</NavLink>
```

## Absolute path & relative path

### Way of getting absolute path

Set prop `to` in the `Link` component get absolute path and append it to the root domain \( + basename configured in the  `BrowserRouter` component\).

### Way of building relative path

```jsx
<Link to={{
    pathname: this.props.match.url + '/new-post',
    hash: '#submit',
    search: '?quick-submit=true'
}}>NewPost</Link>
```

## Get content in the url

Use "/:dynamic\_content" to handle dynamic content in the url, like data's id. It will match all the url with /, so remember: **more specific url first**.

```jsx
<Route path="/:id" exact component={FullPost} />
```

Retrive the dynamic content in url by **making the component route-related** and find the content in props

```jsx
componentDidMount () {
    console.log(this.props);
    // ...
}
```

![](../.gitbook/assets/image%20%283%29.png)

The path in the `match` object will still be the one with dynamic content variable, with content in `match.params`

Retrieve search key-value pair from props.location.search.

Retrieve fragment from props.location.hash

```javascript
componentDidMount() {
    const query = new URLSearchParams(this.props.location.search);
    for (let param of query.entries()) {
        console.log(param); // yields ['start', '5']
    }
}
```

## For the issue in lecture 234

When NewPost is clicked, :id route will also be matched successfully, but with id "new-post". When click a post then click NewPost, the fullpost will remain because the FullPost component's state is not null, and the fetching method is in componentDidMount, so the wrong id will not be used to fetch data.

But when the NewPost is clicked when there is no post clicked, it will throw an error.

## An alternative of `Link` & `NavLink`

Navigate using this.props.history.push

```jsx
postSelectedHandler = (id) => {
    this.props.history.push({pathname: '/' + id});
}
```

## Nested route

* hardcode the entire url
* get the current path dynamically

```jsx
<Route path={`${this.props.match.url}/:id`} exact component={FullPost} />
```

## Redirect

Use `Redirect` component

```jsx
<Switch>
    <Route path="/posts" component={Posts} />
    <Redirect from="/" to="/posts" />
    // from attribute can only be used in Switch Component
</Switch>
```

Render redirect conditionally using state. \(Usage: Set a submitted state, once submitted, set it to true, and the redirect component depend on this state\)

```jsx
render () {
    let redirect = null;
    if (this.state.submitted) {
        redirect = <Redirect to="/posts/" />;
    }
    return (
        <div className="NewPost">
            {redirect}
```

Another way of redirection: using history.push / history.replace

```jsx
postDataHandler = () => {
    const data = {
        title: this.state.title,
        body: this.state.content,
        author: this.state.author
    };
    axios.post('/posts', data)
        .then(response => {
            console.log(response)
            this.props.history.push('/posts');
            // this.setState({submitted: true});
        });
}
```

### Using guard to save prticular pages

set auth state and render the route component based on the state.

### Handling the 404 case

```jsx
<Route render={() => <h1>Not found</h1>} />
// catches all the routes not handled prior to it.
```

### Loading Routes Lazily

Split the component outside of the bundle.js provided by the webpack.

Use a higher order component to replace the original component, import the original component once the hoc is mounted and render the original component.

```jsx
import React, { Component } from 'react';

const asyncComponent = (importComponent) => {
    return class extends Component {
        state = {
            component: null
        }

        componentDidMount () {
            importComponent()
                .then(cmp => {
                    this.setState({component: cmp.default});
                });
        }

        render () {
            const C = this.state.component;

            return C ? <C {...this.props} /> : null;
        }
    }
};

export default asyncComponent;


import asyncComponent from '../../hoc/asyncComponent';
// import NewPost from './NewPost/NewPost';
const AsyncNewPost = asyncComponent(() => {
    return import('./NewPost/NewPost');
});

{this.state.auth ? <Route path="/new-post" component={AsyncNewPost} /> : null}
```

In this way, the newPost component will only be loaded once the new-post is requested by the user.

![](../.gitbook/assets/image%20%2813%29.png)

#### A new way after react 16

```jsx
import { Suspense } from 'react';
const Posts = React.lazy(() => import('./containers/Posts'));

<Route path="/posts" render()={() => <Suspense fallback={<div>Loading...<div>}> <Posts /> </Suspense>} />
// fallback is used in cases where React postpones the rendering of the wrapped component.

state = {showPosts: false};

modeHandler = () => {
    this.setState(prevState => {
        return {showPosts: !prevState.show};
    });    
}



<button onClick={this.modeHandler}>Toggle mode</button>
{this.state.showPosts? (<Suspense fallback={<div>Loading...<div>}> <Posts /> </Suspense>) : <User/>}
```

Prevent request for form submit

```jsx
submitHandler = (event) => {
    event.preventDefault();
}
```

