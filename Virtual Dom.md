# Virtual Dom

A virtual representation of UI in memory which synced with the real DOM by a library such as ReactDOM


# Basic

## Main Concepts

### Components and Props

- Components could be a function as well as a class. Function component takes in props.

- A function component does not modify its input is called pure component (Props are read-only)

- All React components must act like pure functions with respect to their props.



### State and Lifecycle

`setState` maybe async as React may batch multiple `setState()` into a single update for performance.

`setState()` can take a function as argument. The first argument of the function is the previous state and second argument is the props ate the time the update is applied.

```js
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

`setState` is mergeing of object (like Object.assign). Update one key value paire and the rest will remain intact.


#### Handling Events

Event binding is to make ‘this’ work in the callback. If it is not a callback, it is not necessary to bind the event to the component. It is not React-specific behavior, it is a part of how functions work in JS. In general, if you refer to a method without () after it, you should bind it.


**(NOT RECOMMEND) if callback is passed down from as a prop, it will cause extra re-rendering.**

- One way to get rid of bind is to use arrow function in the callback:

```js
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}

```

- Pass argument to event handlers

 ```js
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
 ```
```js
import React, { Component } from "react";
import logo from "./logo.svg";
import "./App.css";
class App extends Component {
  render() {
    return (
      <div className="App"
        // you need to pass in `this`, and you need not pass in `event`
        onClick={this.handleClick.bind(this, "pizzas", "tacos", "nachos")}
      />
    );
  }
  handleClick(a, b, c, event) {
    console.log(a, b, c, event);
  }
}
export default App;
```

```js
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
class App extends Component {
    render() {
        const pizzas = 'pizzas';
        const tacos = 'tacos';
        const nachos = 'nachos';
        return (
            <div
                className="App"
                // you need to pass in `event`, and you need not pass in `this`
                onClick={event => this.handleClick(pizzas, tacos, nachos, event)}
            />
        );
    }
    handleClick(a, b, c, event) {
        console.log(a, b, c, event);
    }
}
export default App;
```
> Read more on [ReactJS - Parameterized Event Handlers](https://www.freecodecamp.org/news/reactjs-pass-parameters-to-event-handlers-ca1f5c422b9/!")



### Conditional Rendering

If conditions become too complex, extract it out to become a component, like a function component.

Tips, construct code in render and return for view only.
```js
  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;

    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }

```

### Lists and Keys

Use map function to iterate a list.

Keys help React identify which items have changes, are added, or are removed. The best way to pick a key is to use value uniquely identifies an item among its sibling.  Item is better to be an object containing value and a unique key. Do not use array index as keys as it may negative impact on performance and may cause issues with component state.


When to use id as key:

- the list and items are static–they are not computed and do not change;

- the items in the list have no ids;

- the list is never reordered or filtered.



### Forms

Controlled components.

In a form all field should have their own state and a handler to handle submit action.

_**`Textarear` and `select` use value attribute in react for its value**_

Fomik library polular library


### Lifting state up


### Composition vs Inheritance

###### Containment.
Some components do not know their children components beforehand. Props.chindren is passed directly to the children element. It is similar as Vue.js slot behavior.
```js
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
```
This allow other components pass arbitrary children to them and render them out directly.

```js
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
When multiple slot or ‘holes’ are needed, you can also create your own convention instead of using ‘children’.
```js
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}

```


### Thinking in React

1. Break The UI Into A Component Hierarchy
![d782621e.png](:storage/48e8eb21-21c5-451a-98b1-06862c7418b9/d782621e.png)

2. Build A Static Version in React (with dummy data passed as props, no interaction and state.) As state is used for interaction.

