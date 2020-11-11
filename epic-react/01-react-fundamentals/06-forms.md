# Forms

You can attach a submit handler to a form element with the `onSubmit` prop. This will be called with the submit event which has a `target`. That `target` is a reference to the `<form>` DOM node which has a reference to the elements of the form which can be used to get the values out of the form!

Always use `event.preventDefault()` to prevent a refresh when creating single page applications.

The `onSubmit` event is a `SyntheticEvent`, an object that React creates for us (for performance reasons) that behaves exactly like a real event.

To access the native event: `event.nativeEvent`



## Debugging

When logging an `event.target`, you will get the markup in your console. To see the properties of the DOM node instead, use `console.dir(event.target)`

 

## Ways to get the value of an input

- Via their index: `event.target.elements[0].value`

- Via the elements object by their `name` or `id` attribute: `event.target.elements.username.value`

- Via a `ref` -- an object that stays consistent between renders of your React component. It has a `current` property on it which can be updated to any value at any time. In the case of interacting with DOM nodes, you can pass a `ref` to a React element and React will set the `current` property to the DOM node that’s rendered.

  So if you create an `inputRef` object via `React.useRef`, you could access the value via: `inputRef.current.value`



## Controlled vs Uncontrolled Input

- An uncontrolled input lets the browser maintain the state of thr input. We look for changes by querying the DOM node.
- A controlled input lets you control the content of the DOM node.
  - `<input value={myInputValue} />`



## Exercise

Get the value of an input using an input name:

```jsx
function UsernameForm({onSubmitUsername}) {
  function handleSubmit(event) {
    event.preventDefault()
    const value = event.target.elements.username.value
    onSubmitUsername(value)
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="usernameInput">Username:</label>
        <input type="text" id="usernameInput" name="username" />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}

function App() {
  const onSubmitUsername = username => alert(`You entered: ${username}`)
  return <UsernameForm onSubmitUsername={onSubmitUsername} />
}
```



Get the value of an input using a ref:

```jsx
function UsernameForm({onSubmitUsername}) {
  const inputRef = React.useRef()

  function handleSubmit(event) {
    event.preventDefault()
    const value = inputRef.current.value
    onSubmitUsername(value)
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="usernameInput">Username:</label>
        <input type="text" id="usernameInput" name="username" ref={inputRef} />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}

function App() {
  const onSubmitUsername = username => alert(`You entered: ${username}`)
  return <UsernameForm onSubmitUsername={onSubmitUsername} />
}
```



## Exercise

Add form validation:

```jsx
function UsernameForm({onSubmitUsername}) {
  const [error, setError] = React.useState(null)
  const inputRef = React.useRef()

  function handleSubmit(event) {
    event.preventDefault()
    const {value} = inputRef.current
    onSubmitUsername(value)
  }

  function handleChange(event) {
    const {value} = inputRef.current
    const isValid = value === value.toLowerCase()
    setError(isValid ? null : 'Username must be lower case')
  }
 
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="usernameInput">Username:</label>
        <input
          type="text"
          id="usernameInput"
          name="username"
          ref={inputRef}
          onChange={handleChange}
        />
      </div>
      <div style={{color: 'red'}}>{error}</div>
      <button type="submit" disabled={Boolean(error)}>
        Submit
      </button>
    </form>
  )
}

function App() {
  const onSubmitUsername = username => alert(`You entered: ${username}`)
  return <UsernameForm onSubmitUsername={onSubmitUsername} />
}
```



## Exercise

Create a controlled input:

```jsx
function UsernameForm({onSubmitUsername}) {
  const [inputValue, setInputValue] = React.useState('')

  function handleSubmit(event) {
    event.preventDefault()
    onSubmitUsername(inputValue)
  }

  function handleChange(event) {
    setInputValue(event.target.value.toLowerCase())
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="usernameInput">Username:</label>
        <input
          type="text"
          id="usernameInput"
          name="username"
          onChange={handleChange}
          value={inputValue}
        />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}

function App() {
  const onSubmitUsername = username => alert(`You entered: ${username}`)
  return <UsernameForm onSubmitUsername={onSubmitUsername} />
}
```

