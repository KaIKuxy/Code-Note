# Debugging React Apps

### Catch Error in React

Wrap a ErrorBoundary component with `componentDidCatch` property outside the components that could cause error.

ComponentDidCatch: Catches exceptions generated in descendant components. Unhandled exceptions will cause the entire component tree to unmount.

```jsx
if ( this.state.showPersons ) {
  persons = (
    <div>
      {this.state.persons.map((person, index) => {
      // We move the key prop into the ErrorBoundary
      // because we still have to make sure the key prop is still at the outermost component
      // when we render a list of components
        return <ErrorBoundary key={person.id}><Person
          click={this.deletePersonHandler.bind(this, index)}
          name={person.name}
          age={person.age}
          changed={(event) => this.nameChangedHandler(event, person.id)}
          /></ErrorBoundary>;
      })}
    </div>
  );
}
```

Some components don’t know their children ahead of time. This is especially common for components like `Sidebar` or `Dialog` that represent generic “boxes”.

We recommend that such components use the special `children` prop to pass children elements directly into their output:

```jsx
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
```

This lets other components pass arbitrary children to them by nesting the JSX:

```jsx
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```

**Only use error boundaries for cases where you know that it might fail and you can't control that.**

