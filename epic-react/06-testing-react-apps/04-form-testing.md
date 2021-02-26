# Form Testing

When testing forms, you need to ensure that the user can find inputs in the form, fill in their information, and validate that when they submit the form the submitted data is correct.

**Tip:** After rendering a component, use `screen.debug()` to see the output.



## Exercises

### Testing a Login Form

Test a log in form with a username and password:

```javascript
import * as React from 'react'
import {getByLabelText, render, screen} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import Login from '../../components/login'

test('submitting the form calls onSubmit with username and password', () => {
  let submittedData
  const handleSubmit = data => (submittedData = data)

  // Render login component
  render(<Login onSubmit={handleSubmit} />)

  // Create some values
  const username = 'chucknorris'
  const password = 'i need no password'

  // Type into the form fields
  userEvent.type(screen.getByLabelText(/username/i), username)
  userEvent.type(screen.getByLabelText(/password/i), password)

  // Submit the form
  userEvent.click(screen.getByRole('button', {name: /submit/i}))

  // Assert
  expect(submittedData).toEqual({
    username,
    password,
  })
})

```



### Use a Jest Mock Function

A mock function means that it doesn't matter what a function does, it just matters what it is called with.

Using a Jest mock function instead gives you some more things you can test for:

```javascript
import * as React from 'react'
import {getByLabelText, render, screen} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import Login from '../../components/login'

test('submitting the form calls onSubmit with username and password', () => {
  const handleSubmit = jest.fn()

  render(<Login onSubmit={handleSubmit} />)

  const username = 'chucknorris'
  const password = 'i need no password'

  userEvent.type(screen.getByLabelText(/username/i), username)
  userEvent.type(screen.getByLabelText(/password/i), password)

  userEvent.click(screen.getByRole('button', {name: /submit/i}))

  expect(handleSubmit).toHaveBeenCalledWith({
    username,
    password,
  })
  // Check that the form wasn't called twice
  expect(handleSubmit).toHaveBeenCalledTimes(1)
})
```



### Generate Test Data

Actual test data values are irrelevant. There's a library called `faker` that lets you generate random username and passwords, to communicate that they are irrelevant.

```javascript
// form testing
// http://localhost:3000/login

import * as React from 'react'
import {getByLabelText, render, screen} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import faker from 'faker'
import Login from '../../components/login'

// May as well create a reusable function
function buildLoginForm() {
  return {
    username: faker.internet.userName(),
    password: faker.internet.password(),
  }
}

test('submitting the form calls onSubmit with username and password', () => {
  const handleSubmit = jest.fn()

  render(<Login onSubmit={handleSubmit} />)

  const {username, password} = buildLoginForm()

  userEvent.type(screen.getByLabelText(/username/i), username)
  userEvent.type(screen.getByLabelText(/password/i), password)

  userEvent.click(screen.getByRole('button', {name: /submit/i}))

  expect(handleSubmit).toHaveBeenCalledWith({
    username,
    password,
  })
  // Check that the form wasn't called twice
  expect(handleSubmit).toHaveBeenCalledTimes(1)
})

/*
eslint
  no-unused-vars: "off",
*/

```



### Allow for Overrides

Sometimes you DO need to test specifc data. To accept overrides:

```javascript
import * as React from 'react'
import {getByLabelText, render, screen} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import faker from 'faker'
import Login from '../../components/login'

function buildLoginForm(overrides) {
  return {
    username: faker.internet.userName(),
    password: faker.internet.password(),
    ...overrides,
  }
}

test('submitting the form calls onSubmit with username and password', () => {
  const handleSubmit = jest.fn()

  render(<Login onSubmit={handleSubmit} />)

  // Use override for the mock object factory
  const {username, password} = buildLoginForm({username: 'chucknorris'})

  userEvent.type(screen.getByLabelText(/username/i), username)
  userEvent.type(screen.getByLabelText(/password/i), password)

  userEvent.click(screen.getByRole('button', {name: /submit/i}))

  expect(handleSubmit).toHaveBeenCalledWith({
    username,
    password,
  })
  // Check that the form wasn't called twice
  expect(handleSubmit).toHaveBeenCalledTimes(1)
})

```



### Use Test Data Bot

The library  [`@jackfranklin/test-data-bot`](https://www.npmjs.com/package/@jackfranklin/test-data-bot) provides a few nice testing utilities. Swapping the  `buildLoginForm`:

```javascript

```



