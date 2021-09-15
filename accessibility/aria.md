# ARIA

**Accessibile Rich Internet Applications (ARIA):** good for bridging areas with accessibilty issues that cann't be managed with native HTML. It works by allowing you to specify attributes that modify the way an element is translated into the accessibilty tree.



This looks like a checkbox to visual users, but not a screen reader:

```html
<li tabindex="0" class="checkbox" checked>
	Receive promotional offers
</li>
```



This looks like a checkbox to both visual users and screen readers:

```html
<li tabindex="0" class="checkbox" role="checkbox" checked aria-checked="true">
	Receive promotional offers
</li>
```

