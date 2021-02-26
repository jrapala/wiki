# Mocking Browser APIs and Modules

We're not running tests in a real browser. We're using JSDOM instead (a simulated browser environment in Node). JSDOM isn't capable of everything, so you may need to mock some functionality. e.g. `window.matchMedia` and `window.resizeTo`

Example:

```javascript
import matchMediaPolyfill from 'mq-polyfill'

beforeAll(() => {
  matchMediaPolyfill(window)
  window.resizeTo = function resizeTo(width, height) {
    Object.assign(this, {
      innerWidth: width,
      innerHeight: height,
      outerWidth: width,
      outerHeight: height,
    }).dispatchEvent(new this.Event('resize'))
  }
})
```



Mocking is also useful if you don't want a module to run during a test.

Example:

```javascript
// math.js
export const add = (a, b) => a + b
export const subtract = (a, b) => a - b

// __tests__/some-test.js
import {add, subtract} from '../math'

jest.mock('../math')

// now all the function exports from the "math.js" module are jest mock functions
// so we can call .mockImplementation(...) on them
// and make assertions like .toHaveBeenCalledTimes(...)
```

```javascript
jest.mock('../math', () => {
  const actualMath = jest.requireActual('../math')
  return {
    ...actualMath,
    subtract: jest.fn(),
  }
})

// now the `add` export is the normal function,
// but the `subtract` export is a mock function.
```





## Exercises

### Mock Geolocation

`window.navigatior.geolocation` is not supported in JSDOM. Let's mock it:

```javascript
import * as React from 'react'
import {render, screen, act} from '@testing-library/react'
import Location from '../../examples/location'

// set window.navigator.geolocation to an object that has a getCurrentPosition mock function
beforeAll(() => {
  window.navigator.geolocation = {
    getCurrentPosition: jest.fn(), // We don't actually care about the location, so just mock it
  }
})

// This allows you to create a promise that you can resolve/reject on demand.
function deferred() {
  let resolve, reject
  const promise = new Promise((res, rej) => {
    resolve = res
    reject = rej
  })
  return {promise, resolve, reject}
}

test('displays the users current location', async () => {
  const fakePosition = {
    coords: {
      latitude: 35,
      longitude: 139,
    },
  }

  // create a deferred promise
  const {promise, resolve} = deferred()

  window.navigator.geolocation.getCurrentPosition.mockImplementation(
    callback => {
      // We need to wait until the loading indicator goes away
      promise.then(() => callback(fakePosition))
    },
  )

  render(<Location />)
  expect(screen.getByLabelText(/loading/i)).toBeInTheDocument()

  // resolve the promise and wait for it to resolve
  resolve()
  await promise

  // need to use queryByLabelText because getByLabelText will throw an error if the label is not found
  expect(screen.queryByLabelText(/loading/i)).not.toBeInTheDocument()
  expect(screen.getByText(/latitude/i)).toHaveTextContent(
    `Latitude: ${fakePosition.coords.latitude}`,
  )
  expect(screen.getByText(/longitude/i)).toHaveTextContent(
    `Longitude: ${fakePosition.coords.longitude}`,
  )
})
```



### Act Function

There is an error in the previous test:

```
    Warning: An update to Location inside a test was not wrapped in act(...).
    
    When testing, code that causes React state updates should be wrapped into act(...):
    
    act(() => {
      /* fire events that update state */
    });
    /* assert on the output */

```

A state-updater function is being called inside a third-party component, which React isn't expecting to happen, so we need to tell it we expect it to happen.



Wrap this in `act` from `@testing-library/react`:

```javascript
  await act(async () => {
    resolve()
    await promise
  })
```

Once the promise resolves, this will flush out all the side effects that happened during that time.

Most of the APIs you use in `@testing-library/react` won't require you to do this. This happens rarely.



### Mock the Module

There's another way to solve this problem. Mock the third-party module.

```javascript
import * as React from 'react'
import {render, screen, act} from '@testing-library/react'
import Location from '../../examples/location'
import {useCurrentPosition} from 'react-use-geolocation'

// Jest will look for all exports from this library
// For any of those functions, Jest will create a mock function
jest.mock('react-use-geolocation')

test('displays the users current location', async () => {
  const fakePosition = {
    coords: {
      latitude: 35,
      longitude: 139,
    },
  }

  // need to move the state updater function out so we can use it later
  let setReturnValue

  function useMockCurrentPosition() {
    const state = React.useState([])
    // take the state updater value and assign it something we can call ourselves
    setReturnValue = state[1]
    return state[0]
  }

  // when useCurrentPosition is called, call useMockCurrentPosition instead
  useCurrentPosition.mockImplementation(useMockCurrentPosition)

  // rendering this means we will be using useCurrentPosition from 'react-use-geolocation'
  // we mocked this library, so useCurrentPosition is now a jest mocked function
  render(<Location />)
  expect(screen.getByLabelText(/loading/i)).toBeInTheDocument()

  act(() => {
    setReturnValue([fakePosition])
  })

  expect(screen.queryByLabelText(/loading/i)).not.toBeInTheDocument()
  expect(screen.getByText(/latitude/i)).toHaveTextContent(
    `Latitude: ${fakePosition.coords.latitude}`,
  )
  expect(screen.getByText(/longitude/i)).toHaveTextContent(
    `Longitude: ${fakePosition.coords.longitude}`,
  )
})
```

