# useCallback: Custom Hooks

The problem with the following code is that updateLocalStorage is re-initialized every render. So, it's brand new every render. So, useEffect will be called every render.

```jsx
const updateLocalStorage = () => window.localStorage.setItem('count', count)

React.useEffect(() => {
	updateLocalStorage()
}, [updateLocalStorage])
```



You can solve this with `useCallback`:

```jsx
const updateLocalStorage = React.useCallback(
	() => window.localStorage.setItem('count', count),
	[count]
)

React.useEffect(() => {
	updateLocalStorage()
}, [updateLocalStorage])
```

We still create a new function every render (to pass to `useCallback`), however React will only give us the new function if the dependency list changes. If `count` doesn't change, `updateLocalStorage` stays the same.

## Why not use useCallback eveywhere?

Post: https://kentcdodds.com/blog/usememo-and-usecallback

Adding `useCallback` causes React to do more work. Using `useCallback` in this example actually decreases performance:

```jsx
const dispense = candy => {
  setCandies(allCandies => allCandies.filter(c => c !== candy))
}
const dispenseCallback = React.useCallback(dispense, [])
```

Just do this instead:

```jsx
const dispense = candy => {
  setCandies(allCandies => allCandies.filter(c => c !== candy))
}
```

**Performance optimizations are not free. They ALWAYS come with a cost but do NOT always come with a benefit to offset that cost.**



Similar case for useMemo. Not as performance intensive, but the benefits is miniscule in situations that don't need it.



## When should I use useCallback (and useMemo)?

These hooks exist because of:

1. Referential equality
2. Computationaly expensive calculations



### Prevent updates due to referential checks

Example of bad code. `useEffect` does a referential equality check. `options` will be new every render. `useEffect` gets called every time.

```jsx
function Foo({bar, baz}) {
  const options = {bar, baz}
  React.useEffect(() => {
    buzz(options)
  }, [options]) // we want this to re-run if bar or baz change
  return <div>foobar</div>
}

function Blub() {
  return <Foo bar="bar value" baz={3} />
}
```



Two ways to fix this:

```jsx
// option 1

function Foo({bar, baz}) {
  React.useEffect(() => {
    // move options object here
    const options = {bar, baz}
    buzz(options)
  }, [bar, baz]) // we want this to re-run if bar or baz change
  
  return <div>foobar</div>
}

function Blub() {
  return <Foo bar="bar value" baz={3} />
}
```

```jsx
// option 2 (if bar and baz are non-primitives)

function Foo({bar, baz}) {
  React.useEffect(() => {
    const options = {bar, baz}
    buzz(options)
  }, [bar, baz])
  return <div>foobar</div>
}

function Blub() {
  const bar = React.useCallback(() => {}, [])
  const baz = React.useMemo(() => [1, 2, 3], [])
  return <Foo bar={bar} baz={baz} />
}
```



## Preventing Unnecssary Rerenders

**Most of the time you should not bother optimizing unnecessary rerenders. React is VERY fast and you're usually wasting your time doing this. Only do this if you're rendering expensive items (graphs, charts, animations)**



```jsx
// original version
function CountButton({onClick, count}) {
  return <button onClick={onClick}>{count}</button>
}

// optimized version -- will only re-render when props change
const CountButton = React.memo(function CountButton({onClick, count}) {
  return <button onClick={onClick}>{count}</button>
})

function DualCounter() {
  const [count1, setCount1] = React.useState(0)
  // useCallback to prevent re-rendering since functions would be new
  const increment1 = React.useCallback(() => setCount1(c => c + 1), [])
  const [count2, setCount2] = React.useState(0)
  const increment2 = React.useCallback(() => setCount2(c => c + 1), [])
  return (
    <>
      <CountButton count={count1} onClick={increment1} />
      <CountButton count={count2} onClick={increment2} />
    </>
  )
}
```



Another example:

```jsx
function App() {
  const [body, setBody] = React.useState()
  const [status, setStatus] = React.useState('idle')
  const fetchConfig = React.useMemo(() => {
    return {
      method: 'POST',
      body,
      headers: {'content-type': 'application/json'},
    }
  }, [body])
  
  const makeFetchRequest = React.useCallback(
    () => (body ? fetch('/post', fetchConfig) : null),
    [body, fetchConfig],
  )
  
  React.useEffect(() => {
    const promise = makeFetchRequest()
    // if no promise was returned, then we didn't make a request
    // so we'll exit early
    if (!promise) return
    setStatus('pending')
    promise.then(
      () => setStatus('fulfilled'),
      () => setStatus('rejected'),
    )
  }, [makeFetchRequest])
  
  function handleSubmit(event) {
    event.preventDefault()
    // get form input values
    setBody(formInputValues)
  }
  
  return (
    <form onSubmit={handleSubmit}>
      {/* form inputs and other neat stuff... */}
    </form>
  )
}
```



