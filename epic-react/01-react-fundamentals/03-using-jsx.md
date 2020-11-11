# Using JSX

Because JSX is not actually JavaScript, you have to convert it using something called a code compiler. [Babel](https://babeljs.io/) is one such tool.

[Online Babel REPL](https://babeljs.io/repl#?builtIns=App&code_lz=MYewdgzgLgBArgSxgXhgHgCYIG4D40QAOAhmLgBICmANtSGgPRGm7rNkDqIATtRo-3wMseAFBA&presets=react&prettier=true)

Babel compiles your JSX and inserts the compiled code into the head of the document (along with a source map).

## Differences Between React JSX and HTML

1. `checked` attribute provided for `<input>` like `checkbox` or `radio`. Also available: `defaultChecked` 
2. `className` used instead of  `class`
3. `dangerouslySetInnerHTML` is the replacement for `innerHTML`. It's dangerous because it can expose your script to XSS attacks. This attribute takes an object with `__html` as a key.
4. `htmlFor` is used instead of `for`, since `for` is a reserved word in JavaScript
5. `onChange` is an event fired whenever a form field is changed
6. `selected` attribute provided for marking `<option>`
7. `style` accepts a JavaScript object with camel-cased properties. Styles are not prefixed (vendor prefixes start with a capital letter, e.g. `WebkitTransition`, except for `ms`: `msTransition`). Use this attribute only for dynamically-computed styles at render time. A `px` suffix is automatically added for certain styles.
8. `suppressContentEditableWarning` is used to suppress a warning for elements with children also marked as `contentEditable`.
9. `suppressHydrationWarning` is used to suppress a warning when the server and client render different data (sometimes occurring in SSR). e.g. timestamps
10. `value` attribute provided form `<input>`, `<select>`, and `<textarea>` to set the value of the component. Also available: `defaultValue`.
11. Everything else is camelCased. e.g. `tabIndex` or `clipPath`





## Exercise

```html
<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@16.13.1/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.13.1/umd/react-dom.development.js"></script>

  <script src="https://unpkg.com/@babel/standalone@7.9.3/babel.js"></script>


  <script type="text/babel">
    const element = <div className="container">Hello World</div>
    ReactDOM.render(element, document.getElementById('root'))
  </script>
</body>

```

