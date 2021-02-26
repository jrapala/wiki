# Simple Test with React Testing Library

[React Testing Library](https://testing-library.com/react) is a DOM implementation of [DOM Testing Library](https://testing-library.com/)

There's also [React Native Testing Library](https://testing-library.com/react-native)

The library is **not** a test runner. You still need something like Jest to run the tests. It simply replaces something like Enzyme.



Simple Example:

```javascript
import {render, fireEvent, screen} from '@testing-library/react'

test('it works', () => {
  const {container} = render(<Example />)
  // container is the div that your component has been mounted onto.

  const input = container.querySelector('input')
  fireEvent.mouseEnter(input) // fires a mouseEnter event on the input

  screen.debug() // logs the current state of the DOM (with syntax highlighting!)
})
```

No need to clean up. There is an [auto-cleanup](https://testing-library.com/docs/react-testing-library/api#cleanup) feature.

## Exercises

### Replace ReactDOM Testing Code with React Testing Library

Refactored:

```javascript
import * as React from 'react'
import {render, fireEvent} from '@testing-library/react'
import Counter from '../../components/counter'

test('counter increments and decrements when the buttons are clicked', () => {
  // No need to create a div. Just render the component.
  const {container} = render(<Counter />)

  const [decrement, increment] = container.querySelectorAll('button')
  const message = container.firstChild.querySelector('div')

  expect(message.textContent).toBe('Current count: 0')

  // No need to configure events. just use fireEvent.
  fireEvent.click(increment)
  expect(message.textContent).toBe('Current count: 1')

  fireEvent.click(decrement)
  expect(message.textContent).toBe('Current count: 0')
})
```



### Assertions

Use `@testing-library/jest-dom` for assertions. These are more specific than assertions that come with Jest, and they give better error messages.

To use:

```javascript
import '@testing-library/jest-dom'
```

OR

```javascript
// jest.config.js

module.exports = {
	...
	setupFilesAfterEnv: {
		'@testing-library/jest-dom/extend-expect'
	}
}
```

OR (in CRA):

```javascript
// src/setupTests.js
import '@testing-library/jest-dom/extend-expect'
```



```javascript
  fireEvent.click(increment)
  expect(message).toHaveTextContent('Current count: 1')
```







### 1. 💯 use @testing-library/jest-dom

Testing Library also has a suite of assertions that can be installed with Jest. They're already added to this project, so you can switch from Jest's built-in assertions to more specific assertions which will give you better error messages. So go ahead and swap the `expect(message.textContent).toBe(...)` assertions with `toHaveTextContent` from [`@testing-library/jest-dom`](http://testing-library.com/jest-dom).