## Using useMemo for computationally expensive functions

Getting prime numbers are expensive:

```jsx
// original
function RenderPrimes({iterations, multiplier}) {
  const primes = calculatePrimes(iterations, multiplier)
  return <div>Primes! {primes}</div>
}

// optimized
function RenderPrimes({iterations, multiplier}) {
  const primes = React.useMemo(() => calculatePrimes(iterations, multiplier), [
    iterations,
    multiplier,
  ])
  return <div>Primes! {primes}</div>
}
```



## Exercises

### Create a Generic useAsync Hook

```jsx
import * as React from 'react'
import {
  fetchPokemon,
  PokemonForm,
  PokemonDataView,
  PokemonInfoFallback,
  PokemonErrorBoundary,
} from '../pokemon'

function asyncReducer(state, action) {
  switch (action.type) {
    case 'pending': {
      return {status: 'pending', data: null, error: null}
    }
    case 'resolved': {
      return {status: 'resolved', data: action.data, error: null}
    }
    case 'rejected': {
      return {status: 'rejected', data: null, error: action.error}
    }
    default: {
      throw new Error(`Unhandled action type: ${action.type}`)
    }
  }
}

function useAsync(asyncCallback, initialState, dependencies) {
  const [state, dispatch] = React.useReducer(asyncReducer, {
    status: 'idle',
    data: null,
    error: null,
    ...initialState,
  })

  React.useEffect(() => {
    // You need to pass in a promise to get this to work
    const promise = asyncCallback()

    if (!promise) {
      return
    }

    dispatch({type: 'pending'})

    promise.then(
      data => {
        dispatch({type: 'resolved', data})
      },
      error => {
        dispatch({type: 'rejected', error})
      },
    )
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, dependencies)

  return state
}

function PokemonInfo({pokemonName}) {
  // We need to pass in dependenciesm, since this is inside the render. We don't want the useEffect
  // in useAsync to trigger over and over again.
  const state = useAsync(
    () => {
      if (!pokemonName) {
        return
      }
      return fetchPokemon(pokemonName)
    },
    {status: pokemonName ? 'pending' : 'idle'},
    [pokemonName],
  )

  const {data: pokemon, status, error} = state

  if (status === 'idle') {
    return 'Submit a pokemon'
  } else if (status === 'pending') {
    return <PokemonInfoFallback name={pokemonName} />
  } else if (status === 'rejected') {
    throw error
  } else if (status === 'resolved') {
    return <PokemonDataView pokemon={pokemon} />
  }

  throw new Error('This should be impossible')
}

function App() {
  const [pokemonName, setPokemonName] = React.useState('')

  function handleSubmit(newPokemonName) {
    setPokemonName(newPokemonName)
  }

  function handleReset() {
    setPokemonName('')
  }

  return (
    <div className="pokemon-info-app">
      <PokemonForm pokemonName={pokemonName} onSubmit={handleSubmit} />
      <hr />
      <div className="pokemon-info">
        <PokemonErrorBoundary onReset={handleReset} resetKeys={[pokemonName]}>
          <PokemonInfo pokemonName={pokemonName} />
        </PokemonErrorBoundary>
      </div>
    </div>
  )
}
```



### Refactor So We Don't have to Pass in a Dependency List (fixes ESLint error)

```jsx
function useAsync(asyncCallback, initialState) {
  const [state, dispatch] = React.useReducer(asyncReducer, {
    status: 'idle',
    data: null,
    error: null,
    ...initialState,
  })

  React.useEffect(() => {
    const promise = asyncCallback()

    if (!promise) {
      return
    }

    dispatch({type: 'pending'})

    promise.then(
      data => {
        dispatch({type: 'resolved', data})
      },
      error => {
        dispatch({type: 'rejected', error})
      },
    )
    // We can now run this only if the asyncCallback is a new function (which
    // happens if the pokemonName changes)
  }, [asyncCallback])

  return state
}

function PokemonInfo({pokemonName}) {
  // Move to a callback to prevent a new function from being formed every render
  const asyncCallback = React.useCallback(() => {
    if (!pokemonName) {
      return
    }
    return fetchPokemon(pokemonName)
  }, [pokemonName])

  // Remove dependency list
  const state = useAsync(asyncCallback, {
    status: pokemonName ? 'pending' : 'idle',
  })

  const {data: pokemon, status, error} = state

  if (status === 'idle') {
    return 'Submit a pokemon'
  } else if (status === 'pending') {
    return <PokemonInfoFallback name={pokemonName} />
  } else if (status === 'rejected') {
    throw error
  } else if (status === 'resolved') {
    return <PokemonDataView pokemon={pokemon} />
  }

  throw new Error('This should be impossible')
}

function App() {
  const [pokemonName, setPokemonName] = React.useState('')

  function handleSubmit(newPokemonName) {
    setPokemonName(newPokemonName)
  }

  function handleReset() {
    setPokemonName('')
  }

  return (
    <div className="pokemon-info-app">
      <PokemonForm pokemonName={pokemonName} onSubmit={handleSubmit} />
      <hr />
      <div className="pokemon-info">
        <PokemonErrorBoundary onReset={handleReset} resetKeys={[pokemonName]}>
          <PokemonInfo pokemonName={pokemonName} />
        </PokemonErrorBoundary>
      </div>
    </div>
  )
}
```



