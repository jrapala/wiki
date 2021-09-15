# Accessible Images

### Logo Images

The alt text should say where the page leads. Not "Logo of..", etc..

```
<a href="#">
	<img
		src="https://...."
		alt="Homepage"
	/>
</a>
```



### Captions

Use an empty `alt` is still needed. The caption will be read. Connection the caption to the image with an `aria-labelledby` will cause the caption to be read twice.

```
<figure>
	<img
		src="https://...."
		alt=""
	/>
	<figcaption id="caption">A white cotton shirt.</figcaption>
</figure>
```

