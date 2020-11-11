# Chapter 3. Functional Programming with JavaScript

When you start to explore React, you’ll likely notice that the topic of functional programming comes up a lot. Functional techniques are being used more and more in JavaScript projects, particularly React projects.

It’s likely that you’ve already written functional JavaScript code without thinking about it. If you’ve mapped or reduced an array, then you’re already on your way to becoming a functional JavaScript programmer. Functional programming techniques are core not only to React but to many of the libraries in the React ecosystem as well.

If you’re wondering where this functional trend came from, the answer is the 1930s, with the invention of *lambda calculus*, or λ-calculus.[1](https://learning.oreilly.com/library/view/learning-react-2nd/9781492051718/ch03.html#idm45901652156344) Functions have been a part of calculus since it emerged in the 17th century. Functions can be sent to functions as arguments or returned from functions as results.  More complex functions, called *higher-order functions*, can manipulate functions and use them as either arguments or results or both. In the 1930s, Alonzo Church was at Princeton experimenting with these higher-order functions when he invented lambda calculus.

In the late 1950s, John McCarthy took the concepts derived from λ-calculus and applied them to a new programming language called Lisp. Lisp implemented the concept of higher-order functions and functions as *first-class members* or *first-class citizens*. A function is considered a first-class member when it can be declared as a variable and sent to functions as an argument. These functions can even be returned from functions.

In this chapter, we’re going to go over some of the key concepts of functional programming, and we’ll cover how to implement functional techniques with JavaScript.

# What It Means to Be Functional

JavaScript supports functional programming because JavaScript functions are first-class citizens. This means that functions can do the same things that variables can do. The latest JavaScript syntax adds language improvements that can beef up your functional programming techniques, including arrow functions, promises, and the spread operator.

In JavaScript, functions can represent data in your application. You may have noticed that you can declare functions with the `var`, `let`, or `const` keywords the same way you can declare strings, numbers, or any other variables:

```
var log = function(message) {
  console.log(message);
};

log("In JavaScript, functions are variables");

// In JavaScript, functions are variables
```

We can write the same function using an arrow function. Functional programmers write a lot of small functions, and the arrow function syntax makes that much easier:

```
const log = message => {
  console.log(message);
};
```

Since functions are variables, we can add them to objects:

```
const obj = {
  message: "They can be added to objects like variables",
  log(message) {
    console.log(message);
  }
};

obj.log(obj.message);

// They can be added to objects like variables
```

Both of these statements do the same thing: they store a function in a variable called `log`. Additionally, the `const` keyword was used to declare the second function, which will prevent it from being overwritten.

We can also add functions to arrays in JavaScript:

```
const messages = [
  "They can be inserted into arrays",
  message => console.log(message),
  "like variables",
  message => console.log(message)
];

messages[1](messages[0]); // They can be inserted into arrays
messages[3](messages[2]); // like variables
```

Functions can be sent to other functions as arguments, just like other variables:

```
const insideFn = logger => {
  logger("They can be sent to other functions as arguments");
};

insideFn(message => console.log(message));

// They can be sent to other functions as arguments
```

They can also be returned from other functions, just like variables:

```
const createScream = function(logger) {
  return function(message) {
    logger(message.toUpperCase() + "!!!");
  };
};

const scream = createScream(message => console.log(message));

scream("functions can be returned from other functions");
scream("createScream returns a function");
scream("scream invokes that returned function");

// FUNCTIONS CAN BE RETURNED FROM OTHER FUNCTIONS!!!
// CREATESCREAM RETURNS A FUNCTION!!!
// SCREAM INVOKES THAT RETURNED FUNCTION!!!
```

The last two examples were of higher-order functions: functions that either take or return other functions. We could describe the same `createScream` higher-order function with arrows:

```
const createScream = logger => message => {
  logger(message.toUpperCase() + "!!!");
};
```

If you see more than one arrow used during a function declaration, this means that you’re using a higher-order function.

We can say that JavaScript supports functional programming because its functions are first-class citizens. This means that functions are data. They can be saved, retrieved, or flow through your applications just like variables.

# Imperative Versus Declarative

Functional programming is a part of a larger programming paradigm: *declarative programming*. Declarative programming is a style of programming where applications are structured in a way that prioritizes describing *what* should happen over defining *how* it should happen.

In order to understand declarative programming, we’ll contrast it with *imperative programming*, or a style of programming that’s only concerned with how to achieve results with code. Let’s consider a common task: making a string URL-friendly. Typically, this can be accomplished by replacing all of the spaces in a string with hyphens, since spaces are not URL-friendly. First, let’s examine an imperative approach to this task:

```
const string = "Restaurants in Hanalei";
const urlFriendly = "";

for (var i = 0; i < string.length; i++) {
  if (string[i] === " ") {
    urlFriendly += "-";
  } else {
    urlFriendly += string[i];
  }
}

console.log(urlFriendly); // "Restaurants-in-Hanalei"
```

In this example, we loop through every character in the string, replacing spaces as they occur. The structure of this program is only concerned with how such a task can be achieved. We use a `for` loop and an `if` statement and set values with an equality operator. Just looking at the code alone does not tell us much. Imperative programs require lots of comments in order to understand what’s going on.

Now let’s look at a declarative approach to the same problem:

```
const string = "Restaurants in Hanalei";
const urlFriendly = string.replace(/ /g, "-");

console.log(urlFriendly);
```

Here we are using `string.replace` along with a regular expression to replace all instances of spaces with hyphens. Using `string.replace` is a way of describing what’s supposed to happen: spaces in the string should be replaced. The details of how spaces are dealt with are abstracted away inside the `replace` function. In a declarative program, the syntax itself describes what should happen, and the details of how things happen are abstracted away.

Declarative programs are easy to reason about because the code itself describes what is happening. For example, read the syntax in the following sample. It details what happens after members are loaded from an API:

```
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

The declarative approach is more readable and, thus, easier to reason about. The details of how each of these functions is implemented are abstracted away. Those tiny functions are named well and combined in a way that describes how member data goes from being loaded to being saved and printed on a map, and this approach does not require many comments. Essentially, declarative programming produces applications that are easier to reason about, and when it’s easier to reason about an application, that application is easier to scale. Additional details about the declarative programming paradigm can be found at the [Declarative Programming wiki](https://oreil.ly/7MbkB).

Now, let’s consider the task of building a document object model, or [DOM](https://www.w3.org/DOM). An imperative approach would be concerned with how the DOM is constructed:

```
const target = document.getElementById("target");
const wrapper = document.createElement("div");
const headline = document.createElement("h1");

wrapper.id = "welcome";
headline.innerText = "Hello World";

wrapper.appendChild(headline);
target.appendChild(wrapper);
```

This code is concerned with creating elements, setting elements, and adding them to the document. It would be very hard to make changes, add features, or scale 10,000 lines of code where the DOM is constructed imperatively.

Now let’s take a look at how we can construct a DOM declaratively using a React component:

```
const { render } = ReactDOM;

const Welcome = () => (
  <div id="welcome">
    <h1>Hello World</h1>
  </div>
);

render(<Welcome />, document.getElementById("target"));
```

React is declarative. Here, the `Welcome` component describes the DOM that should be rendered. The `render` function uses the instructions declared in the component to build the DOM, abstracting away the details of how the DOM is to be rendered. We can clearly see that we want to render our `Welcome` component into the element with the ID of `target`.

# Functional Concepts

Now that you’ve been introduced to functional programming and what it means to be “functional” or “declarative,” we’ll move on to introducing the core concepts of functional programming: immutability, purity, data transformation, higher-order functions, and recursion.

## Immutability

To mutate is to change, so to be *immutable* is to be unchangeable. In a functional program, data is immutable. It never changes.

If you need to share your birth certificate with the public but want to redact or remove private information, you essentially have two choices: you can take a big Sharpie to your original birth certificate and cross out your private data, or you can find a copy machine. Finding a copy machine, making a copy of your birth certificate, and writing all over that copy with that big Sharpie would be preferable. This way you can have a redacted birth certificate to share and your original that’s still intact.

This is how immutable data works in an application. Instead of changing the original data structures, we build changed copies of those data structures and use them instead.

To understand how immutability works, let’s take a look at what it means to mutate data. Consider an object that represents the color `lawn`:

```
let color_lawn = {
  title: "lawn",
  color: "#00FF00",
  rating: 0
};
```

We could build a function that would rate colors and use that function to change the rating of the `color` object:

```
function rateColor(color, rating) {
  color.rating = rating;
  return color;
}

console.log(rateColor(color_lawn, 5).rating); // 5
console.log(color_lawn.rating); // 5
```

In JavaScript, function arguments are references to the actual data. Setting the color’s rating like this changes or mutates the original color object. (Imagine if you tasked a business with redacting and sharing your birth certificate and they returned your original birth certificate with black marker covering the important details. You’d hope that a business would have the common sense to make a copy of your birth certificate and return the original unharmed.) We can rewrite the `rateColor` function so that it does not harm the original goods (the `color` object):

```
const rateColor = function(color, rating) {
  return Object.assign({}, color, { rating: rating });
};

console.log(rateColor(color_lawn, 5).rating); // 5
console.log(color_lawn.rating); // 0
```

Here, we used `Object.assign` to change the color rating. `Object.assign` is the copy machine. It takes a blank object, copies the color to that object, and overwrites the rating on the copy. Now we can have a newly rated color object without having to change the original.

We can write the same function using an arrow function along with the object spread operator. This `rateColor` function uses the spread operator to copy the color into a new object and then overwrite its rating:

```
const rateColor = (color, rating) => ({
  ...color,
  rating
});
```

This version of the `rateColor` function is exactly the same as the previous one. It treats color as an immutable object, does so with less syntax, and looks a little bit cleaner. Notice that we wrap the returned object in parentheses. With arrow functions, this is a required step since the arrow can’t just point to an object’s curly braces.

Let’s consider an array of color names:

```
let list = [{ title: "Rad Red" }, { title: "Lawn" }, { title: "Party Pink" }];
```

We could create a function that will add colors to that array using `Array.push`:

```
const addColor = function(title, colors) {
  colors.push({ title: title });
  return colors;
};

console.log(addColor("Glam Green", list).length); // 4
console.log(list.length); // 4
```

However, `Array.push` is not an immutable function. This `addColor` function changes the original array by adding another field to it. In order to keep the `colors` array immutable, we must use `Array.concat` instead:

```
const addColor = (title, array) => array.concat({ title });

console.log(addColor("Glam Green", list).length); // 4
console.log(list.length); // 3
```

`Array.concat` concatenates arrays. In this case, it takes a new object with a new color title and adds it to a copy of the original array.

You can also use the spread operator to concatenate arrays in the same way it can be used to copy objects. Here’s the emerging JavaScript equivalent of the previous `addColor` function:

```
const addColor = (title, list) => [...list, { title }];
```

This function copies the original list to a new array and then adds a new object containing the color’s title to that copy. It is immutable.

## Pure Functions

A *pure function* is a function that returns a value that’s computed based on its arguments. Pure functions take at least one argument and always return a value or another function. They do not cause side effects, set global variables, or change anything about application state. They treat their arguments as immutable data.

In order to understand pure functions, let’s first take a look at an impure function:

```
const frederick = {
  name: "Frederick Douglass",
  canRead: false,
  canWrite: false
};

function selfEducate() {
  frederick.canRead = true;
  frederick.canWrite = true;
  return frederick;
}

selfEducate();
console.log(frederick);

// {name: "Frederick Douglass", canRead: true, canWrite: true}
```

The `selfEducate` function is not a pure function. It does not take any arguments, and it does not return a value or a function. It also changes a variable outside of its scope: `Frederick`. Once the `selfEducate` function is invoked, something about the “world” has changed. It causes side effects:

```
const frederick = {
  name: "Frederick Douglass",
  canRead: false,
  canWrite: false
};

const selfEducate = person => {
  person.canRead = true;
  person.canWrite = true;
  return person;
};

console.log(selfEducate(frederick));
console.log(frederick);

// {name: "Frederick Douglass", canRead: true, canWrite: true}
// {name: "Frederick Douglass", canRead: true, canWrite: true}
```

# PURE FUNCTIONS ARE TESTABLE

Pure functions are naturally *testable*. They do not change anything about their environment or “world,” and therefore do not require a complicated test setup or teardown. Everything a pure function needs to operate it accesses via arguments. When testing a pure function, you control the arguments, and thus you can estimate the outcome. This `selfEducate` function is also impure: it causes side effects. Invoking this function mutates the objects that are sent to it. If we could treat the arguments sent to this function as immutable data, then we would have a pure function.

Let’s have this function take an argument:

```
const frederick = {
  name: "Frederick Douglass",
  canRead: false,
  canWrite: false
};

const selfEducate = person => ({
  ...person,
  canRead: true,
  canWrite: true
});

console.log(selfEducate(frederick));
console.log(frederick);

// {name: "Frederick Douglass", canRead: true, canWrite: true}
// {name: "Frederick Douglass", canRead: false, canWrite: false}
```

Finally, this version of `selfEducate` is a pure function. It computes a value based on the argument that was sent to it: the `person`. It returns a new `person` object without mutating the argument sent to it and therefore has no side effects.

Now let’s examine an impure function that mutates the DOM:

```
function Header(text) {
  let h1 = document.createElement("h1");
  h1.innerText = text;
  document.body.appendChild(h1);
}

Header("Header() caused side effects");
```

The `Header` function creates a heading—one element with specific text—and adds it to the DOM. This function is impure. It does not return a function or a value, and it causes side effects: a changed DOM.

In React, the UI is expressed with pure functions. In the following sample, `Header` is a pure function that can be used to create `h1` elements just like in the previous example. However, this function on its own does not cause side effects because it does not mutate the DOM. This function will create an `h1` element, and it’s up to some other part of the application to use that element to change the DOM:

```
const Header = props => <h1>{props.title}</h1>;
```

Pure functions are another core concept of functional programming. They will make your life much easier because they will not affect your application’s state. When writing functions, try to follow these three rules:

1. The function should take in at least one argument.
2. The function should return a value or another function.
3. The function should not change or mutate any of its arguments.

## Data Transformations

How does anything change in an application if the data is immutable? Functional programming is all about transforming data from one form to another. We’ll produce transformed copies using functions. These functions make our code less imperative and thus reduce complexity.

You do not need a special framework to understand how to produce one dataset that is based upon another. JavaScript already has the necessary tools for this task built into the language. There are two core functions that you must master in order to be proficient with functional JavaScript: `Array.map` and `Array.reduce`.

In this section, we’ll take a look at how these and some other core functions transform data from one type to another.

Consider this array of high schools:

```
const schools = ["Yorktown", "Washington & Liberty", "Wakefield"];
```

We can get a comma-delimited list of these and some other strings by using the `Array.join` function:

```
console.log(schools.join(", "));

// "Yorktown, Washington & Liberty, Wakefield"
```

`Array.join` is a built-in JavaScript array method that we can use to extract a delimited string from our array. The original array is still intact; `join` simply provides a different take on it. The details of how this string is produced are abstracted away from the programmer.

If we wanted to create a function that creates a new array of the schools that begin with the letter “W,” we could use the `Array.filter` method:

```
const wSchools = schools.filter(school => school[0] === "W");

console.log(wSchools);
// ["Washington & Liberty", "Wakefield"]
```

`Array.filter` is a built-in JavaScript function that produces a new array from a source array. This function takes a *predicate* as its only argument. A predicate is a function that always returns a Boolean value: `true` or `false`. `Array.filter` invokes this predicate once for every item in the array. That item is passed to the predicate as an argument, and the return value is used to decide if that item will be added to the new array. In this case, `Array.filter` is checking every school to see if its name begins with a “W.”

When it’s time to remove an item from an array, we should use `Array.filter` over `Array.pop` or `Array.splice` because `Array.filter` is immutable. In this next sample, the `cutSchool` function returns new arrays that filter out specific school names:

```
const cutSchool = (cut, list) => list.filter(school => school !== cut);

console.log(cutSchool("Washington & Liberty", schools).join(", "));

// "Yorktown, Wakefield"

console.log(schools.join("\n"));

// Yorktown
// Washington & Liberty
// Wakefield
```

In this case, the `cutSchool` function is used to return a new array that does not contain “Washington & Liberty.” Then, the `join` function is used with this new array to create a string out of the remaining two school names. `cutSchool` is a pure function. It takes a list of schools and the name of the school that should be removed and returns a new array without that specific school.

Another array function that is essential to functional programming is `Array.map`. Instead of a predicate, the `Array.map` method takes a function as its argument. This function will be invoked once for every item in the array, and whatever it returns will be added to the new array:

```
const highSchools = schools.map(school => `${school} High School`);

console.log(highSchools.join("\n"));

// Yorktown High School
// Washington & Liberty High School
// Wakefield High School

console.log(schools.join("\n"));

// Yorktown
// Washington & Liberty
// Wakefield
```

In this case, the `map` function was used to append “High School” to each school name. The `schools` array is still intact.

In the last example, we produced an array of strings from an array of strings. The `map` function can produce an array of objects, values, arrays, other functions—any JavaScript type. Here’s an example of the `map` function returning an object for every school:

```
const highSchools = schools.map(school => ({ name: school }));

console.log(highSchools);

// [
// { name: "Yorktown" },
// { name: "Washington & Liberty" },
// { name: "Wakefield" }
// ]
```

An array containing objects was produced from an array that contains strings.

If you need to create a pure function that changes one object in an array of objects, `map` can be used for this, too. In the following example, we’ll change the school with the name of “Stratford” to “HB Woodlawn” without mutating the `schools` array:

```
let schools = [
  { name: "Yorktown" },
  { name: "Stratford" },
  { name: "Washington & Liberty" },
  { name: "Wakefield" }
];

let updatedSchools = editName("Stratford", "HB Woodlawn", schools);

console.log(updatedSchools[1]); // { name: "HB Woodlawn" }
console.log(schools[1]); // { name: "Stratford" }
```

The `schools` array is an array of objects. The `updatedSchools` variable calls the `editName` function and we send it the school we want to update, the new school, and the `schools` array. This changes the new array but makes no edits to the original:

```
const editName = (oldName, name, arr) =>
  arr.map(item => {
    if (item.name === oldName) {
      return {
        ...item,
        name
      };
    } else {
      return item;
    }
  });
```

Within `editName`, the `map` function is used to create a new array of objects based upon the original array. The `editName` function can be written entirely in one line. Here’s an example of the same function using a shorthand `if/else` statement:

```
const editName = (oldName, name, arr) =>
  arr.map(item => (item.name === oldName ? { ...item, name } : item));
```

If you need to transform an array into an object, you can use `Array.map` in conjunction with `Object.keys`. `Object.keys` is a method that can be used to return an array of keys from an object.

Let’s say we needed to transform the `schools` object into an array of schools:

```
const schools = {
  Yorktown: 10,
  "Washington & Liberty": 2,
  Wakefield: 5
};

const schoolArray = Object.keys(schools).map(key => ({
  name: key,
  wins: schools[key]
}));

console.log(schoolArray);

// [
// {
// name: "Yorktown",
// wins: 10
// },
// {
// name: "Washington & Liberty",
// wins: 2
// },
// {
// name: "Wakefield",
// wins: 5
// }
// ]
```

In this example, `Object.keys` returns an array of school names, and we can use `map` on that array to produce a new array of the same length. The `name` of the new object will be set using the key, and `wins` is set equal to the value.

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