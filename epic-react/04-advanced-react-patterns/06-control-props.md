# Control Props

Sometimes, people want to be able to manage the internal state of our component from the outside. The state reducer allows them to manage what state changes are made when a state change happens, but sometimes people may want to make state changes themselves. We can allow them to do this with a feature called "Control Props."

**Real World Projects that use this pattern:**

- [downshift](https://github.com/downshift-js/downshift)
- [`@reach/listbox`](https://reacttraining.com/reach-ui/listbox)



## Exercises

### Control Value of Toggles, While Allowing User Changes

```jsx
// Control Props
// http://localhost:3000/isolated/exercise/06.js

import * as React from 'react'
import {Switch} from '../switch'

const callAll = (...fns) => (...args) => fns.forEach(fn => fn?.(...args))

const actionTypes = {
  toggle: 'toggle',
  reset: 'reset',
}

function toggleReducer(state, {type, initialState}) {
  switch (type) {
    case actionTypes.toggle: {
      return {on: !state.on}
    }
    case actionTypes.reset: {
      return initialState
    }
    default: {
      throw new Error(`Unsupported type: ${type}`)
    }
  }
}

function useToggle({
  initialOn = false,
  reducer = toggleReducer,
  // 1. Add onChange prop
  onChange,
  // 4. Add on prop. It only exists in a controlled component.
  on: controlledOn,
} = {}) {
  const {current: initialState} = React.useRef({on: initialOn})
  const [state, dispatch] = React.useReducer(reducer, initialState)
  // 5. Find out if this is a controlled component. It will be if controlledOn exists.
  const onIsControlled = controlledOn != null

  // 6. If onIsControlled, grab the user's value,
  // otherwise we are managing it ourselves, so grab from state
  const on = onIsControlled ? controlledOn : state.on

  // 2. Create a dispatchWithOnChange function.
  function dispatchWithOnChange(action) {
    // 7. Only call dispatch if on is not controlled
    if (!onIsControlled) {
      dispatch(action)
    }
    // 8. Update state to include new on value
    const newState = reducer({...state, on}, action)
    // Call the onChange, if it exists, with the new state
    onChange?.(newState, action)
  }
  // 3. Use dispatchWithOnChange instead of dispatch
  const toggle = () => dispatchWithOnChange({type: actionTypes.toggle})
  const reset = () =>
    dispatchWithOnChange({type: actionTypes.reset, initialState})

  function getTogglerProps({onClick, ...props} = {}) {
    return {
      'aria-pressed': on,
      onClick: callAll(onClick, toggle),
      ...props,
    }
  }

  function getResetterProps({onClick, ...props} = {}) {
    return {
      onClick: callAll(onClick, reset),
      ...props,
    }
  }

  return {
    on,
    reset,
    toggle,
    getTogglerProps,
    getResetterProps,
  }
}

function Toggle({on: controlledOn, onChange}) {
  const {on, getTogglerProps} = useToggle({on: controlledOn, onChange})
  const props = getTogglerProps({on})
  return <Switch {...props} />
}

function App() {
  const [bothOn, setBothOn] = React.useState(false)
  const [timesClicked, setTimesClicked] = React.useState(0)

  function handleToggleChange(state, action) {
    if (action.type === actionTypes.toggle && timesClicked > 4) {
      return
    }
    setBothOn(state.on)
    setTimesClicked(c => c + 1)
  }

  function handleResetClick() {
    setBothOn(false)
    setTimesClicked(0)
  }

  return (
    <div>
      <div>
        <Toggle on={bothOn} onChange={handleToggleChange} />
        <Toggle on={bothOn} onChange={handleToggleChange} />
      </div>
      {timesClicked > 4 ? (
        <div data-testid="notice">
          Whoa, you clicked too much!
          <br />
        </div>
      ) : (
        <div data-testid="click-count">Click count: {timesClicked}</div>
      )}
      <button onClick={handleResetClick}>Reset</button>
      <hr />
      <div>
        <div>Uncontrolled Toggle:</div>
        <Toggle
          onChange={(...args) =>
            console.info('Uncontrolled Toggle onChange', ...args)
          }
        />
      </div>
    </div>
  )
}

export default App
export {Toggle}


```



### Add Warnings for Controlled Prop Misuse

We should issue the same warnings for people who misuse our controlled props:

1. Passing `on` without `onChange`
2. Passing a value for `on` and later passing `undefined` or `null`
3. Passing `undefined` or `null` for `on` and later passing a value

You can use the `warning` package to do this:

```javascript
warning(doNotWarn, 'Warning message')

// so:
warning(false, 'This will warn')
warning(true, 'This will not warn')
```

A real-world component that does this is [`@reach/listbox`](https://reacttraining.com/reach-ui/listbox/)



**1. Passing `on`** **without `onChange`**

Just trigger side effect is on is controlled, and onChange is not provided. In `useToggle()`:

```jsx
import * as React from 'react'
import {Switch} from '../switch'

const callAll = (...fns) => (...args) => fns.forEach(fn => fn?.(...args))

const actionTypes = {
  toggle: 'toggle',
  reset: 'reset',
}

function toggleReducer(state, {type, initialState}) {
  switch (type) {
    case actionTypes.toggle: {
      return {on: !state.on}
    }
    case actionTypes.reset: {
      return initialState
    }
    default: {
      throw new Error(`Unsupported type: ${type}`)
    }
  }
}

function useToggle({
  initialOn = false,
  reducer = toggleReducer,
  onChange,
  on: controlledOn,
  // accept readOnly
  readOnly = false,
} = {}) {
  const {current: initialState} = React.useRef({on: initialOn})
  const [state, dispatch] = React.useReducer(reducer, initialState)
  const onIsControlled = controlledOn != null
  const on = onIsControlled ? controlledOn : state.on

  // Check for onChange
  const hasOnChange = Boolean(onChange)

  // Add useEffect
  React.useEffect(() => {
    if (!hasOnChange && onIsControlled && !readOnly) {
      console.error(
        `An \`on\` prop was provided to useToggle without an \`onChange\` handler. This will render a read-only toggle. If you want it to be mutable, use \`initialOn\`. Otherwise, set either \`onChange\` or \`readOnly\`.`,
      )
    }
  }, [hasOnChange, onIsControlled, readOnly])

  function dispatchWithOnChange(action) {
    if (!onIsControlled) {
      dispatch(action)
    }
    const newState = reducer({...state, on}, action)
    onChange?.(newState, action)
  }
  const toggle = () => dispatchWithOnChange({type: actionTypes.toggle})
  const reset = () =>
    dispatchWithOnChange({type: actionTypes.reset, initialState})

  function getTogglerProps({onClick, ...props} = {}) {
    return {
      'aria-pressed': on,
      onClick: callAll(onClick, toggle),
      ...props,
    }
  }

  function getResetterProps({onClick, ...props} = {}) {
    return {
      onClick: callAll(onClick, reset),
      ...props,
    }
  }

  return {
    on,
    reset,
    toggle,
    getTogglerProps,
    getResetterProps,
  }
}

