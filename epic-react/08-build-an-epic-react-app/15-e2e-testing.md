# E2E Testing

Integration tests give us the most bang for our buck, however E2E gives us a HUGE amount of confidence. The application works with a real backend.

E2E tests will run with a local instance of a backend, or will reference a "staging" server. Tests can also be run in production using feature flags. [Talia Nassi on Testing in Production](https://kentcdodds.com/chats-with-kent-podcast/seasons/03/episodes/talia-nassi-on-testing-in-production)

[Cypress](https://www.cypress.io/) has a [Testing Library Implementation](https://testing-library.com/cypress). Here's an example of it in use:

```javascript
describe('smoke', () => {
  it('should allow a typical user flow', () => {
    // 📜 https://docs.cypress.io/api/commands/visit.html
    cy.visit('/some-app-route')

    // 📜 https://github.com/testing-library/cypress-testing-library/tree/17c11b47d2649dc3eb5ff62f66dea566030f4613#usage
    cy.findByRole('button', {name: /click me/i}).click()

    // imagine clicking the button opens a modal window. If we want to scope our
    // queries to that dialog, we can search for it, and then use `within`:
    // 📜 https://docs.cypress.io/api/commands/within.html#Syntax
    cy.findByRole('dialog').within(() => {
      // 📜 https://docs.cypress.io/guides/core-concepts/introduction-to-cypress.html#Assertions
      cy.findByRole('alert').should('contain', 'Oh no! You clicked the button!')
    })
  })
})
```



You'll need to configure Cypress:

```javascript
// cypress/plugins/index.js
const isCI = require('is-ci')

module.exports = (on, config) => {
  const isDev = config.watchForFileChanges
  if (!isCI) {
    config.baseUrl = isDev ? 'http://localhost:3000' : 'http://localhost:8811'
  }
  Object.assign(config, {
    integrationFolder: 'cypress/e2e',
    ignoreTestFiles: '**/*.+(exercise|final|extra-)*.js',
  })

  return config
}

```

```javascript
// cypress/support/index.js
import 'cypress-hmr-restarter'
import '@testing-library/cypress/add-commands'

```



Be careful to avoid "repeat testing" as described in [Common Testing Mistakes](https://kentcdodds.com/blog/common-testing-mistakes#mistake-number-3-repeat-testing).



## Exercises

Test the following user flow:

- Arrive at the app and register
- Go to the discover page and search for a book
- Add that book to the reading list
- Go to the reading list and then go to the book page
- Add some notes
- Mark the book as read
- Give the book a 5 star rating
- Go to the finished books screen
- Verify the book shows up and has a 5 star rating
- Click the book to go back to it's page
- Remove it from the reading list
- Make sure the notes and rating are gone
- Go to the finished books screen and make sure that list is empty



```javascript
import {buildUser} from '../support/generate'

describe('smoke', () => {
  it('should allow a typical user flow', () => {
    // Create a fake user
    const user = buildUser()

    // Visit the home route. Base URL is specified in plug ins.
    cy.visit('/')

    // Find the button named "register" and click it
    cy.findByRole('button', {name: /register/i}).click()

    // Fill out username and password
    cy.findByRole('dialog').within(() => {
      cy.findByRole('textbox', {name: /username/i}).type(user.username)
      // Password textboxes don't have a role (for security)
      cy.findByLabelText(/password/i).type(user.password)
      cy.findByRole('button', {name: /register/i}).click()
    })

    // Navigate to the Discover page
    cy.findByRole('navigation').within(() => {
      cy.findByRole('link', {name: /discover/i}).click()
    })

    // Search for a book
    cy.findByRole('main').within(() => {
      cy.findByRole('searchbox', {name: /search/i}).type('Voice of war{enter}')
      // Add book to reading list
      cy.findByRole('listitem', {name: /voice of war/i}).within(() => {
        cy.findByRole('button', {name: /add to list/i}).click()
      })
    })

    // Navigate to the Reading List page
    cy.findByRole('navigation').within(() => {
      cy.findByRole('link', {name: /reading list/i}).click()
    })

    cy.findByRole('main').within(() => {
      // Assert that there is only one book in the list
      cy.findByRole('listitem').should('have.length', 1)
      cy.findByRole('link', {name: /voice of war/i}).click()
    })

    cy.findByRole('main').within(() => {
      // Add some notes
      cy.findByRole('textbox', {name: /notes/i}).type('This is an awesome book')
      // Check spinners
      cy.findByLabelText(/loading/i).should('exist')
      cy.findByLabelText(/loading/i).should('not.exist')

      // Mark as read
      cy.findByRole('button', {name: /mark as read/i}).click()

      // Rate the book
      // We're using stars instead of radio buttons, so use force: true to select
      // visually hidden items
      cy.findByRole('radio', {name: /5 stars/i}).click({force: true})
    })

    // Navigate to the Finished Books  page
    cy.findByRole('navigation').within(() => {
      cy.findByRole('link', {name: /finished books/i}).click()
    })

    cy.findByRole('main').within(() => {
      // Check that there is only one book on this page
      cy.findByRole('listitem').should('have.length', 1)

      // Verify star rating
      cy.findByRole('radio', {name: /5 stars/i}).should('be.checked')

      cy.findByRole('link', {name: /voice of war/i}).click()
    })

    cy.findByRole('main').within(() => {
      // Remove the book from the list
      cy.findByRole('button', {name: /remove from list/i}).click()
      // Check textbox and rating are gone
      cy.findByRole('textbox', {name: /notes/i}).should('not.exist')
      cy.findByRole('radio', {name: /5 stars/i}).should('not.exist')
    })

    // Navigate back to the books page
    cy.findByRole('navigation').within(() => {
      cy.findByRole('link', {name: /finished books/i}).click()
    })

    // Check that there are no books on the list
    cy.findByRole('main').within(() => {
      cy.findByRole('listitem').should('have.length', 0)
    })
  })
})

```



