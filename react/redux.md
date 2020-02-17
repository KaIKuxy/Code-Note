---
description: 'https://redux.js.org/'
---

# Redux

![](../.gitbook/assets/image%20%2812%29.png)

![Redux Flow](../.gitbook/assets/image%20%2818%29.png)

## Redux Basics

State in the store can be anything, it can possibly be just a number, but most of the time it is a js object.

Reducer is a function which receives the previous state and the action and returns the updated state.

Do not modify the state passed into the reducer, create a new one!

To connect the reducer with the central store, create stores with specifing the reducer.

Dispatch the action and information with sending an object with a `type` property.

```javascript
const redux = require("redux");
const createStore = redux.createStore;

const initialState = {
    counter: 0
};

// Reducer
const rootReducer = (state = initialState, action) => {
    if (action.type === 'INC_COUNTER') {
        return {
            ...state,
            counter: state.counter + 1
        }
    }
    if (action.type === 'ADD_COUNTER') {
        return {
            ...state,
            counter: state.counter + action.value
        }
    }
    return state;
};

// Store
const store = createStore(rootReducer);
console.log(store.getState());

// Subcription
store.subscribe(() => {
    console.log('[Subscription]', store.getState());
});

// Dispatching Action
store.dispatch({type: 'INC_COUNTER'});
store.dispatch({type: 'ADD_COUNTER', value: 10});
```

## Work with React

Wrap the whole app component with `Provider` component from react-redux.

```jsx
import { createStore } from 'redux';
import { Provider } from 'react-redux';

import reducer from './store/reducer';

const store = createStore(reducer);

ReactDOM.render(
    <Provider store={store}><App /></Provider>,
    document.getElementById('root')
);
```

Access the store in components by `connect` function from react-redux, which creates a hoc to wrap the component.

```jsx
const mapStateToProps = state => {
    return {
        propName: state.dataName
    };
};

const mapDispatchToProps = dispatch => {
    return {
        propName: () => dispatch({type: actionName, payload: {...}});
    };
}

export default connect(mapStateToProps, mapDispatchToProps)(Componnent);
```

[How does a redux connected component know when to re-render?](https://stackoverflow.com/questions/40386128/how-does-a-redux-connected-component-know-when-to-re-render)

### use multiple Reducers

reducer can be declared in different file and then combine them together with `combineReducers` from redux.

```jsx
const rootReducer = combineReducers({
    ctr: counterReducer,
    res: resultReducer
});

const store = createStore(rootReducer);
```

In this way, we **cannot** use the content from another reducer. We have to still provide the content with packing it in the action payload.

### Should every state be handled through redux?

![](../.gitbook/assets/image%20%282%29.png)

