# Context

We are prop drilling the user everywhere. Use context instead.



## Exercises

### Create and Provie an AuthContext

1. Create context:

   ```jsx
   // auth-context.js
   import React from 'react'
   
   const AuthContext = React.createContext()
   
   export {AuthContext}
   
   ```

2. Wrap app in provider:

   ```jsx
     if (isSuccess) {
       const props = {user, login, register, logout}
   
       return (
         // Wrap in Provider
         <AuthContext.Provider value={props}>
           {user ? (
             <Router>
               {/* Remove spreading props to app*/}
               <AuthenticatedApp />
             </Router>
           ) : (
             <UnauthenticatedApp />
           )}
         </AuthContext.Provider>
       )
     }
   }
   ```

3. Use context within components:

   ```jsx
   // Instead of using passed in props, use Context
   function AuthenticatedApp() {
     const {user, logout} = React.useContext(AuthContext)
     return (
   ```

   

### Grab a Value from Context in a Hook

Hooks, like `useBook`, have access to Context because they are called within the function body of a function component. 



### Use Context Value in ListItem Hooks and AuthenticatedApp

Exercise to replace prop drilling throughout the app to pull values from Context instead.



### Expose User Context Value to refetchBookSearchQuery

The following `refetchBookSearchQuery` function cannot grab the user from context, since it is not running  within the function body of the component. It is running in the clean up function of a React effect (it cannot use a hook):

```jsx

function DiscoverBooksScreen() {
  const {user} = React.useContext(AuthContext)
  const [query, setQuery] = React.useState('')
  const [queried, setQueried] = React.useState(false)
  const {books, error, status} = useBookSearch(query, user)
  
  React.useEffect(() => {
    return () => refetchBookSearchQuery(user)
  }, [user])
```



Instead, we can create a hook that will give up a function that has the user already.

```jsx
// books.js
function useRefectBookSearchQuery() {
  const {user} = React.useContext(AuthContext)
  return async function refetchBookSearchQuery() {
    queryCache.removeQueries('bookSearch')
    await queryCache.prefetchQuery(getBookSearchConfig('', user))
  }
}
```

```jsx
// discover.js
function DiscoverBooksScreen() {
  const {user} = React.useContext(AuthContext)
  const [query, setQuery] = React.useState('')
  const [queried, setQueried] = React.useState(false)
  const {books, error, status} = useBookSearch(query)
  // Get a new refetchBookSearchQuery function, with user already living in it
  const refetchBookSearchQuery = useRefectBookSearchQuery()

  React.useEffect(() => {
    return () => refetchBookSearchQuery()
  }, [refetchBookSearchQuery])
```



However, this should be memoized, so it doesn't refetch after every render:

```jsx
function useRefectBookSearchQuery() {
  const {user} = React.useContext(AuthContext)
  return React.useCallback(
    async function refetchBookSearchQuery() {
      queryCache.removeQueries('bookSearch')
      await queryCache.prefetchQuery(getBookSearchConfig('', user))
    },
    [user],
  )
}
```



### Create a useAuth Hook

Replace these:

```jsx
const {login, register} = React.useContext(AuthContext)
```

With these:

```jsx
const {login, register} = useAuth()
```

```jsx
// auth-context.js

import React from 'react'

const AuthContext = React.createContext()

function useAuth() {
  const context = React.useContext(AuthContext)
  if (context === undefined) {
    throw new Error(`useAuth must be used within an AuthContext provider`)
  }
  return context
}

export {AuthContext, useAuth}

```



### Create an AuthProvider Component

Separate your concerns by creating one component whose sole purpse is to manage and provide the authentication state.

Clean up app.js from this:

```jsx
/** @jsx jsx */
import {jsx} from '@emotion/core'

import * as React from 'react'
import * as auth from 'auth-provider'
import {BrowserRouter as Router} from 'react-router-dom'
import {FullPageSpinner, FullPageErrorFallback} from './components/lib'
import {client} from './utils/api-client'
import {useAsync} from './utils/hooks'
import {AuthContext} from './context/auth-context'
import {AuthenticatedApp} from './authenticated-app'
import {UnauthenticatedApp} from './unauthenticated-app'

async function getUser() {
  let user = null

  const token = await auth.getToken()
  if (token) {
    const data = await client('me', {token})
    user = data.user
  }

  return user
}

function App() {
  const {
    data: user,
    error,
    isLoading,
    isIdle,
    isError,
    isSuccess,
    run,
    setData,
  } = useAsync()

  React.useEffect(() => {
    run(getUser())
  }, [run])

  const login = form => auth.login(form).then(user => setData(user))
  const register = form => auth.register(form).then(user => setData(user))
  const logout = () => {
    auth.logout()
    setData(null)
  }

  if (isLoading || isIdle) {
    return <FullPageSpinner />
  }

  if (isError) {
    return <FullPageErrorFallback error={error} />
  }

  if (isSuccess) {
    const props = {user, login, register, logout}

    return (
      <AuthContext.Provider value={props}>
        {user ? (
          <Router>
            <AuthenticatedApp />
          </Router>
        ) : (
          <UnauthenticatedApp />
        )}
      </AuthContext.Provider>
    )
  }
}

export {App}

```

to this:

