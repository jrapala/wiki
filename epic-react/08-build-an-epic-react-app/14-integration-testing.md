# Integration Testing

[Static vs Unit vs Integration vs E2E Testing for Frontend Apps](https://kentcdodds.com/blog/unit-vs-integration-vs-e2e-tests)

[Write tests. Not too many. Mostly integration.](https://kentcdodds.com/blog/write-tests)

[Stop Mocking Fetch](https://kentcdodds.com/blog/stop-mocking-fetch)

Most case coverage can come from integration tests. They give us the most bang for our buck. Reserve unit tests for pure functions or highly reusable code/components.

You'll still mock dependencies, but usually third-party libraries and HTTP requests. You'll need to create a mock implementation for almost every request your application makes. You'll often need to simulate an authenticated state before running tests. You may need to mock your auth provider module.



## Exercises

**Step 1. Render the Application with AppProviders**

```javascript
import * as React from 'react'
import {render, screen} from '@testing-library/react'
import {AppProviders} from 'context'
import {App} from 'app'

test('renders all the book information', async () => {
  render(<App />, {wrapper: AppProviders})
  screen.debug()
})
```



**Step 2. Wait for Loading Element to Be Removed**

```javascript
import * as React from 'react'
import {render, screen, waitForElementToBeRemoved} from '@testing-library/react'
import {AppProviders} from 'context'
import {App} from 'app'

test('renders all the book information', async () => {
  render(<App />, {wrapper: AppProviders})
	// Callback is called every time there is a DOM change, or on a regular interval
	// It will prevent test from running any further until the element
	// no longer returns
  await waitForElementToBeRemoved(() => screen.getByLabelText(/loading/i))
  screen.debug()
})
```



**Step 3. Reverse-Engineer AuthProvider and Log In**

Try to match the side effect of your auth provider that creates a logged in state.

```javascript
import * as React from 'react'
import {render, screen, waitForElementToBeRemoved} from '@testing-library/react'
import * as auth from 'auth-provider'
import {AppProviders} from 'context'
import {App} from 'app'

test('renders all the book information', async () => {
  // After reverse-engingeering AuthProvider, we found they save a token to local storage
  // So, we can trick AuthProvider to think that user is logged in
  window.localStorage.setItem(auth.localStorageKey, 'SOME_FAKE_TOKEN')

  // Override window.fetch
  const originalFetch = window.fetch
  window.fetch = async (url, config) => {
    // Handle anything to /bootstrap
    if (url.endsWith('/bootstrap')) {
      // Return a successful response
      return {
        ok: true,
        json: async () => ({
          user: {username: 'bob', token: 'SOME_FAKE_TOKEN'},
          listItems: [],
        }),
      }
    }
    return originalFetch(url, config)
  }

  render(<App />, {wrapper: AppProviders})
  await waitForElementToBeRemoved(() => screen.getByLabelText(/loading/i))
  screen.debug()
})
```



**Step 4. Create Fake Users and Books**

```javascript
// src/test/generate.js

import faker from 'faker'

function buildUser(overrides) {
  return {
    id: faker.random.uuid(),
    username: faker.internet.userName(),
    password: faker.internet.password(),
    ...overrides,
  }
}

function buildBook(overrides) {
  return {
    id: faker.random.uuid(),
    title: faker.lorem.words(),
    author: faker.name.findName(),
    coverImageUrl: faker.image.imageUrl(),
    pageCount: faker.random.number(400),
    publisher: faker.company.companyName(),
    synopsis: faker.lorem.paragraph(),
    ...overrides,
  }
}
```



**Step 5. Render a Book Page in a Test**

```javascript
import * as React from 'react'
import {render, screen, waitForElementToBeRemoved} from '@testing-library/react'
import {buildBook, buildUser} from 'test/generate'
import * as auth from 'auth-provider'
import {AppProviders} from 'context'
import {App} from 'app'

test('renders all the book information', async () => {
  // Create fake user
  const user = buildUser()

  window.localStorage.setItem(auth.localStorageKey, 'SOME_FAKE_TOKEN')

  // Create fake book data
  const book = buildBook()

  // Get test on the route that the user is going to land on for the book screen
  window.history.pushState({}, 'Test page', `/book/${book.id}`)
  const originalFetch = window.fetch
  window.fetch = async (url, config) => {
    if (url.endsWith('/bootstrap')) {
      return {
        ok: true,
        json: async () => ({
          user: {...user, token: 'SOME_FAKE_TOKEN'},
          listItems: [],
        }),
      }
    // Add additional handler once we start making a request for a book
    } else if (url.endsWith(`/books/${book.id}`)) {
      return {
        ok: true,
        json: async () => ({book}),
      }
    }
    console.log(url, config)
    return originalFetch(url, config)
  }

  render(<App />, {wrapper: AppProviders})
  await waitForElementToBeRemoved(() => screen.getByLabelText(/loading/i))
  screen.debug()
})
```



**Step 6. Test What UI Element are Present**

```javascript
// Write assertions as best as you can
// If the way things look matters to you, you'll need to add additional tests
// You can use a tool like percy.io or applitools - they integrate well into this test set up
expect(screen.getByRole('heading', {name: book.title})).toBeInTheDocument()
expect(screen.getByText(book.author)).toBeInTheDocument()
expect(screen.getByText(book.publisher)).toBeInTheDocument()
expect(screen.getByText(book.synopsis)).toBeInTheDocument()
expect(screen.getByRole('img', {name: /book cover/i})).toHaveAttribute(
  'src',
  book.coverImageUrl,
)
// This verifies we have the right buttons on the screen (e.g. the option to add to list)
expect(screen.getByRole('button', {name: /add to list/i})).toBeInTheDocument()
expect(
  screen.queryByRole('button', {name: /remove from list/i}),
).not.toBeInTheDocument()
expect(
  screen.queryByRole('button', {name: /mark as read/i}),
).not.toBeInTheDocument()
expect(
  screen.queryByRole('button', {name: /mark as unread/i}),
).not.toBeInTheDocument()
expect(
  screen.queryByRole('textarea', {name: /notes/i}),
).not.toBeInTheDocument()
expect(screen.queryByRole('radio', {name: /star/i})).not.toBeInTheDocument()
expect(screen.queryByLabelText(/start date/i)).not.toBeInTheDocument()
```



**Step 7. Isolate Tests by Cleaning Up State and Cache**

```javascript
import {queryCache} from 'react-query'
import * as auth from 'auth-provider'

afterEach(async () => {
	queryCache.clear()
	await auth.logout()
})
```



### Create Mock msw Server

The `window.fetch` mock should be moved so it can be used for all tests. It is also not very robust (can't change request methods, etc..).

`msw` is a better solution that makes these easier to write.

`msw` was created to allow you to create request handlers (HTTP or GraphQL) and return mock responses. It does this using a ServiceWorker, so you'll see the fetch requests in the network tab, but as long as you have a mock handler, a real fetch call will not be made and instead your request handler can handle the request for you. In production, you can swap `msw` with a real production server.

**Mock Server:**

```javascript
// src/test/server/test-server.js
import {setupServer} from 'msw/node'
import {handlers} from './server-handlers'

const server = setupServer(...handlers)

export * from 'msw'
export {server}

```

**Dev Server:**

```javascript
// src/test/server/dev-server.js
import {setupWorker} from 'msw'
import {handlers} from './server-handlers'
import {homepage} from '../../../package.json'

const fullUrl = new URL(homepage)

const server = setupWorker(...handlers)

server.start({
  quiet: true,
  serviceWorker: {
    url: fullUrl.pathname + 'mockServiceWorker.js',
  },
})

export * from 'msw'
export {server}

```



**Server Handlers:**

```javascript
// src/test/server/server-handlers.js
import {rest} from 'msw'
import {match} from 'node-match-path'
import * as booksDB from 'test/data/books'
import * as usersDB from 'test/data/users'
import * as listItemsDB from 'test/data/list-items'

let sleep
if (process.env.CI) {
  sleep = () => Promise.resolve()
} else if (process.env.NODE_ENV === 'test') {
  sleep = () => Promise.resolve()
} else {
  sleep = (
    t = Math.random() * ls('__bookshelf_variable_request_time__', 400) +
      ls('__bookshelf_min_request_time__', 400),
  ) => new Promise(resolve => setTimeout(resolve, t))
}

function ls(key, defaultVal) {
  const lsVal = window.localStorage.getItem(key)
  let val
  if (lsVal) {
    val = Number(lsVal)
  }
  return Number.isFinite(val) ? val : defaultVal
}

const apiUrl = process.env.REACT_APP_API_URL
const authUrl = process.env.REACT_APP_AUTH_URL

const handlers = [
  rest.post(`${authUrl}/login`, async (req, res, ctx) => {
    const {username, password} = req.body
    const user = await usersDB.authenticate({username, password})
    return res(ctx.json({user}))
  }),

  rest.post(`${authUrl}/register`, async (req, res, ctx) => {
    const {username, password} = req.body
    const userFields = {username, password}
    await usersDB.create(userFields)
    let user
    try {
      user = await usersDB.authenticate(userFields)
    } catch (error) {
      return res(
        ctx.status(400),
        ctx.json({status: 400, message: error.message}),
      )
    }
    return res(ctx.json({user}))
  }),

  rest.get(`${apiUrl}/me`, async (req, res, ctx) => {
    const user = await getUser(req)
    const token = getToken(req)
    return res(ctx.json({user: {...user, token}}))
  }),

  rest.get(`${apiUrl}/bootstrap`, async (req, res, ctx) => {
    const user = await getUser(req)
    const token = getToken(req)
    const lis = await listItemsDB.readByOwner(user.id)
    const listItemsAndBooks = await Promise.all(
      lis.map(async listItem => ({
        ...listItem,
        book: await booksDB.read(listItem.bookId),
      })),
    )
    return res(ctx.json({user: {...user, token}, listItems: listItemsAndBooks}))
  }),

  rest.get(`${apiUrl}/books`, async (req, res, ctx) => {
    if (!req.url.searchParams.has('query')) {
      return ctx.fetch(req)
    }
    const query = req.url.searchParams.get('query')

    let matchingBooks = []
    if (query) {
      matchingBooks = await booksDB.query(query)
    } else {
      if (getToken(req)) {
        const user = await getUser(req)
        const allBooks = await getBooksNotInUsersList(user.id)
        // return a random assortment of 10 books not already in the user's list
        matchingBooks = allBooks.slice(0, 10)
      } else {
        const allBooks = await booksDB.readManyNotInList([])
        matchingBooks = allBooks.slice(0, 10)
      }
    }

    return res(ctx.json({books: matchingBooks}))
  }),

  rest.get(`${apiUrl}/books/:bookId`, async (req, res, ctx) => {
    const {bookId} = req.params
    const book = await booksDB.read(bookId)
    if (!book) {
      return res(
        ctx.status(404),
        ctx.json({status: 404, message: 'Book not found'}),
      )
    }
    return res(ctx.json({book}))
  }),

  rest.get(`${apiUrl}/list-items`, async (req, res, ctx) => {
    const user = await getUser(req)
    const lis = await listItemsDB.readByOwner(user.id)
    const listItemsAndBooks = await Promise.all(
      lis.map(async listItem => ({
        ...listItem,
        book: await booksDB.read(listItem.bookId),
      })),
    )
    return res(ctx.json({listItems: listItemsAndBooks}))
  }),

  rest.post(`${apiUrl}/list-items`, async (req, res, ctx) => {
    const user = await getUser(req)
    const {bookId} = req.body
    const listItem = await listItemsDB.create({
      ownerId: user.id,
      bookId: bookId,
    })
    const book = await booksDB.read(bookId)
    return res(ctx.json({listItem: {...listItem, book}}))
  }),

  rest.put(`${apiUrl}/list-items/:listItemId`, async (req, res, ctx) => {
    const user = await getUser(req)
    const {listItemId} = req.params
    const updates = req.body
    await listItemsDB.authorize(user.id, listItemId)
    const updatedListItem = await listItemsDB.update(listItemId, updates)
    const book = await booksDB.read(updatedListItem.bookId)
    return res(ctx.json({listItem: {...updatedListItem, book}}))
  }),

  rest.delete(`${apiUrl}/list-items/:listItemId`, async (req, res, ctx) => {
    const user = await getUser(req)
    const {listItemId} = req.params
    await listItemsDB.authorize(user.id, listItemId)
    await listItemsDB.remove(listItemId)
    return res(ctx.json({success: true}))
  }),

  rest.post(`${apiUrl}/profile`, async (req, res, ctx) => {
    // here's where we'd actually send the report to some real data store.
    return res(ctx.json({success: true}))
  }),
].map(handler => {
  return {
    ...handler,
    async resolver(req, res, ctx) {
      try {
        if (shouldFail(req)) {
          throw new Error('Request failure (for testing purposes).')
        }
        const result = await handler.resolver(req, res, ctx)
        return result
      } catch (error) {
        const status = error.status || 500
        return res(
          ctx.status(status),
          ctx.json({status, message: error.message || 'Unknown Error'}),
        )
      } finally {
        await sleep()
      }
    },
  }
})

function shouldFail(req) {
  if (JSON.stringify(req.body)?.includes('FAIL')) return true
  if (req.url.searchParams.toString()?.includes('FAIL')) return true
  if (process.env.NODE_ENV === 'test') return false
  const failureRate = Number(
    window.localStorage.getItem('__bookshelf_failure_rate__') || 0,
  )
  if (Math.random() < failureRate) return true
  if (requestMatchesFailConfig(req)) return true

  return false
}

function requestMatchesFailConfig(req) {
  function configMatches({requestMethod, urlMatch}) {
    return (
      (requestMethod === 'ALL' || req.method === requestMethod) &&
      match(urlMatch, req.url.pathname).matches
    )
  }
  try {
    const failConfig = JSON.parse(
      window.localStorage.getItem('__bookshelf_request_fail_config__') || '[]',
    )
    if (failConfig.some(configMatches)) return true
  } catch (error) {
    window.localStorage.removeItem('__bookshelf_request_fail_config__')
  }
  return false
}

const getToken = req => req.headers.get('Authorization')?.replace('Bearer ', '')

async function getUser(req) {
  const token = getToken(req)
  if (!token) {
    const error = new Error('A token must be provided')
    error.status = 401
    throw error
  }
  let userId
  try {
    userId = atob(token)
  } catch (e) {
    const error = new Error('Invalid token. Please login again.')
    error.status = 401
    throw error
  }
  const user = await usersDB.read(userId)
  return user
}

async function getBooksNotInUsersList(userId) {
  const bookIdsInUsersList = (await listItemsDB.readByOwner(userId)).map(
    li => li.bookId,
  )
  const books = await booksDB.readManyNotInList(bookIdsInUsersList)
  return books
}

export {handlers}

```



**Index:**

```javascript
// src/test/server/index.js
// dynamically export the server based on the environment
// using CommonJS in this file because it's a bit simpler
// even though mixing CJS and ESM is not typically recommended
// It is possible to do this with ESM using `codegen.macro`
// and you can take a look at an example of this here:
// https://github.com/kentcdodds/bookshelf/blob/aef4f122428718ff422e203c6a68301dca50b396/src/test/server/index.js

if (process.env.NODE_ENV === 'development') {
  module.exports = require('./dev-server')
} else if (process.env.NODE_ENV === 'test') {
  module.exports = require('./test-server')
} else {
  // in normal apps you'll not do anything in this case
  // but for this workshop app, we're actually going to
  // deploy our mock service worker to production
  // so normally, this condition would just look like this:

  // module.exports = ""

  // but for us, since we're shipping the dev server to prod
  // we'll do the same thing we did for development:
  module.exports = require('./dev-server')
}

```

**Test Setup:**

```javascript
// src/setupTests.js
import '@testing-library/jest-dom'
import {server} from 'test/server'

// enable API mocking in test runs using the same request handlers
// as for the client-side mocking.
beforeAll(() => server.listen())
afterAll(() => server.close())
afterEach(() => server.resetHandlers())

```



**Updated Tests:**

```javascript
import * as React from 'react'
import {render, screen, waitForElementToBeRemoved} from '@testing-library/react'
import {queryCache} from 'react-query'
import {buildBook, buildUser} from 'test/generate'
import * as booksDB from 'test/data/books'
import * as usersDB from 'test/data/users'
import * as listItemsDB from 'test/data/list-items'
import * as auth from 'auth-provider'
import {AppProviders} from 'context'
import {App} from 'app'

afterEach(async () => {
	queryCache.clear()
	// reset DBs
  await Promise.all([
    auth.logout(),
    usersDB.reset(),
    booksDB.reset(),
    listItemsDB.reset(),
  ])
})

test('renders all the book information', async () => {
  const user = buildUser()
  // Save new user to the database
  await usersDB.create(user)
  // Authenticate user
  const authUser = await usersDB.authenticate(user)
  window.localStorage.setItem(auth.localStorageKey, authUser.token)

  // Add new book to the database
  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`
  window.history.pushState({}, 'Test page', route)

  render(<App />, {wrapper: AppProviders})

  // msw takes a little longer, so lets make sure all loading indicators are gone
  await waitForElementToBeRemoved(() => [
    ...screen.queryAllByLabelText(/loading/i),
    ...screen.queryAllByText(/loading/i),
  ])

  expect(screen.getByRole('heading', {name: book.title})).toBeInTheDocument()
  expect(screen.getByText(book.author)).toBeInTheDocument()
  expect(screen.getByText(book.publisher)).toBeInTheDocument()
  expect(screen.getByText(book.synopsis)).toBeInTheDocument()
  expect(screen.getByRole('img', {name: /book cover/i})).toHaveAttribute(
    'src',
    book.coverImageUrl,
  )
  // This verifies we have the right buttons on the screen (e.g. the option to add to list)
  expect(screen.getByRole('button', {name: /add to list/i})).toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /remove from list/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as read/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as unread/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('textarea', {name: /notes/i}),
  ).not.toBeInTheDocument()
  expect(screen.queryByRole('radio', {name: /star/i})).not.toBeInTheDocument()
  expect(screen.queryByLabelText(/start date/i)).not.toBeInTheDocument()
  screen.debug()
})

```



### Write Second Integration Test

```javascript
test('can create a list item for the book', async () => {
  const user = buildUser()
  await usersDB.create(user)
  const authUser = await usersDB.authenticate(user)
  window.localStorage.setItem(auth.localStorageKey, authUser.token)

  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`
  window.history.pushState({}, 'Test page', route)

  render(<App />, {wrapper: AppProviders})

  await waitForElementToBeRemoved(() => [
    ...screen.queryAllByLabelText(/loading/i),
    ...screen.queryAllByText(/loading/i),
  ])

  const addToListButton = screen.getByRole('button', {name: /add to list/i})
  userEvent.click(addToListButton)
  expect(addToListButton).toBeDisabled()

  await waitForElementToBeRemoved(() => [
    ...screen.queryAllByLabelText(/loading/i),
    ...screen.queryAllByText(/loading/i),
  ])

  expect(
    screen.getByRole('button', {name: /mark as read/i}),
  ).toBeInTheDocument()
  expect(
    screen.getByRole('button', {name: /remove from list/i}),
  ).toBeInTheDocument()
  expect(screen.getByRole('textbox', {name: /notes/i})).toBeInTheDocument()

  const startDateNode = screen.getByLabelText(/start date/i)
  // Not recommended to bring in things you're not explicitly testng, however the alternative are way more complex
  // formatDate is also very well unit tested
  expect(startDateNode).toHaveTextContent(formatDate(new Date()))

  expect(
    screen.queryByRole('button', {name: /add to list/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as unread/i}),
  ).not.toBeInTheDocument()
  expect(screen.queryByRole('radio', {name: /star/i})).not.toBeInTheDocument()
})
```



### Abstract Functionality

Let's keep it DRY:

```javascript
const waitForLoadingToFinish = () =>
  waitForElementToBeRemoved(() => [
    ...screen.queryAllByLabelText(/loading/i),
    ...screen.queryAllByText(/loading/i),
  ])

async function loginAsUser(userProperties) {
  const user = buildUser(userProperties)
  await usersDB.create(user)
  const authUser = await usersDB.authenticate(user)
  window.localStorage.setItem(auth.localStorageKey, authUser.token)
  return authUser
}
```



### Custom Render Function

Create a custom render function that wraps the app in the providers and allows different options to be passed in:

```javascript
import * as React from 'react'
import {
  render as rtlRender,
  screen,
  waitForElementToBeRemoved,
} from '@testing-library/react'
import {queryCache} from 'react-query'
import {buildBook, buildUser} from 'test/generate'
import * as booksDB from 'test/data/books'
import * as usersDB from 'test/data/users'
import * as listItemsDB from 'test/data/list-items'
import * as auth from 'auth-provider'
import {AppProviders} from 'context'
import {App} from 'app'
import userEvent from '@testing-library/user-event'
import {formatDate} from 'utils/misc'

afterEach(async () => {
  queryCache.clear()

  await Promise.all([
    auth.logout(),
    usersDB.reset(),
    booksDB.reset(),
    listItemsDB.reset(),
  ])
})

async function render(ui, {route = '/list', user, ...renderOptions} = {}) {
  user = typeof user === 'undefined' ? await loginAsUser() : user

  window.history.pushState({}, 'Test page', route)

  const returnValue = {
    ...rtlRender(ui, {wrapper: AppProviders, ...renderOptions}),
    user,
  }

  await waitForLoadingToFinish()
  return returnValue
}

const waitForLoadingToFinish = () =>
  waitForElementToBeRemoved(() => [
    ...screen.queryAllByLabelText(/loading/i),
    ...screen.queryAllByText(/loading/i),
  ])

async function loginAsUser(userProperties) {
  const user = buildUser(userProperties)
  await usersDB.create(user)
  const authUser = await usersDB.authenticate(user)
  window.localStorage.setItem(auth.localStorageKey, authUser.token)
  return authUser
}

test('renders all the book information', async () => {
  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`
  await render(<App />, {route})

  expect(screen.getByRole('heading', {name: book.title})).toBeInTheDocument()
  expect(screen.getByText(book.author)).toBeInTheDocument()
  expect(screen.getByText(book.publisher)).toBeInTheDocument()
  expect(screen.getByText(book.synopsis)).toBeInTheDocument()
  expect(screen.getByRole('img', {name: /book cover/i})).toHaveAttribute(
    'src',
    book.coverImageUrl,
  )

  expect(screen.getByRole('button', {name: /add to list/i})).toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /remove from list/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as read/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as unread/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('textarea', {name: /notes/i}),
  ).not.toBeInTheDocument()
  expect(screen.queryByRole('radio', {name: /star/i})).not.toBeInTheDocument()
  expect(screen.queryByLabelText(/start date/i)).not.toBeInTheDocument()
})

