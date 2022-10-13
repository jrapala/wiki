# Button
## What Is It?
A `<button>` or `<input type="submit">` is always better, but sometimes you need to make a custom ARIA button (e.g. styling in a range of target browsers).


## Expected Operation
### Activation
Any of the following will activate the button: mouse click, touch (on a touch device), the enter key, or the space bar. If any of those methods does not work, it is a flaw that must be fixed. The button(s) can be scripted to activate any feature of the web site.

### Keyboard
Either the enter key or space bar can be used to activate the button.

### Screen Readers
When the screen reader user tabs to the button, the screen reader will say "button" then read the text within the button element. The button responds appropriately to keyboard commands and acts like an HTML button element.


## Key Accessibility Features

The ARIA buttons are designed to give an identical experience to standard HTML buttons.

## Developer & QA Notes
Use real `<button>` elements whenever possible, instead of ARIA buttons. Real buttons are already natively accessible, and you don't have to re-create their functionality.

### Visual Design
-   The button can be styled in any way, but should have the visual appearance of a button. It should be clearly different from non-button text. This pattern allows for different classes of buttons (primary and secondary) to apply different visual looks, if desired. These visual looks will not be conveyed to screen reader users, so if users must be aware of the difference between primary and secondary buttons, the text in the button must convey that difference.
-   The mouse cursor should be an arrow pointer, not a text selector or a hand icon (which would indicate a link).


## Browser and Screen Reader Support
- **JAWS + IE:** Full support
- **NVDA + Firefox:** Full support
- **VO + iOS:** Full support
- **VO + MacOS:** Full support
- **Narrator + Edge:** Full support

## Code

### HTML
```html
<p>
	<button class="deque-button" id="deque-button-primary-html">Primary Button (HTML)</button>
	<span role="button" class="deque-button deque-button-aria" id="deque-button-primary-aria">Primary Button (ARIA)</span>
</p>

<p>
	<button class="deque-button deque-button-secondary" id="deque-button-secondary-html" type="button">Secondary Button (HTML)</button>
	<span role="button" class="deque-button deque-button-secondary deque-button-aria" id="deque-button-secondary-aria">Secondary Button (ARIA)</span>

</p>

```

### CSS
```css
button.deque-button,
input[type='submit'].deque-button,
input[type='image'].deque-button,
input[type='reset'].deque-button,
[role='button'].deque-button {
	cursor: default;
	margin: 0;
	font-size: 15px;
	max-width: 374px;
	min-width: 120px;
	display: inline-block;
	padding: 9px 12px 10px;
	border: solid 1px #006cc1;
	border-radius: 0;
	overflow: hidden;
	line-height: 1;
	text-align: center;
	white-space: nowrap;
	vertical-align: bottom;
	outline: none;
	background: #006cc1;
	color: #ffffff;
	font-family: inherit;
}

button.deque-button:hover,
input[type='submit'].deque-button:hover,
input[type='image'].deque-button:hover,
input[type='reset'].deque-button:hover,
[role='button'].deque-button:hover {
	background-color: #fdf6e7;
	color: #004c87;
	border-color: #990000;
	margin: 0;
}

button.deque-button.deque-button-secondary,
input[type='submit'].deque-button.deque-button-secondary,
input[type='image'].deque-button.deque-button-secondary,
input[type='reset'].deque-button.deque-button-secondary,
[role='button'].deque-button.deque-button-secondary {
	background-color: #e6e6e6;
	color: #000000;
	border: 1px solid #006cc1;
}

button.deque-button.deque-button-secondary:hover,
input[type='submit'].deque-button.deque-button-secondary:hover,
input[type='image'].deque-button.deque-button-secondary:hover,
input[type='reset'].deque-button.deque-button-secondary:hover,
[role='button'].deque-button.deque-button-secondary:hover {
	background-color: #fdf6e7; 
	color: #004c87; 
	border-color: #990000; 
	border: 2px solid red; 
}

button.deque-button:focus, 
input[type='submit'].deque-button:focus,
input[type='image'].deque-button:focus,
input[type='reset'].deque-button:focus,
[role='button'].deque-button:focus { 
	outline: 1px dashed #000000; 
}

```

### JavaScript
```JavaScript

/* Specify which buttons */
var primaryHtml = document.getElementById('deque-button-primary-html'); 
var primaryAria = document.getElementById('deque-button-primary-aria'); 
var secondaryHtml = document.getElementById('deque-button-secondary-html'); 
var secondaryAria = document.getElementById('deque-button-secondary-aria'); 

/* Define the functionality for the buttons */ 
function primaryHtmlHandler() { 
	alert('You clicked the primary HTML button');
} 

function primaryAriaHandler() {
	alert('You clicked the primary ARIA button'); 
}

function secondaryHtmlHandler() {
	alert('You clicked the secondary HTML button');
}

function secondaryAriaHandler() {
	alert('You clicked the secondary ARIA button');
}

/* Initialize buttons */
deque.button.initializeButton(primaryHtml, primaryHtmlHandler); 
deque.button.initializeButton(primaryAria, primaryAriaHandler); 
deque.button.initializeButton(secondaryHtml, secondaryHtmlHandler); 
deque.button.initializeButton(secondaryAria, secondaryAriaHandler);

```