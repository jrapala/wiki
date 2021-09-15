# Compliance

The only way to ensure accessibility is to follow the **Web Content Accessibilty Guidelines (WCAG)**

- There are four main success areas: perceivable, operable, understandable, and robust (POUR)

**History**

- WAI (1999) - Web Accessbility Initiative - 14 guidelines, 65 priority checkpoints
- WCAG 2.0 (2008) - 12 guidelines, 61 success criteria
- WCAG 2.1 (2018) - 12 guidelines, 82 success criteria

https://webaim.org provides a great breakdown of WCAG.



**POUR**

- Perceivable
  - Adding alt text to images and "text only" website equivalents
  - Supports assistive technologies (JAWS)
  - Adding text and audio transcripts for video and audio features
    - Be aware: auto captions are so error prone that they are not really accessible. It's a good starting point if you want to create your own captions.
    - Three reason to caption:
      1. Accessible - 20% of Americans over age 12 experience enough hearing loss that it interferes with daily communication. Not captioning = not reaching 1/5 of your potential audience.
      2. Searchable - Google can't watch a video. Not captioning means videos can only be based on their titles.
      3. Engaging - Helps people understand more, especially if English is not their first language. It can keep people watching your videos for longer.
    - Also, without captions, it's much harder to translate. 80% of views on YouTube come from outside the U.S. If you add translations, you can capture these people.
  - Remove reliance on shape, size, location, color, or sound to navigate
- Operable
  - Use of keyboard alternative for site navigation
  - No action "timeouts"
  - Eliminate automatic "redirects" and other content changes
  - Eliminate blinking screen features at certain rates (seizures)
  - Alternative way of finding other site pages (Table of Contents, Site Map)
- Understandable
  - Identification of multi-lingual sections
  - Glossaries for acronyms and unusual terms
  - Identified page focus points as they change (for assistive technologies)
  - Consistent functionality presented consistentyl across pages
  - Intuitive and clear definition of input requirements and error messages
- Robust
  - Supports plug ins, scripts, applets and other current and future user agents
  - Accessibility of PDFs and other file types to assistive technologies

| WCAG 2.1 Color Guidelines                        |           |                                                              |
| ------------------------------------------------ | --------- | ------------------------------------------------------------ |
| Success Criterion 1.4.1<br />Use of Color        | Level A   | Color is not used as the only visual means of conveying information, indicating an action, prompting a response, or distinguishing a visual element |
| Success Critierion 1.4.3<br />Contract (Minimum) | Level AA  | The visual presentation of text and images of text has a contrast ratio of at least 4.5:1 |
| Success Criterion 1.4.6<br />Contract (Enhanced) | Level AAA | The visual presentation of text and images of text has a contrast ratio of at least 7:1 |



| **WCAG 2.1 Layout Guidelines**                       |         |                                                              |
| ---------------------------------------------------- | ------- | ------------------------------------------------------------ |
| Success Criterion 1.3.1<br />Info and Relationships  | Level A | Information, structure, and relationships conveyed through presentation can be programmtically determined or are available in text |
| Success Criterion 1.3.2<br />Meaningful Sequence     | Level A | When the sequence in which content is presented affects its meaning, a correct reading sequence can be programmatically determined. |
| Success Criterion 1.3.3<br />Sensory Characteristics | Level A | Instructions provided for understanding and operating content do not rely solely on sensory characteristics of components such a shape, color, size, visual location, orientation, or sound. |