test('can create a list item for the book', async () => {
  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`
  await render(<App />, {route})

  const addToListButton = screen.getByRole('button', {name: /add to list/i})
  userEvent.click(addToListButton)
  expect(addToListButton).toBeDisabled()

  await waitForLoadingToFinish()

  expect(
    screen.getByRole('button', {name: /mark as read/i}),
  ).toBeInTheDocument()
  expect(
    screen.getByRole('button', {name: /remove from list/i}),
  ).toBeInTheDocument()
  expect(screen.getByRole('textbox', {name: /notes/i})).toBeInTheDocument()

  const startDateNode = screen.getByLabelText(/start date/i)
  expect(startDateNode).toHaveTextContent(formatDate(new Date()))

  expect(
    screen.queryByRole('button', {name: /add to list/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as unread/i}),
  ).not.toBeInTheDocument()
  expect(screen.queryByRole('radio', {name: /star/i})).not.toBeInTheDocument()
})

```



### Global Utils

Move cleanup functions to `setupTests`:

```javascript
import '@testing-library/jest-dom'
import {server} from 'test/server'
import {queryCache} from 'react-query'
import * as booksDB from 'test/data/books'
import * as usersDB from 'test/data/users'
import * as listItemsDB from 'test/data/list-items'
import * as auth from 'auth-provider'

// enable API mocking in test runs using the same request handlers
// as for the client-side mocking.
beforeAll(() => server.listen())
afterAll(() => server.close())
afterEach(() => server.resetHandlers())

afterEach(async () => {
  queryCache.clear()

  await Promise.all([
    auth.logout(),
    usersDB.reset(),
    booksDB.reset(),
    listItemsDB.reset(),
  ])
})

```



Create utility file:

```javascript
// src/text/app-test-utils.js
import {
  render as rtlRender,
  screen,
  waitForElementToBeRemoved,
} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import {buildUser} from 'test/generate'
import * as usersDB from 'test/data/users'
import * as auth from 'auth-provider'
import {AppProviders} from 'context'

async function render(ui, {route = '/list', user, ...renderOptions} = {}) {
  user = typeof user === 'undefined' ? await loginAsUser() : user

  window.history.pushState({}, 'Test page', route)

  const returnValue = {
    ...rtlRender(ui, {wrapper: AppProviders, ...renderOptions}),
    user,
  }

  await waitForLoadingToFinish()
  return returnValue
}

const waitForLoadingToFinish = () =>
  waitForElementToBeRemoved(() => [
    ...screen.queryAllByLabelText(/loading/i),
    ...screen.queryAllByText(/loading/i),
  ])

async function loginAsUser(userProperties) {
  const user = buildUser(userProperties)
  await usersDB.create(user)
  const authUser = await usersDB.authenticate(user)
  window.localStorage.setItem(auth.localStorageKey, authUser.token)
  return authUser
}

// export commonly imported items so you don't have to import them into every test file
export * from '@testing-library/react'
export {render, userEvent, loginAsUser, waitForLoadingToFinish}

```



```javascript
// src/__tests__/book-screen.js
import * as React from 'react'
import {
  render,
  screen,
  waitForLoadingToFinish,
  userEvent,
} from 'test/app-test-utils'
import {buildBook} from 'test/generate'
import * as booksDB from 'test/data/books'
import {App} from 'app'
import {formatDate} from 'utils/misc'

test('renders all the book information', async () => {
  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`
  await render(<App />, {route})

  expect(screen.getByRole('heading', {name: book.title})).toBeInTheDocument()
  expect(screen.getByText(book.author)).toBeInTheDocument()
  expect(screen.getByText(book.publisher)).toBeInTheDocument()
  expect(screen.getByText(book.synopsis)).toBeInTheDocument()
  expect(screen.getByRole('img', {name: /book cover/i})).toHaveAttribute(
    'src',
    book.coverImageUrl,
  )

  expect(screen.getByRole('button', {name: /add to list/i})).toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /remove from list/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as read/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as unread/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('textarea', {name: /notes/i}),
  ).not.toBeInTheDocument()
  expect(screen.queryByRole('radio', {name: /star/i})).not.toBeInTheDocument()
  expect(screen.queryByLabelText(/start date/i)).not.toBeInTheDocument()
})

