# Simple Data-Fetching

Here's the basic idea of the Suspense API:

```javascript
function Component() {
  if (data) {
    return <div>{data.message}</div>
  }
  throw promise
  // React will catch this, find the closest "Suspense" component
  // and "suspend" everything from there down from rendering until the
  // promise resolves.
  // 🚨 THIS "API" IS LIKELY TO CHANGE
}

ReactDOM.createRoot(rootEl).render(
  <React.Suspense fallback={<div>loading...</div>}>
    <Component />
  </React.Suspense>,
)
```



## Exercises

### React.Suspense

```javascript
import * as React from 'react'
import {fetchPokemon} from '../pokemon'
import {PokemonDataView} from '../pokemon'

let pokemon

// When this module is loaded, we will try to fetch pokemon
const pokemonPromise = fetchPokemon('pikachu').then(pokemonData => {
  pokemon = pokemonData
})

function PokemonInfo() {
  if (!pokemon) {
    // React is rendering the PokemonInfo in a try/catch
    // When the promise resolves, React will rerender to show show Pokemon
    throw pokemonPromise
  }

  return (
    <div>
      <div className="pokemon-info__img-wrapper">
        <img src={pokemon.image} alt={pokemon.name} />
      </div>
      <PokemonDataView pokemon={pokemon} />
    </div>
  )
}

function App() {
  return (
    <div className="pokemon-info-app">
      <div className="pokemon-info">
        {/* If an error is thrown, the fallback is rendered */}
        <React.Suspense fallback={<div>Loading Pokemon...</div>}>
          <PokemonInfo />
        </React.Suspense>
      </div>
    </div>
  )
}

export default App

```



### Handle Error with Error Boundary

How do you handle searching for an invalid Pokemon?

```javascript
// Simple Data-fetching
// http://localhost:3000/isolated/exercise/01.js

import * as React from 'react'
import {fetchPokemon, PokemonErrorBoundary} from '../pokemon'
import {PokemonDataView} from '../pokemon'

let pokemon
let pokemonError

// Add fake pokemon
const pokemonPromise = fetchPokemon('pikachaaaa').then(
  pokemonData => {
    pokemon = pokemonData
  },
  // Add an error handler. Keep track of the error.
  error => (pokemonError = error),
)

function PokemonInfo() {
  if (pokemonError) {
    // Throw the error
    throw pokemonError
  }

  if (!pokemon) {
    throw pokemonPromise
  }

  return (
    <div>
      <div className="pokemon-info__img-wrapper">
        <img src={pokemon.image} alt={pokemon.name} />
      </div>
      <PokemonDataView pokemon={pokemon} />
    </div>
  )
}

function App() {
  return (
    <div className="pokemon-info-app">
      <div className="pokemon-info">
        {/* Error boundary handles the error */}
        <PokemonErrorBoundary>
          <React.Suspense fallback={<div>Loading Pokemon...</div>}>
            <PokemonInfo />
          </React.Suspense>
        </PokemonErrorBoundary>
      </div>
    </div>
  )
}

export default App

```



### Make More Generic createResource

Genericize it all to make creating resources really easy:

```javascript
import * as React from 'react'
import {fetchPokemon, PokemonErrorBoundary} from '../pokemon'
import {PokemonDataView} from '../pokemon'

function createResource(promise) {
  let status = 'pending'
  let result = promise.then(
    resolved => {
      status = 'resolved'
      result = resolved
    },
    rejected => {
      status = 'rejected'
      result = rejected
    },
  )

  return {
    read() {
      if (status === 'pending') throw result
      if (status === 'rejected') throw result
      if (status === 'resolved') return result
      throw new Error('This should be impossible')
    },
  }
}

const pokemonResource = createResource(fetchPokemon('pikachu'))

function PokemonInfo() {
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

function App() {
  return (
    <div className="pokemon-info-app">
      <div className="pokemon-info">
        <PokemonErrorBoundary>
          <React.Suspense fallback={<div>Loading Pokemon...</div>}>
            <PokemonInfo />
          </React.Suspense>
        </PokemonErrorBoundary>
      </div>
    </div>
  )
}

export default App

```



### Use utils

`createResource` will be used more often so move it to `utils`. You should also create a generic fallback component:

```javascript
import * as React from 'react'
import {
  fetchPokemon,
  PokemonErrorBoundary,
  PokemonInfoFallback,
} from '../pokemon'
import {PokemonDataView} from '../pokemon'
import {createResource} from '../utils'

const pokemonResource = createResource(fetchPokemon('pikachu'))

function PokemonInfo() {
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

function App() {
  return (
    <div className="pokemon-info-app">
      <div className="pokemon-info">
        <PokemonErrorBoundary>
          <React.Suspense fallback={<PokemonInfoFallback name="Pikachu" />}>
            <PokemonInfo />
          </React.Suspense>
        </PokemonErrorBoundary>
      </div>
    </div>
  )
}

export default App

```

```javascript
function PokemonInfoFallback({name}) {
  const initialName = React.useRef(name).current
  const fallbackPokemonData = {
    name: initialName,
    number: 'XXX',
    attacks: {
      special: [
        {name: 'Loading Attack 1', type: 'Type', damage: 'XX'},
        {name: 'Loading Attack 2', type: 'Type', damage: 'XX'},
      ],
    },
    fetchedAt: 'loading...',
  }
  return (
    <div>
      <div className="pokemon-info__img-wrapper">
        <img src={fallbackImgUrl} alt={initialName} />
      </div>
      <PokemonDataView pokemon={fallbackPokemonData} />
    </div>
  )
}

function PokemonDataView({pokemon}) {
  return (
    <>
      <section>
        <h2>
          {pokemon.name}
          <sup>{pokemon.number}</sup>
        </h2>
      </section>
      <section>
        <ul>
          {pokemon.attacks.special.map(attack => (
            <li key={attack.name}>
              <label>{attack.name}</label>:{' '}
              <span>
                {attack.damage} <small>({attack.type})</small>
              </span>
            </li>
          ))}
        </ul>
      </section>
      <small className="pokemon-info__fetch-time">{pokemon.fetchedAt}</small>
    </>
  )
}
```

