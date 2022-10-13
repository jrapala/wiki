# Observer Pattern

## What Is It?

We can _subscribe_ certain objects (**observers**) to another object (an **observable**). 
Whenever an event occurs the observable notifies all of its observers.

Very useful when working with **asynchronous, event-based data**. Maybe you want certain components to get notified whenever certain data has finished downloading, or whenever users sent new messages to a message board and all other members should get notified.

Enforces separation of concerns and the single-responsibility principle. Observer objects are not tightly coupled to the observable object, and can be (de)coupled at any time. The observable object is responsible for monitoring the events, while the observers simply handle the received data.

An observable object usually contains 3 important parts:
-   `observers`: an array of observers that will get notified whenever a specific event occurs
-   `subscribe()`: a method in order to add observers to the observers list
-   `unsubscribe()`: a method in order to remove observers from the observers list
-   `notify()`: a method to notify all observers whenever a specific event occurs

A popular library that uses the observable pattern is RxJS.

> ReactiveX combines the Observer pattern with the Iterator pattern and functional programming with collections to fill the need for an ideal way of managing sequences of events. - RxJS

## Example

### Creating an Observable
```js
class Observable {
  constructor() {
    this.observers = [];
  }

  subscribe(func) {
    this.observers.push(func);
  }

  unsubscribe(func) {
    this.observers = this.observers.filter(observer => observer !== func);
  }

  notify(data) {
    this.observers.forEach(observer => observer(data));
  }
}
```

### Adding Observers
```js
import React from "react";
import { Button, Switch, FormControlLabel } from "@material-ui/core"
import { ToastContainer, toast } from "react-toastify";
import observable from "./Observable"

function handleClick() {
  observable.notify("User clicked button!")
}

function handleToggle() {
  observable.notify("User toggled switch!")
}

// Receives data from notify methods
function logger(data) {
  console.log(`${Date.now()} ${data}`);
}

// Receives data from notify methods
function toastify(data) {
  toast(data, {
    position: toast.POSITION.BOTTOM_RIGHT,
    closeButton: false,
    autoClose: 2000
  });
}

observable.subscribe(logger);
observable.subscribe(toastify);

export default function App() {
  return (
    <div className="App">
      <Button onClick={handleClick}>Click me!</Button>
      <FormControlLabel control={<Switch onChange={handleToggle} } /> 
      <ToastContainer />
    </div>
  );
}
```

### RxJS
```js
import React from "react";
import ReactDOM from "react-dom";
import { fromEvent, merge } from "rxjs";
import { sample, mapTo } from "rxjs/operators";
import "./styles.css";

merge(
  fromEvent(document, "mousedown").pipe(mapTo(false)),
  fromEvent(document, "mousemove").pipe(mapTo(true))
)
  .pipe(sample(fromEvent(document, "mouseup")))
  .subscribe(isDragging => {
    console.log("Were you dragging?", isDragging);
  });

ReactDOM.render(
  <div className="App">Click or drag anywhere and check the console!</div>,
  document.getElementById("root")
);
```

## Cons

If an observer becomes too complex, it may cause performance issues when notifying all subscribers.