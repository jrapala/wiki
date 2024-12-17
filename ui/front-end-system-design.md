## Browser Formatting Contexts

`<html>` creates a formatting context and is the only element capable of creating a `block` formatting context (others need CSS).

Any element with a display style, like `display: inline-block;` creates a new formatting context. Every element placed inside of of it will now be rendered as an inline block item.

`<span>`'s, by default, create their own new inline formatting context.

## Browser Positioning

`position` allows you to override the normal flow of elements in the browser.

`position: relative;` removes the element from normal flow and isolates it completely, which is why it creates a new stacking context.