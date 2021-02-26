# Prop Collections and Getters

For a button functioning as a toggle, it should have the `aria-pressed` attribute set to `true` or `false` if it’s toggled on or off. In addition to remembering that, people need to remember to also add the `onClick` handler to call `toggle`.

Lots of the reusable/flexible components and hooks that we’ll create have some common use-cases and it’d be cool if we could make it easier to use our components and hooks the right way without requiring people to wire things up for common use cases.

**Real World Projects that use this pattern:**

- [downshift](https://github.com/downshift-js/downshift) (uses prop getters)
- [react-table](https://github.com/tannerlinsley/react-table) (uses prop getters)
- [`@reach/tooltip`](https://reacttraining.com/reach-ui/tooltip) (uses prop collections)



## Exercise

### Prop Collections

Apply props to the toggle that should probably be added in cases like accessibilty. 

```jsx
import * as React from 'react'
import {Switch} from '../switch'

function useToggle() {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)

  // These togglerProps contain all the things we would typically add to the component
  return {on, toggle, togglerProps: {'aria-pressed': on, onClick: toggle}}
}

function App() {
  const {on, togglerProps} = useToggle()
  return (
    <div>
      <Switch on={on} {...togglerProps} />
      <hr />
      <button aria-label="custom-button" {...togglerProps}>
        {on ? 'on' : 'off'}
      </button>
    </div>
  )
}

export default App
```



### Prop Getters

Kent uses this more often. Allows passing in a custom onClick instead.

```jsx
import * as React from 'react'
import {Switch} from '../switch'

function useToggle() {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)

  function getTogglerProps({onClick, ...props} = {}) {
    return {
      'aria-pressed': on,
      onClick: () => {
        // call onClick if it's truthy
        onClick && onClick()
        toggle()
      },
      ...props,
    }
  }
  return {
    on,
    toggle,
    getTogglerProps,
  }
}

function App() {
  const {on, getTogglerProps} = useToggle()
  return (
    <div>
      <Switch {...getTogglerProps({on})} />
      <hr />
      <button
        // Invoke a function instead
        // This will accept all the props that you want to pass and
        // composes them into a function that works and returns all
        // the props spread together
        {...getTogglerProps({
          'aria-label': 'custom-button',
          onClick: () => console.info('onButtonClick'),
        })}
      >
        {on ? 'on' : 'off'}
      </button>
    </div>
  )
}

export default App
```



**What if I want to pass in a lot of functions to the setter?**

A cleaner way to do this is with a `callAll` function:

```jsx
import * as React from 'react'
import {Switch} from '../switch'

// callAll takes any number of functions
function callAll(...fns) {
  // returns a function that takes any number of arguments
  return (...args) => {
    fns.forEach(fn => {
      // if the function exists, call it with all of the args
      fn && fn(...args)
    })
  }
}

function useToggle() {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)

  function getTogglerProps({onClick, ...props} = {}) {
    return {
      'aria-pressed': on,
      // pass functions into callAll, to potentially call them all
      // onClick will only be called if it exists
      onClick: callAll(onClick, toggle),
      ...props,
    }
  }
  return {
    on,
    toggle,
    getTogglerProps,
  }
}

function App() {
  const {on, getTogglerProps} = useToggle()
  return (
    <div>
      <Switch {...getTogglerProps({on})} />
      <hr />
      <button
        // Invoke a function instead
        // This will accept all the props that you want to pass and
        // composes them into a function that works and returns all
        // the props spread together
        {...getTogglerProps({
          'aria-label': 'custom-button',
          onClick: () => console.info('onButtonClick'),
        })}
      >
        {on ? 'on' : 'off'}
      </button>
    </div>
  )
}

export default App


```



