# Functions



## Types of Functions

### Function Declarations

```javascript
function logCompliment() {
  console.log("You're doing great!");
}

logCompliment();
```



### Function Expressions

```javascript
const lordify = function(firstName, land) {
  return `${firstName} of ${land}`;
};

```

or

```javascript
const lordify = (firstName, land) => `${firstName} of ${land}`;

console.log(lordify("Don", "Piscataway")); // Don of Piscataway
console.log(lordify("Todd", "Schenectady")); // Todd of Schenectady
```



### Arrow Functions

They protect the scope of `this`:

```javascript
const tahoe = {
  mountains: ["Freel", "Rose", "Tallac", "Rubicon", "Silver"],
  print: function(delay = 1000) {
    setTimeout(() => {
      console.log(this.mountains.join(", "));
    }, delay);
  }
};

tahoe.print(); // Freel, Rose, Tallac, Rubicon, Silver
```



But they also do not block off the scope of `this`.

```javascript
const tahoe = {
  mountains: ["Freel", "Rose", "Tallac", "Rubicon", "Silver"],
  print: (delay = 1000) => {
    setTimeout(() => {
      console.log(this.mountains.join(", "));
    }, delay);
  }
};

tahoe.print(); // Uncaught TypeError: Cannot read property 'join' of undefined (Scope is window)
```



## Functional Programming

Functions are **first-class citizens** because they can be declared as a variable and sent to functions as an argument. Since JavaScript supports these, it supports functional programming.

A **higher-order function** takes or returns other functions

```javascript
const createScream = function(logger) {
  return function(message) {
    logger(message.toUpperCase() + "!!!");
  };
};

// Or..

const createScream = logger => message => {
  logger(message.toUpperCase() + "!!!");
};
```



### Imperative Versus Declarative

**Imperative Programming**: A style of programming that’s only concerned with how to achieve results with code.

Example:

```javascript
const string = "Restaurants in Hanalei";
const urlFriendly = "";

// Not quite clear what is going on at first glance..
for (var i = 0; i < string.length; i++) {
  if (string[i] === " ") {
    urlFriendly += "-";
  } else {
    urlFriendly += string[i];
  }
}

console.log(urlFriendly); // "Restaurants-in-Hanalei"
```



**Declarative Programming**: A style of programming where applications are structured in a way that prioritizes describing *what* should happen over defining *how* it should happen. Functional programming is part of this paradigm.

Example:

```javascript
const string = "Restaurants in Hanalei";
// Easier to understand
const urlFriendly = string.replace(/ /g, "-");

console.log(urlFriendly);
```

or:

```javascript
const loadAndMapMembers = compose(
  combineWith(sessionStorage, "members"),
  save(sessionStorage, "members"),
  scopeMembers(window),
  logMemberInfoToConsole,
  logFieldsToConsole("name.first"),
  countMembersBy("location.state"),
  prepStatesForMapping,
  save(sessionStorage, "map"),
  renderUSMap
);

getFakeMembers(100).then(loadAndMapMembers);
```



Essentially, declarative programming produces applications that are easier to reason about, and when it’s easier to reason about an application, that application is easier to scale. React is declarative.



### Functional Concepts

- **Immutability**: In a functional program, data is immutable. Instead of changing original data structures, build changed copies. Use things like  `Object.assign({}, color, { rating: rating });`, or spread into new objects.

  ```javascript
  const rateColor = (color, rating) => ({
    ...color,
    rating
  });
  ```

  Or `const addColor = (title, array) => array.concat({ title });`

  Or `const addColor = (title, list) => [...list, { title }];`

- **Pure Functions**: A function that returns a value that's computed based on its arguments. Pure functions take at least one argument and always return a value or another function. They do not cause side effects. They treat their arguments as immutable data. Pure functions are naturally testable. In React, the UI is expressed with pure functions. 

  

  JavaScript methods that will help you: `Array.filter` (uses over `.pop` or `.splice`), `Array.map`, or `Array.reduce`

  

  To transfer an array into an object:

  ```javascript
  const schoolArray = Object.keys(schools).map(key => ({
    name: key,
    wins: schools[key]
  }));
  ```

  

  Try to follow these three rules:

  	1. The function should take in at least one argument.
   2. The function should return a value or another function.
   3. The function should not change or mutate any of its arguments.



