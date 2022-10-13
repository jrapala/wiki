# Alert
## What Is It?
An alert is a special type of ARIA live region. Screen readers will announce the text inside the alert, without moving the focus to the alert message. Alerts are usually styled to visually stand out from the rest of the content, to make them obvious when they appear.


## Expected Operation
### Activation
An alert can be activated by a user action (such as clicking on a button), a timed event, or other circumstance.

### Keyboard
The focus stays where it is. The focus does not move anywhere when an alert is activated.

### Screen Readers
An alert is a special kind of assertive ARIA live region, so screen readers should immediately interrupt anything they were previously saying and instead read the announcement. Most screen readers say "Alert," before reading the text inside the alert.


## Key Accessibility Features
- An empty container marked as role="alert"
- Text is injected into the container in response to a trigger event.
- Screen readers announce the text.

## Developer & QA Notes
### Visual Design
- A text message displays on the screen, within the user's current viewport and near the current keyboard focus and/or mouse cursor, so that it is visible immediately, even for screen magnifier users, who can see only a small portion of the screen at a time.
- The alert should be visually distinct from the surrounding content, and subjectively speaking it should look like an alert message. Icons can help convey the overall meaning of the message.

### The Clarity of the Text Message
In this example, three different types of visual styling are shown: for success messages, for general information messages, and for error messages. The styling is purely visual. The icons (a checkmark for success messages, the letter "I" with a circle around it for info messages, and an exclamation point in a triangle for error messages) are generated via CSS, so they will not be conveyed to screen readers, and there is no way to provide alternative text for them. Overall, there is no semantic way to mark messages as success, info, or alert for the benefit of screen reader users, so if the distinction between the types of alerts is important, you should add words (like "warning" or "congratulations") within the message to convey the meaning. Examples:

-   "Warning! This action cannot be undone."
-   "Congratulations! Your submission was successful."

### The Length of the Message
Alert messages should be brief. Screen reader users can interrupt an alert message (e.g. by typing a character or the Control key), and there is no way to tell a screen reader to continue to read an alert message once it has been interrupted. If the message is still on the screen, users can navigate to the message to have that screen reader read it as regular text, but users who are blind may have a hard time finding the message.

### Visual Proximity
Ensure the alert appears where users can see it. It should appear somewhere near the user's current visual and/or keyboard focus. This is especially true for screen magnification users who can only see a small segment of the screen at a time.

If the user is at the bottom of a long page and if the alert appears at the top of the page, above the scroll, where it is not even visible to the user, chances are that sighted users won't know the alert ever appeared.

### The Timeout Option
In most circumstances, it's best to avoid the timeout option, because users may miss the message entirely, or not finish reading it before it disappears. If you do set a timeout, make the duration generous, to maximize the time it is available.

The downside to not using the timeout is that screen reader users may come across the text again in the context of reading the document, and they may think that they're hearing a new alert, when in fact it is an old alert that stayed on the screen. You must balance the needs of sighted users (who may require the alert to remain visible) with screen reader users (who will hear the alert right away, and don't need to hear it again).

### Multiple Alert Messages
It's best to have only one alert message visible at a time because some screen readers will read all of the available alert messages each time that a new alert message is activated. When activating a new alert, it's best to deactivate any other alerts first.

### Alert versus Alert Dialog
**An alert** (`role="alert"`) does not require a user action. It does not move the keyboard focus. No part of the page is hidden or obscured or made unavailable at any time.

**An alert dialog** (`role="alertdialog"`) requires a user action. It acts like a regular dialog, but it is supposed to convey more of a sense of urgency. Screen readers typically say "Alert dialog" when an alert dialog pops up. The focus moves to the dialog and the user is required to take action. Usually this means clicking a button, even if it may just be an "OK" button or a "Close" button. The user cannot navigate out of the dialog with the tab key, and screen reader users cannot use keyboard shortcuts to access semantic elements (headings, landmarks, form elements, etc.) outside of the dialog.

### Automated Testing
-   When using an automated tool like the aXe browser extension, be sure to run two separate tests: one when the alert is inactive AND another when the alert is active.
-   Automated tests should supplement, not replace manual tests, taking into account the notes in this section.

## Browser and Screen Reader Support
- **JAWS + IE:** Full Support
- **NVDA + Firefox:** Full Support
- **VO + iOS:** Full Support
- **VO + MacOS:** Full Support
- **Narrator + Edge:** Full Support

## Code
### HTML
```html
<div id="deque-alert" class="deque-alert" role="alert">
	<div id="showSuccess" class="deque-alert-success">Congratulations! You clicked the button successfully.</div>
	<div id="showInfo" class="deque-alert-info">Here is some information you should be aware of.</div>
	<div id="showError" class="deque-alert-error">Error: You did something wrong.</div>
	<div id="clearAlerts" class="visuallyhidden">Alert message cleared.</div>
<p>
	<label for="useTimeoutInput">Automatically close the alert after 5 seconds</label>
	<input type="checkbox" id="useTimeoutInput" value="5000">
</p>
<p>
	<button id="showSuccess" class="deque-button">Show success alert</button>
	<button id="showInfo" class="deque-button">Show info alert</button>
	<button id="showError" class="deque-button">Show error alert</button>
	<button id="clearAlerts" class="deque-button deque-button-secondary">Clear alert</button>
</p>

<div class="deque-alert-group">
	<div id="deque-alert" class="deque-alert" role="alert" aria-live="polite"></div>
	<p>
		<label for="useTimeoutInput">Automatically close the alert after 5 seconds</label>
		<input type="checkbox" id="useTimeoutInput" value="5000">
	</p>
	<p>
		<button data-id="showSuccess" class="deque-button" data-reference-class="deque-alert-success" data-html="Congratulations! You clicked the button successfully.">Show success alert</button>
		<button data-id="showInfo" class="deque-button" data-reference-class="deque-alert-info" data-html="Here is some information you should be aware of.">Show info alert</button>
		<button data-id="showError" class="deque-button" data-reference-class="deque-alert-error" data-html="Error: You did something wrong.">Show error alert</button>
		<button data-id="clearAlerts" class="deque-button deque-button-secondary" data-reference-class="visuallyhidden" data-html="Alert message cleared.">Clear alert</button>
	</p>
</div>

```