test('can create a list item for the book', async () => {
  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`
  await render(<App />, {route})

  const addToListButton = screen.getByRole('button', {name: /add to list/i})
  userEvent.click(addToListButton)
  expect(addToListButton).toBeDisabled()

  await waitForLoadingToFinish()

  expect(
    screen.getByRole('button', {name: /mark as read/i}),
  ).toBeInTheDocument()
  expect(
    screen.getByRole('button', {name: /remove from list/i}),
  ).toBeInTheDocument()
  expect(screen.getByRole('textbox', {name: /notes/i})).toBeInTheDocument()

  const startDateNode = screen.getByLabelText(/start date/i)
  expect(startDateNode).toHaveTextContent(formatDate(new Date()))

  expect(
    screen.queryByRole('button', {name: /add to list/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as unread/i}),
  ).not.toBeInTheDocument()
  expect(screen.queryByRole('radio', {name: /star/i})).not.toBeInTheDocument()
})


```



### Can Remove List Item for Book

```javascript
test('can remove a list item for the book', async () => {
  // 2. The item must be associated to a user.
  const user = await loginAsUser()
  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`
  // 1. Instead of overtesting and pressing the add button, then remove button, add
  // a list item manually. The list item must be associated to a user.
  await listItemsDB.create(buildListItem({owner: user, book}))

  // 3. We must render the app with the user specified
  await render(<App />, {route, user})

  // 4. Check for the remove from list button instead
  const removeFromList = screen.getByRole('button', {name: /remove from list/i})
  userEvent.click(removeFromList)
  expect(removeFromList).toBeDisabled()

  await waitForLoadingToFinish()

  expect(screen.getByRole('button', {name: /add to list/i})).toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /remove from list/i}),
  ).not.toBeInTheDocument()
})

