# Design for Accessibility

**Groups to Consider When Thinking About Accessibility**

- People with motor disabilities
- People who are deaf or hard of hearing
- People with cognitive disabilities
- People with vision disabilities



Some people have concurrent disabilities.



More than 1 billion people around the world have a disability. So, 13% of people.

In the U.S., 1 out of 4 have a disability.



- [The Designer’s Guide to Accessibility Research](https://design.google/library/designers-guide-accessibility-research/)
- [Accessibility Guide for Google Material](https://material.io/design/usability/accessibility.html)



In the disability community and as UX designers, we focus on the **social model of disability**.  

**Social model of disability**: Defines a disability as being caused by the way society is organized or how products are designed, rather than a person's ability or difference.

We need to account for permanent, temporary, or situational disabilities in our designs. If we make the design of a product easier for people with disabilities, we also often make it a better experience for everyone else. e.g. Typewriters were designed for the blind. Closed captions help everyone.



[Conducting surveys for people in far away places.](https://design.google/library/survey-methods-connecting-global-audience/?)



**Visuals**

- Include diverse representation in your application: race, clothing, physical ability, and social class.

- Pay attention to details, as they speak for themselves. Do you show left handers in your illustrations? Prosthesis in your personas? Will these users have the same experience interacting with your product?

- Learn about local design and embrace the aesthetics you find in your app’s visual language and imagery.

[Diverse UI](https://diverseui.com/)

[Diverse Device Hands](https://design.facebook.com/toolsandresources/diverse-device-hands/)



**Languages**

English is dominant on the web, yet on a global level, it's not that widely spoken or understood. Other languages are spoken and for people with disabilities, literacy rate can be low.

- Use simple, basic English. Avoid jargon.
- Translate to other languages and test the text flow in the UI. e.g. Arabic takes more vertical space the renders RTL.
- Keep sentences short and provide graphical cues. Combine text with images.
- Avoid complex hierarchical structures, such as menus, tabs, or drop downs, as users are more likely to get lost. Screen readers would also take a long time to get through them.
- Minimize the need to type or text search. Whenever possible, allow voice input, autocomplete, and browseable UI.

[Material Design Writing Guidelines](https://material.io/guidelines/style/writing.html)

[Google internationalization checklist](https://developers.google.com/international/)

[Material Design Bidirectionality](https://material.io/design/usability/bidirectionality.html)



**Privacy and Security**

Due to harrassment, people with disabilities are especially hesitant to share personal information like phone numbers or pictures, which can make online participation even harder.

- Help users become aware of their privacy options and controls. Make these controls easy to discover and access.
- Clearly explain security and data handling of personal information. Let users adjust their privacy settings and provide maximum transparency about how third parties or others can access their data.
- Allow users who share the same device to easily discover controls to remove private content like search queries or recommendations, switch accounts easily, and learn about private modes.
- Allow users to report abuse or take down sensitive or harmful content easily, and show immediate results.
- Enable people to backup or export information from your app in case their phone gets lost or stolen.

[Web Application Privacy Best Practices](https://www.w3.org/TR/app-privacy-bp/)

[Android Privacy, Security and Deception](https://play.google.com/about/privacy-security-deception/personal-sensitive/)



**Small Devices**

- Test your layout and rendering on 480 x 800 px and screen sizes smaller than 4 inches. 

- Reduce the download size of your app or offer a “light” version.
- Provide controls and visibility into the device storage and allow users the easy deletion of content.
- Control long-running or high latency processes to minimize battery usage for your app.

[Material Design Device Metrics](https://material.io/devices/)

[Build for Billions: Device capability](https://developer.android.com/develop/quality-guidelines/building-for-billions-device-capacity.html)



**Bad Networks**

- Make your app’s content available offline.
- Provide progress indicators and other status changes with meaningful alternative text for those who access phones through screen readers. User experience is crucial over poor connectivity, including for those who use assistive technologies.
- Support local caching of form inputs to avoid losing data—and user frustration.

- Render content progressively and pre-cache frequently-used content.
- Test your app in airplane mode to simulate a lack of connectivity.

[Build for Billions: Connectivity](https://developer.android.com/develop/quality-guidelines/building-for-billions-connectivity.html)

[Design for Offline](https://design.google/library/offline-design/)



**Keep Data Costs Low**

- Minimize high-bandwidth consumption, including videos, rich graphics, and auto refresh.
- Provide transparency and control into data-heavy applications.

- Keep updates minimal and relevant. Explain the value of each update in straightforward language.

- Allow users to try your app or service for free.

[Build for Billions: Cost](https://developer.android.com/develop/quality-guidelines/building-for-billions-data-cost.html)

[Design for Offline](https://design.google/library/offline-design/)



**Know How to Test**

- Familiarize yourself with the accessibility settings of the operating systems that you build on.
- Keep assistive technologies in reach while building and testing new features to ensure that things work seamlessly for all users.  
- Simulate the most important use cases and user journeys with accessibility settings enabled.
- Test with real users in various countries as often as possible to track app performance, and insights into how to improve the user experience.

[Accessibility Scanner](http://g.co/accessibilityscanner)

[Testing your App’s Accessibility](https://developer.android.com/training/accessibility/testing.html)

[Chrome Accessibility Developer Tools](https://developers.google.com/web/updates/2018/01/devtools#a11y)



**Environmental Factors (Sun and Shade)**

- Make your app high-contrast by default—including all text and interaction design elements.

- Sufficient contrast is defined as having contrast ratio of 4.5:1 or higher for normal text, and 3:1 for large text greater than18p.

- Test your application with a dimmed screen, in bright sunshine, and through the perspective of people who have low vision (by using vision simulation tools).



[WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)

[Accessibility scanner](https://play.google.com/store/apps/details?id=com.google.android.apps.accessibility.auditor&hl=en)

[No Coffee Vision Simulator](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)



**Color**

More than 5% of the male population has some form of colorblindness. Don't rely on color alone.

- Include design elements to ensure that everyone who uses your app receives the same amount of information, regardless of ability to see or interpret colors.
- Use multiple visual cues to communicate important states of your app.  
- Utilize strokes, indicators, patterns, texture, or haptics to describe actions and content.
- Always pair visuals and icons with text, especially on navigation and call-to-action buttons, to ensure robust access.

[Color Blindness Simulator](http://www.color-blindness.com/coblis-color-blindness-simulator/)

[Material Design accessibility color and contrast guidelines](https://material.io/design/usability/accessibility.html#color-contrast)



**Screen Conditions and Input Abilities**

Many people use cracked screens which can make touch targets hard to tap.

- Always ensure that your designs work in both portrait and landscape mode. This ensures full functionality for people who need to mount their devices horizontally (eg. in front of their wheelchair) as well as those with broken screens with dead pixels.  
- Your app should render text, dates, times, addresses, and phone numbers in the local format without cutting or overlapping text.
- Check your visual designs through the lens of magnification and zoom, as designs often break when magnified by up to 200%.

[Material Design metrics and keylines](https://material.io/guidelines/layout/metrics-keylines.html)

[*Digital Trends* on cracked smartphone screens](https://www.digitaltrends.com/mobile/motorola-shattershield-cracked-smartphone-screen-survey/#/2)



**Touch Targets**

Large targets are helpful for those with hand termors, limited dexterity, or low vision.

- Remember minimum size standards for touch targets. 48x48px (or 48x48dp on Android) is the minimum recommended touch target size that helps those with limited dexterity or low vision as well as those with damaged screens.
- Ensure that your interface scales down to 4” and up to 7"+ screens.
- Make text and typography elements large by default.

[Android Accessibility Help](https://support.google.com/accessibility/android/answer/7101858?hl=en)

[Accessibility Scanner](https://play.google.com/store/apps/details?id=com.google.android.apps.accessibility.auditor&hl=en)