# useReducer: Simple Counter

`useReducer` is typically used with an object of state.

When to use it:

- **When it's just an independent element of state you're managing: `useState`**
- **When one element of your state relies on the value of another element of your state in order to update: `useReducer`**



`useReducer` is useful for:

- Separate the state logic from the components that make the state changes.

- When multiple elements of state typically change together--having an object that contains those elements of state can be quite helpful.

## Example

```jsx
function nameReducer(previousName, newName) {
  return newName
}

const initialNameValue = 'Joe'

function NameInput() {
  const [name, setName] = React.useReducer(nameReducer, initialNameValue)
  const handleChange = event => setName(event.target.value)
  return (
    <>
      <label>
        Name: <input defaultValue={name} onChange={handleChange} />
      </label>
      <div>You typed: {name}</div>
    </>
  )
}
```

The `nameReducer` takes two arguments:

1. The current state
2. Whatever the dispatch function (setName) is called with. aka "the action"



## More Complicated Example

Changing a `useState` implementation:

```jsx
function useUndo(initialPresent) {
  const [state, setState] = React.useState({
    past: [],
    present: initialPresent,
    future: [],
  })
  const canUndo = state.past.length !== 0
  const canRedo = state.future.length !== 0
  const undo = React.useCallback(() => {
    setState(currentState => {
      const {past, present, future} = currentState
      if (past.length === 0) return currentState
      const previous = past[past.length - 1]
      const newPast = past.slice(0, past.length - 1)
      return {
        past: newPast,
        present: previous,
        future: [present, ...future],
      }
    })
  }, [])
  const redo = React.useCallback(() => {
    setState(currentState => {
      const {past, present, future} = currentState
      if (future.length === 0) return currentState
      const next = future[0]
      const newFuture = future.slice(1)
      return {
        past: [...past, present],
        present: next,
        future: newFuture,
      }
    })
  }, [])
  const set = React.useCallback(newPresent => {
    setState(currentState => {
      const {present, past} = currentState
      if (newPresent === present) return currentState
      return {
        past: [...past, present],
        present: newPresent,
        future: [],
      }
    })
  }, [])
  const reset = React.useCallback(newPresent => {
    setState(() => ({
      past: [],
      present: newPresent,
      future: [],
    }))
  }, [])
  return [state, {set, reset, undo, redo, canUndo, canRedo}]
}
```



to `useReducer`:

```jsx
const UNDO = 'UNDO'
const REDO = 'REDO'
const SET = 'SET'
const RESET = 'RESET'
function undoReducer(state, action) {
  const {past, present, future} = state
  const {type, newPresent} = action
  switch (action.type) {
    case UNDO: {
      if (past.length === 0) return state
      const previous = past[past.length - 1]
      const newPast = past.slice(0, past.length - 1)
      return {
        past: newPast,
        present: previous,
        future: [present, ...future],
      }
    }
    case REDO: {
      if (future.length === 0) return state
      const next = future[0]
      const newFuture = future.slice(1)
      return {
        past: [...past, present],
        present: next,
        future: newFuture,
      }
    }
    case SET: {
      if (newPresent === present) return state
      return {
        past: [...past, present],
        present: newPresent,
        future: [],
      }
    }
    case RESET: {
      return {
        past: [],
        present: newPresent,
        future: [],
      }
    }
  }
}
function useUndo(initialPresent) {
  const [state, dispatch] = React.useReducer(undoReducer, {
    past: [],
    present: initialPresent,
    future: [],
  })
  const canUndo = state.past.length !== 0
  const canRedo = state.future.length !== 0
  const undo = React.useCallback(() => dispatch({type: UNDO}), [])
  const redo = React.useCallback(() => dispatch({type: REDO}), [])
  const set = React.useCallback(
    newPresent => dispatch({type: SET, newPresent}),
    [],
  )
  const reset = React.useCallback(
    newPresent => dispatch({type: RESET, newPresent}),
    [],
  )
  return [state, {set, reset, undo, redo, canUndo, canRedo}]
}
```



