# Flow Layout

Flow layout is the default layout (grid and flexbox are other examples of layouts).

Every HTML element has an intrinsic `display` value of either `inline`, `block` or  `inline-block`

`inline` elements are generally meant to highlight a seleciton of text.

Most elements are `block` elements. e.g. `div`, `section`, `nav`, `header`, `footer`, `article`, `p`, `form`, `h1`, `blockquote`



### Rules

- `inline` elements can be shifted with `margin-left` or `margin-right`, but you can't change its width or height. They can be given vertical padding, but the results are surprising.
  - There are two exceptions to this rule:
    - **Replaced Elements**: An element that embeds a foreign object. e.g. `img`, `video`, `canvas`. These behave like foreign objects within inline wrappers. Width and heights are applied to the object, but the whole thing is still `inline`.
    - **Buttons**: They can be given a width and a height
- `block` elements don't share. They expand to fill the entire available horizontal space. We can use `width: fit-content` to shrink it down to the minimum required size (e.g. there is text in it)
- `inline` elements have "magic space". When an image is in a div, you may notice space around images that isn't padding or margin. The browser treats inline elements like typography. 
  - To fix: 1. Set images to `display: block`. 2. Set the `line-height` of the wrapping div to 0.
  - This may also be due to the space-sensitive nature of HTML. Try removing any whitespace between image tags.
- `inline` elements can line-wrap. Adding a border around some text will cause the rectangle to split at the wrap.
  - If you need to add horizontal space to wrapped elements, try `box-decoration-break: clone` alone with some horizontal padding. Without it, the padding is only applied to the beginning and end of the line.
- `inline-block` elements internally act line `block` but externally act like `inline`. So, you can give it a `width`. 
  - Note: `inline-block` does not line wrap. So, if you `inline-block` a link, the whole link will shift to the next line during a wrap.



### Width Algorithms

- Percentage based widths are based on the parent element's content space. If the `body` tag makes 400px of space available, any child with 100% width will become 400px wide, regardless of any other circumstances.
- The default `width` of `block` elements is `auto`. It will grow as much as it's able to. So, by default, block elements have dynamic sizing. They're context-aware.



**Keyword Values**

There are two kinds of values we can specify for `width`:

- Measurements (100%, 200px, 5rem)
  - Can be explicit or relative
- Keywords (auto, fit-content)
  - Specifies behavior depending on the context
  - `min-content` specifies that we want our content to become as small as it can, based on the child contents.
  - `max-content` never adds any line-breaks. The elements width will be the smallest value that contains all of the content without breaking it up.
  - `fit-content` doesn't add line breaks unless it's too wide to fit in the parent. It adds line breaks as needed to ensure it never exceeds the available space. 



**Min and Max Widths**

`min-width` and `max-width` adds constrains to an element's size.