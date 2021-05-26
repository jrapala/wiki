# The Cascade

In this example, the paragraph is violet:

```css
<style>
  p {
    font-weight: bold;
    color: hsl(0deg 0% 10%);
  }
  .introduction {
    color: violet;
  }
</style>
<p class="introduction">
  Hello world
</p>
```

This is due to the **cascade algorithm**. The browser will take a set of applicable style rules, and whittle it down to a list of specific declarations that are applicable. This depends on the **specificity** of the selector.

- Classes are more specific than tags
- IDs are more specific than classes

