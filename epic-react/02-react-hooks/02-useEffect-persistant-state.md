# useEffect: Presistent State

To interact with the outside word (e.g. HTTP requests, local storage), use the `useEffect` hook.

`useEffect` lets you run code after React renders (and re-renders) your component to the DOM.

It accept a callback function that runs after the DOM has been updated.



## Exercise

Get intialState from local storage and keep local storage updated:

```jsx
import * as React from 'react'

function Greeting({initialName = ''}) {
  const [name, setName] = React.useState(
    window.localStorage.getItem('name') || initialName,
  )

  // Every time the component rerenders, the useEffect callback gets called.
  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  })

  function handleChange(event) {
    setName(event.target.value)
  }

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input value={name} onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : 'Please type your name'}
    </div>
  )
}

function App() {
  return <Greeting />
}

export default App

```



### Improving Performance with Lazy State Initialization 

Unfortunately, localStorage is read every single time the component function is called. Not good.

To fix this, update the `useState` hook to use lazy state initialization:

```jsx
import * as React from 'react'

function Greeting({initialName = ''}) {
  // pass in a function to get the initial value after first render only
  const [name, setName] = React.useState(() => window.localStorage.getItem('name') || initialName)

  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  })

  function handleChange(event) {
    setName(event.target.value)
  }

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input value={name} onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : 'Please type your name'}
    </div>
  )
}

function App() {
  return <Greeting />
}

export default App

```

The new `getInitialNameValue` function is created during every render which is fine. It is however only called, which is expensive, on the initial render of the component.



### Improving Performance with useEffect Dependencies

We don't need to save to localStorage after each render. We only want it to update after the `name` is changed.

No array of dependencies = run after every render.

The `useEffect` **dependency array** allows you to signal to React that the `useEffect` callback function should only be called when these dependencies change.

```jsx
  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  }, [name])
```

An empty dependency array means it will only run on mount and unmount, but there are better ways of doing this: [Link](https://reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies)

**Note**: This only performs a shallow compare. Passing in an object will cause it to run every single time. A shallow compare checks if both objects point to the same object (reference value)



### Extracting to a Custom Hook

A custom hook must have a name that starts with "use" (or ESLint will yell at you).

```jsx
import * as React from 'react'

// Starting the function name with "use" prevents breaking the rule of hooks ESLint rule
const useSyncLocalStorageWithState = (key, defaultValue = '') => {
  const [state, setState] = React.useState(() => getInitialNameValue())

  function getInitialNameValue() {
    return window.localStorage.getItem(key) || defaultValue
  }

  React.useEffect(() => {
    window.localStorage.setItem(key, state)
  }, [key, state])

  return [state, setState]
}

function Greeting({initialName = ''}) {
  const [name, setName] = useSyncLocalStorageWithState('name', initialName)

  function handleChange(event) {
    setName(event.target.value)
  }

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input value={name} onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : 'Please type your name'}
    </div>
  )
}

function App() {
  return <Greeting />
}

export default App

```



### Custom Hook: Adding Serialization Options

Prevents errors such as passing in numbers for the state:

```jsx
import * as React from 'react'

const useSyncLocalStorageWithState = (
  key,
  defaultValue = '',
  // Accept additional options with set defaults
  {serialize = JSON.stringify, deserialize = JSON.parse} = {},
) => {
  const [state, setState] = React.useState(() => getInitialNameValue())

  function getInitialNameValue() {
    const valueInLocalStorage = window.localStorage.getItem(key)
    if (valueInLocalStorage) {
      // Use deserialize function
      return deserialize(valueInLocalStorage)
    }
    return defaultValue
  }

  React.useEffect(() => {
    // Use serialize function
    window.localStorage.setItem(key, serialize(state))
  }, [key, serialize, state]) // update deps -- could be a different serialize function

  return [state, setState]
}

```



### Custom Hook: Accepting Functions for States

Sometimes, you may need to calculate the state to save. Give the option to pass in a function as the default value.

```jsx
const useSyncLocalStorageWithState = (
  key,
  defaultValue = '',
  {serialize = JSON.stringify, deserialize = JSON.parse} = {},
) => {
  const [state, setState] = React.useState(() => getInitialNameValue())

  function getInitialNameValue() {
    const valueInLocalStorage = window.localStorage.getItem(key)
    if (valueInLocalStorage) {
      return deserialize(valueInLocalStorage)
    }

    // Check for a function
    return typeof defaultValue === "function" ? defaultValue() : defaultValue
  }

  React.useEffect(() => {
    window.localStorage.setItem(key, serialize(state))
  }, [key, serialize, state])

  return [state, setState]
}

```



### Custom Hook: Updating Keys

Sometimes, a user may change the name of they key they wish to use:

```jsx
const useSyncLocalStorageWithState = (
  key,
  defaultValue = '',
  {serialize = JSON.stringify, deserialize = JSON.parse} = {},
) => {
  const [state, setState] = React.useState(() => getInitialNameValue())

  function getInitialNameValue() {
    const valueInLocalStorage = window.localStorage.getItem(key)
    if (valueInLocalStorage) {
      return deserialize(valueInLocalStorage)
    }

    return typeof defaultValue === "function" ? defaultValue() : defaultValue
  }

  // A useRef gives you an object that you can mutate without triggering rerenders
  const prevKeyRef = React.useRef(key)

  React.useEffect(() => {
    const prevKey = prevKeyRef.current
    // Check if the key and previous key are the same. If not, remove the old key.
    if (prevKey !== key) {
      window.localStorage.removeItem(prevKey)
    }
    // Update the previous key name
    prevKeyRef.current = key
    window.localStorage.setItem(key, serialize(state))
  }, [key, serialize, state])

  return [state, setState]
}
```