| WCAG SC 1.3.4, 1.3.6, 2.2.6 Preventing Uncertainty  |           |                                                              |
| --------------------------------------------------- | --------- | ------------------------------------------------------------ |
| Success Criterion 1.3.5<br />Identify Input Purpose | Level AA  | For content created with markup languages, the purposes of specific input fields indicated in WCAG 2.1 needs to be communicated programmatically such as via the autocomplete attribute in HTML. The purpose can then be transformed by personalization tools to communicate this in different ways such as via icons or symbols to assist users with cognitive and learning disabilites. |
| Success Criterion 1.3.6<br />Identify  Purpose      | Level AAA | This builds on SC 1.3.5 and includes communcating purpose for icons, regions, links, buttons, and other user interface elements, to support personalization for people with cognitive and learning disabilities. |
| Success Criterion 2.2.6<br />Timeouts               | Level AAA | When a timeout is used, advice users of the duration of inactivity that will cause the timeout and result in loss of data. Users with cognitive disabilites or other focus/memory-related disabilities may require more time to read content or to complete interactions, such as completing an order form. The use of timed events can present barriers for people who need to take breaks. Providing the duration of inactivity before a timeout occurs will help users plan for break. |

- Ticketmaster shows a timer with the time left to buy a ticket.



| WCAG SC 1.2.2 Captions  |         |                                                              |
| ----------------------- | ------- | ------------------------------------------------------------ |
| Success Criterion 1.2.2 | Level A | Captions are provided for all prerecorded audio content in synchronized media, except when the media is a media alternative for text and is clearly labeled as such. |

Examples of success:

- A captioned tutorial
- A complex legal document contains synchronized media clips for different paragraphs sow a person speaking the contents of the paragraph. Each clip is associated with its corresponding paragraph.
- An instruction manual containing a description of a part and its necessary orientation is accompanied by a synchronized media clip showing the part in its correct orientation. 
- An orchestra providers captions for videos of performances, which includes information to help the user comprehend the nature of the audio, e.g. "Calm melody with a slow tempo"



| WCAG SC 3.1 Mobile Accessibility                             |         |                                                              |
| ------------------------------------------------------------ | ------- | ------------------------------------------------------------ |
| Success Criterion 3.1<br />Keyboard Control for Touchscreen Devices | Level A | Allow mobile devices to be operated by external physical keyboards (e.g. keyboards connected via Bluetooth, USB On-The-Go) or alternative on-screen keyboards (e.g. scanning on-screen keyboards). |
| Success Criterion 3.2<br />Touch Target Size and Spacing     |         | Ensure that touch targets are at least 9 mm high by 9 mm wide. <br />Ensure that touch targets close to the minimum size are surrounded by a small amount of inactive space. |
| Success Criterion 3.3<br />Touchscreen Gestures              |         | Gestures in apps should be as easy as possible to carry out. This is especially important for screen reader interaction modes that replace direct touch manipulation by a two-step process of focusing and activating elements. It is also a challenge for users with motor or dexterity impairments or people who rely on head pointers or a stylus where multi-touch gestures may be difficult or impossible to perform. Often, interface designers have different options for how to implement an action. Widgets requiring complex gestures can be difficult or impossible to use for screen reader users. Usually, design alternatives exist to allow changes to settings via simple tap or swipe gestures.<br />Activating elements via the mouseup or touchend event. Using the mouseup or touchend event to trigger actions helps prevent unintentional actions during touch and mouse interaction. Mouse users clicking on actionable elements (links, buttons, submit inputs) should have the opportunity to move the cursor outside the element to prevent the event from being triggered. This allows users to change their minds without being forced to commit to an action. In the same way, elements accessed via touch interaction should generally trigger an event (e.g. navigation, submits) only when the touchend event is fired (i.e. when all of the following are true: the user has lifted the finger off the screen, the last position of the finger is inside the actionable element, and the last position of the finger equals the position at touchstart).<br />Touchscreen gestures might lack onscreen indicators that remind people how and when to use them |
| Success Criterion 3.4<br />Device Manipulation Gestures      |         | Some device manipulation gestures can be a challenge for people who have difficulty holding or are unable to hold a mobile device.<br />Therefore, even when device manipulation gestures are provided, developers should still provide touch and keyboard operable alternative control options. |
| Success Criterion 3.5<br />Placing buttons where they are easy to access |         | Mobile sites and applications should position interactive elements where they can be easily reached when the device is held in different positions.<br />Allow for use with one hand, whether it's the left or right hand. |

