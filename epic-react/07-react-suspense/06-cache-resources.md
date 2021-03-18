# Cache Resources

Caching is a hard problem to solve. We want to cache the responses to the pokemon names. This is just one solution.



### Promises in render

Creating a new promise in the render method is dangerous because you cannot rely on your render method only being called once, so you have to do things carefully by using a promise cache. Here’s an example of what I mean:

```javascript
const promiseCache = {}
function MySuspendingComponent({value}) {
  let resource = promiseCache[value]
  if (!resource) {
    resource = doAsyncThing(value)
    promiseCache[value] = resource // <-- this is very important
  }
  return <div>{resource.read()}</div>
}
```





## Exercises



### Cache an Object

```jsx
// Cache resources
// http://localhost:3000/isolated/exercise/04.js

import * as React from 'react'
import {
  fetchPokemon,
  PokemonInfoFallback,
  PokemonForm,
  PokemonDataView,
  PokemonErrorBoundary,
} from '../pokemon'
import {createResource} from '../utils'

function PokemonInfo({pokemonResource}) {
  const pokemon = pokemonResource.read()
  return (
    <div>
      <div className="pokemon-info__img-wrapper">
        <img src={pokemon.image} alt={pokemon.name} />
      </div>
      <PokemonDataView pokemon={pokemon} />
    </div>
  )
}

const SUSPENSE_CONFIG = {
  timeoutMs: 4000,
  busyDelayMs: 300,
  busyMinDurationMs: 700,
}

const pokemonResourceCache = {}

// 2. Create a promise cache
function getPokemonResource(name) {
  const lowerName = name.toLowerCase()
  let resource = pokemonResourceCache[lowerName]
  if (!resource) {
    resource = createPokemonResource(lowerName)
    pokemonResourceCache[lowerName] = resource
  }
  return resource
}

function createPokemonResource(pokemonName) {
  return createResource(fetchPokemon(pokemonName))
}

function App() {
  const [pokemonName, setPokemonName] = React.useState('')
  const [startTransition, isPending] = React.useTransition(SUSPENSE_CONFIG)
  const [pokemonResource, setPokemonResource] = React.useState(null)

  React.useEffect(() => {
    if (!pokemonName) {
      setPokemonResource(null)
      return
    }
    startTransition(() => {
      // 1. Change this to get a cached resource instead (if it exists)
      setPokemonResource(getPokemonResource(pokemonName))
    })
  }, [pokemonName, startTransition])

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
      <div className={`pokemon-info ${isPending ? 'pokemon-loading' : ''}`}>
        {pokemonResource ? (
          <PokemonErrorBoundary
            onReset={handleReset}
            resetKeys={[pokemonResource]}
          >
            <React.Suspense
              fallback={<PokemonInfoFallback name={pokemonName} />}
            >
              <PokemonInfo pokemonResource={pokemonResource} />
            </React.Suspense>
          </PokemonErrorBoundary>
        ) : (
          'Submit a pokemon'
        )}
      </div>
    </div>
  )
}

export default App

```





### Create a Context Provider

Refactor to a context/provider pattern:

```jsx
// Cache resources
// http://localhost:3000/isolated/exercise/04.js

import * as React from 'react'
import {
  fetchPokemon,
  PokemonInfoFallback,
  PokemonForm,
  PokemonDataView,
  PokemonErrorBoundary,
} from '../pokemon'
import {createResource} from '../utils'

function PokemonInfo({pokemonResource}) {
  const pokemon = pokemonResource.read()
  return (
    <div>
      <div className="pokemon-info__img-wrapper">
        <img src={pokemon.image} alt={pokemon.name} />
      </div>
      <PokemonDataView pokemon={pokemon} />
    </div>
  )
}

const SUSPENSE_CONFIG = {
  timeoutMs: 4000,
  busyDelayMs: 300,
  busyMinDurationMs: 700,
}

// 1. Create a context
const PokemonResourceCacheContext = React.createContext()

// 5. Create Provider
function PokemonCacheProvider({children}) {
  const cache = React.useRef({})

  // 2. Add this function inside the context
  const getPokemonResource = React.useCallback(name => {
    const lowerName = name.toLowerCase()
    let resource = cache.current[lowerName]
    if (!resource) {
      resource = createPokemonResource(lowerName)
      cache.current[lowerName] = resource
    }
    return resource
  }, [])

  return (
    <PokemonResourceCacheContext.Provider value={getPokemonResource}>
      {children}
    </PokemonResourceCacheContext.Provider>
  )
}

// 3. Create a custom hook to let us consume that function
function usePokemonResourceCache() {
  return React.useContext(PokemonResourceCacheContext)
}

function createPokemonResource(pokemonName) {
  return createResource(fetchPokemon(pokemonName))
}

function App() {
  const [pokemonName, setPokemonName] = React.useState('')
  const [startTransition, isPending] = React.useTransition(SUSPENSE_CONFIG)
  const [pokemonResource, setPokemonResource] = React.useState(null)
  // 4. Consume that function
  const getPokemonResource = usePokemonResourceCache()

  React.useEffect(() => {
    if (!pokemonName) {
      setPokemonResource(null)
      return
    }
    startTransition(() => {
      setPokemonResource(getPokemonResource(pokemonName))
    })
  }, [pokemonName, startTransition, getPokemonResource])

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
      <div className={`pokemon-info ${isPending ? 'pokemon-loading' : ''}`}>
        {pokemonResource ? (
          <PokemonErrorBoundary
            onReset={handleReset}
            resetKeys={[pokemonResource]}
          >
            <React.Suspense
              fallback={<PokemonInfoFallback name={pokemonName} />}
            >
              <PokemonInfo pokemonResource={pokemonResource} />
            </React.Suspense>
          </PokemonErrorBoundary>
        ) : (
          'Submit a pokemon'
        )}
      </div>
    </div>
  )
}

// 6. Wrap the app with the provider
function AppWithProvider() {
  return (
    <PokemonCacheProvider>
      <App />
    </PokemonCacheProvider>
  )
}

export default AppWithProvider

```



