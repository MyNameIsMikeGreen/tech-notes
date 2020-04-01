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
  * What we are actually returning from the function is a `React.createElement()` function call. It is just hidden away by the use of JSX.

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

# Usage

## Rendering

The equivalent of a `main` function when using React is:

```
ReactDOM.render();
```

This is where all of the calls and other JavaScript functions will reside.

This function takes two arguments: The element to be rendered, the parent DOM element to be rendered within.

Note how the function accepts only a single element. It is not possible to simply place multiple JSX statements inside here as they are ultimately just function calls and would not be valid JavaScript syntax. Instead, if we have multiple elements to render (as we would in almost all scenarios), we can do multiple things, but they are all ultimately working to wrap the elements inside a single parent:

* Insert the elements into an array and pass the array to `ReactDOM.render()`:
```javascript
ReactDOM.render( [<MyComponent1 />], [<MyComponent2 />] );
```

* Wrap the components inside a common parent element and pass that to `ReactDOM.render()`:
```javascript
ReactDOM.render( <div> <MyComponent1 /> <MyComponent2 /> <div/> );
```

* Wrap the components inside a React fragment element which is designed especially for this purpose and pass that to `ReactDOM.render()`:
```javascript
ReactDOM.render( <React.fragment> <MyComponent1 /> <MyComponent2 /> <div/> );
```

* As above, but with the React fragment shorthand notation:
```javascript
ReactDOM.render( <> <MyComponent1 /> <MyComponent2 /> </> );
```

This can be then made to look cleaner inside the renderer by calling off to a single function component:
```javascript
function MyParentComponent() {
    return (
      <>
        <MyComponent1 />
        <MyComponent2 />
      <>
    );
}

ReactDOM.render( <MyParentComponent /> );
```

## State

A component requires state to be anything more than a dumb static object. Therefore, we need a way of setting and getting variables from a global scope.

To do this, we use `useState()`. This function creates a state variable, and returns pointers to the functions that get and set the variable (accessor methods).

The pointers are returned in an array in the form [getter, setter] from `useState()` and can then be used wherever else in the function that state variable is required. The value will be maintained even after leaving the component's function and returning to it.

For example:

```javascript
function Button(){
  const [counterValue, setCounterValue] = useState(0);
  return <button onClick={() => setCounterValue(counterValue() + 1)}>
    {counterValue}
  </button>;
}
```

In the above example, we make a button with text set to equal the stateful variable's value. This is done by calling `useState()` to create the variable, and then using JSX's interpolation functionality to insert the result of the variable's getter function.