```



### Can Mark a List Item as Read

```javascript
test('can mark a list item as read', async () => {
  const user = await loginAsUser()
  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`

  // Set finish date to null, to make sure it's unread
  // We also need to use this list item later
  const listItem = await listItemsDB.create(
    buildListItem({owner: user, book, finishDate: null}),
  )

  await render(<App />, {route, user})

  const markAsReadButton = screen.getByRole('button', {name: /mark as read/i})
  userEvent.click(markAsReadButton)
  expect(markAsReadButton).toBeDisabled()

  await waitForLoadingToFinish()

  // Check mark as unread button
  expect(
    screen.getByRole('button', {name: /mark as unread/i}),
  ).toBeInTheDocument()

  // Check start and finish date. Use list item start date.
  const startAndFinishDateNode = screen.getByLabelText(/start and finish date/i)
  expect(startAndFinishDateNode).toHaveTextContent(
    `${formatDate(listItem.startDate)} — ${formatDate(Date.now())}`,
  )

  // Check mark as read button
  expect(
    screen.queryByRole('button', {name: /mark as read/i}),
  ).not.toBeInTheDocument()
})

```



### Can Edit a Note

```javascript
test('can edit a note', async () => {
  const user = await loginAsUser()
  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`

  const listItem = await listItemsDB.create(buildListItem({owner: user, book}))

  await render(<App />, {route, user})

  const newNotes = faker.lorem.words()
  console.log(newNotes)
  const notesTextArea = screen.getByRole('textbox', {name: /notes/i})

  userEvent.clear(notesTextArea)
  userEvent.type(notesTextArea, newNotes)

  // Due to debounce, we need to wait for the loading text to appear first
  await screen.findByLabelText(/loading/i)

  // We remove the wait for loading to go away, because the new notes actually appear first

  expect(notesTextArea).toHaveValue(newNotes)

  // Since we can't verify if the note has saved through the UI
  // check the DB entry instead
  expect(await listItemsDB.read(listItem.id)).toMatchObject({
    notes: newNotes,
  })
})
```



### Use Jest Fake Timers

Sometimes its useful to fake out your timers. Some of the code we write requires long wait times (timeouts, debounces, etc..)

`jest.useFakeTimers()` allows you to do this. Remember to set `jest.useRealTimers()` back (add as a `beforeEach`)

```javascript
test('can edit a note', async () => {
  jest.useFakeTimers()
  const user = await loginAsUser()
  const book = await booksDB.create(buildBook())
  const route = `/book/${book.id}`

  const listItem = await listItemsDB.create(buildListItem({owner: user, book}))

  await render(<App />, {route, user})

  const newNotes = faker.lorem.words()
  const notesTextArea = screen.getByRole('textbox', {name: /notes/i})

  userEvent.clear(notesTextArea)
  userEvent.type(notesTextArea, newNotes)

  await screen.findByLabelText(/loading/i)

  // We now have more control over timing
  await waitForLoadingToFinish()

  expect(notesTextArea).toHaveValue(newNotes)

  expect(await listItemsDB.read(listItem.id)).toMatchObject({
    notes: newNotes,
  })
})
```



**Additional Patch for React Query:**

```javascript
// real times is a good default to start, individual tests can
// enable fake timers if they need, and if they have, then we should
// run all the pending timers (in `act` because this can trigger state updates)
// then we'll switch back to realTimers.
// it's important this comes last here because jest runs afterEach callbacks
// in reverse order and we want this to be run first so we get back to real timers
// before any other cleanup
afterEach(async () => {
  // waitFor is important here. If there are queries that are being fetched at
  // the end of the test and we continue on to the next test before waiting for
  // them to finalize, the tests can impact each other in strange ways.
  await waitFor(() => expect(queryCache.isFetching).toBe(0))
  if (jest.isMockFunction(setTimeout)) {
    act(() => jest.runOnlyPendingTimers())
    jest.useRealTimers()
  }
})

