# Styling React Components & Elements

One way of styling components:

```jsx
// If I want to give a button two state of styles
// When on, green
// When off, red
render() {
    // set default style
    const style = {
        color: "green",
        // ...
    };
    // change style using JS
    if (...) {
        style.color = "red";
    }
    
}
```

Save styling classes in an array and apply to `className` after joining them with `' '`

### Radium

Way of using `pseudo selector` when styling components\(e.g. button:hover\) in JSX: Using [**Radium**](https://www.npmjs.com/package/radium)\*\*\*\*

```jsx
import Radium from 'radium';

class ComponentName extends Component {
    ...
};

// Wrap the component with Radium
// Called higher order component
export default Radium(ComponentName);

// ...
const style = {
  // ...
  ':hover': {
    backgroundColor: 'lightgreen',
    color: 'black'
  }
};
```

Ways of using media queries

Wrap the whole app component in the **StyleRoot**:

```jsx
return (
  <StyleRoot>
    <div className="App">
      // ...
    </div>
  </StyleRoot>
);

const style = {
  '@media (min-width: 500px)': {
    width: '450px'
  }
};
```

[ways of setting animations](https://codepen.io/simonbreiter/pen/aBqLOq)

### styled-components

An alternative to radium: [styled-components](https://styled-components.com/)

```jsx
// StyleDiv is a React Component
const StyledDiv = styled.div`
    
    width: 60%;
    margin: 16px auto;
    border: 1px solid #eee;
    box-shadow: 0 2px 3px #ccc;
    padding: 16px;
    text-align: center;

    @media (min-width: 500px) {
        width: 450px;
    }
`;
```

Styled-Components will automatically give a unique class name to any new styled-element in real-time.

The result of the StyledDiv:

```css
/*
<style data-styled="active" data-styled-version="5.0.0">
*/
.iVfOiF {
    width:60%;
    margin:16px auto;
    border:1px solid #eee;
    box-shadow:0 2px 3px #ccc;
    padding:16px;text-align:center;
}
@media (min-width:500px) {
    .iVfOiF {
        width:450px;
    }
}
/*
</style>
*/
```

Way of using pseudo selector in styled-components:

```jsx
// Add & before pseudo selector
const StyledButton = styled.button`
  backgroun-color: green;
  color: white;
  font: inherit;
  border: 1px solid blue;
  padding: 8px;
  cursor: pointer;
  &:hover {
    backgroundColor: lightgreen;
    color: black;
  }
`;
```

To have dynamic styles, send props into the component when render the JSX and use props to set dynamic styles when the styled-component variable is created.

```jsx
const StyledButton = styled.button`
  background-color: ${props => props.alt ? 'red' : 'green'};
  color: white;
  font: inherit;
  border: 1px solid blue;
  padding: 8px;
  cursor: pointer;
  &:hover {
    background-color: ${props => props.alt ? 'salmon' : 'lightgreen'};
    color: black;
  }
`;

return (
  <div className="App">
    // ...
    <StyledButton
      alt={this.state.showPersons}
      onClick={this.togglePersonsHandler}>Toggle Persons
    </StyledButton>
    // ...
  </div>
);
```

styled-components will not create the class directly when the component is created, but dynamically create the required class when needed and reuse it after.

### work with CSS modules \(Recommended because css and js does not mix together\)

npm run eject

edit in config/webpack.config.dev.js and also webpack.config.prod.js

find `test: /\.css/`

```javascript
options: {
  importLoaders: 1,
  modules: true,
  localIdentName: '[name]__[local]__[hash:base64:5]'
},
```

Then set styles in js file:

```jsx
import classes from './App.css';

    let persons = null;
    let btnClass = [classes.Button];

    if ( this.state.showPersons ) {
      persons = (
        <div>
          {this.state.persons.map((person, index) => {
            return <Person
              key={person.id}
              click={this.deletePersonHandler.bind(this, index)}
              name={person.name}
              age={person.age}
              changed={(event) => this.nameChangedHandler(event, person.id)}
              />;
          })}
        </div>
      );
      btnClass.push(classes.Red);
    }

    let assignedClasses = [];
    if (this.state.persons.length <= 2) {
      assignedClasses.push(classes.red);
    }
    if (this.state.persons.length <= 1) {
      assignedClasses.push(classes.bold);
    }

    return (
      <div className={classes.App}>
        <h1>Hi, I'm a React App</h1>
        <p className={assignedClasses.join(' ')}>This is really working!</p>
        <button className={btnClass.join(' ')}
          onClick={this.togglePersonsHandler}>Toggle Persons
        </button>
        {persons}
      </div>
    );
    
// We can also not use new class Button but use button element selector
// because we already have a App class outside
    
```

When imported, the class will be transformed into unique class name

```jsx
import classes from './App.css';
import Person from './Person/Person';

console.log(classes);
// Object
// App: "App__App__1o-Fp"
// red: "App__red__lKdi_"
// bold: "App__bold__1Ylab"
// Red: "App__Red__2T8IA"
// __proto__: Object
```

**With React-script version 2 and higher, we do not have to do ejection but change the css file to `file_name.module.css`**

By the way, if you somehow also want to define a global \(i.e. un-transformed\) CSS class in such a `.css`  file, you can prefix the selector with `:global`

```css
:global .Post { ... }
```

Now you can use `className="Post"`  anywhere in your app and receive that styling.

