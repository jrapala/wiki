# Dialog (Message Dialog)
## What Is It?
This page shows a simple modal pattern using the (role="dialog") ARIA attribute. A modal is a dialog box/popup window that is displayed on top of the current page and requires a user action to close it. The dialog is only available to users when the modal is active. When the modal is active, the rest of the page is unavailable by mouse, keyboard, touch, or screen reader.

See also the [Official W3C documentation about ARIA dialog widgets. ![opens in a new window](https://dequeuniversity.com/assets/images/template/courses2014/new-window.png)](https://www.w3.org/TR/wai-aria-practices-1.1/#dialog_modal)


## Expected Operation
### Activation
Dialogs are usually activated by user actions (such as activating a button), but may also be the result of a timed event (such as a session timeout warning) or other events.

### Visual Design
The dialog should be visually set apart from the rest of the page. Usually a dialog is centered on the page and given a visual border. The background is usually obscured, by making it appear greyed out or washed out or blurry.

### Keyboard
The tab key must be constrained within the dialog. Users cannot tab out of the dialog. The focus goes to the dialog when it is activated, either to the dialog container, or to the dialog's heading, or to the first focusable element within the dialog, or to the default button in the dialog. There is some leeway in deciding where to send the focus. When the dialog is closed, the focus returns to the original trigger button, or to some other logical location if the button is no longer available or if the dialog was activated by something other than a button.

### Screen Readers
Screen readers will announce "Dialog" or "Alert Dialog" (depending on the dialog type), then they will announce the name of the dialog (usually designated by an `aria-labelledby` reference to the first heading in the dialog, but `aria-label` can also work), then they will announce the `aria-describedby` value, if present. If the focus was sent to a button, the screen reader will also read the button text. If the focus is sent to the container, the screen reader may begin to read the entire text content of the dialog, or may pause to wait for the user to start to navigate through the dialog.

If a screen reader user navigates by elements such as headings, landmarks, links, form elements, etc., the only elements available when the dialog is open are the elements in the dialog itself. There must not be a way to access anything outside of the dialog with screen reader keyboard shortcuts.

This covers the `dialog` pattern and the `alertdialog` pattern. With a little bit more tweaking it will work for the `non-modal dialog` pattern as well.

## Key Accessibility Features
- The dialog is modal: keyboard users cannot use the tab key to navigate outside of the dialog and screen reader users cannot use keyboard shortcuts (e.g. for headings, landmarks, links, etc.) to navigate outside of the dialog.
- The focus is sent to the dialog when it is activated.
- The focus returns back to the original trigger when the dialog is closed.


## Developer & QA Notes
In order to validate this control, you must test manually with screen reader software, a mouse, and a keyboard. Depending on your unique configuration, all user interface elements within the dialog must be tested for proper focus and any custom behavior functionality.

**Important:** You have to keep a reference to the triggerElement activated to show the dialog box so that you can return focus to it after the dialog is closed.

This was written so that you never have to add more than one dialog box to your application. Since they are modal you will never have more than one up at a time. As a result, you can keep your HTML relatively clean and push all of your configuration into the JavaScript. That way, every time you show this dialog box you tell it all the details it needs in order to render itself correctly. If you DO want more than one (for example, for styling purposes) you can still do that without conflict.


## Browser and Screen Reader Support
- **JAWS + IE:** Full support
- **NVDA + Firefox:** Full support
- **VO + iOS:** Full support (but reads the focused item first, and role="document" is treated as a landmark, if present)
- **VO + MacOS:** Full support
- **VO + iPad iOS:** Focus is not on the dialog (manually need to set the focus)
- **Narrator + Edge:** The accessible name and description are not announced. The role is announced.

## Code
### HTML
```html
<div id="deque-dialog" class="deque-dialog" data-id="first-deque-dialog">
	<div class="deque-dialog-screen-wrapper"></div>
	<div class="deque-dialog-screen">
		<h1 id="dialogHeading" class="deque-dialog-heading">Simple Dialog</h1>
		<p class="deque-dialog-description" id="dialogDescription">Please enter your first and last name.</p>
		<form class="deque-dialog-content">
			<label for="nameInput">First Name</label>
			<input class="deque-input" id="nameInput" type="text" data-trap-enter="true">
			<label for="lastNameInput">Last Name</label>
			<input class="deque-input" id="lastNameInput" type="text" data-trap-enter="true">
		</form>
		<div class="deque-dialog-buttons">
			<button class="deque-button deque-dialog-button-submit">Submit</button>
			<button class="deque-button deque-dialog-button-cancel">Cancel</button>
			<button class="deque-dialog-button-close" aria-label="Close dialog"><span aria-hidden="true"></span></button>
		</div>
	</div>
</div>

<div>
	<button id="deque-dialog-trigger" class="deque-dialog-trigger hideFromQuizHint deque-button" aria-controls="deque-dialog" data-id="first-deque-dialog">Show Dialog!</button>
</div>
```

### CSS
```css
.deque-dialog-buttons button {
     cursor: pointer;
     color: #ffffff;
     background-color: #006cc1;
     font-size: 15px;
     max-width: 374px;
     min-width: 120px;
     display: inline-block;
     padding: 9px 12px 10px;
     border: solid 1px transparent;
     border-radius: 0;
     overflow: hidden;
     line-height: 1;
     text-align: center;
     white-space: nowrap;
     vertical-align: bottom;
     outline: none;
}
 .deque-dialog-buttons button:hover {
     cursor: default;
}
 .deque-dialog-trigger {
     cursor: pointer;
     background-color: #006cc1;
     font-size: 15px;
     max-width: 374px;
     min-width: 120px;
     display: inline-block;
     margin-top: 0;
     padding: 9px 12px 10px;
     border: solid 1px transparent;
     border-radius: 0;
     overflow: hidden;
     line-height: 1;
     text-align: center;
     white-space: nowrap;
     vertical-align: bottom;
     outline: none;
}
 .deque-dialog-alert-trigger {
     cursor: pointer;
     background-color: #006cc1;
     font-size: 15px;
     max-width: 374px;
     min-width: 120px;
     display: inline-block;
     margin-top: 0;
     padding: 9px 12px 10px;
     border: solid 1px transparent;
     border-radius: 0;
     overflow: hidden;
     line-height: 1;
     text-align: center;
     white-space: nowrap;
     vertical-align: bottom;
     outline: none;
}
 .deque-dialog-message-trigger {
     cursor: pointer;
     background-color: #006cc1;
     font-size: 15px;
     max-width: 374px;
     min-width: 120px;
     display: inline-block;
     margin-top: 0;
     padding: 9px 12px 10px;
     border: solid 1px transparent;
     border-radius: 0;
     overflow: hidden;
     line-height: 1;
     text-align: center;
     white-space: nowrap;
     vertical-align: bottom;
     outline: none;
}
 .deque-dialog-message-alert-trigger {
     cursor: pointer;
     background-color: #006cc1;
     font-size: 15px;
     max-width: 374px;
     min-width: 120px;
     display: inline-block;
     margin-top: 0;
     padding: 9px 12px 10px;
     border: solid 1px transparent;
     border-radius: 0;
     overflow: hidden;
     line-height: 1;
     text-align: center;
     white-space: nowrap;
     vertical-align: bottom;
     outline: none;
}
 .deque-dialog .deque-dialog-heading, .deque-dialog-alert .deque-dialog-heading, .deque-dialog-message .deque-dialog-heading, .deque-dialog-message-alert .deque-dialog-heading {
     font-size: 20px;
     color: #2f2f2f;
     font-weight: 200;
     line-height: normal;
     padding: 0;
}
 .deque-dialog.deque-dialog-noheading, .deque-dialog-alert.deque-dialog-noheading, .deque-dialog-message.deque-dialog-noheading, .deque-dialog-message-alert.deque-dialog-noheading {
     text-align: center;
     font-size: 1.1em;
}
 .deque-dialog-notify {
     background-color: #ff0000;
     color: #ffffff;
}
 .deque-wrapper .feedback-holder {
     background-color: #fefee7;
     border: 2px solid #f5f573;
     padding: 20px 40px;
     display: block;
}
 .deque-wrapper .feedback-holder:empty {
     display: none;
}
 .deque-wrapper form.deque[data-feedback-type] .feedback-holder {
     outline: none;
}
 .deque-wrapper .container .feedback-holder .info {
     border-color: #000066;
     background-color: #eeeeff;
     border: 1px solid;
     border-radius: 5px;
     margin: 5px auto;
     padding: 5px;
}
 .deque-wrapper .container .feedback-holder .error {
     border-color: #660000;
     background-color: #fff5f5;
     border: 1px solid #ff9494;
     margin: 5px auto;
     padding: 5px;
}
 .deque-wrapper .container .feedback-holder .success {
     border-color: #006600;
     background-color: #eeffee;
     border: 1px solid;
     border-radius: 5px;
     margin: 5px auto;
     padding: 5px;
}
 .deque-dialog-buttons .deque-dialog-button-close {
     font-family: 'mwf-glyphs';
     font-size: 20px;
     width: 20px;
     height: 20px;
     right: 8px;
     top: 8px;
     position: absolute;
     background-color: transparent;
     display: block;
     overflow: visible;
     max-width: 20px;
     min-width: 20px;
     padding: 0;
}
 .deque-dialog-buttons .deque-dialog-button-close span::before {
     content: '\E711';
     color: #006cc1;
     display: inline-block;
     font-size: 20px;
     width: 20px;
     height: 20px;
}
 .deque-dialog-buttons .deque-dialog-button-close:focus {
     outline: 1px dashed #000000;
}
 .deque-show-block .deque-dialog-screen-wrapper {
     display: block;
     content: '';
     position: fixed;
     top: 0;
     bottom: 0;
     left: 0;
     right: 0;
     background: rgba(0, 0, 0, 0.7);
     transition: opacity 200ms;
     opacity: 1;
}
 .deque-hidden .deque-dialog-screen-wrapper {
     display: none;
}
 .deque-show-block .deque-dialog-screen {
     display: block;
     position: fixed;
     left: 50%;
     top: 50%;
     transform: translate(-50%, -50%);
     background: #ffffff;
     border: 1px solid #0078d7;
     margin: 0 auto;
     max-height: 760px;
     max-width: 546px;
     padding: 24px;
     z-index: 1000;
     outline: none;
     width: 80%;
     opacity: 1;
}
 .deque-show-block .deque-dialog-screen .deque-dialog-buttons {
     margin-top: 20px;
}
 .deque-hidden .deque-dialog-screen {
     display: none;
}


```

### JavaScript
```JavaScript

 //import {createContentArea} from './components/contentArea';
 function createDialog(id, role) {
     var screen = (0, _lightboxScreen.createScreen)();
     var alert = document.getElementById(id);
     var dialogButtons = alert.querySelector(".deque-dialog-buttons");
     var buttons = dialogButtons.querySelectorAll("button");
     for (var x = 0; x < buttons.length; x++) {
         buttons[x].addEventListener("click", hide);
     }

     // close button
     var xButton = document.createElement("span");

     function setInitialFocus(config) {
         if (!config.isAlert) {
             var target = (0, _focusUtils.getFirstFocusableChild)(alert);
             if (target.classList.contains("deque-dialog-button-close")) {
                 target = (0, _focusUtils.getNextFocusableChild)(alert, target);
             }

             if (target) {
                 return target.focus();
             }
         }

         if (config.isAlert && config.isDetail) {
             //return content.focus();
         }

         if (buttonBar.getFirstButton()) {}
         //return buttonBar.getFirstButton().focus();

         //alert.focus();
         if (
             alert
             .querySelector(".deque-dialog-screen")
             .classList.contains("imageMagnifier")
         ) {
             alert.querySelector(".deque-dialog-content").focus();
         } else {
             alert.querySelector(".deque-dialog-screen").focus();
         }

         alert.querySelector(".deque-dialog-heading").focus();
     }

     function reset() {
         clearClasses();
         screen.clear();
     }

     function addClasses(classes) {
         if (classes.isArray) {
             classes.forEach(function(item) {
                 return alert.classList.add(item);
             });
         } else {
             alert.classList.add(classes);
         }
     }

     function clearClasses() {
         var toRemove = [];
         for (var i = 0; i < alert.classList.length; i++) {
             toRemove.push(alert.classList[i]);
         }

         toRemove.forEach(function(c) {
             alert.classList.remove(c);
         });

         addClasses(role);
     }

     function setRole(role) {
         alert.setAttribute("role", role);
     }

     function hideCloseButton() {
         xButton.classList.add("deque-hidden");
     }

     function showCloseButton() {
         xButton.classList.remove("deque-hidden");
     }

     //var content = createContentArea(alert);

     var returnFocusTo;

     xButton.addEventListener("click", handleEscape);

     var buttonBar = alert.querySelector(".deque-dialog-buttons");
     buttonBar = (0, _buttonBar.createButtonBar)(buttonBar);

     function show(config) {
         if (config.classes) {
             addClasses(config.classes);
         }

         if (config.describedby) {
             //alert.setAttribute('aria-describedby', config.describedby);
         }

         if (config.labelledby) {
             //alert.setAttribute('aria-labelledby', config.labelledby);
         }

         if (config.hideCloseButton) {
             hideCloseButton();
         } else {
             showCloseButton();
         }

         if (config.wrapperID) {
             document
                 .getElementById(config.wrapperID)
                 .setAttribute("aria-hidden", "true");
         }

         // config.isAlert ? setRole("alertdialog") : setRole("dialog");
         screen.show();
         document.body.appendChild(alert);
         alert.setAttribute("aria-hidden", "false");
         alert.classList.remove("deque-hidden");
         alert.classList.add("deque-show-block");

         if (
             alert
             .querySelector(".deque-dialog-screen")
             .classList.contains("imageMagnifier")
         ) {
             alert
                 .querySelector(".deque-dialog-content")
                 .setAttribute("tabindex", "0");
         } else {
             alert
                 .querySelector(".deque-dialog-screen")
                 .setAttribute("tabindex", "0");
             alert
                 .querySelector(".deque-dialog-screen")
                 .setAttribute("role", "dialog");
         }

         setInitialFocus(config);
     }

     function hide() {
         if (alert.hasAttribute("aria-describedby")) {
             alert.removeAttribute("aria-describedby");
         }

         reset();
         alert.setAttribute("aria-hidden", "true");
         alert.classList.add("deque-hidden");

         returnFocusTo.focus();
     }

     function handleEscape() {
         hide();
     }

     function keyUpHandler(e) {
         if (e.which === 27) {
             handleEscape();
             e.stopPropagation();
         }
     }

     alert.addEventListener("keyup", keyUpHandler);

     (0, _focusUtils.initTabTrap)(alert);

     return function(triggerElement, config) {
         returnFocusTo = triggerElement;
         // make sure we never end up with a case where a non-alert box is treated
         // as a simple alert.
         if (!config.isAlert) {
             config.isDetail = true;
         }

         show(config);
     };
 }

 var activateHander = false;

 var activateDequeDialogButtonClickHandlers =
     function activateDequeDialogButtonClickHandlers() {
         var bodyChilds = document.body.children;
         //var dequeDialogMessageAlert = document.querySelector('.deque-dialog-message-alert') || null;
         //var dequeDialogMessage = document.querySelector('.deque-dialog-message') || null;
         //var dequeDialogAlert = document.querySelector('.deque-dialog-alert') || null;
         //var dequeDialog = document.querySelector('.deque-dialog') || null;
         var dequeDialogBackgroundLayer =
             document.querySelectorAll(".deque-dialog-screen-wrapper") || null;
         var dequeDialogCloseButton =
             document.querySelectorAll(".deque-dialog-button-cancel") || null;

         var ariaHiddenList = null;
         var ariaHiddenFalseList = null;
         var ariaHiddenTrueList = null;
         var triggerNode = null;

         var dequeJsAria = "deque-js-aria";
         var dequeJsAriaTrue = "deque-js-aria-true";
         var dequeJsAriaFalse = "deque-js-aria-false";

         /*
	  var triggerList = [document.querySelector('#deque-dialog-message-alert-trigger'), document.querySelector('#deque-dialog-message-trigger'), document.querySelector('#deque-dialog-alert-trigger'), document.querySelector('#deque-dialog-trigger')];
	  */

         if (
             document.querySelector("#deque-dialog-message-alert-trigger") ||
             document.querySelector("#deque-dialog-message-trigger") ||
             document.querySelector("#deque-dialog-alert-trigger") ||
             document.querySelector("#deque-dialog-trigger")
         ) {
             [].slice
                 .call(
                     document.querySelectorAll(
                         "#deque-dialog-message-alert-trigger, #deque-dialog-message-trigger, #deque-dialog-alert-trigger, #deque-dialog-trigger"
                     )
                 )
                 .forEach(function(triggerBtn) {
                     triggerBtn.addEventListener("click", function(e) {
                         triggerNode = e.currentTarget;

                         [].slice.call(bodyChilds).forEach(function(node) {
                             //if(node != dequeDialogMessageAlert && node != dequeDialogMessage && node != dequeDialogAlert && node != dequeDialog && (node.nodeName).toLowerCase() != 'link' && (node.nodeName).toLowerCase() != 'script') {
                             if (
                                 node.getAttribute("data-id") !=
                                 triggerNode.getAttribute("data-id")
                             ) {
                                 var nodeAriaHidden = node.getAttribute("aria-hidden");
                                 if (
                                     !(
                                         node.classList.contains(dequeJsAria) ||
                                         node.classList.contains(dequeJsAriaTrue) ||
                                         node.classList.contains(dequeJsAriaFalse)
                                     )
                                 ) {
                                     if (!nodeAriaHidden) {
                                         node.classList.add(dequeJsAria);
                                     } else if (nodeAriaHidden == "true") {
                                         node.classList.add(dequeJsAriaTrue);
                                     } else if (nodeAriaHidden == "false") {
                                         node.classList.add(dequeJsAriaFalse);
                                     }
                                 }
                             }
                             //}
                         });

                         var allAriaHiddenList = document.querySelectorAll(
                             "." +
                             dequeJsAria +
                             ", ." +
                             dequeJsAriaTrue +
                             ", ." +
                             dequeJsAriaFalse
                         );
                         [].slice
                             .call(allAriaHiddenList)
                             .forEach(function(ariaNode) {
                                 ariaNode.setAttribute("aria-hidden", "true");
                             });

                         ariaHiddenList = document.querySelectorAll(
                             "." + dequeJsAria
                         );
                         ariaHiddenFalseList = document.querySelectorAll(
                             "." + dequeJsAriaFalse
                         );
                         ariaHiddenTrueList = document.querySelectorAll(
                             "." + dequeJsAriaTrue
                         );
                     });
                 });

             setTimeout(function() {
                 if (
                     document
                     .querySelector(".deque-dialog-screen")
                     .classList.contains("imageMagnifier")
                 ) {
                     document
                         .querySelector(".deque-dialog-content")
                         .setAttribute("tabindex", "0");
                     document
                         .querySelector(".deque-dialog-content")
                         .setAttribute("role", "dialog");
                     document.querySelector(".deque-dialog-content").focus();
                 } else {
                     document
                         .querySelector(".deque-dialog-screen")
                         .setAttribute("tabindex", "0");
                     document
                         .querySelector(".deque-dialog-screen")
                         .setAttribute("role", "dialog");
                     document.querySelector(".deque-dialog-screen").focus();
                 }
             }, 1000);
         }

         var dequeCloseBtnList = document.querySelectorAll(
             ".deque-dialog-button-close"
         );

         if (dequeCloseBtnList.length > 0) {
             [].slice.call(dequeCloseBtnList).forEach(function(btn) {
                 btn.addEventListener("click", function() {
                     if (ariaHiddenList) {
                         [].slice.call(ariaHiddenList).forEach(function(ariaNode) {
                             ariaNode.removeAttribute("aria-hidden");
                             ariaNode.classList.remove(dequeJsAria);
                         });
                     }

                     if (ariaHiddenFalseList) {
                         [].slice
                             .call(ariaHiddenFalseList)
                             .forEach(function(ariaNode) {
                                 ariaNode.setAttribute("aria-hidden", "false");
                                 ariaNode.classList.remove(dequeJsAriaFalse);
                             });
                     }

                     if (ariaHiddenTrueList) {
                         [].slice
                             .call(ariaHiddenTrueList)
                             .forEach(function(ariaNode) {
                                 ariaNode.setAttribute("aria-hidden", "true");
                                 ariaNode.classList.remove(dequeJsAriaTrue);
                             });
                     }
                 });
             });
         }

         var dequeSubmitBtnList = document.querySelectorAll(
             ".deque-dialog-button-submit"
         );
         if (dequeSubmitBtnList.length > 0) {
             [].slice.call(dequeSubmitBtnList).forEach(function(btn) {
                 btn.addEventListener("click", function(e) {
                     e.preventDefault();
                     if (ariaHiddenList) {
                         [].slice.call(ariaHiddenList).forEach(function(ariaNode) {
                             ariaNode.removeAttribute("aria-hidden");
                             ariaNode.classList.remove(dequeJsAria);
                         });
                     }

                     if (ariaHiddenFalseList) {
                         [].slice
                             .call(ariaHiddenFalseList)
                             .forEach(function(ariaNode) {
                                 ariaNode.setAttribute("aria-hidden", "false");
                                 ariaNode.classList.remove(dequeJsAriaFalse);
                             });
                     }

                     if (ariaHiddenTrueList) {
                         [].slice
                             .call(ariaHiddenTrueList)
                             .forEach(function(ariaNode) {
                                 ariaNode.setAttribute("aria-hidden", "true");
                                 ariaNode.classList.remove(dequeJsAriaTrue);
                             });
                     }

                     if (dequeActiveDialogButton) {
                         dequeActiveDialogButton.focus();
                     }
                 });
             });
         }

         /* closing popup on keypress (ESC, etc.,)  and on clicking of background black layer */
         var KEY_CONFIG = {
             27: "ESC",
             //16: 'SHIFT'
         };

         document.body.addEventListener("keydown", function(e) {
             for (var key in KEY_CONFIG) {
                 if (key == e.keyCode) {
                     inPageCloseAction();
                 }
             }
         });

         var inPageCloseAction = function inPageCloseAction(bgLayer) {
             if (bgLayer) {
                 bgLayer.parentNode
                     .querySelector(".deque-dialog-button-close")
                     .click(); /* code to close the popup */
             } else {
                 [].slice
                     .call(document.querySelectorAll(".deque-dialog-button-close"))
                     .forEach(function(btnClose) {
                         btnClose.click();
                     });
             }
             [].slice.call(dequeCloseBtnList).forEach(function(closeBtn) {
                 closeBtn.click();
             });

             if (triggerNode) {
                 triggerNode.focus(); /* code  to retain focus to the trigger button */
             }
         };

         if (dequeDialogBackgroundLayer) {
             [].slice
                 .call(dequeDialogBackgroundLayer)
                 .forEach(function(bgLayer) {
                     bgLayer.addEventListener("click", function() {
                         inPageCloseAction(bgLayer);
                     });
                 });
         }

         if (dequeDialogCloseButton) {
             [].slice.call(dequeDialogCloseButton).forEach(function(clsBtn) {
                 clsBtn.addEventListener("click", function() {
                     inPageCloseAction(clsBtn);
                 });
             });
         }
     };

 var dequeActiveDialogButton = null;

 /*
  * activateAllDialogs(): This method has 4 types of Dialogs.
  * Simple Dialogs, Dialog Alerts, Dialog Messages and Dialog Messages Alerts
  * 1. Looks for all instances of respective classes and loops them over
  * with all details like description, headings and stores them in config class.
  * 2. For each button click, it gets the value of aria-control and creates and shows the respective dialog based on the config values.
  */
 function activateAllDialogs() {
     //activate all simple Dialogs
     var dialogs = document.querySelectorAll(".deque-dialog");

     for (var i = 0; i < dialogs.length; i++) {
         dialogs[i].classList.add("deque-hidden");

         var dialogDataId = "_" + dialogs[i].getAttribute("data-id");
         //dialogs[i].setAttribute('id', dialogs[i].getAttribute('id') + dialogDataId);
         document
             .querySelector(
                 'button[data-id="' + dialogs[i].getAttribute("data-id") + '"]'
             )
             .setAttribute("aria-controls", dialogs[i].getAttribute("id"));

         var dialogHeadingElement = dialogs[i].querySelector(
             ".deque-dialog-heading"
         );
         dialogHeadingElement.setAttribute(
             "id",
             dialogHeadingElement.getAttribute("id") + dialogDataId
         );

         var dialogDescriptionElement = dialogs[i].querySelector(
             ".deque-dialog-description"
         );
         dialogDescriptionElement.setAttribute(
             "id",
             dialogDescriptionElement.getAttribute("id") + dialogDataId
         );

         var dialogDescription;
         var dialogLabel;

         var configDialog = {
             describedby: dialogDescription,
             labelledby: dialogLabel,
             isAlert: false,
         };

         var triggerDialog = document.getElementsByClassName(
             "deque-dialog-trigger deque-button"
         );

         triggerDialog[i].addEventListener("click", function(event) {
             event.preventDefault();
             dequeActiveDialogButton = triggerDialog[i];
             var attributeDialog = this.getAttribute("aria-controls");
             var showDialog = createDialog(attributeDialog, "deque-dialog");

             var dequeDialogWidgetElement = document.querySelectorAll(
                 '.deque-dialog[data-id="' +
                 event.currentTarget.getAttribute("data-id") +
                 '"]'
             )[0];
             if (
                 dequeDialogWidgetElement.querySelector(".deque-dialog-heading")
             ) {
                 configDialog.describedby = dequeDialogWidgetElement
                     .querySelector(".deque-dialog-heading")
                     .getAttribute("id");
             } else {
                 configDialog.describedby = "";
             }
             if (
                 dequeDialogWidgetElement.querySelector(
                     ".deque-dialog-description"
                 )
             ) {
                 configDialog.labelledby = dequeDialogWidgetElement
                     .querySelector(".deque-dialog-description")
                     .getAttribute("id");
             } else {
                 configDialog.labelledby = "";
             }

             if (dequeDialogWidgetElement.querySelector("#nameInput")) {
                 var nameInputElement =
                     dequeDialogWidgetElement.querySelector("#nameInput");
                 var labelInputElement = dequeDialogWidgetElement.querySelector(
                     "label[for=" + nameInputElement.getAttribute("id") + "]"
                 );
                 var modifiedNameInputElementId =
                     nameInputElement.getAttribute("id") +
                     "_" +
                     event.currentTarget.getAttribute("data-id");
                 labelInputElement.setAttribute(
                     "for",
                     modifiedNameInputElementId
                 );
                 nameInputElement.setAttribute("id", modifiedNameInputElementId);
             }

             if (dequeDialogWidgetElement.querySelector("#lastNameInput")) {
                 var lastNameInputElement =
                     dequeDialogWidgetElement.querySelector("#lastNameInput");
                 var labelLastInputElement =
                     dequeDialogWidgetElement.querySelector(
                         "label[for=" + lastNameInputElement.getAttribute("id") + "]"
                     );
                 var modifiedLastNameInputElementId =
                     lastNameInputElement.getAttribute("id") +
                     "_" +
                     event.currentTarget.getAttribute("data-id");
                 labelLastInputElement.setAttribute(
                     "for",
                     modifiedLastNameInputElementId
                 );
                 lastNameInputElement.setAttribute(
                     "id",
                     modifiedLastNameInputElementId
                 );
             }

             showDialog(this, configDialog);
         });
     }

     //activate all dialog alerts
     var dialogAlerts = document.querySelectorAll(".deque-dialog-alert");
     for (var j = 0; j < dialogAlerts.length; j++) {
         dialogAlerts[j].classList.add("deque-hidden");

         dialogDataId = "_" + dialogAlerts[j].getAttribute("data-id");
         dialogAlerts[j].setAttribute(
             "id",
             dialogAlerts[j].getAttribute("id") + dialogDataId
         );
         document
             .querySelector(
                 'button[data-id="' +
                 dialogAlerts[j].getAttribute("data-id") +
                 '"]'
             )
             .setAttribute(
                 "aria-controls",
                 dialogAlerts[j].getAttribute("id")
             );

         dialogHeadingElement = dialogAlerts[j].querySelector(
             ".deque-dialog-heading"
         );
         dialogHeadingElement.setAttribute(
             "id",
             dialogHeadingElement.getAttribute("id") + dialogDataId
         );

         //dialogDescriptionElement = dialogAlerts[j].querySelector('.deque-dialog-description');
         //dialogDescriptionElement.setAttribute('id', dialogDescriptionElement.getAttribute('id') + dialogDataId);

         var dialogAlertDescription;
         var dialogAlertLabel;

         var configDialogAlert = {
             describedby: dialogAlertDescription,
             labelledby: dialogAlertLabel,
             isAlert: true,
         };

         var triggerDialogAlert = document.getElementsByClassName(
             "deque-dialog-alert-trigger deque-button"
         );

         triggerDialogAlert[j].addEventListener("click", function(event) {
             event.preventDefault();
             dequeActiveDialogButton = triggerDialogAlert[i];
             var attributeDialogAlert = this.getAttribute("aria-controls");
             var showDialogAlert = createDialog(
                 attributeDialogAlert,
                 "deque-dialog-alert"
             );
             var dequeDialogWidgetElement = document.querySelectorAll(
                 '.deque-dialog-alert[data-id="' +
                 event.currentTarget.getAttribute("data-id") +
                 '"]'
             )[0];
             if (
                 dequeDialogWidgetElement.querySelector(".deque-dialog-heading")
             ) {
                 configDialogAlert.describedby = dequeDialogWidgetElement
                     .querySelector(".deque-dialog-heading")
                     .getAttribute("id");
             } else {
                 configDialogAlert.describedby = "";
             }
             if (
                 dequeDialogWidgetElement.querySelector(
                     ".deque-dialog-description"
                 )
             ) {
                 configDialogAlert.labelledby = dequeDialogWidgetElement
                     .querySelector(".deque-dialog-description")
                     .getAttribute("id");
             } else {
                 configDialogAlert.labelledby = "";
             }

             showDialogAlert(this, configDialogAlert);
         });
     }

     //activate all dialog messages
     var dialogMessages = document.querySelectorAll(
         ".deque-dialog-message"
     );
     for (var k = 0; k < dialogMessages.length; k++) {
         dialogMessages[k].classList.add("deque-hidden");

         dialogDataId = "_" + dialogMessages[k].getAttribute("data-id");
         dialogMessages[k].setAttribute(
             "id",
             dialogMessages[k].getAttribute("id") + dialogDataId
         );
         document
             .querySelector(
                 'button[data-id="' +
                 dialogMessages[k].getAttribute("data-id") +
                 '"]'
             )
             .setAttribute(
                 "aria-controls",
                 dialogMessages[k].getAttribute("id")
             );

         dialogHeadingElement = dialogMessages[k].querySelector(
             ".deque-dialog-heading"
         );
         dialogHeadingElement.setAttribute(
             "id",
             dialogHeadingElement.getAttribute("id") + dialogDataId
         );

         dialogDescriptionElement = dialogMessages[k].querySelector(
             ".deque-dialog-description"
         );
         dialogDescriptionElement.setAttribute(
             "id",
             dialogDescriptionElement.getAttribute("id") + dialogDataId
         );

         var dialogMessageDescription;
         var dialogMessageLabel;

         var configDialogMessage = {
             describedby: dialogMessageDescription,
             labelledby: dialogMessageLabel,
             isAlert: false,
         };

         var triggerDialogMessage = document.getElementsByClassName(
             "deque-dialog-message-trigger deque-button"
         );

         triggerDialogMessage[k].addEventListener("click", function(event) {
             event.preventDefault();
             dequeActiveDialogButton = triggerDialogMessage[i];
             var attributeDialogMessage = this.getAttribute("aria-controls");
             var showDialogMessage = createDialog(
                 attributeDialogMessage,
                 "deque-dialog-message"
             );
             var dequeDialogWidgetElement = document.querySelectorAll(
                 '.deque-dialog-message[data-id="' +
                 event.currentTarget.getAttribute("data-id") +
                 '"]'
             )[0];
             if (
                 dequeDialogWidgetElement.querySelector(".deque-dialog-heading")
             ) {
                 configDialogMessage.describedby = dequeDialogWidgetElement
                     .querySelector(".deque-dialog-heading")
                     .getAttribute("id");
             } else {
                 configDialogMessage.describedby = "";
             }
             if (
                 dequeDialogWidgetElement.querySelector(
                     ".deque-dialog-description"
                 )
             ) {
                 configDialogMessage.labelledby = dequeDialogWidgetElement
                     .querySelector(".deque-dialog-description")
                     .getAttribute("id");
             } else {
                 configDialogMessage.labelledby = "";
             }
             showDialogMessage(this, configDialogMessage);
         });
     }

     //activate all dialog message alerts
     var dialogMessageAlerts = document.querySelectorAll(
         ".deque-dialog-message-alert"
     );
     for (var l = 0; l < dialogMessageAlerts.length; l++) {
         dialogMessageAlerts[l].classList.add("deque-hidden");

         dialogDataId = "_" + dialogMessageAlerts[l].getAttribute("data-id");
         dialogMessageAlerts[l].setAttribute(
             "id",
             dialogMessageAlerts[l].getAttribute("id") + dialogDataId
         );
         document
             .querySelector(
                 'button[data-id="' +
                 dialogMessageAlerts[l].getAttribute("data-id") +
                 '"]'
             )
             .setAttribute(
                 "aria-controls",
                 dialogMessageAlerts[l].getAttribute("id")
             );

         dialogHeadingElement = dialogMessageAlerts[l].querySelector(
             ".deque-dialog-heading"
         );
         dialogHeadingElement.setAttribute(
             "id",
             dialogHeadingElement.getAttribute("id") + dialogDataId
         );

         dialogDescriptionElement = dialogMessageAlerts[l].querySelector(
             ".deque-dialog-description"
         );
         dialogDescriptionElement.setAttribute(
             "id",
             dialogDescriptionElement.getAttribute("id") + dialogDataId
         );

         var dialogMessageAlertDescription;
         var dialogMessageAlertLabel;

         var configDialogMessageAlert = {
             describedby: dialogMessageAlertDescription,
             labelledby: dialogMessageAlertLabel,
             isAlert: true,
         };

         var triggerDialogMessageAlert = document.getElementsByClassName(
             "deque-dialog-message-alert-trigger deque-button"
         );

         triggerDialogMessageAlert[l].addEventListener(
             "click",
             function(event) {
                 event.preventDefault();
                 dequeActiveDialogButton = triggerDialogMessageAlert[i];
                 var attributeDialogMessageAlert =
                     this.getAttribute("aria-controls");
                 var showDialogMessageAlert = createDialog(
                     attributeDialogMessageAlert,
                     "deque-dialog-message-alert"
                 );

                 var dequeDialogWidgetElement = document.querySelectorAll(
                     '.deque-dialog-message-alert[data-id="' +
                     event.currentTarget.getAttribute("data-id") +
                     '"]'
                 )[0];
                 if (
                     dequeDialogWidgetElement.querySelector(
                         ".deque-dialog-heading"
                     )
                 ) {
                     configDialogMessageAlert.describedby =
                         dequeDialogWidgetElement
                         .querySelector(".deque-dialog-heading")
                         .getAttribute("id");
                 } else {
                     configDialogMessageAlert.describedby = "";
                 }
                 if (
                     dequeDialogWidgetElement.querySelector(
                         ".deque-dialog-description"
                     )
                 ) {
                     configDialogMessageAlert.labelledby = dequeDialogWidgetElement
                         .querySelector(".deque-dialog-description")
                         .getAttribute("id");
                 } else {
                     configDialogMessageAlert.labelledby = "";
                 }
                 showDialogMessageAlert(this, configDialogMessageAlert);
             }
         );
     }

     if (!activateHander) {
         activateDequeDialogButtonClickHandlers();
         activateHander = true;
     }
 }

 window.onload = function() {
     activateAllDialogs();
 };

```