```



### Set Up Mock Profiler for Tests

We are sending Profiler info after few seconds during our tests, which we don't need to do. Let's mock the Profiler instead:

```javascript
// src/components/__mocks__/profiler.js

// we have this mock here so the profiler doesn't actually
// attempt to send profile reports during tests.
const Profiler = ({children}) => children

export {Profiler}

```

In `setupTests.js`, add:

```javascript
jest.mock("components/profiler")
```



### Create Component-Specific Utility

Abstract some common code to the same test file by making a `renderBookScreen` function:

```javascript
import * as React from 'react'
import {
  render,
  screen,
  waitForLoadingToFinish,
  userEvent,
  loginAsUser,
} from 'test/app-test-utils'
import faker from 'faker'
import {buildBook, buildListItem} from 'test/generate'
import * as booksDB from 'test/data/books'
import * as listItemsDB from 'test/data/list-items'
import {App} from 'app'
import {formatDate} from 'utils/misc'

async function renderBookScreen({user, book, listItem} = {}) {
  if (user === undefined) {
    user = await loginAsUser()
  }

  if (book === undefined) {
    book = await booksDB.create(buildBook())
  }

  if (listItem === undefined) {
    listItem = await listItemsDB.create(buildListItem({owner: user, book}))
  }

  const route = `/book/${book.id}`

  const utils = await render(<App />, {route, user})

  return {...utils, user, book, listItem}
}