- Da







---

---

---

---



So far, we’ve learned that we can transform arrays with `Array.map` and `Array.filter`. We’ve also learned that we can change arrays into objects by combining `Object.keys` with `Array.map`. The final tool that we need in our functional arsenal is the ability to transform arrays into primitives and other objects.

The `reduce` and `reduceRight` functions can be used to transform an array into any value, including a number, string, boolean, object, or even a function.

Let’s say we need to find the maximum number in an array of numbers. We need to transform an array into a number; therefore, we can use `reduce`:

```
const ages = [21, 18, 42, 40, 64, 63, 34];

const maxAge = ages.reduce((max, age) => {
  console.log(`${age} > ${max} = ${age > max}`);
  if (age > max) {
    return age;
  } else {
    return max;
  }
}, 0);

console.log("maxAge", maxAge);

// 21 > 0 = true
// 18 > 21 = false
// 42 > 21 = true
// 40 > 42 = false
// 64 > 42 = true
// 63 > 64 = false
// 34 > 64 = false
// maxAge 64
```

The `ages` array has been reduced into a single value: the maximum age, `64`. `reduce` takes two arguments: a callback function and an original value. In this case, the original value is `0`, which sets the initial maximum value to `0`. The callback is invoked once for every item in the array. The first time this callback is invoked, `age` is equal to `21`, the first value in the array, and `max` is equal to `0`, the initial value. The callback returns the greater of the two numbers, `21`, and that becomes the `max` value during the next iteration. Each iteration compares each `age` against the `max` value and returns the greater of the two. Finally, the last number in the array is compared and returned from the previous callback.

If we remove the `console.log` statement from the preceding function and use a shorthand `if/else` statement, we can calculate the max value in any array of numbers with the following syntax:

```
const max = ages.reduce((max, value) => (value > max ? value : max), 0);
```

# ARRAY.REDUCERIGHT

`Array.reduceRight` works the same way as `Array.reduce`; the difference is that it starts reducing from the end of the array rather than the beginning.

Sometimes we need to transform an array into an object. The following example uses `reduce` to transform an array that contains colors into a hash:

```
const colors = [
  {
    id: "xekare",
    title: "rad red",
    rating: 3
  },
  {
    id: "jbwsof",
    title: "big blue",
    rating: 2
  },
  {
    id: "prigbj",
    title: "grizzly grey",
    rating: 5
  },
  {
    id: "ryhbhsl",
    title: "banana",
    rating: 1
  }
];

const hashColors = colors.reduce((hash, { id, title, rating }) => {
  hash[id] = { title, rating };
  return hash;
}, {});

console.log(hashColors);

// {
// "xekare": {
// title:"rad red",
// rating:3
// },
// "jbwsof": {
// title:"big blue",
// rating:2
// },
// "prigbj": {
// title:"grizzly grey",
// rating:5
// },
// "ryhbhsl": {
// title:"banana",
// rating:1
// }
// }
```

In this example, the second argument sent to the `reduce` function is an empty object. This is our initial value for the hash. During each iteration, the callback function adds a new key to the hash using bracket notation and sets the value for that key to the `id` field of the array. `Array.reduce` can be used in this way to reduce an array to a single value—in this case, an object.

We can even transform arrays into completely different arrays using `reduce`. Consider reducing an array with multiple instances of the same value to an array of unique values. The `reduce` method can be used to accomplish this task:

```
const colors = ["red", "red", "green", "blue", "green"];

const uniqueColors = colors.reduce(
  (unique, color) =>
    unique.indexOf(color) !== -1 ? unique : [...unique, color],
  []
);

console.log(uniqueColors);

// ["red", "green", "blue"]
```

