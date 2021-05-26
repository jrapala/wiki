# CSS Basics

### Adding CSS

```html
<link rel="stylesheet" href="styles.css">

// Or, quick styles (not recommended)
<style>
p {
  color: green;
}
</style>
```



### CSS Anatomy

```css
p {
  color: green;
}
```

- **selector**: `p`
- **property**: `color`
- **value**: `green`
- **declaration**: `color: green;`
- **rule:** `p { color: green; }`



### Selectors

```css
/*  all p and li */
/*  NOTE! If any of the selectors are invalid, the rule won't be applied. */
p, li {
    color: green;
}

/*  all of class "special" */
.special {
  color: orange;
  font-weight: bold;
}

/*  any li element that has a class of "special" */
li.special {
  color: orange;
  font-weight: bold;
}

/*  any li or span element that has a class of "special" */
li.special,
span.special {
  color: orange;
  font-weight: bold;
}

/* descendant combinator
	 select an em nested inside an li -- anywhere in the tree (not just direct children) */
li em {
  color: rebeccapurple;
}

/* adjacent sibling combinator
	 select a paragraph when it comes directly after an h1, at the same
	 hierarchy level */
h1 + p {
  font-size: 200%;
}

/* descendant selector
	 on target direct decendents of class "main-list" */
.main-list > li {
  border: 2px dotted;
}

/* state selector (or pseudo-class selector)
	 unvisited links */
a:link {
  color: pink;
}

/* checkboxes and radio buttons that are filled */
input:checked {
	width: 24px;
  height: 24px;
}

/* Any element with a class of "special", which is inside a <p>, which comes just after an <h1>, which is inside a <body>. */
body h1 + p .special {
  color: yellow;
  background-color: black;
  padding: 5px;
}

/* Only navigation links */
nav a {
	color: red;
  font-weight: bold;
}


```



### Media Queries

Media queries use the `@media` syntax. 

```css
@media (condition) {
  /* Some CSS that'll run if the condition is met. */
}
```

Examples of conditions:

- `max-width: 300px`: only applies if the window is between 0px and 300px wide
  - Use to style small screens
- `min-width: 300px`: only applies if the window is greather than or equal to 300px wide
  - USe to style large screens



### Units

- A `px` corresponds more-or-less with what you see on the screen. Never use `px`  for typography. Josh Comeau usually uses `px`  for box model properties (more intuitive than `rem`).
  - React will automatically assume you're using pixels for more propety values (not unitless properties like `line-height` though.)
  - Pixels can be argued to actually be more accessible than `rem` in the box model (e.g. padding). The padding stays fixed as font sizes go up. It won't get as distorted.
- An `em` is equal to the computed font size of the element's parent. So, `1 em` of an `H1` is equal to the size of the letter "m" in an `H1`. Rarely used. Only use if you need to scale one property directly with font size.
- Use `rem` everywhere, including margins and padding, except for borders. Use px for those--they are only decroative. 
- Root font-size for most browsers is 16px.
- [Conversion Tool](http://pxtoem.com/)
- Don't set root font size with px. The size should be determined by browser settings.
- `%` is often used with width/height, as a way to consume a portion of available space.
  - Use pixels for the box model. Percentages are only useful if you need to preserve specific apect ratios.



### User-Agent Stylesheet

Every browser includes a style sheet full of base styles. Here is the full [Chrome stylesheet](https://source.chromium.org/chromium/chromium/src/+/master:third_party/blink/renderer/core/html/resources/html.css).

A **CSS reset** flattens out user-agent stylesheets across different browsers, to ensure every browser starts with the same core set of styles.



### Inheritance

Certain CSS properties inherit properties from their parents, grandparents, etc..

Not all CSS properties are inheritable. Most of the properties that are inheritable are typography related (`color`, `font-size`, `text-shadow`). [List of inheritable properties.](https://www.sitepoint.com/css-inheritance-introduction#list-css-properties-inherit)

e.g. This `em` will inherit the color but not the border.

```css
<style>
  p, em {
    border: 1px solid hotpink;
    color: hotpink;
  }
</style>

<p>
  I know <em>you</em> are, but what am I?
</p>
```



You can force inheritance:

```css
<style>
  a {
    color: -webkit-link;
    cursor: pointer;
    text-decoration: underline;
  }
  a {
    color: inherit;
  }
</style>

<p>
  This paragraph contains <a href="#">a blue hyperlink</a>!
</p>
<p style="color: red;">
  This is a red paragraph with <a href="#">a red link</a>.
</p>
```



### Directions

**Block Direction**: is like lego blocks. They stack together one on top of the other.

**Inline Direction**: is like standing in line. They stand side by side, not on on top of the other.

These are used for property names as well:

```css
.asymmetric-padding {
  padding-top: 20px;
  padding-bottom: 40px;
  padding-left: 60px;
  padding-right: 80px;
}

/* The same thing, but using ✨ logical properties ✨ */
.asymmetric-logical-padding {
  padding-block-start: 20px;
  padding-block-end: 40px;
  padding-inline-start: 60px;
  padding-inline-end: 80px;
}
```



## CSS Isn't Perfect

[CSSWG published list of mistakes they're made in the CSS language](https://wiki.csswg.org/ideas/mistakes)