# Hooks Flow

![](https://juliette-images.s3.us-east-2.amazonaws.com/public/hook-flow.png)



## Mount

1. Run lazy initializiers
   - Lazy state initialization (pass function into useState)
2. Render
   - The remaining contents of the function
3. React updates DOM
   - React makes divs, etc..
4. Run LayoutEffects
   - Similar to a useEffect callback
5. Browser prints screen
   - React stops running, gives the DOM updates, and lets the browser paint the screen
   - So, a classname would be added to an element before it is painted to the screen
6. Cleanup Effects
7. Run Effects
   - e.g. update local storage



## Update

After a user interacts...

1. Render
2. React updates DOM
3. Cleanup LayoutEffects
4. Run LayoutEffects
5. Browser paints screen
6. Cleanup effects
   - Cleans up any side effects from last render
7. Run effects
   - Runs new effects from this render

## Unmount

After a component  gets removed from a screen:

1. Cleanup LayoutEffects
2. Cleanup effects



## Example

```react
import * as React from 'react'

function Child() {
  console.log('%c    Child: render start', 'color: MediumSpringGreen')

  const [count, setCount] = React.useState(() => {
    console.log('%c    Child: useState(() => 0)', 'color: tomato')
    return 0
  })

  React.useEffect(() => {
    console.log('%c    Child: useEffect(() => {})', 'color: LightCoral')
    return () => {
      console.log(
        '%c    Child: useEffect(() => {}) cleanup 🧹',
        'color: LightCoral',
      )
    }
  })

  React.useEffect(() => {
    console.log(
      '%c    Child: useEffect(() => {}, [])',
      'color: MediumTurquoise',
    )
    return () => {
      console.log(
        '%c    Child: useEffect(() => {}, []) cleanup 🧹',
        'color: MediumTurquoise',
      )
    }
  }, [])

  React.useEffect(() => {
    console.log('%c    Child: useEffect(() => {}, [count])', 'color: HotPink')
    return () => {
      console.log(
        '%c    Child: useEffect(() => {}, [count]) cleanup 🧹',
        'color: HotPink',
      )
    }
  }, [count])

  const element = (
    <button onClick={() => setCount(previousCount => previousCount + 1)}>
      {count}
    </button>
  )

  console.log('%c    Child: render end', 'color: MediumSpringGreen')

  return element
}

function App() {
  console.log('%cApp: render start', 'color: MediumSpringGreen')

  const [showChild, setShowChild] = React.useState(() => {
    console.log('%cApp: useState(() => false)', 'color: tomato')
    return false
  })

  React.useEffect(() => {
    console.log('%cApp: useEffect(() => {})', 'color: LightCoral')
    return () => {
      console.log('%cApp: useEffect(() => {}) cleanup 🧹', 'color: LightCoral')
    }
  })

  React.useEffect(() => {
    console.log('%cApp: useEffect(() => {}, [])', 'color: MediumTurquoise')
    return () => {
      console.log(
        '%cApp: useEffect(() => {}, []) cleanup 🧹',
        'color: MediumTurquoise',
      )
    }
  }, [])

  React.useEffect(() => {
    console.log('%cApp: useEffect(() => {}, [showChild])', 'color: HotPink')
    return () => {
      console.log(
        '%cApp: useEffect(() => {}, [showChild]) cleanup 🧹',
        'color: HotPink',
      )
    }
  }, [showChild])

  const element = (
    <>
      <label>
        <input
          type="checkbox"
          checked={showChild}
          onChange={e => setShowChild(e.target.checked)}
        />{' '}
        show child
      </label>
      <div
        style={{
          padding: 10,
          margin: 10,
          height: 50,
          width: 50,
          border: 'solid',
        }}
      >
        {showChild ? <Child /> : null}
      </div>
    </>
  )

  console.log('%cApp: render end', 'color: MediumSpringGreen')

  return element
}

export default App
```



**On mount:**

```
App: render start
App: useState(() => false)
App: render end
App: useEffect(() => {})
App: useEffect(() => {}, [])
App: useEffect(() => {}, [showChild])
```



**After interaction:**

```
App: render start
App: render end
	Child: render start
	Child: useState(() => 0)
	Child: render end
App: useEffect(() => {}) cleanup 🧹
App: useEffect(() => {}, [showChild]) cleanup 🧹
	Child: useEffect(() => {})
	Child: useEffect(() => {}, [])
	Child: useEffect(() => {}, [count])
App: useEffect(() => {})
App: useEffect(() => {}, [showChild])
```

Note that a child is not called until after the "render end." React will only render if necessary. It simply does the createElement portion first.



**On interaction with child:**

```
Child: render start
Child: render end
Child: useEffect(() => {}) cleanup 🧹
Child: useEffect(() => {}, [count]) cleanup 🧹
Child: useEffect(() => {})

```

The app does not care about what happens in the child. The app is completely isolated. Re-render only happens in child.



**After removing child:**

```
App: render start
App: render end
	Child: useEffect(() => {}) cleanup 🧹
	Child: useEffect(() => {}, []) cleanup 🧹
	Child: useEffect(() => {}, [count]) cleanup 🧹
App: useEffect(() => {}) cleanup 🧹
App: useEffect(() => {}, [showChild]) cleanup 🧹
App: useEffect(() => {})
App: useEffect(() => {}, [showChild])

```

