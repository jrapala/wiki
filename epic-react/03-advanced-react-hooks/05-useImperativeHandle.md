# useImperativeHandle: Scroll to top/bottom

With class components, we could do this:

```jsx
class MyInput extends React.Component {
  _inputRef = React.createRef()
  focusInput = () => this._inputRef.current.focus()
  render() {
    return <input ref={this._inputRef} />
  }
}

class App extends React.Component {
  _myInputRef = React.createRef()
  handleClick = () => this._myInputRef.current.focusInput()
  render() {
    return (
      <div>
        <button onClick={this.handleClick}>Focus on the input</button>
				// You could add this ref for access to this component instance
        <MyInput ref={this._myInputRef} />
      </div>
    )
  }
}
```



This cannot be done in function components, unless you forward refs:

```jsx
// This doesn't work
function MyInput() {
  const inputRef = React.useRef()
  const focusInput = () => inputRef.current.focus()
  // where do I put the focusInput method??
  return <input ref={inputRef} />
}
  
// This works
const MyInput = React.forwardRef(function MyInput(props, ref) {
  const inputRef = React.useRef()
  ref.current = {
    focusInput: () => inputRef.current.focus(),
  }
  return <input ref={inputRef} />
})
```



HOWEVER, there will be edge case bugs in concurrent mode/suspense, so you'll need to use `useImperativeHandle`:

```javascript
const MyInput = React.forwardRef(function MyInput(props, ref) {
  const inputRef = React.useRef()
  React.useImperativeHandle(ref, () => {
    return {
      focusInput: () => inputRef.current.focus(),
    }
  })
  return <input ref={inputRef} />
})
```

This allows us to expose imperative methods to developers who pass a ref prop to our component which can be useful when you have something that needs to happen and is hard to deal with declaratively.



## Exercise

Expose the imperative methods `scrollToTop` and `scrollToBottom` on a ref so the parent component can call those directly:



```jsx
// useImperativeHandle: scroll to top/bottom
// http://localhost:3000/isolated/exercise/05.js

import * as React from 'react'

// 2. We now have a ref argument we can use
function MessagesDisplay({messages}, ref) {
  const containerRef = React.useRef()
  React.useLayoutEffect(() => {
    scrollToBottom()
  })

  // 3. Assign the functions we need in the parent to the ref.current
  // This makes everything work, however you should technically wait
  // for everything to be mounted first.
  //
  // ref.current = {
  //   scrollToTop,
  //   scrollToBottom,
  // }

  function scrollToTop() {
    containerRef.current.scrollTop = 0
  }

  function scrollToBottom() {
    containerRef.current.scrollTop = containerRef.current.scrollHeight
  }
  
  // 4. Instead, wrap with useImperativeHandle.
  // Use useImperativeHandle on the ref, to provide a function that returns
  // the value you want assigned to the current property.
  React.useImperativeHandle(ref, () => {
    return {
      scrollToTop,
      scrollToBottom,
    }
  })

  return (
    <div ref={containerRef} role="log">
      {messages.map((message, index, array) => (
        <div key={message.id}>
          <strong>{message.author}</strong>: <span>{message.content}</span>
          {array.length - 1 === index ? null : <hr />}
        </div>
      ))}
    </div>
  )
}

// 1. Forward the ref (this is another way of using a forwardRef)
// eslint-disable-next-line no-func-assign
MessagesDisplay = React.forwardRef(MessagesDisplay)

function App() {
  const messageDisplayRef = React.useRef()
  const [messages, setMessages] = React.useState(allMessages.slice(0, 8))
  const addMessage = () =>
    messages.length < allMessages.length
      ? setMessages(allMessages.slice(0, messages.length + 1))
      : null
  const removeMessage = () =>
    messages.length > 0
      ? setMessages(allMessages.slice(0, messages.length - 1))
      : null

  const scrollToTop = () => messageDisplayRef.current.scrollToTop()
  const scrollToBottom = () => messageDisplayRef.current.scrollToBottom()

  return (
    <div className="messaging-app">
      <div style={{display: 'flex', justifyContent: 'space-between'}}>
        <button onClick={addMessage}>add message</button>
        <button onClick={removeMessage}>remove message</button>
      </div>
      <hr />
      <div>
        <button onClick={scrollToTop}>scroll to top</button>
      </div>
      <MessagesDisplay ref={messageDisplayRef} messages={messages} />
      <div>
        <button onClick={scrollToBottom}>scroll to bottom</button>
      </div>
    </div>
  )
}

export default App

const allMessages = [
  `Leia: Aren't you a little short to be a stormtrooper?`,
  `Luke: What? Oh... the uniform. I'm Luke Skywalker. I'm here to rescue you.`,
  `Leia: You're who?`,
  `Luke: I'm here to rescue you. I've got your R2 unit. I'm here with Ben Kenobi.`,
  `Leia: Ben Kenobi is here! Where is he?`,
  `Luke: Come on!`,
  `Luke: Will you forget it? I already tried it. It's magnetically sealed!`,
  `Leia: Put that thing away! You're going to get us all killed.`,
  `Han: Absolutely, Your Worship. Look, I had everything under control until you led us down here. You know, it's not going to take them long to figure out what happened to us.`,
  `Leia: It could be worse...`,
  `Han: It's worse.`,
  `Luke: There's something alive in here!`,
  `Han: That's your imagination.`,
  `Luke: Something just moves past my leg! Look! Did you see that?`,
  `Han: What?`,
  `Luke: Help!`,
  `Han: Luke! Luke! Luke!`,
  `Leia: Luke!`,
  `Leia: Luke, Luke, grab a hold of this.`,
  `Luke: Blast it, will you! My gun's jammed.`,
  `Han: Where?`,
  `Luke: Anywhere! Oh!!`,
  `Han: Luke! Luke!`,
  `Leia: Grab him!`,
  `Leia: What happened?`,
  `Luke: I don't know, it just let go of me and disappeared...`,
  `Han: I've got a very bad feeling about this.`,
  `Luke: The walls are moving!`,
  `Leia: Don't just stand there. Try to brace it with something.`,
  `Luke: Wait a minute!`,
  `Luke: Threepio! Come in Threepio! Threepio! Where could he be?`,
].map((m, i) => ({id: i, author: m.split(': ')[0], content: m.split(': ')[1]}))

```



This exposes the imperative API to your component.

**Reminder**: Declarative (get what you want) vs Imperative (say what you want to get)

