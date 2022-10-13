# Carousel
## What Is It?

This carousel pattern is based on an ARIA tab panel pattern. Only one tab panel (carousel item) is visible at a time. There are buttons to go forward, back, or to play/pause the carousel.

## Expected Operation

### Keyboard
-   The user can tab to the active tab in the tablist, then use the arrow keys or Ctrl+PageUp/Ctrl+PageDown to navigate between the tabs. Using the tab key does NOT take the user to the next tab in the tablist, because only one tab is focusable at a time, similar to radio buttons.
-   The buttons for previous, next, and play/pause can be activated by the space bar or enter key.

### Screen Readers
When the user focuses on a tab, the screen reader should read the tab's name/label and announce that the element in focus is a tab, and should give a count of the total number of tabs in the tablist. e.g. "Perceivable, tab 1 of 4." JAWS will also explain that the user can press a keyboard combination to go to the corresponding tab panel ("Use JAWS key plus Alt + M to move to controlled element").

## Key Accessibility Features
-   It is bad practice from an accessibility perspective to have a carousel autoplay on page load. It is generally best to let users choose when to go to the next item, rather than to set an arbitrary timer.
-   A pause button is required for anything that has play or autoplay capabilities.
-   Keyboard navigation with the tablist is controlled by the arrow keys, not the tab key.
-   The correct semantic markup allows the screen reader to know how many tabs are in the tablist, and which tab panel is controlled by which tab.

## Developer & QA Notes
The following attributes on the tabs are toggled as the user switches between tabs:

-   aria-selected=true/false
-   tabindex=0/-1

Developers must be wary of the fact that the Chrome browser does not propagate Ctrl+PageUp/Ctrl+PageDown key combinations to the web page when multiple tabs are open.


## Browser and Screen Reader Support
- **JAWS + IE:** Full support (number of tabs not announced)
- **NVDA + Firefox:** Full support
- **VO + iOS:** Full support
- **VO + MacOS:** Full support
- **Narrator + Edge:** Full support (number of tabs not announced)

## Code
### HTML
```html

<div class="deque-tabpanel" id="carousel">
	<ul class="deque-tabpanel-tablist" role="tablist">
		<li class="deque-tabpanel-tab" role="tab" id="tabCitySkyline" aria-controls="panelCitySkyline">City Skyline</li>
		<li class="deque-tabpanel-tab" role="tab" id="tabSunset" aria-controls="panelSunset">Beach Sunset</li>
		<li class="deque-tabpanel-tab" role="tab" id="tabRomanBuilding" aria-controls="panelRomanBuilding">Roman Statue</li>
	</ul>
	<div class="deque-tabpanel-tabpanels">
		<div class="deque-tabpanel-tabpanel" role="tabpanel" id="panelCitySkyline" aria-labelledby="tabCitySkyline">
			<img id="imgCitySkyline" src="assets/js/patterns/images/tempimage01.jpeg" alt="City skyline">
		</div>
		<div class="deque-tabpanel-tabpanel" role="tabpanel" id="panelSunset" aria-labelledby="tabSunset">
			<img id="imgSunset" src="assets/js/patterns/images/tempimage02.jpeg" alt="Sunset over lake">
		</div>
		<div class="deque-tabpanel-tabpanel" role="tabpanel" id="panelRomanBuilding" aria-labelledby="tabRomanBuilding">
			<img id="imgRomanBuilding" src="assets/js/patterns/images/tempimage03.jpeg" alt="Old roman building with statue on top.">
		</div>
	</div>
	<div class="deque-tabpanel-button-bar">
		<button class="deque-button" id="prevButton">prev</button>
		<button class="deque-button" id="playButton">play</button>
		<button class="deque-button" id="pauseButton">pause</button>
		<button class="deque-button" id="nextButton">next</button>
	</div>
</div>
```

### CSS
```css
.deque-tabpanel [role='tablist'] {
	list-style-type: none;
	padding: 0;
	display: flex;
	margin: 0 0 20px 0;
	justify-content: center;
}

.deque-tabpanel img {
	max-width: 100%;
}

.deque-tabpanel ul.deque-tabpanel-tablist[role='tablist'] li {
	font-size: 20px;
	display: inline-block;
	flex-shrink: 0;
	font-weight: 200;
	white-space: nowrap;
	padding: 5px 12px;
	cursor: pointer;
}

.deque-tabpanel ul.deque-tabpanel-tablist[role='tablist'] li:focus {
	outline: 1px dashed #000000;
}

.deque-tabpanel li.deque-tabpanel-tab[role='tab'][aria-selected='true'] {
	color: #000000;
	border-bottom: 2px solid;
	border-top: 2px solid;
}

.deque-tabpanel .deque-tabpanel-tabpanels {
	clear: both;
	padding: 0;
	margin: 0;
}

.deque-tabpanel .deque-tabpanel-tabpanel[role='tabpanel'] {
	width: 100%;
}

.deque-tabpanel .deque-tabpanel-button-bar {
	text-align: center;
}

.deque-tabpanel .deque-tabpanel-button-bar .pause,
.deque-tabpanel .deque-tabpanel-button-bar .play {
	margin: 0 15px;
}

```

