# Render as you Fetch

React Concurrent Mode and React Suspense really encourage a pattern called **Render as you Fetch**.

The idea here is: get the data **as soon as you have the information you need** for the data.

Render as you Fetch lets you request code and data at the same time. 



### Ways of Fetching Data

1. Fetch on Render - Lazy load a component. Inside that component, use `useEffect` to fetch, then render the data from the API.
2. Fetch, then Render - Fetch in a parent component. When we have data, lazy load the component, then render the data.
3. Render as you Fetch - Fetch data, lazy load component, and render data at the same time.



## Exercises

Refactor this to use Render as you Fetch:

```javascript
import * as React from 'react'
import {
  fetchPokemon,
  PokemonInfoFallback,
  PokemonForm,
  PokemonDataView,
} from '../pokemon'

function PokemonInfo({pokemonName}) {
  const [state, setState] = React.useReducer((s, a) => ({...s, ...a}), {
    pokemon: null,
    error: null,
    status: 'pending',
  })

  const {pokemon, error, status} = state

  React.useEffect(() => {
    let current = true
    setState({status: 'pending'})
    fetchPokemon(pokemonName).then(
      p => {
        if (current) setState({pokemon: p, status: 'success'})
      },
      e => {
        if (current) setState({error: e, status: 'error'})
      },
    )
    return () => (current = false)
  }, [pokemonName])

  if (status === 'pending') {
    return <PokemonInfoFallback name={pokemonName} />
  }

  if (status === 'error') {
    return (
      <div>
        There was an error.
        <pre style={{whiteSpace: 'normal'}}>{error.message}</pre>
      </div>
    )
  }

  if (status === 'success') {
    return (
      <div>
        <div className="pokemon-info__img-wrapper">
          <img src={pokemon.image} alt={pokemon.name} />
        </div>
        <PokemonDataView pokemon={pokemon} />
      </div>
    )
  }
}

function App() {
  const [pokemonName, setPokemonName] = React.useState('')

  function handleSubmit(newPokemonName) {
    setPokemonName(newPokemonName)
  }

  return (
    <div className="pokemon-info-app">
      <PokemonForm pokemonName={pokemonName} onSubmit={handleSubmit} />
      <hr />
      <div className="pokemon-info">
        {pokemonName ? (
          <PokemonInfo pokemonName={pokemonName} />
        ) : (
          'Submit a pokemon'
        )}
      </div>
    </div>
  )
}

export default App

```



Result:

```javascript
// Render as you fetch
// http://localhost:3000/isolated/exercise/02.js

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
  // Just read the pokemon
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

function createPokemonResource(pokemonName) {
  return createResource(fetchPokemon(pokemonName))
}

function App() {
  const [pokemonName, setPokemonName] = React.useState('')
  // Need to store the resource
  const [pokemonResource, setPokemonResource] = React.useState(null)

  // Add useEffect to create resource
  React.useEffect(() => {
    if (!pokemonName) {
      setPokemonResource(null)
      return
    }
    setPokemonResource(createPokemonResource(pokemonName))
  }, [pokemonName])

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
        {/* Let's check the pokemonResource instead of pokemonName */}
        {pokemonResource ? (
          // Use the error boundary instead of the "error" component
          <PokemonErrorBoundary
            onReset={handleReset}
            resetKeys={[pokemonResource]}
          >
            {/* Add Suspense with a "pending" fallback */}
            <React.Suspense
              fallback={<PokemonInfoFallback name={pokemonName} />}
            >
              {/* Pass pokemonResource instead */}
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

