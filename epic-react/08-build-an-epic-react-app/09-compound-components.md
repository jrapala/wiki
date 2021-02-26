# Compound Components

This:

```jsx
<Modal>
  <ModalOpenButton>
    <button>Open Modal</button>
  </ModalOpenButton>
  <ModalContents aria-label="Modal label (for screen readers)">
    <ModalDismissButton>
      <button>Close Modal</button>
    </ModalDismissButton>
    <h3>Modal title</h3>
    <div>Some great contents of the modal</div>
  </ModalContents>
</Modal>
```

is more reusable than this:

```jsx
<LoginFormModal
  onSubmit={handleSubmit}
  modalTitle="Modal title"
  modalLabelText="Modal label (for screen readers)"
  submitButton={<button>Submit form</button>}
  openButton={<button>Open Modal</button>}
/>
```







## Exercise

### Modal Compound Components

Turn `LoginFormModal` into something more reusable.

Old File:

```jsx
// unauthenticated-app.js
/** @jsx jsx */
import {jsx} from '@emotion/core'

import * as React from 'react'
import VisuallyHidden from '@reach/visually-hidden'
import {
  Input,
  CircleButton,
  Button,
  Spinner,
  FormGroup,
  ErrorMessage,
  Dialog,
} from './components/lib'
import {Logo} from './components/logo'
import {useAuth} from './context/auth-context'
import {useAsync} from './utils/hooks'

function LoginForm({onSubmit, submitButton}) {
  const {isLoading, isError, error, run} = useAsync()
  function handleSubmit(event) {
    event.preventDefault()
    const {username, password} = event.target.elements

    run(
      onSubmit({
        username: username.value,
        password: password.value,
      }),
    )
  }

  return (
    <form
      onSubmit={handleSubmit}
      css={{
        display: 'flex',
        flexDirection: 'column',
        alignItems: 'stretch',
        '> div': {
          margin: '10px auto',
          width: '100%',
          maxWidth: '300px',
        },
      }}
    >
      <FormGroup>
        <label htmlFor="username">Username</label>
        <Input id="username" />
      </FormGroup>
      <FormGroup>
        <label htmlFor="password">Password</label>
        <Input id="password" type="password" />
      </FormGroup>
      <div>
        {React.cloneElement(
          submitButton,
          {type: 'submit'},
          ...(Array.isArray(submitButton.props.children)
            ? submitButton.props.children
            : [submitButton.props.children]),
          isLoading ? <Spinner css={{marginLeft: 5}} /> : null,
        )}
      </div>
      {isError ? <ErrorMessage error={error} /> : null}
    </form>
  )
}

function LoginFormModal({
  onSubmit,
  modalTitleText,
  modalLabelText,
  submitButton,
  openButton,
}) {
  const [isOpen, setIsOpen] = React.useState(false)

  return (
    <React.Fragment>
      {React.cloneElement(openButton, {onClick: () => setIsOpen(true)})}
      <Dialog
        aria-label={modalLabelText}
        isOpen={isOpen}
        onDismiss={() => setIsOpen(false)}
      >
        <div css={{display: 'flex', justifyContent: 'flex-end'}}>
          <CircleButton onClick={() => setIsOpen(false)}>
            <VisuallyHidden>Close</VisuallyHidden>
            <span aria-hidden>×</span>
          </CircleButton>
        </div>
        <h3 css={{textAlign: 'center', fontSize: '2em'}}>{modalTitleText}</h3>
        <LoginForm onSubmit={onSubmit} submitButton={submitButton} />
      </Dialog>
    </React.Fragment>
  )
}

function UnauthenticatedApp() {
  const {login, register} = useAuth()
  return (
    <div
      css={{
        display: 'flex',
        flexDirection: 'column',
        alignItems: 'center',
        justifyContent: 'center',
        width: '100%',
        height: '100vh',
      }}
    >
      <Logo width="80" height="80" />
      <h1>Bookshelf</h1>
      <div
        css={{
          display: 'grid',
          gridTemplateColumns: 'repeat(2, minmax(0, 1fr))',
          gridGap: '0.75rem',
        }}
      >
        <LoginFormModal
          onSubmit={login}
          modalTitleText="Login"
          modalLabelText="Login form"
          submitButton={<Button variant="primary">Login</Button>}
          openButton={<Button variant="primary">Login</Button>}
        />
        <LoginFormModal
          onSubmit={register}
          modalTitleText="Register"
          modalLabelText="Registration form"
          submitButton={<Button variant="secondary">Register</Button>}
          openButton={<Button variant="secondary">Register</Button>}
        />
      </div>
    </div>
  )
}

export {UnauthenticatedApp}

```