In this example, the `colors` array is reduced to an array of distinct values. The second argument sent to the `reduce` function is an empty array. This will be the initial value for `distinct`. When the `distinct` array does not already contain a specific color, it will be added. Otherwise, it will be skipped, and the current `distinct` array will be returned.

`map` and `reduce` are the main weapons of any functional programmer, and JavaScript is no exception. If you want to be a proficient JavaScript engineer, then you must master these functions. The ability to create one dataset from another is a required skill and is useful for any type of programming paradigm.

## Higher-Order Functions

The use of *higher-order functions* is also essential to functional programming. We’ve already mentioned higher-order functions, and we’ve even used a few in this chapter. Higher-order functions are functions that can manipulate other functions. They can take functions in as arguments or return functions or both.

The first category of higher-order functions are functions that expect other functions as arguments. `Array.map`, `Array.filter`, and `Array.reduce` all take functions as arguments. They are higher-order functions.

Let’s take a look at how we can implement a higher-order function. In the following example, we create an `invokeIf` callback function that will test a condition and invoke a callback function when it’s true and another callback function when the condition is false:

```
const invokeIf = (condition, fnTrue, fnFalse) =>
  condition ? fnTrue() : fnFalse();

const showWelcome = () => console.log("Welcome!!!");

const showUnauthorized = () => console.log("Unauthorized!!!");

invokeIf(true, showWelcome, showUnauthorized); // "Welcome!!!"
invokeIf(false, showWelcome, showUnauthorized); // "Unauthorized!!!"
```

`invokeIf` expects two functions: one for true and one for false. This is demonstrated by sending both `showWelcome` and `showUnauthorized` to `invokeIf`. When the condition is true, `showWelcome` is invoked. When it’s false, `showUnauthorized` is invoked.

Higher-order functions that return other functions can help us handle the complexities associated with asynchronicity in JavaScript. They can help us create functions that can be used or reused at our convenience.

*Currying* is a functional technique that involves the use of higher-order functions.

