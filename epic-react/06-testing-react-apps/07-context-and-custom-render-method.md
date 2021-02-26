# Context and Custom Render Method

`react-testing-library` gives you a wrapper option where you can add all of your context providers:

```javascript
function Wrapper({children}) {
  return <ContextProvider>{children}</ContextProvider>
}

const {rerender} = render(<ComponentToTest />, {wrapper: Wrapper})

rerender(<ComponentToTest newProp={true} />)
```

This works better than just wrapping the component in the `render` because if we wanted to re-render and provide different props or something, we'd have to provide that ThemeProvider again. 



You can also create your own custom render method to do this automatically:

```javascript
import {render as rtlRender} from '@testing-library/react'
// "rtl" is short for "react testing library" not "right-to-left" 😅

function render(ui, options) {
  return rtlRender(ui, {wrapper: Wrapper, ...options})
}

// then in your tests, you don't need to worry about context at all:
const {rerender} = render(<ComponentToTest />)

rerender(<ComponentToTest newProp={true} />)
```



## Exercises

### Wrapper Component

Asset the styles of a button that's wrapped in a ThemeProvider:

```javascript
import * as React from 'react'
import {render, screen} from '@testing-library/react'
import {ThemeProvider} from '../../components/theme'
import EasyButton from '../../components/easy-button'

test('renders with the light styles for the light theme', () => {
  const Wrapper = ({children}) => {
    return <ThemeProvider initialTheme="light">{children}</ThemeProvider>
  }

  render(<EasyButton>Easy</EasyButton>, {wrapper: Wrapper})

  const button = screen.getByRole('button', {name: /easy/i})

  expect(button).toHaveStyle(`
    background-color: white;
    color: black;
  `)
})
```



### Dark Theme

```javascript
import * as React from 'react'
import {render, screen} from '@testing-library/react'
import {ThemeProvider} from '../../components/theme'
import EasyButton from '../../components/easy-button'

test('renders with the light styles for the light theme', () => {
  const Wrapper = ({children}) => {
    return <ThemeProvider initialTheme="light">{children}</ThemeProvider>
  }

  render(<EasyButton>Easy</EasyButton>, {wrapper: Wrapper})

  const button = screen.getByRole('button', {name: /easy/i})

  expect(button).toHaveStyle(`
    background-color: white;
    color: black;
  `)
})

test('renders with the dark styles for the dark theme', () => {
  const Wrapper = ({children}) => {
    return <ThemeProvider initialTheme="dark">{children}</ThemeProvider>
  }

  render(<EasyButton>Easy</EasyButton>, {wrapper: Wrapper})

  const button = screen.getByRole('button', {name: /easy/i})

  expect(button).toHaveStyle(`
    background-color: black;
    color: white;
  `)
})
```



### Custom Render Method

Create a custom render method:

```javascript
import * as React from 'react'
import {render, screen} from '@testing-library/react'
import {ThemeProvider} from '../../components/theme'
import EasyButton from '../../components/easy-button'

function renderWithProviders(ui, {theme = 'light', ...options} = {}) {
  const Wrapper = ({children}) => {
    return <ThemeProvider initialTheme={theme}>{children}</ThemeProvider>
  }
  return render(ui, {wrapper: Wrapper, ...options})
}

test('renders with the light styles for the light theme', () => {
  renderWithProviders(<EasyButton>Easy</EasyButton>)
  const button = screen.getByRole('button', {name: /easy/i})

  expect(button).toHaveStyle(`
    background-color: white;
    color: black;
  `)
})

test('renders with the dark styles for the dark theme', () => {
  renderWithProviders(<EasyButton>Easy</EasyButton>, {theme: 'dark'})

  const button = screen.getByRole('button', {name: /easy/i})

  expect(button).toHaveStyle(`
    background-color: black;
    color: white;
  `)
})
```



### App Test Utils

**Tip:** Don't ever import `@testing-library/react` into your test files. Instead, import it into a `test/test-utils.js` file, and import everything from there. Custom render methods can live there as well.

```javascript
import * as React from 'react'
import {render as rtlRender} from '@testing-library/react'
import {ThemeProvider} from 'components/theme'

function render(ui, {theme = 'light', ...options} = {}) {
  const Wrapper = ({children}) => (
    <ThemeProvider initialTheme={theme}>{children}</ThemeProvider>
  )
  return rtlRender(ui, {wrapper: Wrapper, ...options})
}

export * from '@testing-library/react'
// override React Testing Library's render with our own
export {render}

```

