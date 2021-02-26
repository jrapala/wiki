# Production Performance Monitoring

Adding production performance monitoring gives you a way to monitor any performance regressions that happen over time.

You need to build your app using `react-dom/profiling` and `scheduler/tracing-profiler` because there is a slight ding to performance. Facebook builds two apps, one with each method, and serves performance-monitoring version to some users.

The `<React.Profiler />` component won't give you everything DevTools will give you, but it gives you a lot.

Basic Usage:

```jsx
function onRenderCallback(
  id, // the "id" prop of the Profiler tree that has just committed
  phase, // either "mount" (if the tree just mounted) or "update" (if it re-rendered)
  actualDuration, // time spent rendering the committed update
  baseDuration, // estimated time to render the entire subtree without memoization
  startTime, // when React began rendering this update
  commitTime, // when React committed this update
  interactions, // the Set of interactions belonging to this update
) {
  // Aggregate or log render timings...
}
```



```jsx
<App>
  <Profiler id="Navigation" onRender={onRenderCallback}>
    <Navigation {...props} />
  </Profiler>
  <Main {...props} />
</App>
```



## Exercises

### Add Performance Monitoring to an App

```jsx
// /src/report-profile.js
// This module is just here for the exercise and doesn't actually do anything.
// In reality, what I would recommend for a function like this is that it keeps
// a queue of all updates and every 10 seconds it sends profile data to your
// server if there's any data in the queue.
// Then you presumably graph that data in Grafana or similar
let queue = []

// we're doing every 5 seconds so we don't have to wait forever...
// actual time may vary based on your app's needs
setInterval(sendProfileQueue, 5000)

function reportProfile(
  id, // the "id" prop of the Profiler tree that has just committed
  phase, // either "mount" (if the tree just mounted) or "update" (if it re-rendered)
  actualDuration, // time spent rendering the committed update
  baseDuration, // estimated time to render the entire subtree without memoization
  startTime, // when React began rendering this update
  commitTime, // when React committed this update
  interactions, // the Set of interactions belonging to this update
) {
  queue.push({
    id,
    phase,
    actualDuration,
    baseDuration,
    startTime,
    commitTime,
    interactions,
  })
  // this is a fire and forget, so we don't return anything.
}

function sendProfileQueue() {
  if (!queue.length) {
    return Promise.resolve()
  }
  const queueToSend = [...queue]
  queue = []
  console.info('sending profile queue', queueToSend)
  // here's where we'd actually make the server call to send the queueToSend
  // data to our backend... But we don't have a backend for this workshop so...
  return Promise.resolve()
}

export default reportProfile

```

```jsx
// exercise.js
// Production performance monitoring
// http://localhost:3000/isolated/exercise/07.js

import * as React from 'react'
import reportProfile from '../report-profile'

function Counter() {
  const [count, setCount] = React.useState(0)
  const increment = () => setCount(c => c + 1)
  return <button onClick={increment}>{count}</button>
}

function App() {
  return (
    <div>
      <React.Profiler id="counter" onRender={reportProfile}>
        <div>
          Profiled counter
          <Counter />
        </div>
      </React.Profiler>
      <div>
        Unprofiled counter
        <Counter />
      </div>
    </div>
  )
}

export default App

```

You'll get updates like this:

```jsx
[
	{
  	actualDuration: 0.11499982792884111 // time spent rendering the commit
    baseDuration: 0.3899999428540468 // est. time rendering entire subtree w/o memoization
    commitTime: 22568.659999989904 // when React committed the update
    id: "counter"
    interactions: Set(0) {}
    phase: "update"
    startTime: 22568.285000044852 // when React began rendering the update
	}
]
```



### Experimental Trace API

The `interactions` argument that our `onRenderCallback` accepts are for tracing specific interactions. Interactions like button clicks or HTTP responses, etc. Using this helps us answer more specific questions about what’s causing things to be slow.

Information about this experimental API can be found [here](https://gist.github.com/bvaughn/8de925562903afd2e7a12554adcdda16).

```jsx
import * as React from 'react'
import {unstable_trace as trace} from 'scheduler/tracing'
import reportProfile from '../report-profile'

function Counter() {
  const [count, setCount] = React.useState(0)
  // trace(name, when the interaction started, callback for the thing we want to happen )
  const increment = () =>
    trace('click', performance.now(), () => setCount(c => c + 1))
  return <button onClick={increment}>{count}</button>
}

function App() {
  return (
    <div>
      <React.Profiler id="counter" onRender={reportProfile}>
        <div>
          Profiled counter
          <Counter />
        </div>
      </React.Profiler>
      <div>
        Unprofiled counter
        <Counter />
      </div>
    </div>
  )
}

export default App

```

```jsx
actualDuration: 0.8549999911338091
baseDuration: 0.5649999948218465
commitTime: 11236.275000032037
id: "counter"
interactions: [
	[
    {
      id: 0
			name: "click"
			timestamp: 11233.46500005573
    }
	]
]
phase: "update"
startTime: 11234.805000014603
```

You can also see results in the Profiler/Interactions page in DevTools