The following is an example of currying. The `userLogs` function hangs on to some information (the username) and returns a function that can be used and reused when the rest of the information (the message) is made available. In this example, log messages will all be prepended with the associated username. Notice that we’re using the `getFakeMembers` function that returns a promise from [Chapter 2](https://learning.oreilly.com/library/view/learning-react-2nd/9781492051718/ch02.html#javascript-for-react):

```
const userLogs = userName => message =>
  console.log(`${userName} -> ${message}`);

const log = userLogs("grandpa23");

log("attempted to load 20 fake members");
getFakeMembers(20).then(
  members => log(`successfully loaded ${members.length} members`),
  error => log("encountered an error loading members")
);

// grandpa23 -> attempted to load 20 fake members
// grandpa23 -> successfully loaded 20 members

// grandpa23 -> attempted to load 20 fake members
// grandpa23 -> encountered an error loading members
```

`userLogs` is the higher-order function. The `log` function is produced from `userLogs`, and every time the `log` function is used, “grandpa23” is prepended to the message.

## Recursion

Recursion is a technique that involves creating functions that recall themselves. Often, when faced with a challenge that involves a loop, a recursive function can be used instead. Consider the task of counting down from 10. We could create a `for` loop to solve this problem, or we could alternatively use a recursive function. In this example, `countdown` is the recursive function:

```
const countdown = (value, fn) => {
  fn(value);
  return value > 0 ? countdown(value - 1, fn) : value;
};

countdown(10, value => console.log(value));

// 10
// 9
// 8
// 7
// 6
// 5
// 4
// 3
// 2
// 1
// 0
```

`countdown` expects a number and a function as arguments. In this example, it’s invoked with a value of `10` and a callback function. When `countdown` is invoked, the callback is invoked, which logs the current value. Next, `countdown` checks the value to see if it’s greater than `0`. If it is, `countdown` recalls itself with a decremented value. Eventually, the value will be `0`, and `countdown` will return that value all the way back up the call stack.

Recursion is a pattern that works particularly well with asynchronous processes. Functions can recall themselves when they’re ready, like when the data is available or when a timer has finished.

The `countdown` function can be modified to count down with a delay. This modified version of the `countdown` function can be used to create a countdown clock:

```
const countdown = (value, fn, delay = 1000) => {
  fn(value);
  return value > 0
    ? setTimeout(() => countdown(value - 1, fn, delay), delay)
    : value;
};

const log = value => console.log(value);
countdown(10, log);
```

In this example, we create a 10-second countdown by initially invoking `countdown` once with the number `10` in a function that logs the countdown. Instead of recalling itself right away, the `countdown` function waits one second before recalling itself, thus creating a clock.

Recursion is a good technique for searching data structures. You can use recursion to iterate through subfolders until a folder that contains only files is identified. You can also use recursion to iterate though the HTML DOM until you find an element that does not contain any children. In the next example, we’ll use recursion to iterate deeply into an object to retrieve a nested value:

```
const dan = {
  type: "person",
  data: {
    gender: "male",
    info: {
      id: 22,
      fullname: {
        first: "Dan",
        last: "Deacon"
      }
    }
  }
};

deepPick("type", dan); // "person"
deepPick("data.info.fullname.first", dan); // "Dan"
```

`deepPick` can be used to access `Dan`’s type, stored immediately in the first object, or to dig down into nested objects to locate `Dan`’s first name. Sending a string that uses dot notation, we can specify where to locate values that are nested deep within an object:

```
const deepPick = (fields, object = {}) => {
  const [first, ...remaining] = fields.split(".");
  return remaining.length
    ? deepPick(remaining.join("."), object[first])
    : object[first];
};
```

The `deepPick` function is either going to return a value or recall itself until it eventually returns a value. First, this function splits the dot-notated fields string into an array and uses array destructuring to separate the first value from the remaining values. If there are remaining values, `deepPick` recalls itself with slightly different data, allowing it to dig one level deeper.

This function continues to call itself until the fields string no longer contains dots, meaning that there are no more remaining fields. In this sample, you can see how the values for `first`, `remaining`, and `object[first]` change as `deepPick` iterates through:

```
deepPick("data.info.fullname.first", dan); // "Dan"

// First Iteration
// first = "data"
// remaining.join(".") = "info.fullname.first"
// object[first] = { gender: "male", {info} }

// Second Iteration
// first = "info"
// remaining.join(".") = "fullname.first"
// object[first] = {id: 22, {fullname}}

// Third Iteration
// first = "fullname"
// remaining.join("." = "first"
// object[first] = {first: "Dan", last: "Deacon" }

// Finally...
// first = "first"
// remaining.length = 0
// object[first] = "Deacon"
```

Recursion is a powerful functional technique that’s fun to implement.

## Composition

Functional programs break up their logic into small, pure functions that are focused on specific tasks. Eventually, you’ll need to put these smaller functions together. Specifically, you may need to combine them, call them in series or parallel, or compose them into larger functions until you eventually have an application.

When it comes to composition, there are a number of different implementations, patterns, and techniques. One that you may be familiar with is chaining. In JavaScript, functions can be chained together using dot notation to act on the return value of the previous function.

Strings have a replace method. The replace method returns a template string, which will also have a replace method. Therefore, we can chain together replace methods with dot notation to transform a string:

```
const template = "hh:mm:ss tt";
const clockTime = template
  .replace("hh", "03")
  .replace("mm", "33")
  .replace("ss", "33")
  .replace("tt", "PM");

console.log(clockTime);

// "03:33:33 PM"
```

In this example, the template is a string. By chaining replace methods to the end of the template string, we can replace hours, minutes, seconds, and time of day in the string with new values. The template itself remains intact and can be reused to create more clock time displays.

The `both` function is one function that pipes a value through two separate functions. The output of civilian hours becomes the input for `appendAMPM`, and we can change a date using both of these functions combined into one:

```
const both = date => appendAMPM(civilianHours(date));
```

However, this syntax is hard to comprehend and therefore tough to maintain or scale. What happens when we need to send a value through 20 different functions?

A more elegant approach is to create a higher-order function we can use to compose functions into larger functions:

```
const both = compose(
  civilianHours,
  appendAMPM
);

both(new Date());
```

This approach looks much better. It’s easy to scale because we can add more functions at any point. This approach also makes it easy to change the order of the composed functions.

The `compose` function is a higher-order function. It takes functions as arguments and returns a single value:

```
const compose = (...fns) => arg =>
  fns.reduce((composed, f) => f(composed), arg);
```

`compose` takes in functions as arguments and returns a single function. In this implementation, the spread operator is used to turn those function arguments into an array called `fns`. A function is then returned that expects one argument, `arg`. When this function is invoked, the `fns` array is piped starting with the argument we want to send through the function. The argument becomes the initial value for `compose`, then each iteration of the reduced callback returns. Notice that the callback takes two arguments: composed and a function `f`. Each function is invoked with `compose`, which is the result of the previous function’s output. Eventually, the last function will be invoked and the last result returned.

This is a simple example of a `compose` function designed to illustrate composition techniques. This function becomes more complex when it’s time to handle more than one argument or deal with arguments that are not functions.

## Putting It All Together

Now that we’ve been introduced to the core concepts of functional programming, let’s put those concepts to work for us and build a small JavaScript application.

Our challenge is to build a ticking clock. The clock needs to display hours, minutes, seconds, and time of day in civilian time. Each field must always have double digits, meaning leading zeros need to be applied to single-digit values like 1 or 2. The clock must also tick and change the display every second.

First, let’s review an imperative solution for the clock:

```
// Log Clock Time every Second
setInterval(logClockTime, 1000);

function logClockTime() {
  // Get Time string as civilian time
  let time = getClockTime();

  // Clear the Console and log the time
  console.clear();
  console.log(time);
}

function getClockTime() {
  // Get the Current Time
  let date = new Date();
  let time = "";

  // Serialize clock time
  let time = {
    hours: date.getHours(),
    minutes: date.getMinutes(),
    seconds: date.getSeconds(),
    ampm: "AM"
  };

  // Convert to civilian time
  if (time.hours == 12) {
    time.ampm = "PM";
  } else if (time.hours > 12) {
    time.ampm = "PM";
    time.hours -= 12;
  }

  // Prepend a 0 on the hours to make double digits
  if (time.hours < 10) {
    time.hours = "0" + time.hours;
  }

  // prepend a 0 on the minutes to make double digits
  if (time.minutes < 10) {
    time.minutes = "0" + time.minutes;
  }

  // prepend a 0 on the seconds to make double digits
  if (time.seconds < 10) {
    time.seconds = "0" + time.seconds;
  }

  // Format the clock time as a string "hh:mm:ss tt"
  return time.hours + ":" + time.minutes + ":" + time.seconds + " " + time.ampm;
}
```

This solution works, and the comments help us understand what’s happening. However, these functions are large and complicated. Each function does a lot. They’re hard to comprehend, they require comments, and they’re tough to maintain. Let’s see how a functional approach can produce a more scalable application.

Our goal will be to break the application logic up into smaller parts: functions. Each function will be focused on a single task, and we’ll compose them into larger functions that we can use to create the clock.

First, let’s create some functions that give us values and manage the console. We’ll need a function that gives us one second, a function that gives us the current time, and a couple of functions that will log messages on a console and clear the console. In functional programs, we should use functions over values wherever possible. We’ll invoke the function to obtain the value when needed:

```
const oneSecond = () => 1000;
const getCurrentTime = () => new Date();
const clear = () => console.clear();
const log = message => console.log(message);
```

Next, we’ll need some functions for transforming data. These three functions will be used to mutate the `Date` object into an object that can be used for our clock:

- `serializeClockTime`

  Takes a date object and returns an object for clock time that contains hours, minutes, and seconds.

- `civilianHours`

  Takes the clock time object and returns an object where hours are converted to civilian time. For example: 1300 becomes 1:00.

- `appendAMPM`

  Takes the clock time object and appends time of day (AM or PM) to that object.

```
const serializeClockTime = date => ({
  hours: date.getHours(),
  minutes: date.getMinutes(),
  seconds: date.getSeconds()
});

const civilianHours = clockTime => ({
  ...clockTime,
  hours: clockTime.hours > 12 ? clockTime.hours - 12 : clockTime.hours
});

const appendAMPM = clockTime => ({
  ...clockTime,
  ampm: clockTime.hours >= 12 ? "PM" : "AM"
});
```

These three functions are used to transform data without changing the original. They treat their arguments as immutable objects.

Next, we’ll need a few higher-order functions:

- `display`

  Takes a target function and returns a function that will send a time to the target. In this example, the target will be `console.log`.

- `formatClock`

  Takes a template string and uses it to return clock time formatted based on the criteria from the string. In this example, the template is “hh:mm:ss tt”. From there, `formatClock` will replace the placeholders with hours, minutes, seconds, and time of day.

- `prependZero`

  Takes an object’s key as an argument and prepends a zero to the value stored under that object’s key. It takes in a key to a specific field and prepends values with a zero if the value is less than 10.

```
const display = target => time => target(time);

const formatClock = format => time =>
  format
    .replace("hh", time.hours)
    .replace("mm", time.minutes)
    .replace("ss", time.seconds)
    .replace("tt", time.ampm);

const prependZero = key => clockTime => ({
  ...clockTime,
  key: clockTime[key] < 10 ? "0" + clockTime[key] : clockTime[key]
});
```

These higher-order functions will be invoked to create the functions that will be reused to format the clock time for every tick. Both `formatClock` and `prependZero` will be invoked once, initially setting up the required template or key. The inner functions they return will be invoked once every second to format the time for display.

Now that we have all of the functions required to build a ticking clock, we’ll need to compose them. We’ll use the `compose` function that we defined in the last section to handle composition:

- `convertToCivilianTime`

  A single function that takes clock time as an argument and transforms it into civilian time by using both civilian hours.

- `doubleDigits`

  A single function that takes civilian clock time and makes sure the hours, minutes, and seconds display double digits by prepending zeros where needed.

- `startTicking`

  Starts the clock by setting an interval that invokes a callback every second. The callback is composed using all our functions. Every second the console is cleared, `currentTime` is obtained, converted, civilianized, formatted, and displayed.

```
const convertToCivilianTime = clockTime =>
  compose(
    appendAMPM,
    civilianHours
  )(clockTime);

const doubleDigits = civilianTime =>
  compose(
    prependZero("hours"),
    prependZero("minutes"),
    prependZero("seconds")
  )(civilianTime);

const startTicking = () =>
  setInterval(
    compose(
      clear,
      getCurrentTime,
      serializeClockTime,
      convertToCivilianTime,
      doubleDigits,
      formatClock("hh:mm:ss tt"),
      display(log)
    ),
    oneSecond()
  );

startTicking();
```

This declarative version of the clock achieves the same results as the imperative version. However, there quite a few benefits to this approach. First, all of these functions are easily testable and reusable. They can be used in future clocks or other digital displays. Also, this program is easily scalable. There are no side effects. There are no global variables outside of functions themselves. There could still be bugs, but they’ll be easier to find.

In this chapter, we’ve introduced functional programming principles. Throughout the book when we discuss best practices in React, we’ll continue to demonstrate how many React concepts are based in functional techniques. In the next chapter, we’ll dive into React officially with an improved understanding of the principles that guided its development.

[1](https://learning.oreilly.com/library/view/learning-react-2nd/9781492051718/ch03.html#idm45901652156344-marker) Dana S. Scott, [“λ-Calculus: Then & Now”](https://oreil.ly/k0EpX).