# Intro to Testing

**What is a JavaScript Test?** A JavaScript Test is some code that:

1. Sets up some state,
2. Performs some action
3. Makes an assertion on the new state.



Here is the most basic test:

```javascript
const sum = (a, b) => a + b
const subtract = (a, b) => a - b
module.exports = {sum, subtract}
```

```javascript
// basic-test.js
const {sum, subtract} = require('./math')

let result, expected

result = sum(3, 7)
expected = 10
if (result !== expected) {
  throw new Error(`${result} is not equal to ${expected}`)
}

result = subtract(7, 3)
expected = 4
if (result !== expected) {
  throw new Error(`${result} is not equal to ${expected}`)
}
```



**What is a test?** A test is code that throws an error when the actual result of something does not match the expected output. Pure functions are the easiest to test.

**What is an assertion?**  The `actual !== expected` part. 

**Why use a testing framework?** Their error messages are very helpful. Also shows the code where the error was thrown.



### Using Node's `assert` Module

```javascript
const assert = require('assert')
const {sum, subtract} = require('./math')

let result, expected

result = sum(3, 7)
expected = 10
assert.strictEqual(result, expected)

result = subtract(7, 3)
expected = 4
assert.strictEqual(result, expected)
```



### Creating Your Own Testing Framework

```javascript
const {sum, subtract} = require('./math')

let result, expected

test('sum adds numbers', () => {
  const result = sum(3, 7)
  const expected = 10
  expect(result).toBe(expected)
})

test('subtract subtracts numbers', () => {
  const result = subtract(7, 3)
  const expected = 4
  expect(result).toBe(expected)
})

function test(title, callback) {
  try {
    callback()
    console.log(`✓ ${title}`)
  } catch (error) {
    console.error(`✕ ${title}`)
    console.error(error)
  }
}

function expect(actual) {
  return {
    toBe(expected) {
      if (actual !== expected) {
        throw new Error(`${actual} is not equal to ${expected}`)
      }
    },
  }
}
```

```
$ node 4.js
✕ sum adds numbers
Error: -4 is not equal to 10
    at Object.toBe (/Users/kdodds/Desktop/js-test-example/4.js:29:15)
    at test (/Users/kdodds/Desktop/js-test-example/4.js:6:18)
    at test (/Users/kdodds/Desktop/js-test-example/4.js:17:5)
    at Object.<anonymous> (/Users/kdodds/Desktop/js-test-example/4.js:3:1)
    at Module._compile (module.js:635:30)
    at Object.Module._extensions..js (module.js:646:10)
    at Module.load (module.js:554:32)
    at tryModuleLoad (module.js:497:12)
    at Function.Module._load (module.js:489:3)
    at Function.Module.runMain (module.js:676:10)
✓ subtract subtracts numbers
```



### Switching to Jest

We're almost there. Just need to use Jest:

```javascript
const {sum, subtract} = require('./math')

test('sum adds numbers', () => {
  const result = sum(3, 7)
  const expected = 10
  expect(result).toBe(expected)
})

test('subtract subtracts numbers', () => {
  const result = subtract(7, 3)
  const expected = 4
  expect(result).toBe(expected)
})
```

```
$ jest
 FAIL  ./5.js
  ✕ sum adds numbers (5ms)
  ✓ subtract subtracts numbers (1ms)
● sum adds numbers
expect(received).toBe(expected)
    Expected value to be (using Object.is):
      10
    Received:
      -4
      4 |   const result = sum(3, 7)
      5 |   const expected = 10
    > 6 |   expect(result).toBe(expected)
      7 | })
      8 |
      9 | test('subtract subtracts numbers', () => {
      at Object.<anonymous>.test (5.js:6:18)
Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 passed, 2 total
Snapshots:   0 total
Time:        0.6s, estimated 1s
Ran all test suites.
```



### Common Framework Helper Functions

`beforeEach`

`describe`



### Common Framework Assertions

`toMatchObject`

`toContain`



## What is a JavaScript Mock?

Here is the module we will be testing:

```javascript
// thumb-war.js
import {getWinner} from './utils'

function thumbWar(player1, player2) {
  const numberToWin = 2
  let player1Wins = 0
  let player2Wins = 0
  while (player1Wins < numberToWin && player2Wins < numberToWin) {
    // The winner is non-deterministic
    const winner = getWinner(player1, player2)
    if (winner === player1) {
      player1Wins++
    } else if (winner === player2) {
      player2Wins++
    }
  }
  return player1Wins > player2Wins ? player1 : player2
}

export default thumbWar
```

This is more or less calling to a third party machine learning service that has a testing environment we don't control and is unreliable so we want to mock it for our tests.

This is a rare situation where we need to mock in order to reliably test our code.

**How to Monkey-Patch (Simpliest Form of Mocking)**