### Refactor So We Don't Depend on the User to Pass In a Memoized Function

Let's be real. No one is going to pass in a memoized function (the asyncCallback). We should memoize it for them.

```jsx
function useAsync(initialState) {
  const [state, dispatch] = React.useReducer(asyncReducer, {
    status: 'idle',
    data: null,
    error: null,
    ...initialState,
  })

	// This new run function takes a promise.
  const run = React.useCallback(promise => {
    dispatch({type: 'pending'})

    promise.then(
      data => {
        dispatch({type: 'resolved', data})
      },
      error => {
        dispatch({type: 'rejected', error})
      },
    )
    // ESLint already knows that dispatch will never change. So it
    // doesn't need to be added to the dependency list.
  }, [])

  // Export the run function
  return {...state, run}
}

function PokemonInfo({pokemonName}) {
  // Remove the memoized asyncCallback
  const state = useAsync({
    status: pokemonName ? 'pending' : 'idle',
  })

  // The new run function can now even be used in a click handler
  const {data: pokemon, status, error, run} = state

  React.useEffect(() => {
    if (!pokemonName) {
      return
    }
    return run(fetchPokemon(pokemonName))
  }, [pokemonName, run])

  if (status === 'idle') {
    return 'Submit a pokemon'
  } else if (status === 'pending') {
    return <PokemonInfoFallback name={pokemonName} />
  } else if (status === 'rejected') {
    throw error
  } else if (status === 'resolved') {
    return <PokemonDataView pokemon={pokemon} />
  }

  throw new Error('This should be impossible')
}
```



### Preventing Dispatch from Being Called if Component is Unmounted (Making a SafeDispatch)

Prevent the follow warning from appearing:

```
Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. 
```

```jsx
function useAsync(initialState) {
  const [state, unsafeDispatch] = React.useReducer(asyncReducer, {
    status: 'idle',
    data: null,
    error: null,
    ...initialState,
  })

  // Create a ref
  const mountedRef = React.useRef(false)

  React.useEffect(() => {
    // On first render, component is mounted
    mountedRef.current = true

    // Unmount on unmount
    return () => {
      mountedRef.current = false
    }
  }, [])

  const dispatch = React.useCallback((...args) => {
    if (mountedRef.current) {
      unsafeDispatch(...args)
    }
  }, [])

  const run = React.useCallback(
    promise => {
      dispatch({type: 'pending'})

      promise.then(
        data => {
          dispatch({type: 'resolved', data})
        },
        error => {
          dispatch({type: 'rejected', error})
        },
      )
    },
    [dispatch],
  )

  return {...state, run}
}
```



To turn into a generic `useSafeDispatch` hook:

```jsx
function useSafeDispatch(dispatch) {
  const mountedRef = React.useRef(false)

  // Really, should use React.useLayoutEffect
  // Will run as soon as the component is mounted/unmounted
  React.useEffect(() => {
    mountedRef.current = true

    return () => {
      mountedRef.current = false
    }
  }, [])

  return React.useCallback(
    (...args) => {
      if (mountedRef.current) {
        dispatch(...args)
      }
    },
    [dispatch],
  )
}

function useAsync(initialState) {
  const [state, unsafeDispatch] = React.useReducer(asyncReducer, {
    status: 'idle',
    data: null,
    error: null,
    ...initialState,
  })

  const dispatch = useSafeDispatch(unsafeDispatch)

  const run = React.useCallback(
    promise => {
      dispatch({type: 'pending'})

      promise.then(
        data => {
          dispatch({type: 'resolved', data})
        },
        error => {
          dispatch({type: 'rejected', error})
        },
      )
    },
    [dispatch],
  )

  return {...state, run}
}

```

