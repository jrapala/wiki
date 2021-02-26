# Lifting State

To share state between two sibling component, you need to lift the state up to the lowesst common parent shared between the two components.

The opposite, pushing state down, is know as **colocating state**. It can help performance since it prevents unnecessary child rerenders. (Only the component with the state will rerender). Every update to that state results in an invalidation of the entire React tree. There's also a maintance improvement and more reusability.

Try to keep your state as close to where it's relevant as possible.

One of the leading causes to slow React applications is global state.

Another more complicated option is `useMemo`.

### Another Performance Tip

[One simple trick to optimize React re-renders](https://kentcdodds.com/blog/optimize-react-re-renders)

If you're experiencing performance issues:

1. "Lift" the expensive component to a parent where it will be rendered less often.
2. Then pass the expensive component down as a prop.



If props don't change, then React knows that our effects don't need to be rerun

```jsx
import * as React from 'react'
import ReactDOM from 'react-dom'

function Logger(props) {
  console.log(`${props.label} rendered`)
  return null // what is returned here is irrelevant...
}

function Counter(props) {
  const [count, setCount] = React.useState(0)
  const increment = () => setCount(c => c + 1)
  return (
    <div>
      <button onClick={increment}>The count is {count}</button>
      {props.logger}
      // instead of
      //<Logger label="counter" />
    </div>
  )
}

ReactDOM.render(
  <Counter logger={<Logger label="counter" />} />,
  document.getElementById('root'),
)
```

## Exercise

```react
import * as React from 'react'

function Name({name, onNameChange}) {
  return (
    <div>
      <label htmlFor="name">Name: </label>
      <input id="name" value={name} onChange={onNameChange} />
    </div>
  )
}

function FavoriteAnimal({animal, onAnimalChange}) {
  return (
    <div>
      <label htmlFor="animal">Favorite Animal: </label>
      <input id="animal" value={animal} onChange={onAnimalChange} />
    </div>
  )
}

function Display({name, animal}) {
  return <div>{`Hey ${name}, your favorite animal is: ${animal}!`}</div>
}

function App() {
  const [name, setName] = React.useState('')
  const [animal, setAnimal] = React.useState('')

  return (
    <form>
      <Name name={name} onNameChange={event => setName(event.target.value)} />
      <FavoriteAnimal
        animal={animal}
        onAnimalChange={event => setAnimal(event.target.value)}
      />
      <Display name={name} animal={animal} />
    </form>
  )
}

export default App

```

