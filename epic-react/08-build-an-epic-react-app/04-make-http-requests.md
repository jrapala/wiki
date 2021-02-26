# Make HTTP Requests

Use `window.fetch` to GET data:

```javascript
window
  .fetch('http://example.com/movies.json')
  .then(response => {
    return response.json()
  })
  .then(data => {
    console.log(data)
  })
```

Or to POST data:

```javascript
window
  .fetch('http://example.com/movies', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      // if auth is required. Each API may be different, but
      // the Authorization header with a token is common.
      Authorization: `Bearer ${token}`,
    },
    body: JSON.stringify(data), // body data type must match "content-type" header
  })
  .then(response => {
    return response.json()
  })
  .then(data => {
    console.log(data)
  })
```



It's good practice to reject unsuccessful (`>=400`) status codes:

```javascript
window.fetch(url).then(async response => {
  const data = await response.json()
  if (response.ok) {
    return data
  } else {
    return Promise.reject(data)
  }
})
```

Note that `window.fetch` will not reject a promise unless the network request itself has failed.



## Exercise

Fetch books and displays them to the page:

```javascript
// api-client.js
function client(endpoint, customConfig = {}) {
  const config = {
    method: 'GET',
    ...customConfig,
  }

  // Remember to return the Promise
  return window
    .fetch(`${process.env.REACT_APP_API_URL}/${endpoint}`, config)
    .then(response => {
	    // Remember to return the response
      return response.json()
    })
}
```

```jsx
// discover.js
/** @jsx jsx */
import React from 'react'
import {jsx} from '@emotion/core'

import './bootstrap'
import Tooltip from '@reach/tooltip'
import {FaSearch} from 'react-icons/fa'
import {Input, BookListUL, Spinner} from './components/lib'
import {BookRow} from './components/book-row'
import {client} from './utils/api-client'

function DiscoverBooksScreen() {
  const [status, setStatus] = React.useState('idle')
  const [queried, setQueried] = React.useState(false)
  const [query, setQuery] = React.useState(false)
  const [data, setData] = React.useState(null)

  React.useEffect(() => {
    if (!queried) {
      return
    }
    setStatus('loading')
    client(`books?query=${encodeURIComponent(query)}`).then(responseData => {
      setData(responseData)
      setStatus('success')
    })
  }, [queried, query])

  const isLoading = status === 'loading'
  const isSuccess = status === 'success'

  function handleSearchSubmit(event) {
    event.preventDefault()
    setQueried(true)
    setQuery(event.target.elements.search.value)
  }

  return (
    <div
      css={{maxWidth: 800, margin: 'auto', width: '90vw', padding: '40px 0'}}
    >
      <form onSubmit={handleSearchSubmit}>
        <Input
          placeholder="Search books..."
          id="search"
          css={{width: '100%'}}
        />
        <Tooltip label="Search Books">
          <label htmlFor="search">
            <button
              type="submit"
              css={{
                border: '0',
                position: 'relative',
                marginLeft: '-35px',
                background: 'transparent',
              }}
            >
              {isLoading ? <Spinner /> : <FaSearch aria-label="search" />}
            </button>
          </label>
        </Tooltip>
      </form>

      {isSuccess ? (
        data?.books?.length ? (
          <BookListUL css={{marginTop: 20}}>
            {data.books.map(book => (
              <li key={book.id} aria-label={book.title}>
                <BookRow key={book.id} book={book} />
              </li>
            ))}
          </BookListUL>
        ) : (
          <p>No books found. Try another search.</p>
        )
      ) : null}
    </div>
  )
}

export {DiscoverBooksScreen}

```

### Add Error Handling

```javascript
// api-client.js
function client(endpoint, customConfig = {}) {
  const config = {
    method: 'GET',
    ...customConfig,
  }

  return window
    .fetch(`${process.env.REACT_APP_API_URL}/${endpoint}`, config)
	  // 1. Turn into an async function
    .then(async response => {
      const data = await response.json()
      // 2. Check response
      if (response.ok) {
        return data
      } else {
        // 3. Reject if there's an error
        return Promise.reject(data)
      }
    })
}

export {client}
```



```jsx
// discover.js

// useEffect
  React.useEffect(() => {
    if (!queried) {
      return
    }
    setStatus('loading')
    client(`books?query=${encodeURIComponent(query)}`)
      .then(responseData => {
        setData(responseData)
        setStatus('success')
      })
    	// 4. Catch the error
      .catch(error => {
        setError(error)
        setStatus('error')
      })
  }, [queried, query])

// return
{isError ? (
  <div css={{color: colors.danger}}>
    <p>There was an error:</p>
    <pre>{error.message}</pre>
  </div>
) : null}
```



### Bonus: useAsync() Hook

```jsx
// Example usage:
// const {data, error, run, isLoading, isError, isSuccess} = useAsync()
//
// React.useEffect(() => {
//   run(client(`books?query=${encodeURIComponent(query)}`))
// }, [query, queried, run])
//
// Note: run can be added as a dependency since it is memoized with useCallback (it will never change)

const defaultInitialState = {status: 'idle', data: null, error: null}

function useAsync(initialState) {
  const initialStateRef = React.useRef({
    ...defaultInitialState,
    ...initialState,
  })
  const [{status, data, error}, setState] = React.useReducer(
    (s, a) => ({...s, ...a}),
    initialStateRef.current,
  )

  const safeSetState = useSafeDispatch(setState)

  const setData = React.useCallback(
    data => safeSetState({data, status: 'resolved'}),
    [safeSetState],
  )
  const setError = React.useCallback(
    error => safeSetState({error, status: 'rejected'}),
    [safeSetState],
  )
  const reset = React.useCallback(() => safeSetState(initialStateRef.current), [
    safeSetState,
  ])

  const run = React.useCallback(
    promise => {
      if (!promise || !promise.then) {
        throw new Error(
          `The argument passed to useAsync().run must be a promise. Maybe a function that's passed isn't returning anything?`,
        )
      }
      safeSetState({status: 'pending'})
      return promise.then(
        data => {
          setData(data)
          return data
        },
        error => {
          setError(error)
          return Promise.reject(error)
        },
      )
    },
    [safeSetState, setData, setError],
  )

  return {
    // using the same names that react-query uses for convenience
    isIdle: status === 'idle',
    isLoading: status === 'pending',
    isError: status === 'rejected',
    isSuccess: status === 'resolved',

    setData,
    setError,
    error,
    status,
    data,
    run,
    reset,
  }
}

export {useAsync}
```