### Add Cache Timeout

Currently our cache lives forever. We can add an expiration time if desired:

```jsx
// Cache resources
// http://localhost:3000/isolated/exercise/04.js

import * as React from 'react'
import {
  fetchPokemon,
  PokemonInfoFallback,
  PokemonForm,
  PokemonDataView,
  PokemonErrorBoundary,
} from '../pokemon'
import {createResource} from '../utils'

function PokemonInfo({pokemonResource}) {
  const pokemon = pokemonResource.read()
  return (
    <div>
      <div className="pokemon-info__img-wrapper">
        <img src={pokemon.image} alt={pokemon.name} />
      </div>
      <PokemonDataView pokemon={pokemon} />
    </div>
  )
}

const SUSPENSE_CONFIG = {
  timeoutMs: 4000,
  busyDelayMs: 300,
  busyMinDurationMs: 700,
}

const PokemonResourceCacheContext = React.createContext()

// Accept cacheTime
function PokemonCacheProvider({children, cacheTime}) {
  const cache = React.useRef({})
  // Keep track of expirations
  const expirations = React.useRef({})

  // Check cache times
  React.useEffect(() => {
    const interval = setInterval(() => {
      for (const [name, time] of Object.entries(expirations.current)) {
        if (time < Date.now()) {
          delete cache.current[name]
        }
      }
    }, 1000)
    return () => clearInterval(interval)
  }, [])

  const getPokemonResource = React.useCallback(
    name => {
      const lowerName = name.toLowerCase()
      let resource = cache.current[lowerName]
      if (!resource) {
        resource = createPokemonResource(lowerName)
        cache.current[lowerName] = resource
      }
      // Add expiration time for resource
      expirations.current[lowerName] = Date.now() + cacheTime
      return resource
    },
    [cacheTime],
  )

  return (
    <PokemonResourceCacheContext.Provider value={getPokemonResource}>
      {children}
    </PokemonResourceCacheContext.Provider>
  )
}

function usePokemonResourceCache() {
  return React.useContext(PokemonResourceCacheContext)
}

function createPokemonResource(pokemonName) {
  return createResource(fetchPokemon(pokemonName))
}

function App() {
  const [pokemonName, setPokemonName] = React.useState('')
  const [startTransition, isPending] = React.useTransition(SUSPENSE_CONFIG)
  const [pokemonResource, setPokemonResource] = React.useState(null)
  const getPokemonResource = usePokemonResourceCache()

  React.useEffect(() => {
    if (!pokemonName) {
      setPokemonResource(null)
      return
    }
    startTransition(() => {
      setPokemonResource(getPokemonResource(pokemonName))
    })
  }, [pokemonName, startTransition, getPokemonResource])

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
      <div className={`pokemon-info ${isPending ? 'pokemon-loading' : ''}`}>
        {pokemonResource ? (
          <PokemonErrorBoundary
            onReset={handleReset}
            resetKeys={[pokemonResource]}
          >
            <React.Suspense
              fallback={<PokemonInfoFallback name={pokemonName} />}
            >
              <PokemonInfo pokemonResource={pokemonResource} />
            </React.Suspense>
          </PokemonErrorBoundary>
        ) : (
          'Submit a pokemon'
        )}
      </div>
    </div>
  )
}

// Add cacheTime
function AppWithProvider() {
  return (
    <PokemonCacheProvider cacheTime={5000}>
      <App />
    </PokemonCacheProvider>
  )
}

export default AppWithProvider

```

