# Components & React Internals

### Structures

![A better src structure](../.gitbook/assets/image%20%283%29.png)

### Stateful Components & Stateless Components

Stateful Component \(or container\) is a component that manages state. \(Either using this.setState in class-based component or using React Hooks in functional component\)

Stateless Component \(or presentational component\) is a component that does not manage state.

### Class-based Component vs Functional Components

| class-based | functional |
| :--- | :--- |
| class XY extends Component | const XY = props =&gt; {...} |
| Access to State | Access to State \(useState\(\)\) |
| Lifecycle Hooks | Lifecycle Hook \(not able\) |
| Access State and Props via "this" | Access Props via "props" |
| Use if you need to manage State or access to Lifecycle Hooks and you don't want to use React Hooks! | Use in all other Cases |

## [Component Lifycycle](https://reactjs.org/docs/react-component.html#the-component-lifecycle)

Only available in class-based components.

![Lifecycle Diagram](../.gitbook/assets/image%20%2811%29.png)

```jsx
constructor()
getDerivedStateFromProps()
getSnapshotBeforeUpdate()
componentDidCatch()
componentWillUnmount()
shouldComponentUpdate()
componentDidUpdate()
componentDidMount()
render()
```

### Creation

<table>
  <thead>
    <tr>
      <th style="text-align:left"></th>
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
</table>### Update \(when props or state change\)

<table>
  <thead>
    <tr>
      <th style="text-align:left"></th>
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
</table>### useEffect Hooks

Executed for every render cycle of the component.

\(componentDidMount & componentDidUpdated combined in one effect\)

#### [How to control the useEffect's execution condition](https://www.apodment.com/)

> \(alias\) useEffect\(effect: React.EffectCallback, deps?: React.DependencyList\): void  
> import useEffect
>
> Accepts a function that contains imperative, possibly effectful code.
>
> _@param_ `effect` — Imperative function that can return a cleanup function
>
> _@param_ `deps` — If present, effect will only activate if the values in the list change.

React allows multiple useEffect in a component to set different effect.

```jsx
// Executed each time persons changed
useEffect(() => {
        console.log('[Cockpit.js] useEffect');
        setTimeout(() => {
          alert('Saved data to cloud!');
        }, 1000);
    }, [props.persons]);
    
// Executed with no dependencies, so only executed at the first time
// componentDidMount
useEffect(() => {
        console.log('[Cockpit.js] useEffect');
        setTimeout(() => {
          alert('Saved data to cloud!');
        }, 1000);
    }, []);
```

### cleanup work

class-based component: componentWillUnmount