// Pass in readOnly
function Toggle({on: controlledOn, onChange, readOnly}) {
  // Pass in readOnly
  const {on, getTogglerProps} = useToggle({
    on: controlledOn,
    onChange,
    readOnly,
  })
  const props = getTogglerProps({on})
  return <Switch {...props} />
}

function App() {
  const [bothOn, setBothOn] = React.useState(false)
  const [timesClicked, setTimesClicked] = React.useState(0)

  function handleToggleChange(state, action) {
    if (action.type === actionTypes.toggle && timesClicked > 4) {
      return
    }
    setBothOn(state.on)
    setTimesClicked(c => c + 1)
  }

  function handleResetClick() {
    setBothOn(false)
    setTimesClicked(0)
  }

  return (
    <div>
      <div>
        <Toggle on={bothOn} onChange={handleToggleChange} />
        {*/ Create readOnly toggle with no onChange */}
        <Toggle on={bothOn} readOnly />
      </div>
      {timesClicked > 4 ? (
        <div data-testid="notice">
          Whoa, you clicked too much!
          <br />
        </div>
      ) : (
        <div data-testid="click-count">Click count: {timesClicked}</div>
      )}
      <button onClick={handleResetClick}>Reset</button>
      <hr />
      <div>
        <div>Uncontrolled Toggle:</div>
        <Toggle
          onChange={(...args) =>
            console.info('Uncontrolled Toggle onChange', ...args)
          }
        />
      </div>
    </div>
  )
}



