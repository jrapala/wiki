# Flexible Compound Components

Allows for nested components in compound components. No need to traverse a tree--j

**Real World Projects that use this pattern:**

- [`@reach/accordion`](https://reacttraining.com/reach-ui/accordion)



## Exercise

### Allow for Nested Components

```jsx
import * as React from 'react'
import {Switch} from '../switch'

// 2. Create context
const ToggleContext = React.createContext()
ToggleContext.displayName = 'ToggleContext'

// 1. Turn <Toggle> into a context provider
function Toggle({onToggle, children}) {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)

  return (
    <ToggleContext.Provider value={{on, toggle}}>
      {children}
    </ToggleContext.Provider>
  )
}

// 3. Make a custom context hook
function useToggle() {
  const context = React.useContext(ToggleContext)

  if (!context) {
    throw new Error(
      `useToggle must be rendered within a <Toggle />`,
    )
  }
  return context
}

// 4. Update child composite components to useToggle()
function ToggleOn({children}) {
  const {on} = useToggle()
  return on ? children : null
}

function ToggleOff({children}) {
  const {on} = useToggle()
  return on ? null : children
}

function ToggleButton({...props}) {
  const {on, toggle} = useToggle()
  return <Switch on={on} onClick={toggle} {...props} />
}

function App() {
  return (
    <div>
      <Toggle>
        <ToggleOn>The button is on</ToggleOn>
        <ToggleOff>The button is off</ToggleOff>
        <div>
          <ToggleButton />
        </div>
      </Toggle>
    </div>
  )
}

export default App
```