[functional component](https://reactjs.org/docs/hooks-reference.html#cleaning-up-an-effect)

> **Cleaning up an effect**
>
> Often, effects create resources that need to be cleaned up before the component leaves the screen, such as a subscription or timer ID. To do this, the function passed to `useEffect` may return a clean-up function. For example, to create a subscription:
>
> ```jsx
> useEffect(() => {
>   const subscription = props.source.subscribe();
>   return () => {
>     // Clean up the subscription
>     subscription.unsubscribe();
>   };
> });
> ```
>
> The clean-up function runs before the component is removed from the UI to prevent memory leaks. **Additionally, if a component renders multiple times \(as they typically do\), the previous effect is cleaned up before executing the next effect.** In our example, this means a new subscription is created on every update.

```jsx
// An example of clearup
useEffect(() => {
        console.log('[Cockpit.js] useEffect');
        const timer = setTimeout(() => {
          alert('Saved data to cloud!');
        }, 1000);
        return () => {
          clearTimeout(timer);
          console.log('[Cockpit.js] cleanup work in useEffect');
        };
    }, []);
```

### Optimization: use shouldComponentUpdate

React always goes ahead and re-renders the entire component tree.

#### class-based components

```jsx
class Persons extends Component {
    shouldComponentUpdate(nextProps, nextState) {
        console.log('[Persons.js] shouldComponentUpdate');
        if (nextProps.persons !== this.props.persons) {
        // only compares with the pointer!!!
        // useful here because when updated, a new objects is create in the code
            return true;
        } else {
            return false;
        }
    }
```

**using PureComponent which already implements shouldComponentUpdate with a complete props check.**

#### functional components

wrap the component with React.memo\(\)

the component will be re-rendered only if the props are changed

### How does React Updates the DOM

The render method being called does not immediately also render this to the real DOM.

![](../.gitbook/assets/image%20%282%29.png)

Rendering Adjacent Elements

* Use list with keys
* Use div \(Become a real div in the real DOM\)
* Use higher-order components aux \(Virtual Wraper\)
* React.Fragment same as aux

```jsx
const aux = props => props.children;
export default aux;


import Aux from '../../../hoc/Aux';
class Person extends Component {
    render() {
        console.log('[Person.js] rendering...');
        return (
            <Aux>
                <p onClick={this.props.click}>
                    I'm {this.props.name} and I am {this.props.age} years old!
                </p>
                <p>{this.props.children}</p>
                <input
                    type="text"
                    onChange={this.props.changed}
                    value={this.props.name}
                />
            </Aux>
        );
    }
    
};
```

## Higher-order Component

Two forms of HoCs

```jsx
const withClass = props => (
    <div className={props.classes}>
        {props.children}
    </div>
);


const withClass = (WrappedComponent, className) => {
    return props => (
        <div className={props.classes}>
            <WrappedComponent />
        </div>
    )
};


export default withClass(App, classes.App);
```

Passing unknown props in HoCs

```jsx
const withClass = (WrappedComponent, className) => {
    return props => (
        <div className={className}>
            <WrappedComponent {...props} />
        </div>
    )
};
```

## Setting State Correctly

**setState does not immediately trigger an update of the state of the component in a re-render cycle, but scheduled by React and performed the update and re-render cycle when it has the resourece to do that, so this.state is not guaranteed to be the latest state.**

```jsx
// Incorrect method
this.setState({ persons: persons, changeCounter: this.state.changeCounter + 1 });


// Correct method
this.setState( (prevState, props) => {
    return { persons: persons, changeCounter: prevState.changeCounter + 1 };
});
```

## Using [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html) to check props' type during development.

```jsx
import PropsTypes from 'prop-types';

Person.propTypes = {
    click: PropsTypes.func,
    name: PropsTypes.string,
    age: PropsTypes.number,
    changed: PropsTypes.func
};

```

## Refs

### refs in class-based components

React supports a special attribute that you can attach to any component. The `ref` attribute can be an object created by [`React.createRef()` function](https://reactjs.org/docs/react-api.html#reactcreateref) or a callback function, or a string \(in legacy API\). When the `ref` attribute is a callback function, the function receives the underlying DOM element or class instance \(depending on the type of element\) as its argument. This allows you to have direct access to the DOM element or component instance.

Use refs sparingly. If you find yourself often using refs to “make things happen” in your app, consider getting more familiar with [top-down data flow](https://reactjs.org/docs/lifting-state-up.html).

```jsx
class Person extends Component {

    componentDidMount() {
        this.inputElement.focus();
    }

    render() 
        return (
            <Aux>
                // ...
                <input
                    type="text"
                    ref={(inputEl) => { this.inputElement = inputEl }}
                    onChange={this.props.changed}
                    value={this.props.name}
                />
                
                
class Person extends Component {

    constructor(props) {
        super(props);
        this.inputElementRef = React.createRef();
    }

    componentDidMount() {
        this.inputElementRef.current.focus();
    }

    render() {
        return (
            <Aux>
                // ...
                <input
                    type="text"
                    ref={this.inputElementRef}
                    onChange={this.props.changed}
                    value={this.props.name}
                />
```

### refs in functional components

use useRef to create a reference in functional components

```jsx
const toggleBtnRef = useRef(null);

useEffect(() => {
        console.log('[Cockpit.js] useEffect');
        // setTimeout(() => {
        //   alert('Saved data to cloud!');
        // }, 1000);
        toggleBtnRef.current.click();
        return () => {
          // clearTimeout(timer);
          console.log('[Cockpit.js] cleanup work in useEffect');
        };
    }, []);
  
return (
        <div className={classes.Cockpit}>
            // ...
            <button ref={toggleBtnRef} className={btnClass}
            onClick={props.clicked}>Toggle Persons
            </button>
        </div>
    );
```

## Props Chain Problem & [Context](https://reactjs.org/docs/context.html)

Context provides a way to pass data through the component tree without having to pass props down manually at every level.

In a typical React application, data is passed top-down \(parent to child\) via props, but this can be cumbersome for certain types of props \(e.g. locale preference, UI theme\) that are required by many components within an application. Context provides a way to share values like these between components without having to explicitly pass a prop through every level of the tree.

**Changes in a context object would not cause a re-render cycle**

```jsx
const authContext = React.createContext({
    authenticated: false,
    login: () => {}
});

// Provider
// Wrap everything that need to use context with the Provider
<AuthContext.Provider value={{authenticated: this.state.authenticated, login: this.loginHandler}}>
    {this.state.showCockpit ? 
    <Cockpit
        title={this.props.appTitle}
        showPersons = {this.state.showPersons}
        personsLength = {this.state.persons.length}
        clicked = {this.togglePersonsHandler}
    /> : null}
    {persons}
</AuthContext.Provider>
        
// Consumer
// Wrap with the consumer to use context
<AuthContext.Consumer>
    {(context) => <button onClick={context.login}>Log in</button>}
</AuthContext.Consumer>
```

### A more flexible context consumer

Add a static property `contextType` in the component and assign it to the context imported;

React will connect the component to the context and gives a new property `this.context` in the component.

```jsx
static contextType = AuthContext;
{this.context.authenticated ? <p>Authenticated!</p> : <p>Please log in</p>}
```

Use `useContext` hook to use context in functional components.

```text
const authContext = useContext(AuthContext);
```

