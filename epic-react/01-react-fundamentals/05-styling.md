# Styling

There are two primary ways to style react components

1. Inline styles with the `style` prop
2. Regular CSS with the `className` prop



## Exercise

Combining classNames and styles:

```jsx
// Set default className to prevent an undefined class from being passed in
function Box({className = '', style, ...rest}) {
  return (
    <div
    	// optional: trim in case last class name is blank
      className={`box ${className}`.trim()}
      style={{fontStyle: 'italic', ...style}}
      {...rest}
    />
  )
}

function App() {
  return (
    <div>
      <Box className="box--small" style={{backgroundColor: 'lightblue'}}>
        small lightblue box
      </Box>
      <Box className="box--medium" style={{backgroundColor: 'pink'}}>
        medium pink box
      </Box>
      <Box className="box--large" style={{backgroundColor: 'orange'}}>
        large orange box
      </Box>
      <Box>No style</Box>
    </div>
  )
}
```



Passing the style in as a prop instead:

```jsx
function Box({className = '', style, size, ...rest}) {
  const sizeClassName = size ? `box--${size}` : ''
  return (
    <div
      className={`box ${sizeClassName}`.trim()}
      style={{fontStyle: 'italic', ...style}}
      {...rest}
    />
  )
}

function App() {
  return (
    <div>
      <Box size="small" style={{backgroundColor: 'lightblue'}}>
        small lightblue box
      </Box>
      <Box size="medium" style={{backgroundColor: 'pink'}}>
        medium pink box
      </Box>
      <Box size="large" style={{backgroundColor: 'orange'}}>
        large orange box
      </Box>
      <Box>No style</Box>
    </div>
  )
}
```

