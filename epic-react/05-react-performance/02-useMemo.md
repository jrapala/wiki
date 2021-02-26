# useMemo for Expensive Calculations

Use `useMemo` for expensive functions so you don't have to rerun them after every render:

```jsx
function Distance({x, y}) {
  const distance = React.useMemo(() => calculateDistance(x, y), [x, y])
  return (
    <div>
      The distance between {x} and {y} is {distance}.
    </div>
  )
}
```

This allows us to put that calculation behind a function which is only called when the result actually needs to be re-evaluated (when the dependencies change). 



## Using DevTools

1. Performance > Settings Gear > Set CPU from “no throttling” to “6x slowdown.” 
2. Click the “Record” circle icon
3. Force rerender
4. Stop Record
5. Check out what functions were called. Look for red lines in graph, then look for very wide functions in those red line areas.



## Exercises

### Wrap a Function in useMemo

```jsx
const allItems = React.useMemo(() => getItems(inputValue), [inputValue])
```

`getItems` matches an input against a giant JSON file. Only run it if the input changes.



### Production Mode

Always run your app in production mode when profiling measuring. There's a lot in development mode that slows React down.

The goal is to get the JavaScript to run in 16ms (1000ms / 60FPS).



## Web Workers

[Learn about Web Workers](https://kentcdodds.com/blog/speed-up-your-app-with-web-workers)

A web worker lets you offload expensive functions off the main browser thread

```jsx
// src/workerized-filter-cities.js

// Uses a webpack loader called workerize! to use web workers
// Gives a factory function back
import makeFilterCitiesWorker from 'workerize!./filter-cities'

// Use the factory function to create a web worker
// getItems is now asynchronous
const {getItems} = makeFilterCitiesWorker()

export {getItems}

/*
eslint
  import/no-webpack-loader-syntax: 0,
*/

```

```jsx
// src/exercise/02.js

// change import
import {getItems} from '../workerized-filter-cities'


// Use getItems asynchronously

  const {data: allItems, run} = useAsync({data: [], status: 'pending'})
  
  React.useEffect(() => {
    run(getItems(inputValue))
  }, [inputValue, run]
  )

  const items = allItems.slice(0, 100)
```

