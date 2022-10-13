# Checkbox (Tri-State)
## What Is It?

A tri-state checkbox can be checked, not checked, or partially checked. The condition of being partially checked is based on the selection of child elements. If all child elements are selected, the parent checkbox is checked. If some child elements are selected, the parent checkbox is partially checked.

See also the [Official W3C documentation about ARIA checkboxes. ![opens in a new window](https://dequeuniversity.com/assets/images/template/courses2014/new-window.png)](https://www.w3.org/TR/wai-aria-practices-1.1/#checkbox)


## Expected Operation

### Keyboard
- The tri-state checkbox will act like a native HTML checkbox: Users can tab to it then use the space bar to select it.
- When users select one or more (but not all) of the child checkbox elements, the tri-state checkbox will show a mixed state.
- When users select all of the child checkbox elements, the tri-state checkbox will show a checked state.

### Screen Readers
- The tri-state checkbox should be read as "checked", "not checked," or "mixed." Not all screen readers support the mixed state, though, so in some cases the screen reader will say "not checked" when the checkbox is in the mixed state.

## Key Accessibility Features
This ARIA checkbox is designed to act exactly like a native HTML checkbox, with the added functionality of a partially-selected state.

## Developer & QA Notes
### Visual Design
- The visual styling for the tri-state checkbox should look different from the styling for a regular dual-state checkbox.
- Ensure the method you choose to visually mark the checkbox as selected or not selected is visible in Windows High Contrast Mode. In Internet Explorer, background images and background colors will be overridden. Methods that work visually include using an `<img>` element or a font icon in the CSS. This example uses a font icon.

## Browser and Screen Reader Support
- **JAWS + IE:** Full Support
- **NVDA + Firefox:** Checkbox name spoken twice. Group name not spoken.
- **VO + iOS:** Group name not spoken.
- **VO + MacOS:** Full support
- **Narrator + Edge:** Group name not spoken
- **NVDA + Chrome:** NVDA does not read changes to the checked/unchecked state of the first example (with fieldset) when the user toggles the state.

## Code
### HTML
Method 1: `<fieldset>` and `<legend>`
```html
<fieldset class="deque-checkbox-tristate-group">
	<legend class="deque-checkbox-group-label">Choose the ingredients for your pizza.</legend>
	<div class = "deque-checkbox-tristate-parent">
		<div>
			<span class="deque-checkbox-tristate" aria-labelledby="all-ingredients"></span>
			<span id="all-ingredients">Select all ingredients</span>
		</div>
	</div>
	<div class="deque-checkbox-tristate-children">
		<div>
			<span class="deque-checkbox-tristate" aria-labelledby="cheese"></span>
			<span id="cheese">Cheese</span>
		</div>
		<div>
			<span class="deque-checkbox-tristate" aria-labelledby="mushrooms"></span>
			<span id="mushrooms">Mushrooms</span>
		</div>
		<div>
			<span class="deque-checkbox-tristate" aria-labelledby="olives"></span>
			<span id="olives">Olives</span>
		</div>
	</div>
</fieldset>
```

Method 2: `role="checkbox"` and `aria-labelledby`
```html
<div class="custom-checkbox-widget" data-group-title="Choose the toppings for your ice cream" data-group-option-title="Select all toppings" data-delimiter="--" data-options="Oreos, Gems -- Gummy Bears --Strawberries"></div>

<div class="custom-checkbox-widget" data-group-title="Choose Operating Systems" data-group-option-title="Select all Operating Systems" data-options="Windows, Linux, MacOS"></div>
```

### CSS
```css
.deque-checkbox-label {
     vertical-align: middle;
     margin-left: 10px;
}
 .deque-checkbox-label:hover {
     cursor: default;
}
 .deque-checkbox-aria[role='checkbox'] {
     user-select: none;
}
 .deque-checkbox-aria[role='checkbox'].deque-checkbox-focused {
     outline: none;
}
 .deque-checkbox-aria[role='checkbox'].deque-checkbox-focused .deque-checkbox-indicator {
     outline: 1px dashed #000000;
}
 .deque-checkbox-aria[role='checkbox'] .deque-checkbox-indicator {
     height: 20px;
     left: 0;
     width: 20px;
     line-height: 20px;
     box-shadow: inset 0 0 0 1px #000000, inset 0 0 0 10px transparent;
     top: 0;
     display: inline-block;
     position: relative;
     vertical-align: middle;
     border: 1px solid transparent;
}
 .deque-checkbox-aria[role='checkbox'] .deque-checkbox-indicator:focus {
     outline: none;
     border: 1px dashed #000000;
}
 .deque-checkbox-aria[role='checkbox'][aria-checked='true'] .deque-checkbox-indicator {
     box-shadow: inset 0 0 0 10px #0078d7;
}
 .deque-checkbox-aria[role='checkbox'][aria-checked='true'] .deque-checkbox-indicator:after {
     content: '\E8FB';
     font-family: 'mwf-glyphs';
     color: #ffffff;
     font-size: 16px;
     padding-left: 2px;
     position: absolute;
     top: 0;
     left: 0;
     right: 0;
     bottom: 0;
}
 .deque-checkbox-aria[role='checkbox'][aria-checked='mixed'] .deque-checkbox-indicator {
     box-shadow: inset 0 0 0 10px #0078d7;
}
 .deque-checkbox-aria[role='checkbox'][aria-checked='mixed'] .deque-checkbox-indicator:after {
     content: '\E738';
     font-family: 'mwf-glyphs';
     color: #ffffff;
     font-size: 16px;
     padding-left: 2px;
     position: absolute;
     top: 0;
     left: 0;
     right: 0;
     bottom: 0;
}
 .deque-checkbox-tristate-group {
     margin: 0;
     padding: 10px;
}
 .deque-checkbox-tristate[role='checkbox'] {
     cursor: default;
     user-select: none;
}
 .deque-checkbox-tristate[role='checkbox'].deque-checkbox-focused {
     outline: none;
}
 .deque-checkbox-tristate[role='checkbox'].deque-checkbox-focused .deque-checkbox-indicator {
     outline: 1px dashed #000000;
}
 .deque-checkbox-tristate[role='checkbox'] .deque-checkbox-indicator {
     height: 20px;
     left: 0;
     width: 20px;
     line-height: 20px;
     box-shadow: inset 0 0 0 1px #000000, inset 0 0 0 10px transparent;
     top: 0;
     display: inline-block;
     position: relative;
     vertical-align: middle;
     border: 1px solid transparent;
}
 .deque-checkbox-tristate[role='checkbox'] .deque-checkbox-indicator:focus {
     outline: none;
     border: 1px dashed #000000;
}
 .deque-checkbox-tristate[role='checkbox'][aria-checked='true'] .deque-checkbox-indicator {
     box-shadow: inset 0 0 0 10px #0078d7;
}
 .deque-checkbox-tristate[role='checkbox'][aria-checked='true'] .deque-checkbox-indicator:after {
     content: '\E8FB';
     font-family: 'mwf-glyphs';
     color: #ffffff;
     font-size: 16px;
     padding-left: 2px;
     position: absolute;
     top: 0;
     left: 0;
     right: 0;
     bottom: 0;
}
 .deque-checkbox-tristate[role='checkbox'][aria-checked='mixed'] .deque-checkbox-indicator {
     box-shadow: inset 0 0 0 10px #0078d7;
}
 .deque-checkbox-tristate[role='checkbox'][aria-checked='mixed'] .deque-checkbox-indicator:after {
     content: '\E738';
     font-family: 'mwf-glyphs';
     color: #ffffff;
     font-size: 16px;
     padding-left: 2px;
     position: absolute;
     top: 0;
     left: 0;
     right: 0;
     bottom: 0;
}
 .custom-checkbox-widget {
     margin: 20px 0px;
}
 .custom-checkbox-widget .checkbox-group-heading {
     margin: 10px 0px;
}
 .deque-checkbox-tristate-children {
     margin-left: 10px;
}
 .custom-checkbox-widget .checkbox-holder .checkbox-indicator {
     height: 20px;
     width: 20px;
     border: 1px #000 solid;
     display: inline-block;
     margin-right: 12px;
}
 .custom-checkbox-widget .checkbox-holder[aria-checked="true"] .checkbox-indicator {
     border: 1px #0078d7 solid;
}
 .custom-checkbox-widget .checkbox-holder[aria-checked="mixed"] .checkbox-indicator {
     border: 1px #0078d7 solid;
}
 .custom-checkbox-widget .checkbox-holder[aria-checked="true"] .checkbox-indicator, .custom-checkbox-widget .checkbox-holder[aria-checked="mixed"] .checkbox-indicator {
     background: #0078d7;
     position: relative;
}
 .custom-checkbox-widget .checkbox-holder[aria-checked="false"] .checkbox-indicator {
     background: none;
}
 .custom-checkbox-widget .child-checkbox {
     margin-left: 16px;
}
 .custom-checkbox-widget .checkbox-holder[aria-checked="true"] .checkbox-indicator::after {
     content: '\E8FB';
     font-family: mwf-glyphs;
     color: #fff;
     font-size: 16px;
     padding-left: 2px;
     position: absolute;
     top: -3px;
     left: 0;
     right: 0;
     bottom: 0;
}
 .custom-checkbox-widget .checkbox-holder[aria-checked="mixed"] .checkbox-indicator::after {
     content: '\E738';
     font-family: mwf-glyphs;
     color: #fff;
     font-size: 16px;
     padding-left: 2px;
     position: absolute;
     top: -3px;
     left: 0;
     right: 0;
     bottom: 0;
}
 .custom-checkbox-widget .checkbox-label {
     position: relative;
     top: -5px;
}


```

### JavaScript
```JavaScript
/*
	
        TO DO:
        
        - Throw an error if the label is missing
        */
function toggle(element) {
    if (isToggledOn(element)) {
        toggleOff(element);
    } else {
        toggleOn(element);
    }
}

function isToggledOn(element) {
    return getCheckboxData(element) === "true";
}

function replaceSpace(str) {
    return str.replace(/ /g, "_").toLowerCase();
}

function buildCheckboxTristate() {
    var _customCheckboxTristateWidgets = document.querySelectorAll(
        ".custom-checkbox-widget"
    );
    var _instanceChkTristateCount = 0;
    if (_customCheckboxTristateWidgets.length > 0) {
        [].slice
            .call(_customCheckboxTristateWidgets)
            .forEach(function(_eachCustomWidget) {
                var _dataConfig = {
                    groupTitle: _eachCustomWidget.getAttribute("data-group-title"),
                    groupOptionTitle: _eachCustomWidget.getAttribute(
                        "data-group-option-title"
                    ),
                    options: _eachCustomWidget.getAttribute("data-options"),
                    delimiter: _eachCustomWidget.getAttribute("data-delimiter") || ",",
                };

                if (_dataConfig.options) {
                    _dataConfig.options = _dataConfig.options.split(
                        _dataConfig.delimiter
                    );
                }

                var _id =
                    "instance_" +
                    _instanceChkTristateCount +
                    "_" +
                    replaceSpace(_dataConfig.groupTitle);

                var _elementControl =
                    '<div class="custom-checkbox" id="' +
                    _id +
                    '" role="group" aria-labelledby="group-header' +
                    _id +
                    '">';
                _elementControl +=
                    '<div class="checkbox-group-heading" id="group-header' +
                    _id +
                    '">' +
                    _dataConfig.groupTitle +
                    "</div>";
                _elementControl +=
                    '<div role="checkbox" name="parent-checkbox[]" data-childs="' +
                    _id +
                    '_childs" class="parent-checkbox checkbox-holder" \
                                                      aria-labelledby="' +
                    _id +
                    replaceSpace(_dataConfig.groupOptionTitle) +
                    '" tabindex="0"> \
                                                      <span class="checkbox-indicator"></span> \
                                                      <span class="checkbox-label" id="' +
                    _id +
                    replaceSpace(_dataConfig.groupOptionTitle) +
                    '">' +
                    _dataConfig.groupOptionTitle +
                    '</span> \
                                                  </div> \
                                                  <div class="child-checkbox-list" data-group="' +
                    _id +
                    '">';

                for (var _elementOptionIndex in _dataConfig.options) {
                    _elementControl +=
                        '<div role="checkbox" name="child-checkbox[]" class="child-checkbox checkbox-holder ' +
                        _id +
                        '_childs" aria-labelledby="' +
                        _id +
                        replaceSpace(_dataConfig.options[_elementOptionIndex]) +
                        '" tabindex="0"> \
                                                          <span class="checkbox-indicator"></span> \
                                                          <span class="checkbox-label" id="' +
                        _id +
                        replaceSpace(_dataConfig.options[_elementOptionIndex]) +
                        '">' +
                        _dataConfig.options[_elementOptionIndex] +
                        "</span> \
                                                      </div>";
                }

                _elementControl += "</div></div>";

                _eachCustomWidget.innerHTML = _elementControl;
                _instanceChkTristateCount++;
            });

        var parentCheckboxElements =
            document.querySelectorAll(".parent-checkbox");
        [].slice
            .call(parentCheckboxElements)
            .forEach(function(eachParent) {
                eachParent.addEventListener("click", checkboxEvent);
                eachParent.addEventListener("keyup", checkboxEvent);
            });
        var childElements = document.querySelectorAll(".child-checkbox");
        [].slice.call(childElements).forEach(function(eachChildElement) {
            eachChildElement.addEventListener("click", checkboxEvent);
            eachChildElement.addEventListener("keyup", checkboxEvent);
        });
    }
}

buildCheckboxTristate();

function checkboxEvent(event) {
    if (event.keyCode == 13 || event.keyCode == 32 || !event.keyCode) {
        var element = event.currentTarget;
        if (element.getAttribute("aria-checked") == "true") {
            element.setAttribute("aria-checked", "false");
            element.classList.remove("active");
        } else {
            element.setAttribute("aria-checked", "true");
            element.classList.add("active");
        }

        if (element.getAttribute("data-childs")) {
            var childElements = document.querySelectorAll(
                "." + element.getAttribute("data-childs")
            );
            [].slice.call(childElements).forEach(function(eachChildElement) {
                if (element.classList.contains("parent-checkbox")) {
                    eachChildElement.setAttribute(
                        "aria-checked",
                        element.getAttribute("aria-checked")
                    );
                }
            });
        }

        var parentElement = document.querySelector(
            "#" + element.parentElement.getAttribute("data-group")
        );
        if (parentElement) {
            var innerParent = parentElement.querySelector(".parent-checkbox");
            if (innerParent) {
                var checkboxCounter = parentElement.querySelectorAll(
                    '.child-checkbox[aria-checked="true"]'
                ).length;
                var _childElements =
                    parentElement.querySelectorAll(".child-checkbox");
                if (checkboxCounter == 0) {
                    innerParent.setAttribute("aria-checked", "false");
                } else if (checkboxCounter < _childElements.length) {
                    innerParent.setAttribute("aria-checked", "mixed");
                } else if (checkboxCounter == _childElements.length) {
                    innerParent.setAttribute("aria-checked", "true");
                }
            }
        }
    }
}

function setCheckboxData(element, value) {
    element.setAttribute("aria-checked", value);
    var dataElement = document.getElementById(
        "checkboxTristateData[" +
        element.getAttribute("aria-labelledby") +
        "]"
    );
    if (dataElement) dataElement.value = value;
}

function getCheckboxData(element) {
    var dataElement = document.getElementById(
        "checkboxTristateData[" +
        element.getAttribute("aria-labelledby") +
        "]"
    );
    return dataElement ? dataElement.value : null;
}

function toggleOn(element) {
    setCheckboxData(element, "true");
}

function toggleOff(element) {
    setCheckboxData(element, "false");
}

function toggleMixed(element) {
    setCheckboxData(element, "mixed");
}

function createSingleCheckbox(checkbox, isChecked) {
    var onChange =
        arguments.length > 2 && arguments[2] !== undefined ?
        arguments[2] :
        function() {};

    checkbox.setAttribute("tabindex", "0");
    checkbox.setAttribute("role", "checkbox");
    var indicator = document.createElement("span");
    indicator.classList.add("deque-checkbox-indicator");

    checkbox.appendChild(indicator);

    var labelText = checkbox.getAttribute("aria-labelledby");
    var label = document.getElementById(labelText);
    //label.setAttribute('aria-hidden', 'true'); // prevents double readout
    label.classList.add("deque-checkbox-label");

    var hiddenCheckbox = document.createElement("input");
    hiddenCheckbox.type = "hidden";
    hiddenCheckbox.name = "checkboxTristateData[" + labelText + "]";
    hiddenCheckbox.id = "checkboxTristateData[" + labelText + "]";
    hiddenCheckbox.classList.add("deque-checkbox-data");

    checkbox.appendChild(hiddenCheckbox);

    /*checkbox.addEventListener('focus', function () {
            var allCheckboxElements = document.querySelectorAll('.deque-checkbox-tristate-parent');
            [].slice.call(allCheckboxElements).forEach(element => {
              element.setAttribute('aria-hidden', 'true');
            });
          });*/

    if (isChecked) {
        toggleOn(checkbox);
    } else {
        toggleOff(checkbox);
    }

    function changeHandler(e) {
        e.stopPropagation();
        e.preventDefault();
        toggle(checkbox);
        broadcastChange();
    }

    function broadcastChange() {
        onChange({
            element: checkbox,
            isToggledOn: isToggledOn(label)
        });
    }

    checkbox.parentNode.addEventListener("click", changeHandler);
    (0, _keyboardUtils.onElementSpace)(checkbox, changeHandler);
    (0, _keyboardUtils.onElementEnter)(checkbox, changeHandler);

    checkbox.parentNode.addEventListener("focus", function() {
        checkbox.classList.add("deque-checkbox-focused");
    });

    checkbox.parentNode.addEventListener("blur", function() {
        checkbox.classList.remove("deque-checkbox-focused");
    });

    return checkbox;
}

function createSingleCheckboxForRadio(
    checkbox,
    checkboxLabel,
    isChecked
) {
    var onChange =
        arguments.length > 3 && arguments[3] !== undefined ?
        arguments[3] :
        function() {};

    checkbox.setAttribute("tabindex", "0");
    checkbox.setAttribute("role", "checkbox");

    var indicator = document.createElement("span");
    indicator.classList.add("deque-checkbox-indicator");

    checkbox.appendChild(indicator);

    var labelText = checkbox.getAttribute("aria-labelledby");
    var label = document.getElementById(labelText);
    //label.setAttribute('aria-hidden', 'true'); // prevents double readout
    label.classList.add("deque-checkbox-label");

    var hiddenRadio = document.createElement("input");
    hiddenRadio.type = "hidden";
    hiddenRadio.name = "checkboxTristateData[" + labelText + "]";
    hiddenRadio.id = "checkboxTristateData[" + labelText + "]";
    hiddenRadio.classList.add("deque-checkbox-radio-data");
    checkbox.appendChild(hiddenRadio);
    checkbox.appendChild(label);

    if (isChecked) {
        toggleOn(checkbox);
    } else {
        toggleOff(checkbox);
    }

    function changeHandler(e) {
        e.stopPropagation();
        e.preventDefault();
        toggle(checkbox);
        broadcastChange();
    }

    function broadcastChange() {
        onChange({
            element: checkbox,
            isToggledOn: isToggledOn(label)
        });
    }

    checkbox.addEventListener("click", changeHandler);
    checkbox.addEventListener("keydown", changeHandler);

    checkboxLabel.addEventListener("click", changeHandler);
    (0, _keyboardUtils.onElementSpace)(checkbox, changeHandler);

    checkbox.addEventListener("focus", function() {
        checkbox.classList.add("deque-radio-focused");
    });

    checkbox.addEventListener("blur", function() {
        checkbox.classList.remove("deque-radio-focused");
    });

    return checkbox;
}

function createCheckboxGroup(parent, children) {
    var onChange =
        arguments.length > 2 && arguments[2] !== undefined ?
        arguments[2] :
        function() {};

    parent = createSingleCheckbox(parent, false, function(e) {
        onChange(e);
        rootClicked(getCorrectRootState());
    });

    children = Array.prototype.slice.call(children);

    children = children.map(function(child) {
        return createSingleCheckbox(child, false, function() {
            if (onChange) {
                onChange(child);
            }
            setCorrectRootState();
        });
    });

    var rootClickHandlers = {
        true: function _true() {
            children.forEach(toggleOff);
            toggleOff(parent);
        },
        false: function _false() {
            children.forEach(toggleOn);
            toggleOn(parent);
        },
        mixed: function mixed() {
            children.forEach(toggleOn);
            toggleOn(parent);
        },
    };

    function rootClicked(rootState) {
        rootClickHandlers[rootState]();
    }

    function getCorrectRootState() {
        if (children.every(isToggledOn)) {
            return "true";
        } else if (
            children.every(function(child) {
                return !isToggledOn(child);
            })
        ) {
            return "false";
        } else {
            return "mixed";
        }
    }

    var leafClickHandlers = {
        true: function _true() {
            return toggleOn(parent);
        },
        false: function _false() {
            return toggleOff(parent);
        },
        mixed: function mixed() {
            return toggleMixed(parent);
        },
    };

    function setCorrectRootState() {
        leafClickHandlers[getCorrectRootState()]();
    }
}

function activateAllCheckboxes() {
    var checkboxes = document.querySelectorAll(".deque-checkbox-aria");
    for (var i = 0; i < checkboxes.length; i++) {
        var childNode = checkboxes[i].querySelector(".deque-checkbox-data");
        if (!checkboxes[i].contains(childNode)) {
            createSingleCheckbox(checkboxes[i], false);
        }
    }

    var tristates = document.querySelectorAll(
        ".deque-checkbox-tristate-group"
    );
    for (var j = 0; j < tristates.length; j++) {
        var parentGroup = tristates[j].querySelector(
            ".deque-checkbox-tristate-parent"
        );
        var parent = parentGroup.querySelector(".deque-checkbox-tristate");
        var childrenGroup = tristates[j].querySelector(
            ".deque-checkbox-tristate-children"
        );
        var children = childrenGroup.querySelectorAll(
            ".deque-checkbox-tristate"
        );
        childNode = childrenGroup.querySelector(".deque-checkbox-data");
        if (!childrenGroup.contains(childNode)) {
            createCheckboxGroup(parent, children);
        }
    }
}

activateAllCheckboxes();


```