New file:

```jsx
// unauthenticated-app.js
/** @jsx jsx */
import {jsx} from '@emotion/core'

import * as React from 'react'
import VisuallyHidden from '@reach/visually-hidden'
import {
  Input,
  CircleButton,
  Button,
  Spinner,
  FormGroup,
  ErrorMessage,
} from './components/lib'
import {
  Modal,
  ModalOpenButton,
  ModalContents,
  ModalDismissButton,
} from './components/modal'
import {Logo} from './components/logo'
import {useAuth} from './context/auth-context'
import {useAsync} from './utils/hooks'

function LoginForm({onSubmit, submitButton}) {
  const {isLoading, isError, error, run} = useAsync()
  function handleSubmit(event) {
    event.preventDefault()
    const {username, password} = event.target.elements

    run(
      onSubmit({
        username: username.value,
        password: password.value,
      }),
    )
  }

  return (
    <form
      onSubmit={handleSubmit}
      css={{
        display: 'flex',
        flexDirection: 'column',
        alignItems: 'stretch',
        '> div': {
          margin: '10px auto',
          width: '100%',
          maxWidth: '300px',
        },
      }}
    >
      <FormGroup>
        <label htmlFor="username">Username</label>
        <Input id="username" />
      </FormGroup>
      <FormGroup>
        <label htmlFor="password">Password</label>
        <Input id="password" type="password" />
      </FormGroup>
      <div>
        {React.cloneElement(
          submitButton,
          {type: 'submit'},
          ...(Array.isArray(submitButton.props.children)
            ? submitButton.props.children
            : [submitButton.props.children]),
          isLoading ? <Spinner css={{marginLeft: 5}} /> : null,
        )}
      </div>
      {isError ? <ErrorMessage error={error} /> : null}
    </form>
  )
}

const circleDismissButton = (
  <div css={{display: 'flex', justifyContent: 'flex-end'}}>
    <ModalDismissButton>
      <CircleButton>
        <VisuallyHidden>Close</VisuallyHidden>
        <span aria-hidden>×</span>
      </CircleButton>
    </ModalDismissButton>
  </div>
)

function UnauthenticatedApp() {
  const {login, register} = useAuth()
  return (
    <div
      css={{
        display: 'flex',
        flexDirection: 'column',
        alignItems: 'center',
        justifyContent: 'center',
        width: '100%',
        height: '100vh',
      }}
    >
      <Logo width="80" height="80" />
      <h1>Bookshelf</h1>
      <div
        css={{
          display: 'grid',
          gridTemplateColumns: 'repeat(2, minmax(0, 1fr))',
          gridGap: '0.75rem',
        }}
      >
        <Modal>
          <ModalOpenButton>
            <Button variant="primary">Login</Button>
          </ModalOpenButton>
          <ModalContents aria-label="Login form">
            {circleDismissButton}
            <h3 css={{textAlign: 'center', fontSize: '2em'}}>Login</h3>
            <LoginForm
              onSubmit={login}
              submitButton={<Button variant="primary">Login</Button>}
            />
          </ModalContents>
        </Modal>

        <Modal>
          <ModalOpenButton>
            <Button variant="secondary">Register</Button>
          </ModalOpenButton>
          <ModalContents aria-label="Registration form">
            {circleDismissButton}
            <h3 css={{textAlign: 'center', fontSize: '2em'}}>Register</h3>
            <LoginForm
              onSubmit={register}
              submitButton={<Button variant="secondary">Register</Button>}
            />
          </ModalContents>
        </Modal>
      </div>
    </div>
  )
}

export {UnauthenticatedApp}

```

