# Typography

- A **web safe** font comes preinstalled on all major operating systems. e.g. Arial, Times New Roman, Tahoma

- A **web font** is a custom font downloaded by the browser.
  - Typically surrounded in quotation marks. e.g. `font-family: 'Roboto', sans-serif;`

- Bold fonts are typically `700`.
- Use `<strong>` for semantic meaning (critically important items).
- A browser will create it's own default bold or italic font if none is available. If we supply a normal font and use `font-weight: bold`, the browser will beef it up to make it look bold (generally muddy).
- Underlined text typically communicates a link. `text-decoration` property.
- Not all links need underlines (e.g. nav links). Nav links rely on other cues to convey that they are clickable.
- `text-transform`: Changes the formatting of your text. `uppercase`, `capitalize`
- `letter-spacing`: Changes the horizontal gap between characters
- `line-height`: Changes the vertical distane between lines. It is unitless. `line-height: 2` means twice as tall as normal.