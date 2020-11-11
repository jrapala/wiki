# ImagePaint

We can use `Color` as a `View`, but it conforms to a different protocol - `ShapeStyle` when used for fills, strokes and borders.



## Using ImagePaint

There is an `ImagePaint` type that gives you more control over images. It lets you use images for borders and fills.



Here we use an image in a border that is rendered at 1/5th the normal size:

```swift
Text("Hello World")
    .frame(width: 300, height: 300)
    .border(ImagePaint(image: Image("Example"), scale: 0.2), width: 30)
```



Here, we will show the entire width of our example image, but only the middle half. You must pass in a `CGRect`:

```swift
Text("Hello World")
    .frame(width: 300, height: 300)
    .border(ImagePaint(image: Image("Example"), sourceRect: CGRect(x: 0, y: 0.25, width: 1, height: 0.5), scale: 0.1), width: 30)
```



`ImagePaint` can also be used for strokes. Here we have a Capsule with our image tiled as its stroke. The image is tiled until it has filled the area.

```swift
Capsule()
    .strokeBorder(ImagePaint(image: Image("Example"), scale: 0.1), lineWidth: 20)
    .frame(width: 300, height: 200)
```

