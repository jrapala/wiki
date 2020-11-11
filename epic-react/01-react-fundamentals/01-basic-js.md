# Basic JS "Hello World"

Modern JavaScript frameworks help programmatically create the DOM



## Exercise

- Create a div DOM node with the text "Hello World"



```html
<html>
  <body>
    <script type="module">
      const root = document.createElement('div')
      root.id = 'root'
      document.body.append(root)
      const div = document.createElement('div')
      div.textContent = 'Hello World'
      div.classList = 'container'
      root.append(div)
    </script>
  </body>
</html>

```