1. Import utils as `*` so we have an object to manipulate. Store the original function at the beginning and restore it at the end. The `utils.getWinner = (p1, p2) => p2` is the monkey patch.

   ```javascript
   import thumbWar from '../thumb-war'
   import * as utils from '../utils'
   
   test('returns winner', () => {
     const originalGetWinner = utils.getWinner
     // eslint-disable-next-line import/namespace
     utils.getWinner = (p1, p2) => p2
     
     const winner = thumbWar('Ken Wheeler', 'Kent C. Dodds')
     expect(winner).toBe('Kent C. Dodds')
     
     // eslint-disable-next-line import/namespace
     utils.getWinner = originalGetWinner
   })
   ```

2. Add code to see if `getWinner` was called twice, and with the right args:

   ```jsx
   import thumbWar from '../thumb-war'
   import * as utils from '../utils'
   
   test('returns winner', () => {
     const originalGetWinner = utils.getWinner
     // eslint-disable-next-line import/namespace
     utils.getWinner = (...args) => {
       utils.getWinner.mock.calls.push(args)
       return args[1]
     }
     // Add mock object
     utils.getWinner.mock = {calls: []}
     
     const winner = thumbWar('Ken Wheeler', 'Kent C. Dodds')
     expect(winner).toBe('Kent C. Dodds')
     // Check if called the right number of times
     expect(utils.getWinner.mock.calls).toHaveLength(2)
     // Check if the right arguments are being used (is function called properly?)
     utils.getWinner.mock.calls.forEach(args => {
       expect(args).toEqual(['Ken Wheeler', 'Kent C. Dodds'])
     })
     
     // eslint-disable-next-line import/namespace
     utils.getWinner = originalGetWinner
   })
   ```

   **Pro Tip:** Add some [contract testing](https://martinfowler.com/bliki/ContractTest.html) as well to ensure the contract between `getWinner` and the thrid party service is kept in check.

3. Scrap it all and just use Jest instead:

   ```javascript
   import thumbWar from '../thumb-war'
   import * as utils from '../utils'
   
   test('returns winner', () => {
     const originalGetWinner = utils.getWinner
     // eslint-disable-next-line import/namespace
     utils.getWinner = jest.fn((p1, p2) => p2
                               
     const winner = thumbWar('Ken Wheeler', 'Kent C. Dodds')
     expect(winner).toBe('Kent C. Dodds')
     expect(utils.getWinner).toHaveBeenCalledTimes(2)
     utils.getWinner.mock.calls.forEach(args => {
       expect(args).toEqual(['Ken Wheeler', 'Kent C. Dodds'])
     })
     
     // eslint-disable-next-line import/namespace
     utils.getWinner = originalGetWinner
   })
   ```

   `jest.fn` is a special Jest mock function. 

4. Use Jest's `spyOn` to simplify further:

   ```javascript
   import thumbWar from '../thumb-war'
   import * as utils from '../utils'
   
   test('returns winner', () => {
     jest.spyOn(utils, 'getWinner')
     utils.getWinner.mockImplementation((p1, p2) => p2)
     
     const winner = thumbWar('Ken Wheeler', 'Kent C. Dodds')
     expect(winner).toBe('Kent C. Dodds')
     
     utils.getWinner.mockRestore()
   })
   ```

   Mock functions are also called **spies**. 

5. Fix the ESLint errors (particulary *import/namespace: Reports on assignment to a member of an imported namespace.*)
   Our code works by sheer luck with how Babel transpiles it and how the require cache works. If you upgrade to ES modules, it will break.
   Use `jest.mock` instead to seamlessly swap a mock implementation of a module for a real one:

   ```javascript
   import thumbWar from '../thumb-war'
   import * as utilsMock from '../utils'
   
   jest.mock('../utils', () => {
     return {
       getWinner: jest.fn((p1, p2) => p2),
     }
   })
   
   test('returns winner', () => {
     const winner = thumbWar('Ken Wheeler', 'Kent C. Dodds')
     expect(winner).toBe('Kent C. Dodds')
     expect(utilsMock.getWinner).toHaveBeenCalledTimes(2)
     utilsMock.getWinner.mock.calls.forEach(args => {
       expect(args).toEqual(['Ken Wheeler', 'Kent C. Dodds'])
     })
   })
   ```

   Jest will now use mock version instead of real one in all files.

6. Move mocks to a separate `__mocks__` directory:

   ```
   other/whats-a-mock/
   ├── __mocks__
   │   └── utils.js
   ├── __tests__/
   ├── thumb-war.js
   └── utils.js
   ```

   ```javascript
   // __mocks__/utils.js
   export const getWinner = jest.fn((p1, p2) => p2)
   ```

   ```javascript
   // __tests__/thumb-war.js
   import thumbWar from '../thumb-war'
   import * as utilsMock from '../utils'
   
   jest.mock('../utils')
   
   test('returns winner', () => {
     const winner = thumbWar('Ken Wheeler', 'Kent C. Dodds')
     expect(winner).toBe('Kent C. Dodds')
     expect(utilsMock.getWinner).toHaveBeenCalledTimes(2)
     utilsMock.getWinner.mock.calls.forEach(args => {
       expect(args).toEqual(['Ken Wheeler', 'Kent C. Dodds'])
     })
   })
   ```

   You can continue to use `mockImplementation` to change the result of a mock util in different tests.

