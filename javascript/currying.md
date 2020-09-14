# Currying

Currying is creating a function that can later be called multiple times with different arguments.

```javascript
var abc = function(a, b, c) {
	return [a, b, c]
}

// This function can be called up to three times with different values
// Once it hits it's third value, it returns the result
var curried = _.curry(abc)

curried(1)(2)(3)
// => [1, 2, 3]

curried(1, 2)(3)
// => [1, 2, 3]
```



## Creating a Curried Function

```javascript
const curry = (fn) => {
  return (arg) => {
    return (arg2) => {
      return fn(arg1, arg2)
    }
  }
}

var ab = function(a, b) {
	return [a, b]
}

// curried holds the value of the curry function
// The ab function was passed into it
var curried = _.curry(ab)

// The function is called with argument 1
// It returns another function
// That function is called with argument 2
// It returns the ab function that uses arg 1 and 2 (preserved due to scope)
curried(1)(2)
// => [1, 2]
```



```javascript
const curry = (fn) => {
  return (arg) => {
    return (arg2) => {
      return fn(arg1, arg2)
    }
  }
}

// curried holds the curry function
var curried = _.curry(ab)

curried(1)(2)
// => [1, 2]
```



## React Example

```javascript
const updateFieldValue = field => event => {
	dispatch({
		type: 'updateFieldValue',
		field,
		value: event.target.value
	})
}

<input
	className={styles.input}
	type="text"
	name="name"
	value={state.name}
	onChange={updateFieldValue('name')}
/>
```

Instead of calling a function with two arguments, you call it one argument that returns another function that accepts the second argument. 

Here we pass in the field name, and return a function that accepts the event and both are sent to the reducer.