test('renders all the book information', async () => {
  const {book} = await renderBookScreen({listItem: null})

  expect(screen.getByRole('heading', {name: book.title})).toBeInTheDocument()
  expect(screen.getByText(book.author)).toBeInTheDocument()
  expect(screen.getByText(book.publisher)).toBeInTheDocument()
  expect(screen.getByText(book.synopsis)).toBeInTheDocument()
  expect(screen.getByRole('img', {name: /book cover/i})).toHaveAttribute(
    'src',
    book.coverImageUrl,
  )

  expect(screen.getByRole('button', {name: /add to list/i})).toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /remove from list/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as read/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as unread/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('textarea', {name: /notes/i}),
  ).not.toBeInTheDocument()
  expect(screen.queryByRole('radio', {name: /star/i})).not.toBeInTheDocument()
  expect(screen.queryByLabelText(/start date/i)).not.toBeInTheDocument()
})

test('can create a list item for the book', async () => {
  await renderBookScreen({listItem: null})

  const addToListButton = screen.getByRole('button', {name: /add to list/i})
  userEvent.click(addToListButton)
  expect(addToListButton).toBeDisabled()

  await waitForLoadingToFinish()

  expect(
    screen.getByRole('button', {name: /mark as read/i}),
  ).toBeInTheDocument()
  expect(
    screen.getByRole('button', {name: /remove from list/i}),
  ).toBeInTheDocument()
  expect(screen.getByRole('textbox', {name: /notes/i})).toBeInTheDocument()

  const startDateNode = screen.getByLabelText(/start date/i)
  expect(startDateNode).toHaveTextContent(formatDate(new Date()))

  expect(
    screen.queryByRole('button', {name: /add to list/i}),
  ).not.toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /mark as unread/i}),
  ).not.toBeInTheDocument()
  expect(screen.queryByRole('radio', {name: /star/i})).not.toBeInTheDocument()
})

