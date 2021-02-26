# Performance

## Exercises

### Code Splitting

The entire app is being loaded before a user is even authenticated. 

Before:

```jsx
import * as React from 'react'
import {useAuth} from './context/auth-context'

// We can't see anything until this is loaded, parsed and run
import {AuthenticatedApp} from './authenticated-app'
import {UnauthenticatedApp} from './unauthenticated-app'

function App() {
  const {user} = useAuth()
  return user ? <AuthenticatedApp /> : <UnauthenticatedApp />
}

export {App}

```

After:

```jsx
import * as React from 'react'
import {useAuth} from './context/auth-context'
import {FullPageSpinner} from './components/lib'

const AuthenticatedApp = React.lazy(() => import('./authenticated-app'))
const UnauthenticatedApp = React.lazy(() => import('./unauthenticated-app'))

function App() {
  const {user} = useAuth()

  return (
    <React.Suspense fallback={<FullPageSpinner />}>
      {user ? <AuthenticatedApp /> : <UnauthenticatedApp />}
    </React.Suspense>
  )
}

export {App}

```

Remember, you must have default exports:

```jsx
export default AuthenticatedApp
```



### Prefetch the Authenticated App

Use magic comments to fetch authenticated app before login:

```jsx
import * as React from 'react'
import {useAuth} from './context/auth-context'
import {FullPageSpinner} from './components/lib'

const AuthenticatedApp = React.lazy(() =>
  import(/* webpackPrefetch: true */ './authenticated-app'),
)
const UnauthenticatedApp = React.lazy(() => import('./unauthenticated-app'))

function App() {
  const {user} = useAuth()

  return (
    <React.Suspense fallback={<FullPageSpinner />}>
      {user ? <AuthenticatedApp /> : <UnauthenticatedApp />}
    </React.Suspense>
  )
}

export {App}

```



### Memoize Content

THIS IS UNNECESSARY. Measuring before and after shows you that this doesn't change anything. But here we go anyway:

```jsx
  // useCallback for all items used in useMemo
  const login = React.useCallback(
    form => auth.login(form).then(user => setData(user)),
    [setData],
  )
  const register = React.useCallback(
    form => auth.register(form).then(user => setData(user)),
    [setData],
  )
  const logout = React.useCallback(() => {
    auth.logout()
    setData(null)
  }, [setData])

  // useMemo here
  const value = React.useMemo(() => ({user, login, register, logout}), [
    login,
    logout,
    register,
    user,
  ])

  if (isLoading || isIdle) {
    return <FullPageSpinner />
  }

  if (isError) {
    return <FullPageErrorFallback error={error} />
  }

  if (isSuccess) {
    // Prevent rendering of all consumers when this value changes
    return <AuthContext.Provider value={value} {...props} />
  }

```



### Production Monitoring with a Custom Profiler Component

Create custom `Profiler` component:

```jsx
// index.js

import {loadDevTools} from './dev-tools/load'
import './bootstrap'
import * as React from 'react'
import ReactDOM from 'react-dom'
import {Profiler} from 'components/profiler'
import {App} from './app'
import {AppProviders} from './context'

loadDevTools(() => {
  ReactDOM.render(
    <Profiler id="App Root" phases={['mount']}>
      <AppProviders>
        <App />
      </AppProviders>
    </Profiler>,
    document.getElementById('root'),
  )
})

```

```jsx
// components/profiler.js
import React from 'react'
import {client} from 'utils/api-client'

// Collect events
let queue = []

// Every five second, post the queue to the API
setInterval(sendProfileQueue, 5000)

function sendProfileQueue() {
  // If queue is empty, resolve
  if (!queue.length) {
    return Promise.resolve({success: true})
  }
  // If queue if full, copy, empty original queue, and post the copy
  const queueToSend = [...queue]
  queue = []
  return client('profile', {data: queueToSend})
}

// Create Profiler component
function Profiler({phases, ...props}) {
  function reportProfile(
    id,
    phase,
    actualDuration,
    baseDuration,
    startTime,
    commitTime,
    interactions,
  ) {
    // Add event to queue for either all phases, or for specified phase (if included)
    if (!phases || phases.includes(phase)) {
      queue.push({
        id,
        phase,
        actualDuration,
        baseDuration,
        startTime,
        commitTime,
        interactions: [...interactions],
      })
    }
  }
  return <React.Profiler onRender={reportProfile} {...props} />
}

export {Profiler}

```



### Add Metadata and Profile Book Screen

It can be useful to send additional information along:

```jsx
// Let's get some metadata
<Profiler id="Book Screen" metadata={{bookId, listItemId: listItem?.id}}>
```

```jsx
// Update Profiler to use metadata
function Profiler({phases, metadata, ...props}) {
  function reportProfile(
    id,
    phase,
    actualDuration,
    baseDuration,
    startTime,
    commitTime,
    interactions,
  ) {
    if (!phases || phases.includes(phase)) {
      queue.push({
        metadata,
        id,
        phase,
        actualDuration,
        baseDuration,
        startTime,
        commitTime,
        // interactions are a Set -- convert to an array
        interactions: [...interactions],
      })
    }
  }
  return <React.Profiler onRender={reportProfile} {...props} />
}
```



### List Item List and Discover Screen List Profiling

```jsx
// list-item-list.js

<Profiler
  id="List Item List"
  metadata={{listItemCount: filterListItems.length}}
 >
```

```jsx
// discover.js

<Profiler
  id="Discover Books Screen Book List"
  metadata={{query, bookCount: books.length}}
 >
```



### Adding Profiling to Production Builds

If using `react-scripts`, just run `npx react-scripts build --profile`

If using webpack, need to update the following:

```
react-dom -> react-dom/profiling
scheduler/tracing -> scheduler/tracing-profiling
```



### Add Interaction Tracing

```jsx
// status-buttons.js
import {unstable_trace as trace} from 'scheduler/tracing'

function TooltipButton({label, highlight, onClick, icon, ...rest}) {
  const {isLoading, isError, error, run, reset} = useAsync()

  function handleClick() {
    if (isError) {
      reset()
    } else {
      // Wrap the interaction you're interested in inside a trace function
      trace(`Click ${label}`, performance.now(), () => run(onClick()))
    }
  }

```



### Profile All Updates in an Interaction

Associate functions to functions you are monitoring with `trace`

When a status button is clicked, the interaction (from `trace`) will be combined with the update run after `run` is run.

```jsx
// hook.js
import {unstable_wrap as wrap} from 'scheduler/tracing'

// In the useAsync hook:
  const run = React.useCallback(
    promise => {
      if (!promise || !promise.then) {
        throw new Error(
          `The argument passed to useAsync().run must be a promise. Maybe a function that's passed isn't returning anything?`,
        )
      }
      safeSetState({status: 'pending'})
      return promise.then(
        // wrap this function
        wrap(data => {
          setData(data)
          return data
        }),
        // wrap this function
        wrap(error => {
          setError(error)
          return error
        }),
      )
    },
    [safeSetState, setData, setError],
  )

```

These are useful in the React Profiler (DevTools) as well (Interactions tab)



**Tip:** Instead of importing from scheduler, just do this:

```jsx
// profiler.js
export {unstable_trace as trace, unstable_wrap as wrap} from 'scheduler/tracing'

// anywhere else
import {wrap} from 'components/trace'
```

