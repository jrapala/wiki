# Mocking HTTP Requests

Don't make HTTP requests in unit tests. Do that in E2E. 

Trade-off some confidence in your unit and integration tests for convenience. Make up for it with E2E.

We should run a mock server to handle all `window.fetch` requests we make in our tests. (Since `window.fetch` is not supported in `JSDOM/Node`, we will use `whatwg-fetch` which polyfills fetch).

This server is not really a server, but a request interceptor. We don't have to worry about finding available ports, and can mock requests made to other domains. `MSW` is a good request interceptor to use.

Example:

```javascript
// __tests__/fetch.test.js
import * as React from 'react'
import {rest} from 'msw'
import {setupServer} from 'msw/node'
import {render, waitForElementToBeRemoved, screen} from '@testing-library/react'
import {userEvent} from '@testing-library/user-event'
import Fetch from '../fetch'

const server = setupServer(
  rest.get('/greeting', (req, res, ctx) => {
    return res(ctx.json({greeting: 'hello there'}))
  }),
)

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())

test('loads and displays greeting', async () => {
  render(<Fetch url="/greeting" />)

  userEvent.click(screen.getByText('Load Greeting'))

  await waitForElementToBeRemoved(() => screen.getByText('Loading...'))

  expect(screen.getByRole('heading')).toHaveTextContent('hello there')
  expect(screen.getByRole('button')).toHaveAttribute('disabled')
})

test('handles server error', async () => {
  server.use(
    rest.get('/greeting', (req, res, ctx) => {
      return res(ctx.status(500))
    }),
  )

  render(<Fetch url="/greeting" />)

  userEvent.click(screen.getByText('Load Greeting'))

  await waitForElementToBeRemoved(() => screen.getByText('Loading...'))

  expect(screen.getByRole('alert')).toHaveTextContent('Oops, failed to fetch!')
  expect(screen.getByRole('button')).not.toHaveAttribute('disabled')
})
```



## Exercises

### Mock Service Worker and Responses

Write a test that connects the login form with a backend request for when the user submits the form:

```javascript
import * as React from 'react'
import {render, screen, waitForElementToBeRemoved} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import {build, fake} from '@jackfranklin/test-data-bot'
import {rest} from 'msw'
import {setupServer} from 'msw/node'
import Login from '../../components/login-submission'

const buildLoginForm = build({
  fields: {
    username: fake(f => f.internet.userName()),
    password: fake(f => f.internet.password()),
  },
})

const server = setupServer(
  // Check for missing username or password
  if (!req.body.password) {
    return res(ctx.status(400), ctx.json({message: 'password required'}))
  }
  if (!req.body.username) {
    return res(ctx.status(400), ctx.json({message: 'username required'}))
  }
  rest.post(
    'https://auth-provider.example.com/api/login',
    // call async handler that takes request, response, and context object
    async (req, res, ctx) => {
      // the response is a function you can call to tell msw what response to send
      // call response with the context json utility, and pass back what the user passed to us
      return res(ctx.json({username: req.body.username}))
    },
  ),
)
beforeAll(() => server.listen())
afterAll(() => server.close())

test(`logging in displays the user's username`, async () => {
  render(<Login />)
  const {username, password} = buildLoginForm()

  userEvent.type(screen.getByLabelText(/username/i), username)
  userEvent.type(screen.getByLabelText(/password/i), password)
  userEvent.click(screen.getByRole('button', {name: /submit/i}))

  // wait for loading indicator to go away
  await waitForElementToBeRemoved(() => screen.getByLabelText(/loading/i))

  expect(screen.getByText(username)).toBeInTheDocument()
})

```



### Reuse Server Request Handlers

You can use a mock server during development. It's often more reliable and works offline. Write UI for APIs that aren't finished yet.

```javascript
// test/server-handlers.js

import {rest} from 'msw'

const delay = process.env.NODE_ENV === 'test' ? 0 : 1500

