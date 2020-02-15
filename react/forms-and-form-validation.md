# Forms and form validation

How to set state for a nested object: Partial DeepCopy

```jsx
state = {
    orderForm: {
        name: {
            elementType: 'input',
            elementConfig: {
                type: 'text',
                placeholder: 'Your Name'
            },
            value: ''
        },
        street: {
            elementType: 'input',
            elementConfig: {
                type: 'text',
                placeholder: 'Street'
            },
            value: ''
        },
        zipCode: {
            elementType: 'input',
            elementConfig: {
                type: 'text',
                placeholder: 'ZIP Code'
            },
            value: ''
        },
        country: {
            elementType: 'input',
            elementConfig: {
                type: 'text',
                placeholder: 'Country'
            },
            value: ''
        },
        email: {
            elementType: 'input',
            elementConfig: {
                type: 'email',
                placeholder: 'Your E-Mail'
            },
            value: ''
        },
        deliveryMethod: {
            elementType: 'select',
            elementConfig: {
                options: [
                    {value: 'fastest', displayValue: 'Fastest'},
                    {value: 'cheapest', displayValue: 'Cheapest'},
                ]
            },
            value: ''
        }
    },
    loading: false
};
 
// Only the [inputIdentifier] part is deeply copied
inputChangedHandler = (event, inputIdentifier) => {
    const updatedOrderForm = {
        ...this.state.orderForm
    };
    const updatedFormElement = {
        ...updatedOrderForm[inputIdentifier]
    };
    updatedFormElement.value = event.target.value;
    updatedOrderForm[inputIdentifier] = updatedFormElement;
    this.setState({orderForm: updatedOrderForm});
}
```

Wrap the form element into an Input component

```jsx
// We can dynamically and easily add input into the app.
const input = (props) => {
    let inputElement = null;

    switch (props.elementType) {
        case ('input'):
            inputElement = <input
                className={classes.InputElement}
                {...props.elementConfig}
                value={props.value}
                onChange={props.changed} />;
            break;
        case ('textarea'):
            inputElement = <textarea
                {...props.elementConfig}
                value={props.value}
                onChange={props.changed} />;
            break;
        case ('select'):
            inputElement = (
                <select
                    className={classes.InputElement}
                    value={props.value}
                    onChange={props.changed}>
                    {props.elementConfig.options.map(option => (
                        <option key={option.value} value={option.value}>{option.displayValue}</option>
                    ))}
                </select>
            );
            break;
        default:
            inputElement = <input
                className={classes.InputElement}
                {...props.elementConfig}
                value={props.value}
                onChange={props.changed} />;
    }
```

