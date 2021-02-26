# Compound Components

Compound components are components that work together to form a complete UI. The classic example of this is `<select>` and `<option>` in HTML:

```html
<select>
  <option value="1">Option 1</option>
  <option value="2">Option 2</option>
</select>
```

`<select>` manages state.

`<option>` configures how `<select>` should operate. (What are the options and values?)



This isn't really good:

```jsx
<CustomSelect
  options={[
    {value: '1', display: 'Option 1'},
    {value: '2', display: 'Option 2'},
  ]}
/>
```

Why? Can't add additional options based on what is rendered. Can't change display based on what is selected.



**Real World Projects that use this pattern:**

- [`@reach/tabs`](https://reacttraining.com/reach-ui/tabs)
- Actually most of [Reach UI](https://reacttraining.com/reach-ui) implements this pattern



**Example of a Compound Component:** 

```jsx
function Example() {
  return (
    <Tabs>
      <TabList>
        <Tab>One</Tab>
        <Tab>Two</Tab>
        <Tab>Three</Tab>
      </TabList>
      <TabPanels>
        <TabPanel>
          <p>one!</p>
        </TabPanel>
        <TabPanel>
          <p>two!</p>
        </TabPanel>
        <TabPanel>
          <p>three!</p>
        </TabPanel>
      </TabPanels>
    </Tabs>
  );
}
```



## Exercise

### Create a Compound Component for a Toggle



Add implicit state to child components:

```jsx
import * as React from 'react'
import {Switch} from '../switch'

function Toggle({children}) {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)

  return React.Children.map(children, child => {
    // DOM component support, like a <div/>
    if (typeof child.type === 'string') {
      return child
    }
    // Otherwise, handle the composite components
    return React.cloneElement(child, {on: on, toggle: toggle})
  })
}

const ToggleOn = ({on, children}) => (on ? children : null)
const ToggleOff = ({on, children}) => (on ? null : children)
const ToggleButton = ({on, toggle}) => <Switch on={on} onClick={toggle} />

function App() {
  return (
    <div>
      <Toggle>
        <ToggleOn>The button is on</ToggleOn>
        <ToggleOff>The button is off</ToggleOff>
        <span>HELLO</span>
        <ToggleButton />
      </Toggle>
    </div>
  )
}

export default App
```

