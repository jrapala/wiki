# Rendering Arrays

Each child in a list should have a unique key prop.

React doesn't know how JSX has changed. It only knows that JSX has changed.

React doesn't keep track of how the browser is changing nodes (e.g. you update an input field), or changing a component that maintains its own state.

A key lets React know if you updated an item, removed an item, added an item, etc..

The key needs to be consistent over the lifetime of the element. Avoid using the index since that is simply the default behavior.

## Exercise

Add a key prop to a list of items.



## Extra Credit Demo

Demo that lets you see how adding a unique key lets you maintain focus in an input field that is constantly being shuffled.

```jsx
import React from 'react'

function FocusDemo() {
  const [items, setItems] = React.useState([
    {id: 'a', value: 'apple'},
    {id: 'o', value: 'orange'},
    {id: 'g', value: 'grape'},
    {id: 'p', value: 'pear'},
  ])

  React.useEffect(() => {
    const id = setInterval(() => setItems(shuffle), 1000)
    return () => clearInterval(id)
  }, [])

  function getChangeHandler(item) {
    return event => {
      const newValue = event.target.value
      setItems(allItems =>
        allItems.map(i => ({
          ...i,
          value: i.id === item.id ? newValue : i.value,
        })),
      )
    }
  }

  return (
    <div>
      <div>
        <h1>Without a key</h1>
        {items.map(item => (
          <input value={item.value} onChange={getChangeHandler(item)} />
        ))}
      </div>
      <div>
        <h1>With array index as key</h1>
        {items.map((item, index) => (
          <input
            key={index}
            value={item.value}
            onChange={getChangeHandler(item)}
          />
        ))}
      </div>
      <div>
        <h1>With a Proper Key</h1>
        {items.map(item => (
          <input
            key={item.id}
            value={item.value}
            onChange={getChangeHandler(item)}
          />
        ))}
      </div>
    </div>
  )
}

function shuffle(originalArray) {
  const array = [...originalArray]
  let currentIndex = array.length
  let temporaryValue
  let randomIndex
  // While there remain elements to shuffle...
  while (0 !== currentIndex) {
    // Pick a remaining element...
    randomIndex = Math.floor(Math.random() * currentIndex)
    currentIndex -= 1
    // And swap it with the current element.
    temporaryValue = array[currentIndex]
    array[currentIndex] = array[randomIndex]
    array[randomIndex] = temporaryValue
  }
  return array
}

function App() {
  return <FocusDemo />
}

export default App
```

