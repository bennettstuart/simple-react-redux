# Redux Notes

> I visualise redux as a single service that handles the state of the whole app of which components can have just a select number of functions mapped to either store and/or receive data.

There are 3 main concepts to Redux: Reducers, Containers and Actions.

## Reducers
Reducers are functions that are fired when any *Action* is dispatched. They listen for actions and each reducer will should be written to only catch actions that they're interested in - they know this by the actions type. The Reducer will take the payload of the action and set return that value to be added to the global state in most cases.

```js
// State argument is not application state, only the state
// this reducer is responsible for.
export default function(state = null, action) {
  switch(action.type) {
    case 'BOOK_SELECTED':
      return action.payload;
  }
  return state;
}
```

The rootReducer is where the state properties are defined. in the example below, the BooksReducer returns a list of books. The states `books` will hold a list of books

```js
const rootReducer = combineReducers({
  books: BooksReducer,
  activeBook: ActiveBook
});
```

## Containers
Containers, or "smart" components, are components that have some state props mapped to their props.
A lot of the time a Container will be a parent to child components that all use the global state prop that they're mapping, hense the name. It is bad practise to have containers in containers that are accessing the same global state properties

in their component file you will see something similar to the example below. This is how global state properties are mapped to components.

```js
function mapStateToProps(state) {
  return {
    book: state.activeBook
  };
}

export default connect (mapStateToProps)(BookDetail);
```

## Actions
Actions, as mentioned in the reducer description, have a type (which is like a name) and a payload, which is some data or information.
Actions are fairly simple in most cases, and are just functions that return an object. Note how the 'book' argument is passed in and becomes the payload.
```js
export function selectBook(book) {
  // selectBook is an ActionCreator, it needs to return an action
  // an object with a type property.
  return {
    type: 'BOOK_SELECTED',
    payload: book
  };
} 
```

Components can call actions by using an action creator. below is an example how to map an action creator/dispatcher to be available on components props.
Here the component will have the property `selectBook` which is the function in the example above.
```js
// Anything returned fromthis function will end up as props
// on the BookList container
function mapDispatchToProps(dispatch) {
  // Whenever selectBook is called, the result should be pass
  // to all of our reducers
  return bindActionCreators({ selectBook: selectBook }, dispatch);
}

```
When an action is dispatched all reducers that have been defined will receive the action.

# Adding Redux to a projects

The app is wrapped a Provider as shown below, the `reducers` is the rootReducer that has all the reducers attached. the rootReducer is in the `./reducers/index.js` file.
```js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore, applyMiddleware } from 'redux';

import App from './components/app';
import reducers from './reducers';

const createStoreWithMiddleware = applyMiddleware()(createStore);

ReactDOM.render(
  <Provider store={createStoreWithMiddleware(reducers)}>
    <App />
  </Provider>
  , document.querySelector('.container'));
```