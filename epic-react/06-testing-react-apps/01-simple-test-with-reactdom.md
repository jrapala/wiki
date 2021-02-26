# Simple Test with ReactDOM

Testing is 100% about confidence. Can you ship code after adding new features?

Remember to **never test implementation details**. Implementation details are things which users of your code will not typically use, see, or even know about.

Tests which test implementation details can give you a false negative when you refactor your code. This leads to brittle and frustrating tests that seem to break anytime you so much as look at the code.

React Testing Library makes it very difficult to include implementation details into tests.

## Exercises

### Render Counter Component

Test for:

- Counter starting with "Current count: 0"
- Clicking Increment incremenets
- Clicking Decrement decrements

Steps:

	1. Create a DOM node
 	2. Add it to the body
 	3. Render the component to that DOM node
 	4. Clean up the DOM node once test is finished

```javascript
import * as React from 'react'
import ReactDOM from 'react-dom'
import Counter from '../../components/counter'

beforeEach(() => {
  // Clean up
  document.body.innerHTML = ""
})

test('counter increments and decrements when the buttons are clicked', () => {
  // Create a div
  const div = document.createElement('div')
  // Append the div
  document.body.append(div)

  // Render the counter in that div
  ReactDOM.render(<Counter />, div)

  // Get buttons
  const [decrement, increment] = div.querySelectorAll('button')
  // Get message
  const message = div.firstChild.querySelector('div')

  expect(message.textContent).toBe('Current count: 0')
  increment.click()
  expect(message.textContent).toBe('Current count: 1')
  decrement.click()
  expect(message.textContent).toBe('Current count: 0')
})
```



### Use dispatchEvent

Use `.dispatchEvent` instead of a `.click()` to fire an event that doesn't have a dedicated method (like `mouseover`).

Make sure your event config sets `bubbles: true`. React uses event delegation. Bubbling is required for event delegation to work.

Example:

```javascript
new MouseEvent('click', {
  bubbles: true,
  cancelable: true,
  button: 0,
})
```

Exercise:

```javascript
  const incrementClickEvent = new MouseEvent('click', {
    bubbles: true,  // need event delegation
    cancelable: true, // this is the default
    button: 0,  // left button click
  })
  increment.dispatchEvent(incrementClickEvent)
```



