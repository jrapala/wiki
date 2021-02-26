# useLayoutEffect: auto-scrolling textarea

99% of the time you'll want to use `useEffect`, however `useLayoutEffect` may improve your experience.

Use `useLayoutEffect` if the side effect that you are performing makes an observable change to the DOM that will require the browser to paint that update that you've made.



## useEffect vs useLayoutEffect

`useEffect` runs *after* React renders your component, to not block browser painting.

If your effect mutates the DOM (via a ref) **and** this mutation changes the appearance of the DOM node between the time that it is rendered and your effect mutates it, use `useLayoutEffect`. Otherwise, you may see a flicker.

`useLayoutEffect` runs immediately after all DOM mutations. Useful for making DOM measurements, and then making DOM mutations.

**Summary:**

- **useLayoutEffect:** If you need to mutate the DOM and/or **do need** to perform measurements
- **useEffect:** If you don't need to interact with the DOM at all or your DOM changes are unobservable (seriously, most of the time you should use this).



## One Special Case

One other situation you might want to use `useLayoutEffect` instead of `useEffect` is if you're updating a value (like a `ref`) and you want to make sure it's up-to-date before any other code runs. For example:

```javascript
const ref = React.useRef()
React.useEffect(() => {
  ref.value = 'some value'
})

// then, later in another hook or something
React.useLayoutEffect(() => {
  console.log(ref.value) // <-- this logs an old value because this runs first!
})
```



## Example

```jsx
// useLayoutEffect: auto-scrolling textarea
// http://localhost:3000/isolated/exercise/04.js

import * as React from 'react'

function MessagesDisplay({messages}) {
  const containerRef = React.useRef()
  // 🐨 replace useEffect with useLayoutEffect
  // With useEffect, the message shows up before the scroll happens.
  React.useLayoutEffect(() => {
    containerRef.current.scrollTop = containerRef.current.scrollHeight
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

// this is to simulate major computation/big rendering tree/etc.
function sleep(time = 0) {
  const wakeUpTime = Date.now() + time
  while (Date.now() < wakeUpTime) {}
}

function SlooooowSibling() {
  // try this with useLayoutEffect as well to see
  // how it impacts interactivity of the page before updates.
  React.useLayoutEffect(() => {
    // increase this number to see a more stark difference
    sleep(300)
  })
  return null
}

function App() {
  const [messages, setMessages] = React.useState(allMessages.slice(0, 8))
  const addMessage = () =>
    messages.length < allMessages.length
      ? setMessages(allMessages.slice(0, messages.length + 1))
      : null
  const removeMessage = () =>
    messages.length > 0
      ? setMessages(allMessages.slice(0, messages.length - 1))
      : null

  return (
    <div className="messaging-app">
      <div style={{display: 'flex', justifyContent: 'space-between'}}>
        <button onClick={addMessage}>add message</button>
        <button onClick={removeMessage}>remove message</button>
      </div>
      <hr />
      <MessagesDisplay messages={messages} />
      <SlooooowSibling />
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