3. Identify The Minimal (but complete) Representation Of UI State
    Think of minimal set of mutable state you needed. DRY: do not repeat yourself.
    List down all the data:
      - The original list of products
      - The search text the user has entered
      - The value of the checkbox
      - The filtered list of products
  Ask three questions about each piece of data:
      - Is it passed in from a parent via props? If so, it probably isn’t state.
      - Does it remain unchanged over time? If so, it probably isn’t state.
      - Can you compute it based on any other state or props in your component? If so, it isn’t state.
  The original list of products is passed in as props, so that’s not state. The search text and the checkbox seem to be state since they change over time and can’t be computed from anything. And finally, the filtered list of products isn’t state because it can be computed by combining the original list of products with the search text and value of the checkbox.

  So finally, our state is:
    - The search text the user has entered
    - The value of the checkbox



4. Identify Where Your State Should Live

We need to identify which component mutates, or owns, this state.
For each piece of state in your application:
- Identify every component that renders something based on that state.
- Find a common owner component (a single component above all the components that need the state in the hierarchy).
- If you can’t find a component where it makes sense to own the state, create a new component solely for holding the state and add it somewhere in the hierarchy above the common owner component.


5. Add Inverse Data Flow, Pass called back down to children component with setState in it.



# Advanced
## React Fragment



## Accessibility

Web accessibility is design and creation of websites that can be used by everyone (including disables) through assistive technology interpreting web pages.

1. Aria (very powerful, most of the time accessibility can be done through html tab)  which is a way of adding and override accessibility to HTML elements .
    ```js
    <input
      type="text"
      aria-label={labelText}
      aria-required="true"
      onChange={onchangeHandler}
      value={inputValue}
      name="name"
    />
    ```

2. Semantic HTML
    Using the various HTML elements to reinforce the meaning of information in our websites whihc will often give us accessibility for free.
    E.g <address> tag indicates the person or organizations' contact info

3. Grouping
    Sometimes `<div>` is add for React to work which could break some html grouping like `<table> <ol>` ..
    React Fragments can be used to group element together.
4. Accesible Forms
    - Labeling – for accessibility and error msg
5. Focus control, to make sure website is fully operational with Keyboard only (Tab to nivigate).
    Focus outline, CSS focus. Pointer and mouse events can be triggered by keyboard only.
6. Language, `lang` attributes to specify language for screen reader
7. Setting title for SPA, React Document Title Component

## Code-Splitting

Follow bundler (webpack, or others) guideline to configure for code splitting
Motivation is to reduce the bundle size and load your application faster.
_**A very important and powerful tool for Client Side Rendering (CSR) to reduce the initial loading speeding as the intial bundle is smaller.**_
- Dynamic import (It is not a part of the JavaScript standard.) Hopefully it will be in future. When correctly configured bundler sees the syntax, it will start codes plitting.
- React .lazy
- Suspensus
Others skipped as this topic is on bundling


## Context
- The child component consuming the context will find the closest provider above and use its value.
- The defaultValue argument is only used when a component does not have a matching Provider above it in the tree
- Function component consume context
- Consume multiple context
- Caveats (move context value to state so that when provider re-render, the consumers do not re-render.

## Error Boundaries
It does not catch event handlers error, async code e.g seTimeout callbacks, SSR, Errors thrown in the error boundary itself (rather than its children).
A component wrapped around a normal component, if the normal component fail, Error boundary component will catch it and prevent the app from crashing

```jsx
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

## Forwarding Refs

Ref forwarding is a technique for automatically passing a ‘ref’ through a component to one of its children.







## React Hooks

Allow you to use state and other React features without writing a class.
Hooks are functions that let you “hook into” React state and lifecycle features from function components.

#### Effect Hook
Operations like fetching data, subscriptions or manually changing the DOM from React Component are “side effects” or “effects” because they can affect other components and can’t be done during rendering.

#### useEffect API is an API which syncs with all component lifecycle.

#### Rules of Hooks
Tow rules:
- Only call Hooks at the top level. Don’t call Hooks inside loops, conditions, or nested functions.
- Only call Hooks from React function components. Don’t call Hooks from regular JavaScript functions. (There is just one other valid place to call Hooks — your own custom Hooks. We’ll learn about them in a moment.)