```jsx
// modal.js
import React from 'react'
import {Dialog} from './lib'

// 1. Create context to share state between components
const ModalContext = React.createContext()

// 2. Create Modal to provide context
function Modal(props) {
  const [isOpen, setIsOpen] = React.useState(false)

  return <ModalContext.Provider value={[isOpen, setIsOpen]} {...props} />
}

// 3. Create ModalDismissButton that consumes context
function ModalDismissButton({children: child}) {
  const [, setIsOpen] = React.useContext(ModalContext)
  return React.cloneElement(child, {
    onClick: () => setIsOpen(false),
  })
}

// 4. Create ModalOpenButton that consumes context
function ModalOpenButton({children: child}) {
  const [, setIsOpen] = React.useContext(ModalContext)
  return React.cloneElement(child, {
    onClick: () => setIsOpen(true),
  })
}

// 5. Create ModalContents that consumes context
function ModalContents(props) {
  const [isOpen, setIsOpen] = React.useContext(ModalContext)

  return (
    <Dialog isOpen={isOpen} onDismiss={() => setIsOpen(false)} {...props} />
  )
}

export {Modal, ModalOpenButton, ModalContents, ModalDismissButton}

```



### Add callAll

Allow the modal open and close buttons to do additional things besides opening and closing the modals with a `callAll`.

So, make this work:

```jsx
<ModalOpenButton>
  <button onClick={() => console.log('opening the modal')}>Open Modal</button>
</ModalOpenButton>
```



**Solution:**

```jsx
// modal.js
import React from 'react'
import {Dialog} from './lib'

// 1. Add callAll
const callAll = (...fns) => (...args) => fns.forEach(fn => fn && fn(...args))

const ModalContext = React.createContext()

function Modal(props) {
  const [isOpen, setIsOpen] = React.useState(false)

  return <ModalContext.Provider value={[isOpen, setIsOpen]} {...props} />
}

function ModalDismissButton({children: child}) {
  const [, setIsOpen] = React.useContext(ModalContext)
  return React.cloneElement(child, {
    // 2. Use callAll
    onClick: callAll(() => setIsOpen(false), child.props.onClick),
  })
}

function ModalOpenButton({children: child}) {
  const [, setIsOpen] = React.useContext(ModalContext)
  return React.cloneElement(child, {
    // 2. Use callAll
    onClick: callAll(() => setIsOpen(true), child.props.onClick),
  })
}

function ModalContents(props) {
  const [isOpen, setIsOpen] = React.useContext(ModalContext)

  return (
    <Dialog isOpen={isOpen} onDismiss={() => setIsOpen(false)} {...props} />
  )
}

export {Modal, ModalOpenButton, ModalContents, ModalDismissButton}

```



### Create ModalContentsBase

Most modals will have an X button to close the modal, so just build it into the ModalContents

Change structure of modals:

```jsx
        <Modal>
          <ModalOpenButton>
            <Button onClick={() => alert('hello')} variant="primary">
              Login
            </Button>
          </ModalOpenButton>
          <ModalContents aria-label="Login form" title="Login">
            { /* Only child left is the form, close button and title have moved */}
            <LoginForm
              onSubmit={login}
              submitButton={<Button variant="primary">Login</Button>}
            />
          </ModalContents>
        </Modal>
```





```jsx
// Create ModalContentsBase if you don't want a close button or title
function ModalContentsBase(props) {
  const [isOpen, setIsOpen] = React.useContext(ModalContext)

  return (
    <Dialog isOpen={isOpen} onDismiss={() => setIsOpen(false)} {...props} />
  )
}

// Create a new ModalContents with close button and title
function ModalContents({title, children, ...props}) {
  return (
    <ModalContentsBase {...props}>
      <div css={{display: 'flex', justifyContent: 'flex-end'}}>
        <ModalDismissButton>
          <CircleButton>
            <VisuallyHidden>Close</VisuallyHidden>
            <span aria-hidden>×</span>
          </CircleButton>
        </ModalDismissButton>
      </div>
      <h3 css={{textAlign: 'center', fontSize: '2em'}}>{title}</h3>
      {children}
    </ModalContentsBase>
  )
}
```