const handlers = [
  rest.post(
    'https://auth-provider.example.com/api/login',
    async (req, res, ctx) => {
      if (!req.body.password) {
        return res(
          ctx.delay(delay),
          ctx.status(400),
          ctx.json({message: 'password required'}),
        )
      }
      if (!req.body.username) {
        return res(
          ctx.delay(delay),
          ctx.status(400),
          ctx.json({message: 'username required'}),
        )
      }
      return res(ctx.delay(delay), ctx.json({username: req.body.username}))
    },
  ),
]

export {handlers}

```

```javascript
// test/server.js - the server used in the application

import {setupWorker} from 'msw'
import {handlers} from './server-handlers'
import {homepage} from '../../package.json'

setupWorker(...handlers)

const fullUrl = new URL(homepage)

const server = setupWorker(...handlers)

server.start({
  quiet: true,
  serviceWorker: {
    url: fullUrl.pathname + 'mockServiceWorker.js',
  },
})

```

```javascript
// test 
import * as React from 'react'
import {render, screen, waitForElementToBeRemoved} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import {build, fake} from '@jackfranklin/test-data-bot'
import {setupServer} from 'msw/node'
import Login from '../../components/login-submission'
import {handlers} from 'test/server-handlers'

const buildLoginForm = build({
  fields: {
    username: fake(f => f.internet.userName()),
    password: fake(f => f.internet.password()),
  },
})

// Just use handlers instead
const server = setupServer(...handlers)

beforeAll(() => server.listen())
afterAll(() => server.close())

....
```



### Unhappy Path

Add a test for what happens if the response to our login request is a failure:

```javascript
test('omitting the password results in an error', async () => {
  render(<Login />)
  const {username} = buildLoginForm()

  userEvent.type(screen.getByLabelText(/username/i), username)
  // not going to fill in the password

  userEvent.click(screen.getByRole('button', {name: /submit/i}))

  await waitForElementToBeRemoved(() => screen.getByLabelText(/loading/i))

  expect(screen.getByRole('alert')).toHaveTextContent('password required')
})
```



### Use Inline Snapshots

If error messages from the API change, then your tests may break. Use a special assertion to take a "snapshot" of an error message and have Jest will update our code for us. 

Step 1: Add this assertion

```javascript
  expect(screen.getByRole('alert')).toMatchInlineSnapshot()
```

Step 2: Run the test. Your fill will be auto-updated to the following:

```javascript
  expect(screen.getByRole('alert')).toMatchInlineSnapshot(`
    <div
      role="alert"
      style="color: red;"
    >
      password required
    </div>
  `)
```

Step 3: We don't want that. We just want to test the text content. So let's update the code:

```
  expect(screen.getByRole('alert').textContent).toMatchInlineSnapshot(`
    <div
      role="alert"
      style="color: red;"
    >
      password required
    </div>
  `)
```

Step 4: Our test is now broken (it doesn't match the snapshot). Press `u` to update failing snapshot in the command line:

```javascript
  expect(screen.getByRole('alert').textContent).toMatchInlineSnapshot(
    `"password required"`,
  )
```

Step 5: This is what we want. If we ever change the error message, just press `u` to update the snapshot.

### Use One-Off Server Handlers

Add a test if there is an unexpected server error. Add a runtime server handler (a handle added after the server has already been started) that's just specific to the test. Don't bother adding this handler to the application-wide handlers.

```javascript
const server = setupServer(...handlers)

beforeAll(() => server.listen())
afterAll(() => server.close())
// cleanup any runtime handlers
afterEach(() => server.resetHandlers())

test('unknown server error displays the error message', async () => {
  // set the intention that the server response will be the same as the text content in the assertion
  const testErrorMessage = 'Oh no, something bad happened'

  // add runtime handler
  server.use(
    rest.post(
      'https://auth-provider.example.com/api/login',
      async (req, res, ctx) => {
        return res(ctx.status(500), ctx.json({message: testErrorMessage}))
      },
    ),
  )
  render(<Login />)

  userEvent.click(screen.getByRole('button', {name: /submit/i}))

  await waitForElementToBeRemoved(() => screen.getByLabelText(/loading/i))

  expect(screen.getByRole('alert')).toHaveTextContent(testErrorMessage)
})

```