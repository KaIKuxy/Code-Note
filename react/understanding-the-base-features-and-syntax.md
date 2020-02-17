# Understanding the Base Features & Syntax

### Build Workflow

![Using a Build Workflow](../.gitbook/assets/image%20%287%29.png)

Use create-react-app to create react app.

```bash
create-react-app my-app
cd my-app
npm start
```

### Component

Components are the **core building block of React apps**. Actually, React really is just a library for creating components in its core. A typical React app therefore could be depicted as a **component tree** - having one root component \("App"\) and then an potentially infinite amount of nested child components. Each component needs to return / render some **JSX** code - it defines which HTML code React should render to the real DOM in the end. **JSX is NOT HTML** but it looks a lot like it. Differences can be seen when looking closely though \(for example className in JSX vs class in "normal HTML"\). JSX is just syntactic sugar for JavaScript, allowing you to write HTMLish code instead of nested React.createElement\(...\) calls.

Component either returns JSX or the compiled JS code from JSX.

```jsx
// JSX
return (
   <div className="App">
      <h1>Hi, I'm a React App</h1>
   </div>
);
   
// Compiled JSX 
return React.createElement(
   'div',
   {className: 'App'},
   React.createElement("h1", null, "Hi, I'm a React App...")
 );
```

There are **two different ways** of creating components:

1. **Functional components** \(also referred to as "presentational", "dumb" or "stateless" components\) =&gt; `const cmp = () => { return <div>some JSX</div> }` \(using ES6 arrow functions as shown here is recommended but optional\)
2. **class-based components** \(also referred to as "containers", "smart" or "stateful" components\) =&gt; `class Cmp extends Component { render () { return <div>some JSX</div> } }` 

### Props & State

Use `props` to pass data from a parent \(wrapping\) component to a child \(embedded\) component.

```jsx
class App extends Component {
  state = {
    persons: [
      { name: "Max", age: 28},
      { name: "Manu", age: 29},
      { name: "Stephanie", age: 26}
    ],
    otherState: 'some other value'
  }

  switchNameHandler = () => {
    // console.log("Button clicked!");
    // DON'T DO THIS: this.state.persons[0].name = 'Test';
    this.setState({
      persons: [
        { name: "Test", age: 28},
        { name: "Manu", age: 29},
        { name: "Stephanie", age: 27}
      ]
    });
    // this.setState will merge the old state with the intended state,
    // so the "otherState" will still exist
  }

  render() {
    return (
      <div className="App">
        <h1>Hi, I'm a React App</h1>
        <p>This is really working!</p>
        <button onClick={this.switchNameHandler}>Switch Name</button>
        <Person name={this.state.persons[0].name} age={this.state.persons[0].age}/>
        <Person name={this.state.persons[1].name} age={this.state.persons[1].age}>My Hobbies: Racing</Person>
        <Person name={this.state.persons[2].name} age={this.state.persons[2].age}/>
      </div>
    );
    // return React.createElement("div", {className: 'App'}, React.createElement("h1", null, "Hi, I'm a React App..."));
  }
}

const person = ( props ) => {
    return (
        <div>
            <p>I'm {props.name} and I am {props.age} years old!</p>
            <p>{props.children}</p>
            // children refers to any element
            // between the opening and closing tab of the component
            // If there is no element, the props.children will be null
        </div>
    );
};

// When using class-based components, it's this.props
class Person extends Component {
    render() {
        return <p>My name is {this.props}</p>;
    }
}
```

Only changes in `props` and / or `state` trigger React to re-render the componnets and potentially update the DOM in the browser.

[To Which Events You Can Listen?](https://reactjs.org/docs/events.html#supported-events)

### Update state using Hooks

To use Hooks, the components have to be defined with functions instead of class.

useState: Returns a stateful value, and a function to update it.

**Update function returned by useState from Hooks will replace the whole state with the new state.**

To protect the other state, either explicitly re-write it or create another hook by calling useState again.

```jsx
const App = props => {
  const [personsState, setPersonsState] = useState({
    persons: [
      { name: "Max", age: 28},
      { name: "Manu", age: 29},
      { name: "Stephanie", age: 26}
    ]
  });
  const [otherState, setOtherState] = useState('some other value');
}
```

Two ways of [setting event](https://reactjs.org/docs/handling-events.html)

```jsx
// Use arrow function
// (Not recommended)
<button onClick={() => this.switchNameHandler("Maximilian!!") }>
    Switch Name
</button>

// Use bind
<Person
    name={this.state.persons[1].name}
    age={this.state.persons[1].age}
    click={this.switchNameHandler.bind(this, "Max")}
>
    My Hobbies: Racing
</Person>
```

Change values by user's input: two way binding

```jsx
nameChangedHandler = (event) => {
    this.setState({
      persons: [
        { name: "Max", age: 28},
        { name: event.target.value, age: 29},
        { name: "Stephanie", age: 26}
      ]
    });
  }
  
  
<Person
  name={this.state.persons[1].name}
  age={this.state.persons[1].age}
  click={this.switchNameHandler.bind(this, "Max")}
  changed={this.nameChangedHandler}
>My Hobbies: Racing</Person>
        
        
<input type="text" onChange={props.changed} value={props.name}/>
```

### CSS styling

Two ways of styling CSS in React: using CSS files / inline-styling in js files

```jsx
// CSS files are always global, so be sure to add className to the returned JSX.
// CSS files are injected dynamically by webpack.
import './Person.css'
<div className="Person">
    // ...
    
// inline-styling
const style = {
  backgroundColor: 'white',
  font: 'inherit',
  border: '1px solid blue',
  padding: '8px',
  cursor: 'pointer'
};
<button 
    style={style}
    onClick={() => this.switchNameHandler("Maximilian!!") }
>
    Switch Name
</button>
```

