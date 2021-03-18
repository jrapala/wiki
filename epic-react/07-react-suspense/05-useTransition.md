# useTransition for Improved Loading States

There is an intentional delay before a fallback is rendered (in case the API is really fast).



If you know your API will take time, instead for transitioning to a loading state, you can wait a bit first.

There is a `useTransition` hook that can help skip loading states and wait for some content to load.

Here's what that looks like:

```javascript
const SUSPENSE_CONFIG = {timeoutMs: 4000}

function Component() {
  const [startTransition, isPending] = React.useTransition(SUSPENSE_CONFIG)
  // etc...

  function handleClick() {
    // do something that triggers some interim state change we want to
    // happen before suspending starts
    startTransition(() => {
      // do something that triggers a suspending component to render
    })
  }

  // if needed, you can use the `isPending` boolean to display a loading spinner
  // or similar
}
```

 

## Exercises

### Use useTransition with a Suspense Config

In this exercise, we'll wrap the existing call to set the resource in a transition so the input value gets updated when you select a pokemon. We'll also make the pokemon information area "appear stale" by making it slightly transparent if `isPending` is true.

This is a good one to play around with setting the `delay` argument to `fetchPokemon` a bit.

📣 **BREAKING CHANGE ALERT:** The version of React in this project works like it does in the recorded videos. However in the future experimental builds of React, the `SUSPENSE_CONFIG` option to `useTransition` has been completely removed. Read more about this here: https://github.com/facebook/react/pull/19703

Here's your reminder that you're learning about experimental software which can change at any moment without notice 😅

**Note:** The fallback will always be shown when we first mount the component. `useTransition` doesn't override that. It's more of a way to indicate when we're looking at stale data. If you don't want that to happen, move the Suspense component up the tree.

```javascript
// useTransition for improved loading states
// http://localhost:3000/isolated/exercise/03.js

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

// isPending state will show for 4 seconds before we go to the callback state
// There will be a way to extend this timeout if the entire timeout is needed
const SUSPENSE_CONFIG = {timeoutMs: 4000}

function createPokemonResource(pokemonName) {
  let delay = 1500
  return createResource(fetchPokemon(pokemonName, delay))
}

function App() {
  const [pokemonName, setPokemonName] = React.useState('')
  // startTransition is a function - determines what interactions should cause isPending
  // isPending is a boolean
  const [startTransition, isPending] = React.useTransition(SUSPENSE_CONFIG)
  const [pokemonResource, setPokemonResource] = React.useState(null)

  React.useEffect(() => {
    if (!pokemonName) {
      setPokemonResource(null)
      return
    }
    startTransition(() => {
      setPokemonResource(createPokemonResource(pokemonName))
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
      {/* During the pending state, we will just lower the opacity instead */}
      <div className="pokemon-info" style={{opacity: isPending ? 0.6 : 1}}>
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