## Using lazy state initialization with useReducer

```jsx
const reducerFn = (prevState, dispatchArg) => newState
const initializationFn = initialArg => initialArg
const [state, dispatch] = useReducer(reducerFn, initialArg, initializationFn)
```



or: 

```jsx
function init(initialStateFromProps) {
  return {
    pokemon: null,
    loading: false,
    error: null,
  }
}

// ...

const [state, dispatch] = React.useReducer(reducer, props.initialState, init)
```



## Exercises

### Exercise #1: Switch useState to useReducer

Switch a `useState` implementation:

```jsx
import * as React from 'react'

function Counter({initialCount = 0, step = 1}) {
  const [count, setCount] = React.useState(initialCount)

  const increment = () => setCount(count + step)

  return <button onClick={increment}>{count}</button>
}

function App() {
  return <Counter />
}

export default App

```



to `useReducer`:

```jsx
import * as React from 'react'

// return the new state
function countReducer(state, newState) {
  return newState
}

function Counter({initialCount = 0, step = 1}) {
  // pass in a reducer and the initial state
  const [count, setCount] = React.useReducer(countReducer, initialCount)

  // reducer will get new state as the action
  const increment = () => setCount(count + step)

  return <button onClick={increment}>{count}</button>
}

function App() {
  return <Counter />
}

export default App

```



### Exercise #2: Accept "Step" as an Action

```jsx
import * as React from 'react'

// Return the current state plus the step
function countReducer(state, step) {
  return state + step
}

function Counter({initialCount = 0, step = 1}) {
  const [count, changeCount] = React.useReducer(countReducer, initialCount)

  // We just want to increment by the step.
  const increment = () => changeCount(step)

  return <button onClick={increment}>{count}</button>
}

function App() {
  return <Counter />
}

export default App
```



### Exercise #3: Refactor to simulate setState

```jsx
import * as React from 'react'

// We are now passing in an action. Take the original state and override it with the action.
function countReducer(state, action) {
  return {...state, ...action}
}

function Counter({initialCount = 0, step = 1}) {
  // Use more generic naming. Pass object for initial state.
  const [state, setState] = React.useReducer(countReducer, {
    count: initialCount,
  })

  const {count} = state
  // update state
  const increment = () => setState({count: count + step})

  return <button onClick={increment}>{count}</button>
}

function App() {
  return <Counter />
}

export default App

import * as React from 'react'

function countReducer(state, newState) {
  return {count: newState.count}
}


```



### Exercise #4: Accept function

`this.setState` from class components can also accept a function. 

Add that support:

```jsx
import * as React from 'react'

function countReducer(state, action) {
  return {
    ...state,
    // Check if the action is a function. If it is, calle it with the current state.
    ...(typeof action === 'function' ? action(state) : action),
  }
}

function Counter({initialCount = 0, step = 1}) {
  const [state, setState] = React.useReducer(countReducer, {
    count: initialCount,
  })

  const {count} = state
  const increment = () =>
  	// The action here is now a function
    setState(currentState => ({count: currentState.count + step}))

  return <button onClick={increment}>{count}</button>
}

function App() {
  return <Counter />
}

export default App

```



### Exercise #5: Traditional dispatch object with a type and switch statement

```jsx
import * as React from 'react'

function countReducer(state, action) {
  // check the action type
  switch (action.type) {
    case 'INCREMENT':
      // on increment, return state count plus the payload step
      return {count: state.count + action.step}
    default:
      throw new Error('unsupported action.type')
  }
}

function Counter({initialCount = 0, step = 1}) {
  const [state, dispatch] = React.useReducer(countReducer, {
    count: initialCount,
  })

  const {count} = state
  // Add redux style action type and payload
  const increment = () => dispatch({type: 'INCREMENT', step})

  return <button onClick={increment}>{count}</button>
}

function App() {
  return <Counter />
}

export default App
```

