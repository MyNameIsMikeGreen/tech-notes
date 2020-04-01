React
=======

# Overview
React is a JavaScript library for builidng user interfaces.

It is declarative. The programmer describes what is to be built, React handles how to build and maintain it.

# Core Concepts
* Virtual DOM
  * Instead of interfacing with a browser's native DOM API, when writing react, a programmer interfaces with a virtual DOM.
    * As this virtual DOM stays the same across platforms, it makes the code more portable.
    * The virtual DOM is generally considered nicer or more ideal than the native DOM APIs.
* Reactive updates
  * Whenever the state of a component changes, so does the view of the component.
  * React handles the update logic so that the developer doesn't have to.

## Components

* Basic visual building blocks of a React interface.
* Like functions, takes an input or a state, produces an output.
  * The output of components is JSX. JSX is a HTML-like JavaScript syntax which represents HTML objects.
* Reusable
* Can be either class or function level

### Class Components
* Can interface directly with the state variables of the class.
  * These state variables can change during runtime. React will apply the reactive update principle to update the component automatically when this happens.

### Function Components
* Held within a wider class
* Explicitly take in a 'props' (properties) argument which it then acts upon.
  * Props are immutable so the component is static once created **[CONFIRM THIS]**
* The function name will become the name of the tag used to call/create a component.
  * For example, a function like this:
  ```javascript
  function MyComponent(){
      return <h1>This is my component</h1>;
  }
  ```
  Will be called using a tag like this:
  ```javascript
  <MyComponent />
  ```
  And, when fully compiled, will end up looking like this: **[CONFIRM THIS]**
  ```javascript
  <MyComponent>
      <h1>This is my component</h1>;
  </MyComponent>
  ```

## JSX

* A HTML-like JavaScript syntax which is a shorthand for writing full React API calls for the generation of HTML elements.
* Yes, we write near-HTML, to generate React, to generate HTML. Frontend code is stupid.
* JSX itself is not valid JavaScript. JSX is compiled into React API calls either by a compiler used by an IDE, or an extension installed on a browser.
  * One such compiler is 'Babel'.

Example JSX:

```javascript
<div className="containter">
  <h1>Getting Started</h1>
</div>
```

Equivalent React API calls which the JSX is converted into:

```javascript
React.createElement("div", {className: "container"},
  React.createElement("h1", null, "Getting Started")
)
```