```jsx
/** @jsx jsx */
import {jsx} from '@emotion/core'

import {BrowserRouter as Router} from 'react-router-dom'
import {useAuth} from './context/auth-context'
import {AuthenticatedApp} from './authenticated-app'
import {UnauthenticatedApp} from './unauthenticated-app'

function App() {
  const {user} = useAuth()
  return user ? (
    <Router>
      <AuthenticatedApp />
    </Router>
  ) : (
    <UnauthenticatedApp />
  )
}

export {App}

```



`auth-context.js` is updated to:

```jsx
/** @jsx jsx */
import {jsx} from '@emotion/core'

import React from 'react'
import * as auth from 'auth-provider'
import {FullPageSpinner, FullPageErrorFallback} from 'components/lib'
import {client} from 'utils/api-client'
import {useAsync} from 'utils/hooks'

const AuthContext = React.createContext()
// Help out your dev tools
AuthContext.displayName = 'AuthContext'

async function getUser() {
  let user = null

  const token = await auth.getToken()
  if (token) {
    const data = await client('me', {token})
    user = data.user
  }

  return user
}

function AuthProvider(props) {
  const {
    data: user,
    error,
    isLoading,
    isIdle,
    isError,
    isSuccess,
    run,
    setData,
  } = useAsync()

  React.useEffect(() => {
    run(getUser())
  }, [run])

  const login = form => auth.login(form).then(user => setData(user))
  const register = form => auth.register(form).then(user => setData(user))
  const logout = () => {
    auth.logout()
    setData(null)
  }

  if (isLoading || isIdle) {
    return <FullPageSpinner />
  }

  if (isError) {
    return <FullPageErrorFallback error={error} />
  }

  if (isSuccess) {
    const value = {user, login, register, logout}
		
    { /* Return the provider with props passed in (same as wrapping children) */}
    return <AuthContext.Provider value={value} {...props} />
  }
}

function useAuth() {
  const context = React.useContext(AuthContext)
  if (context === undefined) {
    throw new Error(`useAuth must be used within an AuthProvider`)
  }
  return context
}

export {AuthProvider, useAuth}

```



And wrap the whole app in the provider:

```jsx
// index.js
// 🐨 you don't need to do anything for the exercise, but there's an extra credit!
import {loadDevTools} from './dev-tools/load'
import './bootstrap'
import * as React from 'react'
import ReactDOM from 'react-dom'
import {ReactQueryConfigProvider} from 'react-query'
import {AuthProvider} from './context/auth-context'
import {App} from './app'

const queryConfig = {
  retry(failureCount, error) {
    if (error.status === 404) return false
    else if (failureCount < 2) return true
    else return false
  },
  useErrorBoundary: true,
  refetchAllOnWindowFocus: false,
}

loadDevTools(() => {
  ReactDOM.render(
    <ReactQueryConfigProvider config={queryConfig}>
      <AuthProvider>
        <App />
      </AuthProvider>
    </ReactQueryConfigProvider>,
    document.getElementById('root'),
  )
})

```



### Colocate Global Providers

Instead of wrapping the < App /> in context providers, colocate all of the providers:

```jsx
// index.js
import {loadDevTools} from './dev-tools/load'
import './bootstrap'
import * as React from 'react'
import ReactDOM from 'react-dom'
import {App} from './app'
import {AppProviders} from './context'

loadDevTools(() => {
  ReactDOM.render(
    <AppProviders>
      <App />
    </AppProviders>,
    document.getElementById('root'),
  )
})

```

```jsx
// app.js
/** @jsx jsx */
import {jsx} from '@emotion/core'

import {useAuth} from './context/auth-context'
import {AuthenticatedApp} from './authenticated-app'
import {UnauthenticatedApp} from './unauthenticated-app'

function App() {
  const {user} = useAuth()
  return user ? <AuthenticatedApp /> : <UnauthenticatedApp />
}

export {App}
```

```jsx
// context/index.js
import * as React from 'react'
import {BrowserRouter as Router} from 'react-router-dom'
import {ReactQueryConfigProvider} from 'react-query'
import {AuthProvider} from './auth-context'

const queryConfig = {
  retry(failureCount, error) {
    if (error.status === 404) return false
    else if (failureCount < 2) return true
    else return false
  },
  useErrorBoundary: true,
  refetchAllOnWindowFocus: false,
}

function AppProviders({children}) {
  return (
    <ReactQueryConfigProvider config={queryConfig}>
      <Router>
        <AuthProvider>{children}</AuthProvider>
      </Router>
    </ReactQueryConfigProvider>
  )
}

export {AppProviders}
```



### Creat a useClient Hook

Each hook needs to get a user for the token. It would be better to have a hook that gives us an authenticated client instead (no need to pass token around)

```jsx
function useClient() {
  const {
    user: {token},
  } = useAuth()
  return React.useCallback(
    (endpoint, config) => client(endpoint, {...config, token}),
    [token],
  )
}

```



```jsx
const getBookSearchConfig = (query, client) => ({
  queryKey: ['bookSearch', {query}],
  queryFn: () =>
  	// This client is authenticated. No need to pass in a uaer token.
    client(`books?query=${encodeURIComponent(query)}`).then(data => data.books),
  config: {
    onSuccess(books) {
      for (const book of books) {
        setQueryDataForBook(book)
      }
    },
  },
})

function useBookSearch(query) {
  // 1. Grab the client
  const client = useClient()
  // 2. Pass the client in
  const result = useQuery(getBookSearchConfig(query, client))
  return {...result, books: result.data ?? loadingBooks}
}
```

