# useState: Greeting

What is a hook? Hooks are special functions used to store data (like state) or perform action (or side-effects).

`React.useState` is a function that accepts a single argument. That argument is the initial state for the instance of the component.

`React.useState` returns a pair of values. It does this by returning an array with two elements (and we use destructuring syntax to assign each of those values to distinct variables). The first of the pair is the state value and the second is a function we can call to update the state.

What is **state**? Data that changes over time.

Every call to the state updater function rerenders the component.



## Exercise

Use `useState` to control display. Accept an `initialName` prop for the name.

Need to use a controlled input in order to render the intial value.

A controlled input has it's data handled by a React component. An uncontrolled input has it's data handled by the DOM.

```jsx
import * as React from 'react'

// set default to prevent it from being undefined (which triggers an error in <input />)
function Greeting({ initialName = '' }) {
  const [name, setName] = React.useState(initialName)

  function handleChange(event) {
    setName(event.target.value)
  }

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        // This is now a controlled input
        <input value={name} onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : 'Please type your name'}
    </div>
  )
}

function App() {
  return <Greeting initialName="Jane"/>
}

export default App

```

