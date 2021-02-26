# Routing

[React Router](https://reacttraining.com/react-router) is the standard.

A router is an API that informs you of changes in the URL and renders the appropriate content, among other navigation tools.

The `BrowserRouter` is a context provider that all the React Router use to implicitly access the router data. Wrap your authenticated app with this.

## Demo

```jsx
import * as React from 'react'
import ReactDOM from 'react-dom'
import {
  BrowserRouter as Router,
  Routes,
  Route,
  useParams,
  Link,
} from 'react-router-dom'

function Home() {
  return <h2>Home</h2>
}

function About() {
  return <h2>About</h2>
}

function Dog() {
  const params = useParams()
  const {dogId} = params
  return <img src={`/img/dogs/${dogId}`} />
}

function Nav() {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
      <Link to="/dog/123">My Favorite Dog</Link>
    </nav>
  )
}

function YouLost() {
  return <div>You lost?</div>
}

function App() {
  return (
    <div>
      <h1>Welcome</h1>
      <Nav />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/dog/:dogId" element={<Dog />} />
        <Route path="*" element={<YouLost />} />
      </Routes>
    </div>
  )
}

function AppWithRouter() {
  return (
    <Router>
      <App />
    </Router>
  )
}

ReactDOM.render(<AppWithRouter />, document.getElementById('app'))
```



Post: [How to Build  Your Own React Router (v4)](https://tylermcginnis.com/build-your-own-react-router-v4/)



## Exercises



### Create Book Pages

Steps:

1. `import {BrowserRouter as Router} from 'react-router-dom'`

2. Wrap the authenticated app with the `<Router>`. Every component here now has access to the context provided by the `BrowserRouter`.

3. Update the one link in the nav to a `<Link>` and change the `href` to a `to` prop.

4. Create an `<AppRoutes>` component that renders the router inside the main content, next to `<Nav />`.

   ```jsx
   function AppRoutes({user}) {
     return (
       <Routes>
         <Route path="/discover" element={<DiscoverBooksScreen user={user} />} />
         <Route path="/book/:bookId" element={<BookScreen user={user} />} />
         <Route path="*" element={<NotFoundScreen />} />
       </Routes>
     )
   }
   ```

5. Update the Discover screen to have a `Link` that directs ```to={`/book/${book.id}`}```

6. In the Book screen, use the the `useParams` hook to get the book id: `const {bookId} = useParams()`



### Handle URL Redirects

The problem with redirecting with the client side router is that search engines don't get the proper status codes. You need to set a 301/302 status code. So, do your redirects through your server config.

There are three environment that is serving content:

1. Local environment during development.
2. Local when running `npm run serve` code.
3. In production via Netlify.

**Configuring Local Development Redirects**

CRA lets you use a `./src/setupProxy.js` file to add redirects to webpack server that `react-scripts` is running:

```js
module.exports = app => {
  // app is an Express app
  // take the home route and redirect to the discover page
  app.get(/^\/$/, (req, res) => res.redirect('/discover'))
}

```

**Configuring Local Production Redirects**

Use a module called `./public/serve.json` to configure:

```json
{
	"redirects": [
		{
		"source": "/",
		"destination": "/discover",
		"type": 302
		}
	]
}

```

**Configuring Production Redirects**

Using Netlify, create a `./public/_redirects` file:

```
/	    /discover		  302!
/*    /index.html   200
```



### Matching Routes for Styling

To style the current route in the nav bar, use the `useMatch` hook:

```jsx
import {Routes, Route, Link, useMatch} from 'react-router-dom'

function NavLink(props) {
  const match = useMatch(props.to)
  return (
    <Link
      css={[
        {
          display: 'block',
          padding: '8px 15px 8px 10px',
          margin: '5px 0',
          width: '100%',
          height: '100%',
          color: colors.text,
          borderRadius: '2px',
          borderLeft: '5px solid transparent',
          ':hover': {
            color: colors.indigo,
            textDecoration: 'none',
            background: colors.gray10,
          },
        },
        match
          ? {
              borderLeft: `5px solid ${colors.indigo}`,
              background: colors.gray10,
              ':hover': {
                background: colors.gray20,
              },
            }
          : null,
      ]}
      {...props}
    />
  )
}
```



