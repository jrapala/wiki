# Browser Quirks

- On MacOS, both Firefox and Safari won't focus a button on click. On Safari, this is a known bug.

- On Safari, “Tab” skips buttons and links. You'll need to use “Option” + “Tab” (and “Option” + “Shift” + “Tab”) to tab between buttons

- Internet Explorer HSL syntax:

  ```css
  .colorful-thing {
    color: hsl(200deg, 100%, 50%);
    background-color: hsla(200deg, 100%, 50%, 0.2); /* or hsl() if no alpha */
  }
  ```

- Chrome's stylesheet: https://source.chromium.org/chromium/chromium/src/+/master:third_party/blink/renderer/core/html/resources/html.css

- 

