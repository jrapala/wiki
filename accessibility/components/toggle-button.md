# Button (Toggle)
## What Is It?
A toggle button is similar to a checkbox because it is either selected or not selected, and every toggle button can operate independent of all other toggle buttons. In fact, a checkbox can be an acceptable alternative to a toggle button.


## Expected Operation
### Keyboard
Either the enter key or space bar can be used to mark the button as pressed or not pressed.

### Screen Readers
When the screen reader user tabs to the button, the screen reader will say "toggle button," read the text within the button element, and will communicate the button's toggle state. The exact wording varies from one screen reader to the next. It could say "on" or "off" (Narrator), or "pressed" or "not pressed" (NVDA), or similar.


## Key Accessibility Features
-   The ARIA buttons are designed to give an identical experience to standard HTML buttons.
-   The attribute `aria-pressed` is set to `true` or `false`, to indicate the current state.

## Developer & QA Notes
Use real `<button>` elements whenever possible, instead of ARIA buttons. Real buttons are already natively accessible, and you don't have to re-create their functionality.

### Visual Design
- The toggle button can be styled in any way, but should have the visual appearance of a button. It should be clearly different from non-button text.
- The toggle state — the difference between selected and not selected — must be visually indicated in a way that does not rely on color alone. Users with low contrast vision, color-blindness, or other visual disabilities may not be able to discern the differences between colors. Also, Windows High Contrast Mode will override text and background colors, rendering them useless as a way to distinguish the toggle states. Methods that work visually include using an `<img>` element or a font icon in the CSS. In the implementation here, a checkmark icon is used in addition to the color change of blue to black when the button is selected.
- The mouse cursor should be an arrow pointer, not a text selector or a hand icon (which would indicate a link).


## Browser and Screen Reader Support
- **JAWS + IE:** Full support
- **NVDA + Firefox:** Full support
- **VO + iOS:** Standard button does not read aria-pressed state correctly. Bug in iOS where it won't work unless `role="button"` is present.
- **VO + MacOS:** Not supported. The state change is not spoken.
- **Narrator + Edge:** Not supported. The state change is not spoken.

## Code

### HTML
```html
<p>
	<button class="deque-button deque-button-toggle" id="toggleHtml">Toggle Button (HTML)</button>
	<span role="button" class="deque-button deque-button-toggle" id="toggleAria">Toggle Button (ARIA)</span>
</p>
```

### CSS
```css
button.deque-button.deque-button-toggle, 
[role='button'].deque-button.deque-button-toggle { 
	min-width: auto; 
} 

button.deque-button.deque-button-toggle[aria-pressed='true'], 
[role='button'].deque-button.deque-button-toggle[aria-pressed='true'] { 
	background-color: #2f2f2f; 
	color: #ffffff; 
	border: 1px solid #000000; 
}

button.deque-button.deque-button-toggle[aria-pressed='true']:hover, 
[role='button'].deque-button.deque-button-toggle[aria-pressed='true']:hover { 
	background-color: #2f2f2f; 
	color: #ffffff; 
}

button.deque-button.deque-button-toggle[aria-pressed='true']::before, 
[role='button'].deque-button.deque-button-toggle[aria-pressed='true']::before { 
	font-family: 'mwf-glyphs'; 
	content: '\E73E'; 
	font-size: 12px; 
	font-weight: bold; 
	padding-right: 5px; 
	float: left; 
}

.deque-button {
	font-family: system-ui;
}

button.deque-button, 
input[type='submit'].deque-button, 
input[type='image'].deque-button, 
input[type='reset'].deque-button, 
[role='button'].deque-button { 
	white-space: normal;
}
```

### JavaScript
```JavaScript

var toggleHtml = document.getElementById('toggleHtml');
var toggleAria = document.getElementById('toggleAria');

function toggleHandler(e) { 
	var currentlyPressed = e.target.getAttribute('aria-pressed') === 'true'; 
	return !currentlyPressed; 
} 

deque.button.initializeToggleButton(toggleHtml, toggleHandler); 
deque.button.initializeToggleButton(toggleAria, toggleHandler);

```