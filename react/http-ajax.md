# Http / Ajax

### For multi-page React application

> You probably have your application, your page where you submit a form and that sends a request to your
>
> backend and you get back a new html page with potentially new react code in there.

### For single-page React application

> If you have such a single page application, you have a strong differentiation between your frontend,
>
> your react app and your backend though. The react app and the servers still need to communicate from time
>
> to time but they don't communicate by exchanging html pages.

![](../.gitbook/assets/image%20%281%29.png)

Or send some data to the server if you want to create some resources on the server.

The server is therefore typically a RESTful API, just exposing some API endpoints to which you can send requests to get and send data from the standalone React application.

### Two ways of sending Ajax Requests

* XMLHttpRequest
* Third-party library Axios

```jsx
componentDidMount () {
    axios.get('https://jsonplaceholder.typicode.com/posts')
        .then(response => {
            const posts = response.data.slice(0, 4);
            const updatedPosts = posts.map(post => {
                return {
                    ...post,
                    author: 'Max'
                }
            });
            this.setState({posts: updatedPosts});
            // console.log( response );
        });
}

postDataHandler = () => {
    const data = {
        title: this.state.title,
        body: this.state.content,
        author: this.state.author
    };
    axios.post('https://jsonplaceholder.typicode.com/posts', data)
        .then(response => {
            console.log(response)
        });
}
```

### Lifecycles review

![Lifecycle Review](../.gitbook/assets/image%20%288%29.png)

<table>
  <thead>
    <tr>
      <th style="text-align:left">Creation</th>
      <th style="text-align:left">Called methods in order</th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Default ES6 class Feature</td>
      <td style="text-align:left">constructor(props)</td>
      <td style="text-align:left">
        <p>Call super(props)</p>
        <p><b>Do</b>: Set up State</p>
        <p><b>Don&apos;t</b>: Cause Side-Effects (e.g. send HTTP request)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">getDerivedStateFromProps(props, state)</td>
      <td style="text-align:left">
        <p><b>Do</b>: Sync State</p>
        <p><b>Don&apos;t</b>: Cause Side-Effects</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">render()</td>
      <td style="text-align:left">Prepare &amp; Structure the JSX Code</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Render Child Components</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">componentDidMount()</td>
      <td style="text-align:left">
        <p><b>Do</b>: Cause Side-Effects</p>
        <p><b>Don&apos;t</b>: Update State(triggers re-render; dont call setState
          synchronously)</p>
      </td>
    </tr>
  </tbody>
</table><table>
  <thead>
    <tr>
      <th style="text-align:left">Update</th>
      <th style="text-align:left">Called methods in order</th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">getDerivedStateFromProps(props, state)</td>
      <td style="text-align:left">
        <p><b>Do</b>: Sync State to Props</p>
        <p><b>Don&apos;t</b>: Cause Side-Effects</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">May cancel updating process</td>
      <td style="text-align:left">shouldComponentUpdate(nextProps, nextState)</td>
      <td style="text-align:left">
        <p><b>Do</b>: Decide whether to Continue or Not</p>
        <p><b>Don&apos;t</b>: Cause Side-Effects</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">render()</td>
      <td style="text-align:left">Prepare &amp; Structure the JSX Code</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Update Child Component Props</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">getSnapshotBeforeUpdate(prevProps, prevState)</td>
      <td style="text-align:left">
        <p><b>Do</b>: Last-minute DOM ops</p>
        <p><b>Don&apos;t</b>: Cause Side-Effects</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">componentDidUpate(prevProps, prevState, snapshot)</td>
      <td style="text-align:left">
        <p>Do: Cause Side-Effects</p>
        <p>Dont: Update States(triggers re-render)</p>
      </td>
    </tr>
  </tbody>
</table>Send Http requests in `componentDidMount` & `componentDidUpdate`.

When update state in componentDidUpdate, the component will be updated again and enter an infinite loop, so use data to check if we need to send request and update state.

```jsx
componentDidUpdate (prevProps, prevState) {
    console.log("[FullPost] componentDidUpdate");
    if (this.props.id) {
        if (!(this.state.loadedPost && this.state.loadedPost.id === this.props.id)) {
            axios.get(`https://jsonplaceholder.typicode.com/posts/${this.props.id}`)
            .then(response => {
                // console.log(response);
                console.log("Data received!");
                this.setState({loadedPost: response.data});
            })
        }
    }
}
```

**The update process:**

1. The post button clicked
2. `Handler` function triggered, `selectedPostId` in state changed, so the App re-rendered
3. `FullPost` component re-rendered \(1st time\) with showing post loading, \[FullPost\] componentDidUpdate printed, data request started asynchronously.
4. Data received, `loadedPost` in state changed, so the `FullPost` component re-rendered again \(2nd time\).
5. \[FullPost\] componentDidUpdate printed, data requested stopped because the `loadedPostId` is the same as `this.props.id`.

![Log Info](../.gitbook/assets/image%20%286%29.png)

The first didupdate caused by cascading re-render when The `Blog` component received the data and `setState`.

### Handle error

```jsx
componentDidMount () {
    axios.get('https://jsonplaceholder.typicode.com/posts')
        .then(response => {
            // ...
        })
        .catch(error => {
            // console.log(error);
            this.setState({error: true});
            // set as a state to render error message
        });
}
```

Using axios to handle error globally

```jsx
axios.interceptors.request.use(request => {
    console.log(request);
    // Edit requeset config
    return request;
}, error => {
    console.log(error);
    return Promise.reject(error);
});

axios.interceptors.response.use(response => {
    console.log(response);
    // Edit requeset config
    return response;
}, error => {
    console.log(error);
    return Promise.reject(error);
});


// Removing Interceptors
var myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```

> So being able to define these interceptors can be quite powerful, again a very common use case is for the request interceptor to add some common headers. For example, an authorization header. Though we also have a different way of accessing global axios configuration,

### Using Axios

```jsx
axios.defaults.baseURL = 'https://jsonplaceholder.typicode.com';
axios.defaults.headers.common['Authorization'] = 'AUTH TOKEN';
axios.defaults.headers.post['Content-Type'] = 'application/json';

// axios instance
import axios from 'axios';

const instance = axios.create({
    baseURL: 'https://jsonplaceholder.typicode.com'
});

instance.defaults.headers.common['Authorization'] = 'AUTH TOKEN FROM INSTANCE';

// instance.interceptors.request...

export default instance;
```

If set the data in state dynamically, check the elements that use the data carefully.

Set interceptors at constructor, setting it does not cause side-effect.

Remove interceptors when the component will be unmounted to prevent memory-leaking.