### JavaScript
```JavaScript
function createTabpanel(widget, config) {
	var widgetTablist = widget.querySelector("[role=tablist]");
	widgetTablist.classList.add("deque-tabpanel-tablist");

	var pause = widget.querySelector(".deque-tabpanel-button-bar");
	if (pause && !pause.classList.contains("hidden")) {
		pause.click();
	}


	function initializeTabs(widget) {
		var tabs = widget.querySelectorAll("[role=tab]");
		for (var i = 0; i < tabs.length; i++) {
			tabs[i].classList.add("deque-tabpanel-tab");
			if (i == 0) {
				tabs[i].setAttribute("aria-selected", "true");
				tabs[i].setAttribute("tabindex", "0");
			} else {
				tabs[i].setAttribute("aria-selected", "false");
				tabs[i].setAttribute("tabindex", "-1");
			}
		}
		return tabs;
	}

	var tabs = initializeTabs(widget);

	function initializePanels(widget) {
		var panels = widget.querySelectorAll("[role=tabpanel]");
		for (var i = 0; i < panels.length; i++) {
			panels[i].classList.add("deque-tabpanel-tabpanel");
			if (i != 0) {
				panels[i].classList.add("deque-hidden");
			}
		}
		return panels;
	}

	var panels = initializePanels(widget);

	// create a live region to toss tab-panel-related announcements into
	var region = (0, _containerUtils.createLiveRegion)();
	document.body.appendChild(region);

	var autoplayControls;
	var autoplayConfig;
	if (config.autoplay) {
		autoplayConfig = {
			onPlay: function onPlay() {},
			onPause: function onPause() {},
			onNext: function onNext() {
				var nextTab = getNext(getCurrentTab(tabs));
				selectTab(nextTab, tabs, panels);
				region.notify(nextTab.innerText + "tab");
			},
			onPrevious: function onPrevious() {
				var previousTab = getPrev(getCurrentTab(tabs));
				selectTab(previousTab, tabs, panels);
				region.notify(previousTab.innerText + "tab");
			},
		};

		autoplayControls = (0, _carouselControls.activateCarouselControls)(
			widget,
			region,
			autoplayConfig,
			config.autoplay || 3000
		);
	}
	
	function selectTab(selectedTab, tabs, panels) {
		var selectedTabLabeledBy = selectedTab.getAttribute("aria-controls");
		for (var i = 0; i < panels.length; i++) {
			if (panels[i].id === selectedTabLabeledBy) {
				panels[i].classList.remove("deque-hidden");
			} else {
				panels[i].classList.add("deque-hidden");
			}
		}

		for (var j = 0; j < tabs.length; j++) {
			if (tabs[j].getAttribute("aria-controls") === selectedTabLabeledBy) {
				tabs[j].setAttribute("tabindex", "0");
				tabs[j].setAttribute("aria-selected", "true");
			} else {
				tabs[j].setAttribute("tabindex", "-1");
				tabs[j].setAttribute("aria-selected", "false");
			}
		}
	}

	function applyNavigationLogic(tabs, panels, _ref) {
		var autoselect = _ref.autoselect,
		vertical = _ref.vertical;
		
		var tabstopConfig = {
			onSpace: function onSpace(item) {
				var selectedTabLabeledBy = item.getAttribute("aria-controls");
				for (var i = 0; i < panels.length; i++) {
					if (panels[i].id === selectedTabLabeledBy) {
						panels[i].classList.remove("deque-hidden");
					} else {
						panels[i].classList.add("deque-hidden");
					}
				}
			},
			onClick: function onClick(item) {
				var selectedTabLabeledBy = item.getAttribute("aria-controls");
				for (var i = 0; i < panels.length; i++) {
					if (panels[i].id === selectedTabLabeledBy) {
						panels[i].classList.remove("deque-hidden");
					} else {
						panels[i].classList.add("deque-hidden");
					}
				}
			},
			select: selectTab,
			useAriaSelected: true,
			autoselect: autoselect,
		};

		if (vertical) {
			tabstopConfig.onUp = function (item) {
				if (autoplayControls) {
					autoplayControls.pause();
				}
				return getPrev(item);
			};

			tabstopConfig.onDown = function (item) {
				if (autoplayControls) {
					autoplayControls.pause()
				}
				return getNext(item);
			};
		} else {
			tabstopConfig.onLeft = function (item) {
				if (autoplayControls) {
					autoplayControls.pause();
				}
		 
				return getPrev(item);
			};

			tabstopConfig.onRight = function (item) {
				if (autoplayControls) {
					autoplayControls.pause();
				}

				return getNext(item);
			};
		}

		(0, _tabstopUtils.createSingleTabstopStructure)(
			tabs,
			panels,
			tabstopConfig
		);
	}

	applyNavigationLogic(tabs, panels, config);

	function getCurrentTab(tabs) {
		for (var i = 0; i < tabs.length; i++) {
			if (tabs[i].getAttribute("tabindex") === "0") {
				return tabs[i];
			}
		}
	}

 
	function getPrev(item) {
		var output = item.previousElementSibling || tabs[tabs.length - 1];
		return output;
	}

  

	function getNext(item) {
		var output = item.nextElementSibling || tabs[0];
		return output;
	}
}

  

function initializeAllTabpanels() {
	var widgets = document.querySelectorAll(".deque-tabpanel");
	var config;

	for (var i = 0; i < widgets.length; i++) {
		if (widgets[i].id == "carousel") {
			config = {
				autoselect: true,
				autoplay: 3000,
			};
		} else {
			config = {
				autoselect: true,
				autoplay: false,
			};
		}

		createTabpanel(widgets[i], config);
	}
}

  
initializeAllTabpanels();


```