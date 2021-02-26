# Avoid Implementation Details

Implementation details are the things that users don't care about. Function vs. class components. Libraries used. Etc.. If you're changing tests after refactors, you're probably testing for implementation details.

**Implementation details** is a term referring to how an abstraction accomplishes a certain outcome. Thanks to the expressiveness of code, you can typically accomplish the same outcome using completely different implementation details.



Example:

```javascript
const multiply = (a, b) => a * b
```

vs

```javascript
function multiply(a, b) {
  let total = 0
  for (let i = 0; i < b; i++) {
    total += a
  }
  return total
}
```

The implementation of your abstractions does not matter to the users of your abstraction and if you want to have confidence that it continues to work through refactors then **neither should your tests.**



## Exercises

### Refactor to Ignore Implementation Details

Use `getByRole` and `getByText` to get elements instead:

```javascript
import * as React from 'react'
import {render, screen, fireEvent} from '@testing-library/react'
import Counter from '../../components/counter'

test('counter increments and decrements when the buttons are clicked', () => {
  // We still need to render the component
  render(<Counter />)

  // Let's not make assumptions about button positions on the screen
  // Let's get it by role instead, with a specific name, using regex to ignore case
  const decrement = screen.getByRole('button', {name: /decrement/i})
  const increment = screen.getByRole('button', {name: /increment/i})
  // Same applies to getting the message
  const message = screen.getByText(/current count/i)

  expect(message).toHaveTextContent('Current count: 0')
  fireEvent.click(increment)
  expect(message).toHaveTextContent('Current count: 1')
  fireEvent.click(decrement)
  expect(message).toHaveTextContent('Current count: 0')
})

```

**Tips:**

- [Testing Playground](https://testing-playground.com/) (there's a Chrome extension as well!)
- Read up on `screen` here: https://testing-library.com/docs/dom-testing-library/api-queries#screen



### Use userEvent

The user is triggering more events than just a click. If we want to be truly implementation detail free, then we should probably fire all those same events too. You can now swamp on onClick for a onMouseOver without your tests breaking.

Testing Library has us covered with `@testing-library/user-event`. This may one-day be baked directly into `@testing-library/dom`, but for now it's in a separate package.

Swap out `fireEvent` for `userEvent`:

```javascript
import * as React from 'react'
import {render, screen} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import Counter from '../../components/counter'

test('counter increments and decrements when the buttons are clicked', () => {
  render(<Counter />)

  const decrement = screen.getByRole('button', {name: /decrement/i})
  const increment = screen.getByRole('button', {name: /increment/i})
  const message = screen.getByText(/current count/i)

  expect(message).toHaveTextContent('Current count: 0')
	// Use userEvent
  userEvent.click(increment)
  expect(message).toHaveTextContent('Current count: 1')
  userEvent.click(decrement)
  expect(message).toHaveTextContent('Current count: 0')
})

```

