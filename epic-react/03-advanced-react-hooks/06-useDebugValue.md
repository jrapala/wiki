# useDebugValue: useMedia

Useful in custom hooks only (can't be used in components).

Used with the React DevTools browser extension.

Let's you see debug values:

![](https://juliette-images.s3.us-east-2.amazonaws.com/public/06-devtools-after.png)



## Example:

```jsx
function useCount({initialCount = 0, step = 1} = {}) {
  React.useDebugValue({initialCount, step})
  const [count, setCount] = React.useState(0)
  const increment = () => setCount(c => c + 1)
  return [count, increment]
}
```



It also takes a second argument for an optional formatter:

```jsx
const formatCountDebugValue = ({initialCount, step}) =>
  `init: ${initialCount}; step: ${step}`

function useCount({initialCount = 0, step = 1} = {}) {
  React.useDebugValue({initialCount, step}, formatCountDebugValue)
  const [count, setCount] = React.useState(0)
  const increment = () => setCount(c => c + 1)
  return [count, increment]
}
```

This is only useful if the formating is computationally expensive (no need for it in production)

## Exercise

Add debug value to a `useMedia` hook:

```jsx
// useDebugValue: useMedia
// http://localhost:3000/isolated/exercise/06.js

import * as React from 'react'

function useMedia(query, initialState = false) {
  const [state, setState] = React.useState(initialState)
  React.useDebugValue(`\`${query}\` => ${state}`)

  React.useEffect(() => {
    let mounted = true
    const mql = window.matchMedia(query)
    function onChange() {
      if (!mounted) {
        return
      }
      setState(Boolean(mql.matches))
    }

    mql.addListener(onChange)
    setState(mql.matches)

    return () => {
      mounted = false
      mql.removeListener(onChange)
    }
  }, [query])

  return state
}

function Box() {
  const isBig = useMedia('(min-width: 1000px)')
  const isMedium = useMedia('(max-width: 999px) and (min-width: 700px)')
  const isSmall = useMedia('(max-width: 699px)')
  const color = isBig ? 'green' : isMedium ? 'yellow' : isSmall ? 'red' : null

  return <div style={{width: 200, height: 200, backgroundColor: color}} />
}

function App() {
  return <Box />
}

export default App

```



With formatter (though this doesn't make sense to do--costs more to make the function):

```jsx
// useDebugValue: useMedia
// http://localhost:3000/isolated/exercise/06.js

import * as React from 'react'

const formatDebugValue = ({query, state}) => `\`${query}\` => ${state}`

function useMedia(query, initialState = false) {
  const [state, setState] = React.useState(initialState)
  React.useDebugValue({query, state}, formatDebugValue)
	....


```