```



Or use `warning` package:

```jsx
 React.useEffect(() => {
    // Show warning if first argument is false
    warning(
      !(!hasOnChange && onIsControlled && !readOnly),
      `An \`on\` prop was provided to useToggle without an \`onChange\` handler. This will render a read-only toggle. If you want it to be mutable, use \`initialOn\`. Otherwise, set either \`onChange\` or \`readOnly\`.`,
    )
  }, [hasOnChange, onIsControlled, readOnly])
```



**2. Passing a value for `on` and later passing `undefined` or `null` OR Passing `undefined` or `null` for `on` and later passing a value** 

```jsx
// Collect initial value. If it ever changes, it was changed from
  // controlled to uncontrolled, or vice versa
  const {current: onWasControlled} = React.useRef(onIsControlled)
  React.useEffect(() => {
    warning(
      !(onIsControlled && !onWasControlled),
      `\`useToggle\` is changing from uncontrolled to be controlled. Components should not switch from uncontrolled to controlled (or vice versa). Decide between using a controlled or uncontrolled \`useToggle\` for the lifetime of the component. Check the \`on\` prop.`,
    )
    warning(
      !(!onIsControlled && onWasControlled),
      `\`useToggle\` is changing from controlled to be uncontrolled. Components should not switch from controlled to uncontrolled (or vice versa). Decide between using a controlled or uncontrolled \`useToggle\` for the lifetime of the component. Check the \`on\` prop.`,
    )
  }, [onIsControlled, onWasControlled])
```



### Extracting Warnings to a Custom Hook

```jsx
function useControlledSwitchWarning(
  controlPropValue,
  controlPropName,
  componentName,
) {
  const isControlled = controlPropValue != null
  const {current: wasControlled} = React.useRef(isControlled)
  React.useEffect(() => {
    warning(
      !(isControlled && !wasControlled),
      `\`${componentName}\` is changing from uncontrolled to be controlled. Components should not switch from uncontrolled to controlled (or vice versa). Decide between using a controlled or uncontrolled \`${componentName}\` for the lifetime of the component. Check the \`${controlPropName}\` prop.`,
    )
    warning(
      !(!isControlled && wasControlled),
      `\`${componentName}\` is changing from controlled to be uncontrolled. Components should not switch from controlled to uncontrolled (or vice versa). Decide between using a controlled or uncontrolled \`${componentName}\` for the lifetime of the component. Check the \`${controlPropName}\` prop.`,
    )
  }, [isControlled, wasControlled, componentName, controlPropName])
```

In `useToggle`:

```jsx
useControlledSwitchWarning(controlledOn, 'on', 'useToggle')
```



```jsx
function useOnChangeReadOnlyWarning(
  controlPropValue,
  controlPropName,
  componentName,
  hasOnChange,
  readOnly,
  readOnlyProp,
  initialValueProp,
  onChangeProp,
) {
  const isControlled = controlPropValue != null
  React.useEffect(() => {
    warning(
      !(!hasOnChange && isControlled && !readOnly),
      `A \`${controlPropName}\` prop was provided to \`${componentName}\` without an \`${onChangeProp}\` handler. This will result in a read-only \`${controlPropName}\` value. If you want it to be mutable, use \`${initialValueProp}\`. Otherwise, set either \`${onChangeProp}\` or \`${readOnlyProp}\`.`,
    )
  }, [
    componentName,
    controlPropName,
    isControlled,
    hasOnChange,
    readOnly,
    onChangeProp,
    initialValueProp,
    readOnlyProp,
  ])
}

```

In `useToggle`:

```jsx
  useOnChangeReadOnlyWarning(
    controlledOn,
    'on',
    'useToggle',
    Boolean(onChange),
    readOnly,
    'readOnly',
    'initialOn',
    'onChange',
  )
```



### Remove Warnings in Production

You can tell whether we’re running in production with `process.env.NODE_ENV === 'production'`

In `useToggle`:

```jsx
if (process.env.NODE_ENV !== 'production') {
  	// This is the one time it's okay to break the rules of hooks
  	// The process.env will NEVER change in the lifetime of the app
    // eslint-disable-next-line react-hooks/rules-of-hooks
    useControlledSwitchWarning(controlledOn, 'on', 'useToggle')
    // eslint-disable-next-line react-hooks/rules-of-hooks
    useOnChangeReadOnlyWarning(
      controlledOn,
      'on',
      'useToggle',
      Boolean(onChange),
      readOnly,
      'readOnly',
      'initialOn',
      'onChange',
    )
  }
```

