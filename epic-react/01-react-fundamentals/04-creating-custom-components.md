# Creating Custom Components

Components are basically functions which return something that is “renderable” (more React elements, strings, `null`, numbers, etc.)

## Exercise

```javascript
const message = ({children}) => {
  return (
    <div className="message">{children}</div>
	)
}

const element = (
  <div className="container">
  	{message({children: "Hello World"})}
		{message({children: "Goodbye World"})}
	</div>
)

ReactDOM.render(element, document.getElementById('root'))
```



Another option:

```javascript
const message = ({children}) => {
  return (
    <div className="message">{children}</div>
	)
}

const hello = React.createElement(message, {children: "Hello World"})
const goodbye = React.createElement(message, {children: "Goodbye World"})
    
const element = (
  <div className="container">
	  {React.createElement(message, { children: hello})}
		{React.createElement(message, { children: goodbye})}
	</div>
)

ReactDOM.render(element, document.getElementById('root'))
```



## JSX --> React.createElement

The way we write JSX tells React how to pass it into `React.createElement`.

One way to do this is by capitalizing the first letter of the element. This will tell Babel to pass the element name into `React.createElement` as a function, instead of a string. In JSX, a capital letter after a `<` means it's a reference to a variable that is in scope.

```javascript
const Message = ({children}) => {
  return (
    <div className="message">{children}</div>
	)
}

const hello = React.createElement(message, {children: "Hello World"})
const goodbye = React.createElement(message, {children: "Goodbye World"})
    
const element = (
  <div className="container">
	  <Message>Hello World</Message>
		{React.createElement(Message, { children: goodbye})}
	</div>
)

ReactDOM.render(element, document.getElementById('root'))
```



### Additional Examles of Babel Output:

```javascript
ui = <Capitalized /> // React.createElement(Capitalized)
ui = <property.access /> // React.createElement(property.access)
ui = <Property.Access /> // React.createElement(Property.Access)
ui = <Property['Access'] /> // SyntaxError
ui = <lowercase /> // React.createElement('lowercase')
ui = <kebab-case /> // React.createElement('kebab-case')
ui = <Upper-Kebab-Case /> // React.createElement('Upper-Kebab-Case')
ui = <Upper_Snake_Case /> // React.createElement(Upper_Snake_Case)
ui = <lower_snake_case /> // React.createElement('lower_snake_case')
```



## How PropTypes Work

```javascript
function Message({subject, greeting}) {
  return (
    <div className="message">
	    {greeting}, {subject}
		</div>
	)
}

const PropTypes = {
	string(props, propName, componentName) {
  	if (typeof props[propName] !== 'string') {
    	return new Error('Prop must be a string')
    }
  },
}

// React references propTypes when it renders a component
Message.propTypes = {
  // call string() method in PropTypes
	greeting: PropTypes.string,
  subject: PropTypes.string,
}

const element = (
  <div className="container">
  <Message greeting="Hello" subject="World" />
  <Message greeting="Goodbye" subject="World" />
  </div>
)

ReactDOM.render(element, document.getElementById('root'))
```



**Note:** these will be stripped out in production mode since it's expensive.

