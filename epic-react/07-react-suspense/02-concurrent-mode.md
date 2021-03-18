# Concurrent Mode

To use React Concurrent mode:

```javascript
// I just don't want to have to type "unstable_" prefixes everywhere
React.useTransition = React.unstable_useTransition
React.SuspenseList = React.unstable_SuspenseList
ReactDOM.createRoot = ReactDOM.unstable_createRoot
```

```javascript
import * as React from 'react'
import ReactDOM from 'react-dom'

function App() {
  return <div>Hello React World!</div>
}

const rootEl = document.getElementById('root')

// the old way:
// ReactDOM.render(<App />, rootEl)

// the new way:
const root = ReactDOM.createRoot(rootEl)
root.render(<App />)

```