### CSS
```css
.deque-alert [class^='deque-alert']:not(:empty) {
	padding: 10px 10px 10px 60px;
	min-height: 24px;
}

.deque-alert [class^='deque-alert']:not(:empty)::before {
	font-family: 'mwf-glyphs';
	font-size: 36px;
	margin: 0 0 0 -50px;
	position: relative;
	top: 0;
	display: inline-block;
	float: left;
}

.deque-alert .deque-alert-info {
	padding: 10px;
	min-height: 74px;
	border: 1px solid #006cc1;
	background-color: #fafdff;
}
	
.deque-alert .deque-alert-info::before {
	content: '\E946';
	color: #006cc1;
}

.deque-alert .deque-alert-error {
	border: 1px solid #ae0d23;
	background-color: #fffad2;
}

.deque-alert .deque-alert-error::before {
	content: '\E814';
	color: #ae0d23;
}

.deque-alert .deque-alert-success {
	border: 1px solid #0c5e0c;
	background-color: #f2fdf2;
}

.deque-alert .deque-alert-success::before {
	content: '\E8FB';
	color: #0c5e0c;
}

```

### JavaScript
```JavaScript

function createAlert(message) {
	var classes = arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : [];
	var timeout = arguments[2];
	
	var output = document.createElement("div");
	
	classes.forEach(function (c) {
		return output.classList.add(c);
	});
	
	output.innerHTML = message;
	if (timeout) {
		setTimeout(function () {
			if (output.parentElement) {
				output.remove();
			}
		}, timeout); }
		
		return output;
	}
}
	
function createHTMLAlert(element, alertRegion, timeout) {
	clearAlerts(alertRegion);
	
	if (element.getAttribute("data-id")) {
		var alertBox = '<div id="' + element.getAttribute("data-id") + '" class="' + element.getAttribute("data-reference-class") + '">' + element.getAttribute("data-html") + "</div>";
		document.getElementById("deque-alert").innerHTML += alertBox;
	}
	
	var activeElementList = alertRegion.querySelectorAll( "div:not(.dequ-hidden)" );
	if (activeElementList) {
		if (activeElementList.length > 0) {
			[].slice
			.call(activeElementList)
			.forEach(function (eachChildElement) {
				if (eachChildElement.getAttribute("data-msg")) { 
					eachChildElement.innerHTML = eachChildElement.getAttribute("data-msg"); 
				} 
			});
		}
	}
	
	if (timeout) {
		setTimeout(function () {
			var activeElementList = alertRegion.querySelectorAll( "div:not(.dequ-hidden)" );
			if (activeElementList) {
				if (activeElementList.length > 0) {
					[].slice
					.call(activeElementList)
					.forEach(function (eachChildElement) { 
						if (eachChildElement.getAttribute("data-msg")) { 
							eachChildElement.innerHTML = eachChildElement.getAttribute("data-msg");
						}
						eachChildElement.classList.add("deque-hidden");
						eachChildElement.classList.remove("deque-show-block");
						eachChildElement.innerHTML = "";
					});
				}
			}
		}, timeout);
	}
}

function activateAllAlerts() {
	var alerts = document.querySelectorAll(".deque-alert-group");
	
	for (var i = 0; i < alerts.length; i++) {
		var useTimeoutInput = alerts[i].querySelector("#useTimeoutInput");
		var buttons = alerts[i].querySelectorAll(".deque-button");
		var alertRegion = alerts[i].querySelector(".deque-alert");
		var alertBoxes = alertRegion.querySelectorAll("div");
		
		for (var k = 0; k < alertBoxes.length; k++) {
			alertBoxes[k].classList.add("deque-hidden");
			alertBoxes[k].setAttribute("data-msg", alertBoxes[k].innerHTML);
			alertBoxes[k].innerHTML = "";
		}
		for (var j = 0; j < buttons.length; j++) {
			buttons[j].addEventListener( "click", showAlertMessage.bind( null, buttons[j], useTimeoutInput, alertRegion ) );
		}
	}
}

function showAlertMessage(id, useTimeoutInput, alertRegion) {
	var useTimeout;
	if (useTimeoutInput) {
		if (useTimeoutInput.getAttribute("type") == "checkbox") {
			useTimeout = useTimeoutInput.checked;
		} else {
			useTimeout = true;
		}
	} else { 
		useTimeout = false;
	}
	
	clearAlerts(alertRegion);
	if (useTimeout) {
		var timeoutValue = useTimeoutInput.getAttribute("value");
		if (timeoutValue) {
			createHTMLAlert(id, alertRegion, timeoutValue);
		} else {
			createHTMLAlert(id, alertRegion, 5000);
		}
	} else {
		createHTMLAlert(id, alertRegion);
	}
}

function clearAlerts(alertRegion) {
	var alertBoxes = alertRegion.querySelectorAll("div");
	for (var k = 0; k < alertBoxes.length; k++) { 
		alertBoxes[k].classList.remove("deque-show-block"); 
		alertBoxes[k].innerHTML = "";
		alertBoxes[k].classList.add("deque-hidden");
	} 
}

activateAllAlerts();

```