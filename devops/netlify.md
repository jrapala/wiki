# Netlify

## `netlify` CLI Tool

`netlify open` opens the Netlify admin URL of your site

`netlify open --site` opens the site

### Deployment

1. `netlify init`
2. Choose `Create & configure a new site`
3. Choose team
4. Add site name
5. Add build command (e.g. `yarn build`)
6. Add deploy directory (e.g. `public`)



## Serverless Functions

1. Create a serverless function

```javascript
// functions/hello.js

exports.handler = async () => ({
	status: 200,
	body: 'Hello world!'
})
```

2. Create a `netlify.toml` in the root:

```toml
[build]
	functions = "functions"
```

3. Run `netlify dev` to test things out locally. Function will be available at `http://localhost:8888/.netlify/functions/hello`



To change address:

```toml
[build]
	functions = "functions"

[[redirects]]
	from = "/api/*"
	to = "/.netlify/functions/:splat"
	status = 200
```

Now, `http://localhost:8888/api/hello` works.



## Identity

1. On your admin dashboad, navigation to Identity and click "Enable"

2. Add `react-netlify-identity`, `react-netlify-identity-widget`, `@reach/dialog`, `@reach/tabs`, and `@reach/visually-hidden` to your project.

3. Use the `IdentityContextProvider` to wrap your app:

   ```javascript
   import React from 'react';
   import { Link } from 'gatsby';
   import { IdentityContextProvider } from 'react-netlify-identity-widget'
   
   import './layout.css'
   
   const Layout = ({children}) => (
   	<IdentityContextProvider url="https://juliettes-jamstack-auth-app.netlify.com">
   		<header>
   			<Link to="/">JAMstack App</Link>
   		</header>
   		<main>{children}</main>
   	</IdentityContextProvider>
   )
   ```

4. Use  `useIdentityContext`  to get user info:

   ```javascript
   import React from 'react';
   import { useIdentityContext } from "react-netlify-identity"
   import { navigate } from 'gatsby';
   
   const RouteLogin = () => {
   	// get user info
   	const identity = useIdentityContext()
   
   	if (identity && identity.isLoggedIn) {
   		navigate('/dashboard/secret', { replace: true })
   	}
   
   	return (
   		<>
   			<h1>Log In or Sign Up</h1>
   			<button>Log In</button>
   		</>
   	)
   }
   
   export default RouteLogin
   ```

5. There is a modal you can use as well:

   ```javascript
   import React, { useEffect } from 'react';
   import { Router } from '@reach/router';
   import { navigate } from 'gatsby';
   import IdentityModal from "react-netlify-identity-widget"
   
   import Layout from '../components/layout';
   import Profile from '../components/profile';
   import RouteBase from '../components/route-base';
   import RouteSecret from '../components/route-secret';
   import RouteLogin from '../components/route-login';
   
   import 'react-netlify-identity-widget/styles.css'
   
   const Dashboard = ({ location }) => {
   
   	useEffect(() => {
   		if (location.pathname.match(/^\/dashboard\/?$/)) {
   			navigate('/dashboard/login', { replace: true })
   		}
   	}, [location.pathname])
   
   	return (
   		<Layout>
   			<Profile />
   			<Router>
   				<RouteBase path="/dashboard/base"/>
   				<RouteSecret path="/dashboard/secret"/>
   				<RouteLogin path="/dashboard/login"/>
   			</Router>
   			<IdentityModal
   				showDialog={true}
   			/>
   		</Layout>
   	)
   }
   
   export default Dashboard
   ```

   

6. 