test('can remove a list item for the book', async () => {
  await renderBookScreen()

  const removeFromList = screen.getByRole('button', {name: /remove from list/i})
  userEvent.click(removeFromList)
  expect(removeFromList).toBeDisabled()

  await waitForLoadingToFinish()

  expect(screen.getByRole('button', {name: /add to list/i})).toBeInTheDocument()
  expect(
    screen.queryByRole('button', {name: /remove from list/i}),
  ).not.toBeInTheDocument()
})

test('can mark a list item as read', async () => {
  const {listItem} = await renderBookScreen()
  await listItemsDB.update(listItem.id, {finishDate: null})

  const markAsReadButton = screen.getByRole('button', {name: /mark as read/i})
  userEvent.click(markAsReadButton)
  expect(markAsReadButton).toBeDisabled()

  await waitForLoadingToFinish()

  expect(
    screen.getByRole('button', {name: /mark as unread/i}),
  ).toBeInTheDocument()

  const startAndFinishDateNode = screen.getByLabelText(/start and finish date/i)
  expect(startAndFinishDateNode).toHaveTextContent(
    `${formatDate(listItem.startDate)} — ${formatDate(Date.now())}`,
  )

  expect(
    screen.queryByRole('button', {name: /mark as read/i}),
  ).not.toBeInTheDocument()
})

