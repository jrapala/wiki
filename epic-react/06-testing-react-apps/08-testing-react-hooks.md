# Testing Custom Hooks

Honestly, this is something you probably shouldn't do. Just test the components that use them instead. The hooks are inside the components, and you should really only be testing components.

However, if necessary, often, the easiest and most straightforward way to test a custom hook is to create a component that uses it and then test that component instead.



## Exercises

### Test Functionality of Custom Hook

Make a test component that uses the hook in the typical way:

```javascript
import * as React from 'react'
import {render, screen} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import useCounter from '../../components/use-counter'

function UseCounterHookExample() {
  const {count, increment, decrement} = useCounter()
  return (
    <div>
      <div>Current count: {count}</div>
      <button onClick={decrement}>Decrement</button>
      <button onClick={increment}>Increment</button>
    </div>
  )
}

test('exposes the count and increment/decrement functions', () => {
  render(<UseCounterHookExample />)
  const increment = screen.getByRole('button', {name: /increment/i})
  const decrement = screen.getByRole('button', {name: /decrement/i})
  const message = screen.getByText(/current count/i)

  expect(message).toHaveTextContent('Current count: 0')
  userEvent.click(increment)
  expect(message).toHaveTextContent('Current count: 1')
  userEvent.click(decrement)
  expect(message).toHaveTextContent('Current count: 0')
})
```



### Fake Component

Sometimes it's hard to write a test component without making a pretty complicated "TestComponent." For those situations, you can try something like this:

```javascript
let result
function TestComponent(props) {
  result = useCustomHook(props)
  return null
}

// interact with and assert on results here
```



Doing this in the exercise:

```javascript
import * as React from 'react'
import {render, screen, act} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import useCounter from '../../components/use-counter'

test('exposes the count and increment/decrement functions', () => {
  // create an object that will have all the methods returned by the hook
  let result
  function TestComponent() {
    result = useCounter()
    return null // needed to make this a valid component
  }
  render(<TestComponent />)
  expect(result.count).toBe(0)
  // act flushes out side effects, after the callback is run
  // it get the component into a stable state, not an
  // intermediary state whe side effects haven't been run yet
  act(() => result.increment())
  expect(result.count).toBe(1)
  act(() => result.decrement())
  expect(result.count).toBe(0)
})
```





### Setup Function

Add tests titled:

1. allows customization of the initial count
2. allows customization of the step

```javascript
test('exposes the count and increment/decrement functions', () => {
  let result

  function TestComponent() {
    result = useCounter()
    return null
  }

  render(<TestComponent />)

  expect(result.count).toBe(0)
  act(() => result.increment())
  expect(result.count).toBe(1)
  act(() => result.decrement())
  expect(result.count).toBe(0)
})

test('allows customization of the initial count', () => {
  let result

  function TestComponent() {
    result = useCounter({initialCount: 3})
    return null
  }

  render(<TestComponent />)

  expect(result.count).toBe(3)
  act(() => result.increment())
  expect(result.count).toBe(4)
  act(() => result.decrement())
  expect(result.count).toBe(3)
})

test('allows customization of the step', () => {
  let result

  function TestComponent() {
    result = useCounter({step: 2})
    return null
  }

  render(<TestComponent />)

  expect(result.count).toBe(0)
  act(() => result.increment())
  expect(result.count).toBe(2)
  act(() => result.decrement())
  expect(result.count).toBe(0)
})
```



Remove duplication:

```javascript
import * as React from 'react'
import {render, screen, act} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import useCounter from '../../components/use-counter'

function setup({initialProps} = {}) {
  // We need to pass the same object into tests
  // If we just assigned useCount to result, result would change after the component updates
  const result = {}
  function TestComponent() {
    result.current = useCounter(initialProps)
    return null
  }
  render(<TestComponent />)
  return result
}

test('exposes the count and increment/decrement functions', () => {
  const result = setup()
  expect(result.current.count).toBe(0)
  act(() => result.current.increment())
  expect(result.current.count).toBe(1)
  act(() => result.current.decrement())
  expect(result.current.count).toBe(0)
})

test('allows customization of the initial count', () => {
  const result = setup({initialProps: {initialCount: 3}})
  expect(result.current.count).toBe(3)
  act(() => result.current.increment())
  expect(result.current.count).toBe(4)
  act(() => result.current.decrement())
  expect(result.current.count).toBe(3)
})

test('allows customization of the step', () => {
  const result = setup({initialProps: {step: 2}})

  expect(result.current.count).toBe(0)
  act(() => result.current.increment())
  expect(result.current.count).toBe(2)
  act(() => result.current.decrement())
  expect(result.current.count).toBe(0)
})
```



### Using React-Hooks Testing Library

[`@testing-library/react-hooks`](https://github.com/testing-library/react-hooks-testing-library) has a function similar to `setup` called  `renderHook`:

```javascript
import {renderHook, act} from '@testing-library/react-hooks'
import userEvent from '@testing-library/user-event'
import useCounter from '../../components/use-counter'

test('exposes the count and increment/decrement functions', () => {
  const {result} = renderHook(useCounter)
  expect(result.current.count).toBe(0)
  act(() => result.current.increment())
  expect(result.current.count).toBe(1)
  act(() => result.current.decrement())
  expect(result.current.count).toBe(0)
})

test('allows customization of the initial count', () => {
  const {result} = renderHook(useCounter, {initialProps: {initialCount: 3}})
  expect(result.current.count).toBe(3)
  act(() => result.current.increment())
  expect(result.current.count).toBe(4)
  act(() => result.current.decrement())
  expect(result.current.count).toBe(3)
})

test('allows customization of the step', () => {
  const {result} = renderHook(useCounter, {initialProps: {step: 2}})
  expect(result.current.count).toBe(0)
  act(() => result.current.increment())
  expect(result.current.count).toBe(2)
  act(() => result.current.decrement())
  expect(result.current.count).toBe(0)
})
```



There is a rerender function available:

```javascript
test('the step can be changed', () => {
  const {result, rerender} = renderHook(useCounter, {initialProps: {step: 3}})
  expect(result.current.count).toBe(0)
  act(() => result.current.increment())
  expect(result.current.count).toBe(3)
  rerender({step: 2})
  act(() => result.current.decrement())
  expect(result.current.count).toBe(1)
})
```



