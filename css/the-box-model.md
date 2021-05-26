# The Box Model

The `box-sizing` CSS property allows us to change the rules for size calculations. The default value (`content-box`) only takes the inner content into account, but it offers an alternative value: `border-box`.

With a `width` set to `100%`, a div would take up 100% of the available width. By default, this refers specifically to the element's *inner content*.

With `box-sizing: border-box`, things behave **much** more intuitively.



The size of the following box is 500px x 250px:

```css
<style>
  * {
    box-sizing: border-box;
  }
  section {
    width: 500px;
    height: 250px;
  }
  .box {
    width: 100%;
    height: 100%;
    padding: 30px;
    border: 4px solid;
    margin: 20px;
  }
</style>
<section>
  <div class="box"></div>
</section>
```



The size of the following box is 450px x 200px:

```css
<style>
  * {
    box-sizing: border-box;
  }
  section {
    width: 500px;
    height: 250px;
    padding: 25px;
  }
  .box {
    width: 100%;
    height: 100%;
    border: 2px solid;
  }
</style>
<section>
  <div class="box"></div>
</section>
```



The size of the following box is 400px x 200px:

```css
<style>
  * {
    box-sizing: border-box;
  }
  main {
    width: 900px;
    height: 500px;
    border: 10px solid;
  }
  section {
    width: 50%;
    height: 100%;
    padding: 20px;
    border-bottom: 40px solid;
  }
  .box {
    width: 100%;
    height: 50%;
  }
</style>
<main>
  <section>
    <div class="box"></div>
  </section>
</main>
```

Our `main` element is 900×500, which shrinks to a content box of 880×480 after accounting for the border.

Our `section` element consumes 50% of that width (440px), and its content box is 400px wide (subtracting 20px padding from each side).

Our `section` element also consumes 100% of the available height (480px), but its content box is only 400px tall (subtracting 20px top padding, 20px bottom padding, and 40px bottom border).

Therefore, our `.box` element lives in a context box of 400×400. It fills that available width, but only takes up half of the available height, for a final answer of 400×200.



**To Default to Border-Box:**

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```



### Border

There are three styles specific to border:

- Border width (eg. `3px`, `1em`)
- Border style (eg. `solid`, `dotted`)
- Border color (eg. `hotpink`, `black`)

They can be combined into a shorthand. The only required field is `border-style`.

```css
.box {
  border: 3px solid hotpink;
}
```

If we don't specify a border color, *it'll use the font's color by default*. 

If you want to specify this behaviour explicitly, it can be done with the special `currentColor` keyword - a reference to the element's derived text color (whether set or inherited).

```css
.box {
  color: hotpink;
  border: 1px solid currentColor;
  box-shadow: 2px 2px 2px currentColor;
}
```



**Types of Border Styles**

- solid

- dotted

- dashed

- double

- groove

- ridge

- inset

- outset

  

**Border-Radius**

You can use percentages. 50% will turn your shapre into a circle or oval, since each corner's radius is 50% of the total width/height.



### Margin

Negative margins can:

- Pull an element outside it's parent
- Pull a element's sibling closer
- Effect the position of **all** siblings (if, for example, added to the first box in a series)

Another way to shift the position of an element is with `translate`

[The Intricacies of Negative Margin](https://www.quirksmode.org/blog/archives/2020/02/negative_margin.html)



**Auto** seeks to fill the maximum available space.

- This only works for horizontal margin
- The only works on elements with an explicit width



**Exercise:** Stretch an image over page gutters to the full width of the page.

- Note, images are a sort of foreign object. They are embedded into the DOM.

- Solution: Wrap the image in a div. Add negative margins to the div.

  ```css
  .wrapper {
    margin-left: -32px;
    margin-right: -32px;
  }
  ```

  