test('can edit a note', async () => {
  jest.useFakeTimers()
  const {listItem} = await renderBookScreen()

  const newNotes = faker.lorem.words()
  const notesTextArea = screen.getByRole('textbox', {name: /notes/i})

  userEvent.clear(notesTextArea)
  userEvent.type(notesTextArea, newNotes)

  await screen.findByLabelText(/loading/i)

  // We now have more control over timing
  await waitForLoadingToFinish()

  expect(notesTextArea).toHaveValue(newNotes)

  expect(await listItemsDB.read(listItem.id)).toMatchObject({
    notes: newNotes,
  })
})

```



### Show Error When Load Fails

Make sure app works properly when there is an error message:

```javascript
test('shows an error message when the book fails to load', async () => {
  const book = {id: 'BAD_ID'}
  await renderBookScreen({listItem: null, book})

  expect((await screen.findByRole('alert')).textContent).toMatchInlineSnapshot(
    `"There was an error: Book not found"`,
  )
})
```



### Scope Hooks to Describe Block

If you're testing for errors, the errors will display when you run the test. A `jest.spyOn(console, 'error').mockImplementation(() => {})` will fix it, but you must scope it to the test you are using it for, so actual errors can appear elsewhere. You can scope with `describe`:

```javascript
describe('console errors', () => {
  beforeAll(() => {
    jest.spyOn(console, 'error').mockImplementation(() => {})
  })

  afterAll(() => {
    console.error.mockRestore()
  })

  test('shows an error message when the book fails to load', async () => {
    const book = {id: 'BAD_ID'}
    await renderBookScreen({listItem: null, book})

    expect(
      (await screen.findByRole('alert')).textContent,
    ).toMatchInlineSnapshot(`"There was an error: Book not found"`)
  })
})
```

Kent only uses `describe` in situations like this. Otherwise, you're just wasting time grouping things together.

### Update Failures are Displayed

Testing a failure of updating a book's note. We must add a new server handler specific for this test:

```javascript
  test('note update failures are displayed', async () => {
    jest.useFakeTimers()
    const {listItem} = await renderBookScreen()

    const newNotes = faker.lorem.words()
    const notesTextArea = screen.getByRole('textbox', {name: /notes/i})

    server.use(
      rest.put(`${apiURL}/list-items/:listItemId`, async (req, res, ctx) => {
        return res(ctx.status(400), ctx.json({status: 400, message: 'OH NO!!'}))
      }),
    )
    userEvent.clear(notesTextArea)
    userEvent.type(notesTextArea, newNotes)

    await screen.findByLabelText(/loading/i)

    await waitForLoadingToFinish()

    expect(screen.getByRole('alert').textContent).toMatchInlineSnapshot(
      `"There was an error: OH NO!!"`,
    )
    expect(await listItemsDB.read(listItem.id)).not.toMatchObject({
      notes: newNotes,
    })
  })
```



​          

