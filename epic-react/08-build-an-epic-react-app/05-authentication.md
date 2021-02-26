# Authentication

We don't want to store the user's password and send that with every request. A common solution to this problem is to use a special limited use "token" which represents the user's current session. That way the token can be invalidated (in the case that it's lost or stolen) and the user doesn't have to change their password. They simply re-authenticate and they can get a fresh token.

So, every request the client makes must include this token to make authenticated requests. 

A common way to attach a token is with an `Authorization` request header:

```javascript
const token = await authProvider.getToken()

const headers = {
  Authorization: token ? `Bearer ${token}` : undefined,
}

window.fetch('http://example.com/pets', {headers})
```

A common token standard is the JSON Web Token (JWT).



## How to Manage Tokens

Use a service to handle this, such as [Auth0](https://auth0.com/), [Netlify Identity](https://docs.netlify.com/visitor-access/identity/#enable-identity-in-the-ui), and [Firebase Authentication](https://firebase.google.com/products/auth). You'll need to call an API to retriece and token, and then send the token with your requests.



## Using Tokens with React

1. Split your apps into an authenticated and unauthenticated state.
2. Choose which to render based on whether you have user info.
3. When the app is called, use your auth provider's API to retireve a token if the user is already logged in. 

More Info: https://kentcdodds.com/blog/authentication-in-react-applications

## Exercises

### Basic Auth

Use an auth service called "Auth Provider" to make an authenticated request.

```jsx
/** @jsx jsx */
import {jsx} from '@emotion/core'

import * as React from 'react'
import * as auth from 'auth-provider'
import {AuthenticatedApp} from './authenticated-app'
import {UnauthenticatedApp} from './unauthenticated-app'

function App() {
  const [user, setUser] = React.useState(null)

  const login = form => {
    return auth.login(form).then(u => setUser(u))
  }

  const register = form => {
    return auth.register(form).then(u => setUser(u))
  }

  const logout = () => {
    auth.logout()
    setUser(null)
  }

  if (user) {
    return <AuthenticatedApp user={user} logout={logout} />
  }

  return <UnauthenticatedApp login={login} register={register} />
}

export {App}


```



### Load User Data on Page Load

Page:

```jsx
/** @jsx jsx */
import {jsx} from '@emotion/core'

import * as React from 'react'
import * as auth from 'auth-provider'
import {client} from './utils/api-client'
import {AuthenticatedApp} from './authenticated-app'
import {UnauthenticatedApp} from './unauthenticated-app'

async function getUser() {
  let user = null
  const token = await auth.getToken()

  // If we have a token, get user data
  if (token) {
    const data = await client('me', {token})
    user = data.user
  }

  return user
}

function App() {
  const [user, setUser] = React.useState(null)

  // Try to get a token
  React.useEffect(() => {
    getUser().then(u => setUser(u))
  }, []) // only run on mount

  const login = form => {
    return auth.login(form).then(u => setUser(u))
  }

  const register = form => {
    return auth.register(form).then(u => setUser(u))
  }

  const logout = () => {
    auth.logout()
    setUser(null)
  }

  if (user) {
    return <AuthenticatedApp user={user} logout={logout} />
  }

  return <UnauthenticatedApp login={login} register={register} />
}

export {App}

```



API Client:

```jsx
const apiURL = process.env.REACT_APP_API_URL

// Pass in headers with token
function client(
  endpoint,
  {token, headers: customHeaders, ...customConfig} = {},
) {
  const config = {
    method: 'GET',
    headers: {
      Authorization: token ? `Bearer ${token}` : undefined,
      ...customHeaders,
    },
    ...customConfig,
  }

  return window.fetch(`${apiURL}/${endpoint}`, config).then(async response => {
    const data = await response.json()
    if (response.ok) {
      return data
    } else {
      return Promise.reject(data)
    }
  })
}

export {client}

```



### Use the useAsync Hook Instead

```jsx
/** @jsx jsx */
import {jsx} from '@emotion/core'

import * as React from 'react'
import * as auth from 'auth-provider'
import {client} from './utils/api-client'
import {AuthenticatedApp} from './authenticated-app'
import {UnauthenticatedApp} from './unauthenticated-app'
import {useAsync} from './utils/hooks'
import * as colors from './styles/colors'
import {FullPageSpinner} from 'components/lib'

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
  // Replace state with useAsync
  const {
    data: user,
    error,
    isIdle,
    isLoading,
    isError,
    isSuccess,
    run,
    setData,
  } = useAsync()

  // Use the async function
  React.useEffect(() => {
    run(getUser())
  }, [run])

  const login = form => {
    // Change to setData
    return auth.login(form).then(user => setData(user))
  }

  const register = form => {
    // Change to setData
    return auth.register(form).then(user => setData(user))
  }

  const logout = () => {
    auth.logout()
    // Change to setData
    setData(null)
  }

  // Check for different states
  if (isLoading || isIdle) {
    return <FullPageSpinner />
  }

  if (isError) {
    return (
      <div
        css={{
          color: colors.danger,
          height: '100vh',
          display: 'flex',
          flexDirection: 'column',
          justifyContent: 'center',
          alignItems: 'center',
        }}
      >
        <p>Uh oh... There's a problem. Try refreshing the app.</p>
        <pre>{error.message}</pre>
      </div>
    )
  }
  if (isSuccess) {
    if (user) {
      return <AuthenticatedApp user={user} logout={logout} />
    } else {
      return <UnauthenticatedApp login={login} register={register} />
    }
  }
}

export {App}

```



### Automatically Log Out on 401

401 usually happens when a user makes a request they are not allowed to make (bad token, expired token)

```jsx
import * as auth from 'auth-provider'
const apiURL = process.env.REACT_APP_API_URL

function client(
  endpoint,
  {token, headers: customHeaders, ...customConfig} = {},
) {
  const config = {
    method: 'GET',
    headers: {
      Authorization: token ? `Bearer ${token}` : undefined,
      ...customHeaders,
    },
    ...customConfig,
  }

  return window.fetch(`${apiURL}/${endpoint}`, config).then(async response => {
    // Just add this catch here
    if (response.status === 401) {
      await auth.logout()
      // Refresh the page
      window.location.assign(window.location)
      // Reject (though the page will be refreshed)
      return Promise.reject({message: 'Please re-authenticate'})
    }

    const data = await response.json()
    if (response.ok) {
      return data
    } else {
      return Promise.reject(data)
    }
  })
}

export {client}

```



### Support POSTing Data

```jsx
import * as auth from 'auth-provider'
const apiURL = process.env.REACT_APP_API_URL

function client(
  endpoint,
  // Add data option
  {data, token, headers: customHeaders, ...customConfig} = {},
) {
  const config = {
    // If we have data, it's a POST request
    method: data ? 'POST' : 'GET',
    // Stringify any data
    body: data ? JSON.stringify(data) : undefined,
    headers: {
      Authorization: token ? `Bearer ${token}` : undefined,
      // If we have data, we need a Content-Type in the header
      'Content-Type': data ? 'application/json' : undefined,
      ...customHeaders,
    },
    ...customConfig,
  }

  return window.fetch(`${apiURL}/${endpoint}`, config).then(async response => {
    if (response.status === 401) {
      await auth.logout()
      window.location.assign(window.location)
      return Promise.reject({message: 'Please re-authenticate'})
    }

    const data = await response.json()
    if (response.ok) {
      return data
    } else {
      return Promise.reject(data)
    }
  })
}

export {client}

```

