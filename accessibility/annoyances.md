# Accessibility Annoyances

If things are annoying they are probably inaccessibile and exclude people with disabilites.

**A Page Taking Too Long to Load**

The moment an element finishes loading, the screen reader should be alerted.

```html
<!-- while reloading -->
<section class="news-wrapper" aria-live="polite" aria-busy="true">
  <!-- contents -->
</section>

<!-- while stable -->
<section class="news-wrapper" aria-live="polite" aria-busy="false">
  <!-- contents -->
</section>
```



**Poor Functionality of a Desktop Site**



**App Leads to Web Site**



**Links Going to Different Places/Dead Links**



**Poor Content Layout**

Proper hierarchy allows your users to scan the page in a logical way.

Set up what is next for users in a way that aligns with their expectations.



**Pop-Ups**

They interrupt whatever we were about to do.

Always allow the user to close the pop-up once it appears.

Restrict focus to just the modal dialogs. https://handbook.floeproject.org/ModalDialogs.html

```html
<article role="dialog" aria-labelledby="dialogTitle" aria-describedby="dialogContents" aria-hidden="false">
	<h1 id="dialogTitle">Send email without a subject?</h1>
  <p id="dialogContents">Are you sure you want to send the email with a subject?</p>
  <input type="button" value="No">
  <input type="button" value="Yes">
</article>
```



**Icons Not Clear**



**FONTS ALL CAPS**



**Too Much Text**

Too much text can be overwhelming.

When people with dyslexia view too much text, they might not be able to distinguish between the letters.

One way to alleviate too much text is referring back to hierarchy and page layout, or breaking the content into different pages.



**Text Spans are Full Width**



**Misplaced Button Boxes**



**AutoPlay**

Videos and flashes can trigger seizures or panic and anxiety.

If you must have autoplay, provide a play/stop button, have the video only play once, refrain from using audio and opt to have captions with transcripts available.



**Uncertainty Related to Buttons and Links**

Buttons are used for actions that affect the website’s front-end or back-end.

Links are used for navigation and actions that don’t affect the website at all.



- Use color and underline to indicate a link.

- When used in navigation menus, a link’s underline may be removed, unless the link color is red or green, as those may cause issues with the most common forms of color blindness.

- Never underline or use the same link color for text that isn’t a link.

- Use different shades of the same color to indicate whether a website has been visited.

- Avoid certain hover effects like bold-facing, since this can realign text.

- - Sometimes a tooltip indicating where the link will lead to is helpful.
  - If a custom tooltip is not feasible, the built-in tooltip provided by most browsers will suffice in informing users the link’s destination. This can be achieved using the title attribute.

- Most links should be over 12 points, unless it is a less-needed link, like copyright info.



**Allow Users to Skip to Content**



**Correct Contrast**