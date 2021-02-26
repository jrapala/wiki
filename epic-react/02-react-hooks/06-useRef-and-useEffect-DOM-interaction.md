# useRef and useEffect: DOM Interaction

To access a DOM node, you must act React to give you access via a special prop called a `ref`. 

Add to `useEffect` to access it after mount.

```react
function MyDiv() {
  const myDivRef = React.useRef()
  React.useEffect(() => {
    const myDiv = myDivRef.current
    // myDiv is the div DOM node!
    console.log(myDiv)
  }, [])
  return <div ref={myDivRef}>hi</div>
}
```



## Other Uses of useRef

Anytime you want to maintain a reference to something, and make changes to it, without triggering a rerender.

1. Detecting outside clicks (`!ref.current.contains(event.target)`)

2. Storing mutable state - keep track of the previous value of a state variable

3. Determining size of an element

4. Allow for forwarding refs

   ```react
   const Button = React.forwardRef((props, ref) => {
     return <button className="btn btn-default" ref={ref} />;
   });
   ```

   

## Exercise

Get node for use with VanillaTilt:

```react
import * as React from 'react'
import VanillaTilt from 'vanilla-tilt'

function Tilt({children}) {
  const tiltRef = React.useRef()

  React.useEffect(() => {
    const tiltNode = tiltRef.current
    VanillaTilt.init(tiltNode, {
      max: 25,
      speed: 400,
      glare: true,
      'max-glare': 0.5,
    })
    return () => tiltNode.vanillaTilt.destroy()
    // [] dependencies = run only after mount
  }, [])

  return (
    <div className="tilt-root" ref={tiltRef}>
      <div className="tilt-child">{children}</div>
    </div>
  )
}

function App() {
  return (
    <Tilt>
      <div className="totally-centered">vanilla-tilt.js</div>
    </Tilt>
  )
}

export default App
```



