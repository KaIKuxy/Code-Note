# Working with Lists and Conditionals

To render content conditionally, use conditional operator to include content or not.

```jsx
// Use state space to maintain whether the content should be showed or not.
{ this.state.showContent ? 
  <div>
    // content
  </div> : null
}
```

Another way to render content conditionally is to save the partial JSX into another variable and then include it in the returned JSX.

```jsx
let content = null;
if ( this.state.showContent ) {
    content = (
        // content
    );
}

return (
    // other contents
    {content}
    // other contents
);
```

Using map function to output dynamic length array

```jsx
{
    this.state.persons.map(
        person => {
            return <Person name={person.name} age={person.age} />;
        }
    )
}
```

Dont even try to edit the variable that points directly to the state, copy the data.

```jsx
// Dont do this!!!
// ---------------------------------
// const persons = this.state.person;
// persons.splice(personIndex, 1);
// ---------------------------------
// Dont do this!!!

// Use slice() or spread operator to copy data
// const persons = this.state.persons.slice();
const persons = [...this.state.persons];
persons.splice(personIndex, 1);
this.setState({persons: persons});
```

Keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements inside the array to give the elements a stable identity.

A good rule of thumb is that elements inside the `map()` call need keys. \(Because you always use map to create serveral component siblings\)

Keys used within arrays should be unique among their siblings. However they don’t need to be globally unique. We can use the same keys when we produce two different arrays.



