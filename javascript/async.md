# Asynchronous JavaScript

## Simple Promises with Fetch

A `Promise` is an object that represents whether the async operation is pending, has been completed, or has failed. 

In `fetch`, the `then` method will invoke the callback function once the promise has resolved. Whatever you return from this function becomes the argument of the next `then` function. So we can chain together `then` functions to handle a promise that has been successfully resolved:

```javascript
fetch("https://api.randomuser.me/?nat=US&results=1")
  .then(res => res.json())
  .then(json => json.results)
  .then(console.log)
  .catch(console.error);
```

The `catch` function invokes a callback if `fetch` did not resolve successfully.



## Async/Await

`async` functions can be told to wait for the promise to resolve before further executing any code found in the function.

Let’s make another API request but wrap the functionality with an async function:

```javascript
const getFakePerson = async () => {
  try {
    let res = await fetch("https://api.randomuser.me/?nat=US&results=1");
    let { results } = res.json();
    console.log(results);
  } catch (error) {
    console.error(error);
  }
};

getFakePerson();
```

When using `async` and `await`, you need to surround your promise call in a `try`…`catch` block to handle any errors that may occur due to an unresolved promise.



## Building Promises

The `getPeople` function returns a new promise. The promise makes a request to the API. If the promise is successful, the data will load. If the promise is unsuccessful, an error will occur:

```javascript
const getPeople = count =>
  new Promise((resolves, rejects) => {
    const api = `https://api.randomuser.me/?nat=US&results=${count}`;
    const request = new XMLHttpRequest();
    request.open("GET", api);
    request.onload = () =>
      request.status === 200
        ? resolves(JSON.parse(request.response).results)
        : reject(Error(request.statusText));
    request.onerror = err => rejects(err);
    request.send();
  });
```

```javascript
getPeople(5)
  .then(members => console.log(members))
  .catch(error => console.error(`getPeople failed: ${error.message}`))
);
```



## Example in React

```javascript
// in React:
function GetGreetingForSubject({subject}) {
  const [isLoading, setIsLoading] = React.useState(false)
  const [error, setError] = React.useState(null)
  const [greeting, setGreeting] = React.useState(null)
  React.useEffect(() => {
    async function fetchGreeting() {
      try {
        const response = await window.fetch('https://example.com/api/greeting')
        const data = await response.json()
        setGreeting(data.greeting)
      } catch (error) {
        setError(error)
      } finally {
        setIsLoading(false)
      }
    }
    setIsLoading(true)
    fetchGreeting()
  }, [])
  return isLoading ? (
    'loading...'
  ) : error ? (
    'ERROR!'
  ) : greeting ? (
    <div>
      {greeting} {subject}
    </div>
  ) : null
}
```

