# Walkthrough of Project Setup

This is a `create-react-app` applications. It uses `react-scripts` to run build and a development server. 



## `jsonconfig.json`

This gives us some type completions for Cypress and sets the root file source for relative paths. First checks `./src`, then checks `node_modules`.

```json
{
  "compilerOptions": {
    "baseUrl": "./src"
  },
  "include": ["src", "node_modules/cypress", "./cypress/**/*.js"]
}

```



## `jest.config.js`

Normally Jest is baked into a CRA, but Jest is customized for this project.