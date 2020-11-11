# Intro to Raw React APIs

React abstracts away the imperative browser API from you to give you a much more declarative API to work with.

## Imperative vs Declarative Programming

> “Imperative programming is like **how** you do something, and declarative programming is more like **what** you do.”

> Declarative programming is “the act of programming in languages that conform to the mental model of the developer rather than the operational model of the machine.”

**An Imperative Approach** will describe how you're going to acheive something. List the steps. (e.g. "I am going to walk over to that specific table and sit there", or directions to a location)

**A Declarative Approach** is concerned with what you want. (e.g. "a table for two", or an address)

- Many (if not all) declarative approaches have some sort of underlying imperative abstraction.



### Languages

**Imperative**: C, C++, Java

**Declarative**: SQL, HTML

**(Can Be) Mix**: JavaScript, C#, Python



## Exercise

Create the Hello World app using the React API

```javascript
<body>
  <div id="root"></div>

  <script src="https://unpkg.com/react@16.13.1/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.13.1/umd/react-dom.development.js"></script>

  <script type="module">
    const rootElement = document.getElementById('root')
    const elementProps = { className: "container", children: "Hello World"}
    
    // This creates an object that describes the type of UI you want created
    const reactElement = React.createElement("div", elementProps)
    
    // This takes the object and creates the element for us, appending it to the correct 		location
    ReactDOM.render(reactElement, rootElement)
  </script>
</body>

```



Another option is:

```javascript
const reactElement = React.createElement("div", { className: "container"}, "Hello World")
```





### Extra Credit

- Passing in an array of elements:

  ```javascript
   const containerElementProps = { className: "container", children: [span01, span02]}
  ```

  ```javascript
  const containerElement = React.createElement("div", { className: "container"}, span01, span02)
  ```

  