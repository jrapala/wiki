# Coordinate Suspending Components with SuspenseList

When your app is simple, you can pretty much expect everything to be there and load together when you need them, and that works nicely. But when your app grows and you start code splitting and loading data alongside the code that needs it, pretty soon you end up in situations where you have several things loading all at once. Having those all pop into place on the page can be a jarring experience for the user.

A better experience for the user is a more predictable loading experience, even if it means that they see the data displayed out of order from how it was loaded.

Coordinating these loading states is a really hard problem, but thanks to Suspense and `<React.SuspenseList />`, it’s fairly trivial.

```jsx
<React.SuspenseList revealOrder="forwards">
  <React.Suspense fallback="Loading...">
    <ProfilePicture id={1} />
  </React.Suspense>
  <React.Suspense fallback="Loading...">
    <ProfilePicture id={2} />
  </React.Suspense>
  <React.Suspense fallback="Loading...">
    <ProfilePicture id={3} />
  </React.Suspense>
</React.SuspenseList>
```

The `SuspenseList` component has the following props:

- `revealOrder`: the order in which the suspending components are to render
  - `{undefined}`: the default behavior: everything pops in when it’s loaded (as if you didn’t wrap everything in a `SuspenseList`).
  - `"forwards"`: Only show the component when all components before it have finished suspending.
  - `"backwards"`: Only show the component when all the components after it have finished suspending.
  - `"together"`: Don’t show any of the components until they’ve all finished loading
- `tail`: determines how to show the fallbacks for the suspending components
  - `{undefined}`: the default behavior: show all fallbacks
  - `"collapsed"`: Only show the fallback for the component that should be rendered next (this will differ based on the `revealOrder` specified).
  - `"hidden"`: Opposite of the default behavior: show none of the fallbacks
- `children`: other react elements which render `<React.Suspense />` components. Note: `<React.Suspense />` components do not have to be direct children as in the example above. You can wrap them in `<div />`s or other components if you need.



## Exercise

Improve the loading UI with SuspenseList:

```jsx
// Coordinate Suspending components with SuspenseList
// http://localhost:3000/isolated/exercise/07.js

import * as React from 'react'
import '../suspense-list/style-overrides.css'
import * as cn from '../suspense-list/app.module.css'
import Spinner from '../suspense-list/spinner'
import {createResource} from '../utils'
import {fetchUser, PokemonForm, PokemonErrorBoundary} from '../pokemon'

// 💰 this delay function just allows us to make a promise take longer to resolve
// so we can easily play around with the loading time of our code.
const delay = time => promiseResult =>
  new Promise(resolve => setTimeout(() => resolve(promiseResult), time))

// 🐨 feel free to play around with the delay timings.
const NavBar = React.lazy(() =>
  import('../suspense-list/nav-bar').then(delay(500)),
)
const LeftNav = React.lazy(() =>
  import('../suspense-list/left-nav').then(delay(2000)),
)
const MainContent = React.lazy(() =>
  import('../suspense-list/main-content').then(delay(1500)),
)
const RightNav = React.lazy(() =>
  import('../suspense-list/right-nav').then(delay(1000)),
)

const fallback = (
  <div className={cn.spinnerContainer}>
    <Spinner />
  </div>
)
const SUSPENSE_CONFIG = {timeoutMs: 4000}

function App() {
  const [startTransition] = React.useTransition(SUSPENSE_CONFIG)
  const [pokemonResource, setPokemonResource] = React.useState(null)

  function handleSubmit(pokemonName) {
    startTransition(() => {
      setPokemonResource(createResource(fetchUser(pokemonName)))
    })
  }

  if (!pokemonResource) {
    return (
      <div className="pokemon-info-app">
        <div
          className={`${cn.root} totally-centered`}
          style={{height: '100vh'}}
        >
          <PokemonForm onSubmit={handleSubmit} />
        </div>
      </div>
    )
  }

  function handleReset() {
    setPokemonResource(null)
  }

  return (
    <div className="pokemon-info-app">
      <div className={cn.root}>
        <PokemonErrorBoundary
          onReset={handleReset}
          resetKeys={[pokemonResource]}
        >
          {/* Add SuspenseLists */}
          <React.SuspenseList revealOrder="forwards" tail="collapsed">
            <React.Suspense fallback={fallback}>
              <NavBar pokemonResource={pokemonResource} />
            </React.Suspense>
            <div className={cn.mainContentArea}>
              <React.SuspenseList revealOrder="forwards" tail="collapsed">
                <React.Suspense fallback={fallback}>
                  <LeftNav />
                </React.Suspense>
                <React.SuspenseList revealOrder="together">
                  <React.Suspense fallback={fallback}>
                    <MainContent pokemonResource={pokemonResource} />
                  </React.Suspense>
                  <React.Suspense fallback={fallback}>
                    <RightNav pokemonResource={pokemonResource} />
                  </React.Suspense>
                </React.SuspenseList>
              </React.SuspenseList>
            </div>
          </React.SuspenseList>
        </PokemonErrorBoundary>
      </div>
    </div>
  )
}

export default App

```

