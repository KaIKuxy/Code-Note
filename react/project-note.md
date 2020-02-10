# Project Note

## Planning a React App

1. Component Tree / Component Structure
2. Application State \(Data\)
3. Components vs Containers

### Layout and Component Tree

![](../.gitbook/assets/image%20%289%29.png)

### State

```text
- Ingredients
    { meat: 1, cheese: 2 }
- Purchased: true
- Total Price: 12.99
```

Maintain State at BurgerBuilder Component

### [Here’s Why Mapping a Constructed Array in JavaScript Doesn’t Work](https://itnext.io/heres-why-mapping-a-constructed-array-doesn-t-work-in-javascript-f1195138615a)

flatten the array

`arr.reduce(callback( accumulator, currentValue[, index[, array]] )[, initialValue])`

```jsx
const transformedIngredients = Object.keys(props.ingredients)
    .map(igKey => {
        return [...Array(props.ingredients[igKey])].map((_, i) => {
            return <BurgerIngredient key={igKey + i} type={igKey}/>;
        });
    })
        .reduce((arr, el) => {
            return arr.concat(el);
        }, []);
```

Import a pic into the project

```jsx
import burgerLogo from '../../assets/images/burger-logo.png';


<img src={burgerLogo} />
```

**Border**-**box** tells the browser to account for any **border** and padding in the values you specify for an element's width and height. If you set an element's width to 100 pixels, that 100 pixels will include any **border** or padding you added, and the content **box** will shrink to absorb that extra width.

Set both width and max-width to be safe on super narrow screen.

Be cautious to use pure components.



