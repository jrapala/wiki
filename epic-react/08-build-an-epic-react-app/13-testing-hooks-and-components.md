# Testing Hooks and Components

Most hooks and components should be covered by integration tests, instead of individualized component or hook tests. However, highly reusable or complex components/hooks can benefit from a suite of dedicated tests.

### Libraries for Testing Components

- [React Testing Library](https://testing-library.com/react)

- [`@testing-library/user-event`](https://github.com/testing-library/user-event) to help with our user interactions. 

- [`@testing-library/jest-dom`](https://github.com/testing-library/jest-dom)



### Libraries for Testing Hooks

- [`@testing-library/react-hooks`](https://github.com/testing-library/react-hooks-testing-library)

## Exercises

### Modal Compound Components

**Tip:** If you don't know what role you're looking for, try `screen.getByRole('blah')` to trigger an error that will show you all roles available.



```javascript
import React from 'react'
import {render, screen, within} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import {Modal, ModalContents, ModalOpenButton} from '../modal'

test('can be opened and closed', () => {
  const label = 'Modal Label'
  const title = 'Modal Title'
  const content = 'Modal content'

  // 1. Render components
  render(
    <Modal>
      <ModalOpenButton>
        <button>Open</button>
      </ModalOpenButton>
      <ModalContents aria-label={label} title={title}>
        <div>{content}</div>
      </ModalContents>
    </Modal>,
  )

  // 2. Open modal
  userEvent.click(screen.getByRole('button', {name: /open/i}))

  // 3. Find dialog
  const modal = screen.getByRole('dialog')
  // 4. Check dialog text
  // toHaveAttribute comes from '@testing-library/jest-dom' (imported in setupTests.js)
  expect(modal).toHaveAttribute('aria-label', label)
  // within() gives you a new set of queries within the modal (so, like screen, but scoped to the modal)
  const inModal = within(modal)
  // 5. Check dialog title
  expect(inModal.getByRole('heading', {name: title})).toBeInTheDocument()
  // 6. Check dialog content
  expect(inModal.getByText(content)).toBeInTheDocument()
  // 7. Close modal
  userEvent.click(inModal.getByRole('button', {name: /close/i}))
  // 8. Check if dialog has closed
  expect(screen.queryByRole('dialog')).not.toBeInTheDocument()
})

```



### Set Up useAsync Test with renderHook

This example is a set up for the test:

```javascript
test('calling run with a promise which resolves', async () => {
	// You must use a hook inside a component. renderHook keeps you from having to create 
	// your own TestComponent
	const {result} = renderHook(() => useAsync())
	// Copied and rearranged from a console log
	// Any functions are expected to just be a function
  expect(result.current).toEqual({
    status: 'idle',
    data: null,
    error: null,
    
    isIdle: true,
    isLoading: false,
    isError: false,
    isSuccess: false,
    
    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })
})
```



### Wrap an act around an async Function

Test the `run()` function (you must use `act` here):

```javascript
test('calling run with a promise which resolves', async () => {
  const promise = new Promise(() => {})

  const {result} = renderHook(() => useAsync())

  expect(result.current).toEqual({
    status: 'idle',
    data: null,
    error: null,

    isIdle: true,
    isLoading: false,
    isError: false,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })

  // run must take a promise - so lets pass in a generic promise
  act(() => {
    result.current.run(promise)
  })

	// check for changes after run is called
  expect(result.current).toEqual({
    status: 'pending',
    data: null,
    error: null,

    isIdle: false,
    isLoading: true,
    isError: false,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })
})
```



### Add an async act to Resolve a Promise

```javascript
test('calling run with a promise which resolves', async () => {
  // 1. Keep track of resolve
  let resolve
  const promise = new Promise((res, rej) => {
    resolve = res
  })

  const {result} = renderHook(() => useAsync())

  expect(result.current).toEqual({
    status: 'idle',
    data: null,
    error: null,

    isIdle: true,
    isLoading: false,
    isError: false,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })

  let p
  act(() => {
    // 2. Keep track of promise returned from run
    p = result.current.run(promise)
  })

  expect(result.current).toEqual({
    status: 'pending',
    data: null,
    error: null,

    isIdle: false,
    isLoading: true,
    isError: false,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })

  // 3. Use Symbol to ensure the same exact object is resolved
  const resolvedValue = Symbol('resolved value')
  await act(async () => {
    // 4. Resolve
    resolve(resolvedValue)
    await p
  })

  // 5. Check for changes
  expect(result.current).toEqual({
    status: 'resolved',
    data: resolvedValue,
    error: null,

    isIdle: false,
    isLoading: false,
    isError: false,
    isSuccess: true,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })
})
```



**Bonus:** Extract generic promise:

```javascript
function deferred() {
  let resolve, reject
  const promise = new Promise((res, rej) => {
    resolve = res
    reject = rej
  })
  return {promise, resolve, reject}
}
```

```javascript
const {promise, resolve} = deferred()
```



### Reset React State in a Test

```javascript
	// 1. Reset to original state
  act(() => {
    result.current.reset()
  })

	// 2. Check post-reset
  expect(result.current).toEqual({
    status: 'idle',
    data: null,
    error: null,

    isIdle: true,
    isLoading: false,
    isError: false,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })
```



### Call Run with a Promise That Rejected

```javascript
test('calling run with a promise which rejects', async () => {
  // 1. Grab reject
  const {promise, reject} = deferred()
  const {result} = renderHook(() => useAsync())

  expect(result.current).toEqual({
    status: 'idle',
    data: null,
    error: null,

    isIdle: true,
    isLoading: false,
    isError: false,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })

  let p
  act(() => {
    p = result.current.run(promise)
  })

  expect(result.current).toEqual({
    status: 'pending',
    data: null,
    error: null,

    isIdle: false,
    isLoading: true,
    isError: false,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })

  // 2. Update to reject
  const rejectedValue = Symbol('rejected value')
  await act(async () => {
    reject(rejectedValue)
    await p.catch(() => {
      // ignore error
    })
  })

  // 3. Update assertion
  
  expect(result.current).toEqual({
    status: 'rejected',
    data: null,
    error: rejectedValue,

    isIdle: false,
    isLoading: false,
    isError: true,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })

  act(() => {
    result.current.reset()
  })

  expect(result.current).toEqual({
    status: 'idle',
    data: null,
    error: null,

    isIdle: true,
    isLoading: false,
    isError: false,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })
})
```



### Can Specify an Initial State

```javascript
test('can specify an initial state', async () => {
  const mockData = Symbol('resolved value')
  const customInitialState = {status: 'resolved', data: mockData}
  const {result} = renderHook(() => useAsync(customInitialState))

  expect(result.current).toEqual({
    status: 'resolved',
    data: mockData,
    error: null,

    isIdle: false,
    isLoading: false,
    isError: false,
    isSuccess: true,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })
})
```



### Can Set the Data

```javascript
test('can set the data', async () => {
  const mockData = Symbol('resolved value')
  const {result} = renderHook(() => useAsync())

  act(() => {
    result.current.setData(mockData)
  })

  expect(result.current).toEqual({
    status: 'resolved',
    data: mockData,
    error: null,

    isIdle: false,
    isLoading: false,
    isError: false,
    isSuccess: true,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })
})
```



### Can Set the Error

```javascript
test('can set the error', async () => {
  const mockError = Symbol('rejected value')
  const {result} = renderHook(() => useAsync())

  act(() => {
    result.current.setError(mockError)
  })

  expect(result.current).toEqual({
    status: 'rejected',
    data: null,
    error: mockError,

    isIdle: false,
    isLoading: false,
    isError: true,
    isSuccess: false,

    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
  })
})
```



### No State Updates if Unmounted

```javascript
beforeEach(() => {
  jest.spyOn(console, 'error')
})

// even if a test fails, we're still restored
afterEach(() => {
  console.error.mockRestore()
})

test('No state updates happen if the component is unmounted while pending', async () => {
  const {promise, resolve} = deferred()
  const {result, unmount} = renderHook(() => useAsync())

  let p
  act(() => {
    p = result.current.run(promise)
  })

  unmount()

  await act(async () => {
    resolve()
    await p
  })

  // ensure that console.error is not called (React will call console.error if updates happen when unmounted)
  expect(console.error).not.toHaveBeenCalled()
})
```



### Call run without Promise Errors

```javascript
test('calling "run" without a promise results in an early error', async () => {
	const {result} = renderHook(() => useAsync())
	// expect calls the function inside a try/catch
  expect(() => result.current.run()).toThrowErrorMatchingInlineSnapshot(
    `"The argument passed to useAsync().run must be a promise. Maybe a function that's passed isn't returning anything?"`,
  )
})
```



### AHA Testing

[AHA Programming](https://kentcdodds.com/blog/aha-programming) stands for "Avoid Hasty Abstractions" and it suggests that you should "prefer duplication over the wrong abstraction" and "optimize for change first."

[When applied to testing](https://kentcdodds.com/blog/aha-testing), it can seriously improve the way the tests can be understood. You can communicate what matters through the abstractions you write.

The "customer" of the test is developers, so you want to make it as easy for them to understand as possible so they can work out what's happening when a test fails.



Typically you can clean up tests via helper functions or "Test Object Factory" functions. So we could create a function like this:

```javascript
function getAsyncState(overrides) {
  return {
		data: null,
		isIdle: true,
		isLoading: false,
		isError: false,
		isSuccess: false,
    error: null,
    status: 'idle',
    run: expect.any(Function),
    reset: expect.any(Function),
    setData: expect.any(Function),
    setError: expect.any(Function),
    ...overrides,
  }
}
```



**Exercise:**

```javascript
import {renderHook, act} from '@testing-library/react-hooks'
import {useAsync} from '../hooks'

beforeEach(() => {
  jest.spyOn(console, 'error')
})

afterEach(() => {
  console.error.mockRestore()
})

function deferred() {
  let resolve, reject
  const promise = new Promise((res, rej) => {
    resolve = res
    reject = rej
  })
  return {promise, resolve, reject}
}

// Create various states
const defaultState = {
  status: 'idle',
  data: null,
  error: null,

  isIdle: true,
  isLoading: false,
  isError: false,
  isSuccess: false,

  run: expect.any(Function),
  reset: expect.any(Function),
  setData: expect.any(Function),
  setError: expect.any(Function),
}

const pendingState = {
  ...defaultState,
  status: 'pending',
  isIdle: false,
  isLoading: true,
}

const resolvedState = {
  ...defaultState,
  status: 'resolved',
  isIdle: false,
  isSuccess: true,
}

const rejectedState = {
  ...defaultState,
  status: 'rejected',
  isIdle: false,
  isError: true,
}

test('calling run with a promise which resolves', async () => {
  const {promise, resolve} = deferred()
  const {result} = renderHook(() => useAsync())

  expect(result.current).toEqual(defaultState)

  let p
  act(() => {
    p = result.current.run(promise)
  })

  expect(result.current).toEqual(pendingState)

  const resolvedValue = Symbol('resolved value')
  await act(async () => {
    resolve(resolvedValue)
    await p
  })

  expect(result.current).toEqual({
    ...resolvedState,
    data: resolvedValue,
  })

  act(() => {
    result.current.reset()
  })

  expect(result.current).toEqual(defaultState)
})

test('calling run with a promise which rejects', async () => {
  const {promise, reject} = deferred()
  const {result} = renderHook(() => useAsync())

  expect(result.current).toEqual(defaultState)

  let p
  act(() => {
    p = result.current.run(promise)
  })

  expect(result.current).toEqual(pendingState)

  const rejectedValue = Symbol('rejected value')
  await act(async () => {
    reject(rejectedValue)
    await p.catch(() => {
      // ignore error
    })
  })

  expect(result.current).toEqual({
    ...rejectedState,
    error: rejectedValue,
  })

  act(() => {
    result.current.reset()
  })

  expect(result.current).toEqual(defaultState)
})

test('can specify an initial state', async () => {
  const mockData = Symbol('resolved value')
  const customInitialState = {status: 'resolved', data: mockData}
  const {result} = renderHook(() => useAsync(customInitialState))

  expect(result.current).toEqual({
    ...resolvedState,
    data: mockData,
  })
})

test('can set the data', async () => {
  const mockData = Symbol('resolved value')
  const {result} = renderHook(() => useAsync())

  act(() => {
    result.current.setData(mockData)
  })

  expect(result.current).toEqual({
    ...resolvedState,
    data: mockData,
  })
})

test('can set the error', async () => {
  const mockError = Symbol('rejected value')
  const {result} = renderHook(() => useAsync())

  act(() => {
    result.current.setError(mockError)
  })

  expect(result.current).toEqual({
    ...rejectedState,
    error: mockError,
  })
})

test('No state updates happen if the component is unmounted while pending', async () => {
  const {promise, resolve} = deferred()
  const {result, unmount} = renderHook(() => useAsync())

  let p
  act(() => {
    p = result.current.run(promise)
  })

  unmount()

  await act(async () => {
    resolve()
    await p
  })

  expect(console.error).not.toHaveBeenCalled()
})

test('calling "run" without a promise results in an early error', async () => {
  const {result} = renderHook(() => useAsync())
  expect(() => result.current.run()).toThrowErrorMatchingInlineSnapshot(
    `"The argument passed to useAsync().run must be a promise. Maybe a function that's passed isn't returning anything?"`,
  )
})

```

