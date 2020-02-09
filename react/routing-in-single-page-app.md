# Routing in Single-Page-App

![](../.gitbook/assets/image%20%2812%29.png)

Two packages for routing: react-router & react-router-dom

Wrap the whole App with `BrowserRouter` from `react-router-dom`

```jsx
import React, { Component } from 'react';
import { BrowserRouter } from 'react-router-dom';

import Blog from './containers/Blog/Blog';

class App extends Component {
  render() {
    return (
      <BrowserRouter>
        <div className="App">
          <Blog />
        </div>
      </BrowserRouter>
    );
  }
}

export default App;
```

Render different component using `Route` component from `react-router-dom`

```jsx
<Route path="/" exact render={() => <h1>Home</h1>} />
// The Route component will match the path as a prefix with the current url
// Use exact to render the route only match exactly with the path
```

Load component directly:

```jsx
<Route path="/" exact component={Posts} />
```

If the link is a tag, then the page is reloaded again and again, and the state is lost.

Use `Link` component from `react-router-dom`

```jsx
// Either a string or an object
<Link to="/">Home</Link>
<Link to={{
    pathname: '/new-post',
    hash: '#submit',
    search: '?quick-submit=true'
}}>NewPost</Link>
```

Link prevents from sending new request.

Use withRouter from `react-router-dom` to wrap the component and add route-related props from descendent component

```jsx
export default withRouter(post);
```

Setting `to` in Link component get absolute path and append it to the root domain.

Way of building relative paths:

```jsx
<Link to={{
    pathname: this.props.match.url + '/new-post',
    hash: '#submit',
    search: '?quick-submit=true'
}}>NewPost</Link>
```

Use `NavLink` from react-router-dom to get active class `.active`. Also remember to set exact to `NavLink` component to prevent multiple active class.

Setting `activeClassName` to use custom active class.

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

Use "/:dynamic\_content" to handle dynamic content in the url, like data's id. And put higher-priority router at front.

```jsx
<Route path="/:id" exact component={FullPost} />
```

Retrive the dynamic content in url by making the component route-related and find the content in props:

```jsx
componentDidMount () {
    console.log(this.props);
    // ...
}
```

![](../.gitbook/assets/image%20%283%29.png)

The path in the `match` object will still be the one with dynamic content variable, with content in `match.params`

retrieve search key-value pair from props.location.search.

retrieve fragment from props.location.hash

```javascript
componentDidMount() {
    const query = new URLSearchParams(this.props.location.search);
    for (let param of query.entries()) {
        console.log(param); // yields ['start', '5']
    }
}
```

Wrap switch component by `Switch` from `react-router-dom` to only render one route-related component.

### For the issue in lecture 234

When NewPost is clicked, :id route will also be matched successfully, but with id "new-post". When click a post then click NewPost, the fullpost will remain because the FullPost component's state is not null, and the fetching method is in componentDidMount, so the wrong id will not be used to fetch data.

But when the NewPost is clicked when there is no post clicked, it will throw an error.

Navigate using this.props.history.push

```jsx
postSelectedHandler = (id) => {
    this.props.history.push({pathname: '/' + id});
}
```

### Nested route

* hardcode the entire url
* get the current path dynamically

```jsx
<Route path={`${this.props.match.url}/:id`} exact component={FullPost} />
```

### Redirect

Trivial way, have multiple routes that render the same component.

Using Redirect component

```jsx
<Switch>
    <Route path="/new-post" component={NewPost} />
    <Route path="/posts" component={Posts} />
    <Redirect from="/" to="/posts" />
    // from attribute can only be used in Switch Component
</Switch>
```

Render redirect conditionally using state. \(Set a submitted state, once submitted, set it to true, and the redirect component depend on this state\)

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

