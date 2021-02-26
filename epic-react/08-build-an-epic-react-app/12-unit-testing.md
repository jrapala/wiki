# Unit Testing

Create unit tests for functions. Not everything needs a unit tests. Non-pure functions can benefit from testing as well. Just cover your use cases.



## Exercises

### Create a unit test for `formatDate`

```javascript
import {formatDate} from '../misc'

test('formatDate formats the date to look nice', () => {
  expect(formatDate(new Date('October 18, 1988'))).toBe('Oct 88')
})

```



### Set up a Server to Test Requests

Use `msw` to set up a mock server.

```javascript
import {server, rest} from 'test/server'
import {client} from '../api-client'

const apiURL = process.env.REACT_APP_API_URL

beforeAll(() => server.listen())
afterAll(() => server.close())
afterEach(() => server.resetHandlers())

test('calls fetch at the endpoint with the arguments for GET requests', async () => {
  const endpoint = 'test-endpoint'
  const mockResult = {mockValue: 'VALUE'}

  // add a server handler to handle a test request you'll be making
  server.use(
    rest.get(`${apiURL}/${endpoint}`, async (req, res, ctx) => {
      return res(ctx.json(mockResult))
    }),
  )

  const result = await client(endpoint)
  expect(result).toEqual(mockResult)
})
```



### Test if a Request has an Auth Header

```javascript
test('adds auth token when a token is provided', async () => {
  // Create fake token
  const token = 'FAKE_TOKEN'

  // We need to check the request that is returned
  let request
  const endpoint = 'test-endpoint'
  const mockResult = {mockValue: 'VALUE'}

  server.use(
    rest.get(`${apiURL}/${endpoint}`, async (req, res, ctx) => {
      request = req
      return res(ctx.json(mockResult))
    }),
  )

  // Pass in the token
  await client(endpoint, {token})
  // See if we get the token back
  expect(request.headers.get('Authorization')).toBe(`Bearer ${token}`)
})
```



### Client Request Config Overrides

```javascript
test('allows for config overrides', async () => {
  let request
  const endpoint = 'test-endpoint'
  const mockResult = {mockValue: 'VALUE'}

  server.use(
    // Change to put
    rest.put(`${apiURL}/${endpoint}`, async (req, res, ctx) => {
      request = req
      return res(ctx.json(mockResult))
    }),
  )

  // Create custom config
  const customConfig = {
    method: 'PUT',
    headers: {'Content-Type': 'fake-type'},
  }

  // Use custom config
  await client(endpoint, customConfig)
  
  // Check content type that is returned
  expect(request.headers.get('Content-Type')).toBe(
    customConfig.headers['Content-Type'],
  )
})
```



### POST By Default when Body Present and Stringified

```javascript
test('when data is provided, it is stringified and the method defaults to POST', async () => {
  const endpoint = 'test-endpoint'

  server.use(
    // Use a post request
    rest.post(`${apiURL}/${endpoint}`, async (req, res, ctx) => {
      // we are going to check the body
      return res(ctx.json(req.body))
    }),
  )

  const data = {a: 'b'}

  const result = await client(endpoint, {data})

  expect(result).toEqual(data)
})
```



### Automatic Log Out on 401 Error

```javascript
import {queryCache} from 'react-query'
import * as auth from 'auth-provider'
import {server, rest} from 'test/server'
import {client} from '../api-client'

const apiURL = process.env.REACT_APP_API_URL

// mock for testing logout
jest.mock('react-query')
jest.mock('auth-provider')

...

test('automatically logs the user out if a request returns a 401', async () => {
  const endpoint = 'test-endpoint'
  const mockResult = {mockValue: 'VALUE'}

  server.use(
    rest.get(`${apiURL}/${endpoint}`, async (req, res, ctx) => {
      // Return a 401 status
      return res(ctx.status(401), ctx.json(mockResult))
    }),
  )

  // Catch the error
  const result = await client(endpoint).catch(e => e)

  expect(result.message).toMatchInlineSnapshot(`"Please re-authenticate."`)

  // Check if mocked functions were called
  expect(queryCache.clear).toHaveBeenCalledTimes(1)
  expect(auth.logout).toHaveBeenCalledTimes(1)
})

```



### Ensure Promise Rejects on Error

```javascript
test('correctly rejects the promise if there is an error', async () => {
  const endpoint = 'test-endpoint'
  const testError = {mockValue: 'Test error'}

  server.use(
    rest.get(`${apiURL}/${endpoint}`, async (req, res, ctx) => {
      return res(ctx.status(400), ctx.json(testError))
    }),
  )

  // Need to check if the promise was actually rejected
  await expect(client(endpoint)).rejects.toEqual(testError)
})
```



### Use setupTests.js

Refactor and move the before/after logic to `setupTests.js` so they apply to all tests. This is a special file name.

```javascript
// setupTests.js
import {server} from 'test/server'

beforeAll(() => server.listen())
afterAll(() => server.close())
afterEach(() => server.resetHandlers())
```













