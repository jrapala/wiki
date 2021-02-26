# Render a React App



## Exercise: Create a Basic App

```react
import React from 'react'
import ReactDOM from 'react-dom'

import {Logo} from './components/logo'

function App() {
  const handleLogIn = () => {
    alert('log in clicked')
  }

  const handleRegister = () => {
    alert('log in clicked')
  }

  return (
    <div>
      <Logo width="80" height="80" />
      <h1>Bookshelf</h1>
      <div>
        <button onClick={handleLogIn}>Login</button>
      </div>
      <div>
        <button onClick={handleRegister}>Register</button>
      </div>
    </div>
  )
}

const rootElement = document.getElementById('root')
ReactDOM.render(<App />, rootElement)

```



## Exercise: Use `@reach/dialog`

https://reacttraining.com/reach-ui/dialog

Use the `Dialog` component from `@react/dialog` and open it when a user clicks Login or Register.

```react
import React, {useState} from 'react'
import ReactDOM from 'react-dom'
import {Dialog} from '@reach/dialog'
import '@reach/dialog/styles.css'

import {Logo} from './components/logo'

function App() {
  const [openModal, setOpenModal] = useState('none')

  const closeModal = () => {
    setOpenModal('none')
  }

  return (
    <div>
      <Logo width="80" height="80" />
      <h1>Bookshelf</h1>
      <div>
        <button onClick={() => setOpenModal('login')}>Login</button>
      </div>
      <div>
        <button name="register" onClick={() => setOpenModal('register')}>
          Register
        </button>
      </div>
      <Dialog
        isOpen={openModal === 'login'}
        onDismiss={closeModal}
        aria-label="Login form"
      >
        <button onClick={closeModal}>Close</button>
        <h3>Login</h3>
      </Dialog>
      <Dialog
        isOpen={openModal === 'register'}
        onDismiss={closeModal}
        aria-label="Register form"
      >
        <button onClick={closeModal}>Close</button>
        <h3>Register</h3>
      </Dialog>
    </div>
  )
}

const rootElement = document.getElementById('root')
ReactDOM.render(<App />, rootElement)

```



## Exercise: Render a Form

```react
import React, {useState} from 'react'
import ReactDOM from 'react-dom'
import {Dialog} from '@reach/dialog'
import '@reach/dialog/styles.css'

import {Logo} from './components/logo'

function LoginForm({onSubmit, buttonText}) {
  const handleSubmit = event => {
    event.preventDefault()
    const {username, password} = event.target.elements

    onSubmit({
      username: username.value,
      password: password.value,
    })
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">Username:</label>
        <input type="text" id="username" />
      </div>
      <div>
        <label htmlFor="password">Password:</label>
        <input type="password" id="password" />
      </div>
      <div>
        <button type="submit">{buttonText}</button>
      </div>
    </form>
  )
}
function App() {
  const [openModal, setOpenModal] = useState('none')

  const closeModal = () => {
    setOpenModal('none')
  }

  const login = formData => {
    console.log('login', formData)
  }

  const register = formData => {
    console.log('register', formData)
  }

  return (
    <div>
      <Logo width="80" height="80" />
      <h1>Bookshelf</h1>
      <div>
        <button onClick={() => setOpenModal('login')}>Login</button>
      </div>
      <div>
        <button name="register" onClick={() => setOpenModal('register')}>
          Register
        </button>
      </div>
      <Dialog
        isOpen={openModal === 'login'}
        onDismiss={closeModal}
        aria-label="Login form"
      >
        <button onClick={closeModal}>Close</button>
        <h3>Login</h3>
        <LoginForm onSubmit={login} buttonText="Login" />
      </Dialog>
      <Dialog
        isOpen={openModal === 'register'}
        onDismiss={closeModal}
        aria-label="Register form"
      >
        <button onClick={closeModal}>Close</button>
        <h3>Register</h3>
        <LoginForm onSubmit={register} buttonText="Register" />
      </Dialog>
    </div>
  )
}

const rootElement = document.getElementById('root')
ReactDOM.render(<App />, rootElement)

```



