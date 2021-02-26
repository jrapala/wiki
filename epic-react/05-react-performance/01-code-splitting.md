# Code Splitting

Usually the single biggest impact you can have on performance. Load less code = increase speed.

If you're loading complex code, lazy load it until it's needed.



## Dynamic Imports

```jsx
import('/some-module.js').then(
  module => {
    // do stuff with the module's exports
  },
  error => {
    // there was some error loading the module...
  },
)
```

Learn more about dynamic imports in the browser in [Super Simple Start to ESModules in the browser](https://kentcdodds.com/blog/super-simple-start-to-es-modules-in-the-browser)



### Loading Modules as React Components

```jsx
// smiley-face.js
import * as React from 'react'

function SmileyFace() {
  return <div>😃</div>
}

export default SmileyFace

// app.js
import * as React from 'react'

const SmileyFace = React.lazy(() => import('./smiley-face'))

function App() {
  return (
    <div>
      <React.Suspense fallback={<div>loading...</div>}>
        <SmileyFace />
      </React.Suspense>
    </div>
  )
}
```

**Note: **The default export in `'./smiley-face'` must be a component!

The `<Suspense>` boundary is more or less a way to insert a fallback (which is required).



## Coverage Debugging

To determing the need/benefit of code splitting, use the Coverage tool.

Dev Tools > Network > Coverage (if not visible, Escape Key > Hamburger > Coverage)

This will show you how much code is loaded and how much of it is run. You can dive in deeper and see which lines of code are loaded, but not run.

This tool will let you see which code could potentially be lazy loaded.



## Exercises

### Lazy Load a Component

```jsx
// Code splitting
// http://localhost:3000/isolated/exercise/01.js

import * as React from 'react'

// 1. Create a Globe component with a dynamic import
const Globe = React.lazy(() => import('../globe'))

function App() {
  const [showGlobe, setShowGlobe] = React.useState(false)

  return (
    <div
      style={{
        display: 'flex',
        alignItems: 'center',
        flexDirection: 'column',
        justifyContent: 'center',
        height: '100%',
        padding: '2rem',
      }}
    >
      <label style={{marginBottom: '1rem'}}>
        <input
          type="checkbox"
          checked={showGlobe}
          onChange={e => setShowGlobe(e.target.checked)}
        />
        {' show globe'}
      </label>
      <div style={{width: 400, height: 400}}>
        {/* 2. Wrap the component with React.Suspense and a fallback*/}
        {/* Show before the check to future proof for Concurrent mode */}        
        <React.Suspense fallback={<div>Loading...</div>}>
          {showGlobe ? <Globe /> : null}
        </React.Suspense>
      </div>
    </div>
  )
}

export default App

```



### Eager Loading

Instead, have the globe start loading once a user hovers over a checkbox that could show the globe.

**Note:** It doesn't matter how many times you call a dynamic import. Webpack will only ever load the module once.

 ```jsx
// Code splitting
// http://localhost:3000/isolated/exercise/01.js

import * as React from 'react'

const Globe = React.lazy(() => import('../globe'))

function App() {
  const [showGlobe, setShowGlobe] = React.useState(false)

  // 2. Load the file. Once the lazy function runs, the file is already in the cache.
  function loadGlobe() {
    return import('../globe')
  }

  return (
    <div
      style={{
        display: 'flex',
        alignItems: 'center',
        flexDirection: 'column',
        justifyContent: 'center',
        height: '100%',
        padding: '2rem',
      }}
    >
      {/* 1. Attach a function that will load the component */}
      <label
        style={{marginBottom: '1rem'}}
        onMouseEnter={loadGlobe}
        onFocus={loadGlobe}
      >
        <input
          type="checkbox"
          checked={showGlobe}
          onChange={e => setShowGlobe(e.target.checked)}
        />
        {' show globe'}
      </label>
      <div style={{width: 400, height: 400}}>
        <React.Suspense fallback={<div>Loading...</div>}>
          {showGlobe ? <Globe /> : null}
        </React.Suspense>
      </div>
    </div>
  )
}

export default App

 ```



You can also simplify:

```jsx
const loadGlobe = () => import('../globe')
const Globe = React.lazy(loadGlobe)

```



### Webpack Magic Comments

There is a built in API in the browser that lets you load things once the browser is no longer busy.

We can leverage this API with Webpack by using [magic comments](https://webpack.js.org/api/module-methods/#magic-comments).



To have Webpack instruct the browser to prefetch dynamic imports:

```javascript
import(/* webpackPrefetch: true */ './some-module.js')
```

When webpack sees this comment, it adds this to your document’s `head`:

```javascript
<link rel="prefetch" as="script" href="/static/js/1.chunk.js">
```

With this, the browser will automatically load this JavaScript file into the browser cache so it’s ready ahead of time.

The change itself is minimal, but pull up the DevTools to make sure it’s loading properly (you’ll need to uncheck the “Disable cache” button to observe any changes).

---

```jsx
// Code splitting
// http://localhost:3000/isolated/exercise/01.js

import * as React from 'react'

// 1. Add magic comment here
const Globe = React.lazy(() => import(/* webpackPrefetch: true */ '../globe'))

function App() {
  const [showGlobe, setShowGlobe] = React.useState(false)

  return (
    <div
      style={{
        display: 'flex',
        alignItems: 'center',
        flexDirection: 'column',
        justifyContent: 'center',
        height: '100%',
        padding: '2rem',
      }}
    >
      <label style={{marginBottom: '1rem'}}>
        <input
          type="checkbox"
          checked={showGlobe}
          onChange={e => setShowGlobe(e.target.checked)}
        />
        {' show globe'}
      </label>
      <div style={{width: 400, height: 400}}>
        <React.Suspense fallback={<div>Loading...</div>}>
          {showGlobe ? <Globe /> : null}
        </React.Suspense>
      </div>
    </div>
  )
}

export default App

```



**Note:** There is an additional comment called `webpackChunkName` that lets you group modules together in the same chunk, so you can group code together that should be loaded at the same time.