# Accessibilty Basics

Resource to check out: https://a11y.coffee/



### Input and Labels

Screen readers need to announce what label is associated with an input.

Link labels to inputs:

```html
// Using attributes
<label for="username">Username</label>
<input type="text" name="username" id="username">  

// Using a wrapper
<label>
	Username
	<input type="text" name="username">
</label>  

// Using aria-labelledby if there is more than one suitable label
<h2 id="resetHeading">Reset Password</h2>
<span id="resetUsername">Username</span>
<input type="text" name="username" aria-labelledby="resetHeading resetUsername">  

// For non-visual labels
<input type="text" name="searchTerm" aria-label="Search">
<button type="submit">Search</button>

```



### Add Text Alternatives When Using an Icon Font

[Font Awesome Guide](https://fontawesome.com/how-to-use/on-the-web/other-topics/accessibility)

If replacing text with an icon on small screens (i.e. using a **semantic icon** instead of a **decorative icon**), have the screen reader read the text.

**Decorative Icon:**

```html
<button>  
  Download    
  <i class="fas fa-download" aria-hidden="true"></i>
 <button> 

```

`aria-hidden` set to true so screen reader doesn't announce Unicode character



**Semantic Icon:**

```html
<style>    
  .download-text {    
    display: none;  
  }   
  
	@media screen and (min-width: 768px) {    
    .download-text {      
      display: inline;    
    }  
  }
</style>  

<button>  
  <span class="sr-only">Download</span>
  <span class="download-text" aria-hidden="true">Download</span>
  <i class="fas fa-download" aria-hidden="true" title="Download"></i> // title for tooltip
</button>  

```

The `.sr-only` class positions the text outside the viewport, so it can't be seen, but exists in the DOM.



**Semantic Icons that Require Focus**

No need for a hidden span:

```html
<a aria-label="Delete" class="btn btn-danger" href="path/to/settings">
	<i aria-hidden="true" class="fas fa-trash" title="Delete this item?"></i>
</a>
```



### Click Handlers

Do not add click handlers to non-interactive elements:

```html
// NO
<div onClick={this.addToBasket}>Add To Basket</div>    
<span @click="checkout">Checkout</span>

// YES
<button onClick={this.addToBasket}>Add To Basket</button>
	<a href="#" @click="checkout">Checkout</a>  


```



### Eay Improvements To Make

1. **Increase Color Contrast**: use WebAIM Contrast Checker
2. **Increase Font Legibility**
3. **Improve Semantic Markup**
4. **Improve Keyboard-Only Navigation**
5. **Enhance Functionality for Screen Readers**



### Better Outlines

```css
*:focus {
	outline: 4px solid lime; /* use the opposite of your brand color */
}
```

