# Blurs

Blend modes allow us to control the way one view is rendered on top of another view. The default is `.normal`, which just draws the pixels from the new view onto whatever is behind. There are lots of options for controlling color and opacity.



## .multiply

`.multiply` multiplies each source pixel color with the destination pixel color – in our case, each pixel of the image and each pixel of the rectangle on top. Each pixel has color values for RGBA, ranging from 0 (none of that color) through to 1 (all of that color), so the highest resulting color will be 1x1, and the lowest will be 0x0.

Using multiply with a solid color applies a really common tint effect: blacks stay black (because they have the color value of 0, so regardless of what you put on top multiplying by 0 will produce 0), whereas lighter colors become various shades of the tint.

```swift
ZStack {
    Image("PaulHudson")

    Rectangle()
        .fill(Color.red)
        .blendMode(.multiply)
}
.frame(width: 400, height: 500)
.clipped()
```

There's a shortcut for this as well:

```swift
var body: some View {
    Image("PaulHudson")
        .colorMultiply(.red)
}
```

![](https://juliette-images.s3.us-east-2.amazonaws.com/public/swift007.png)



## .screen

The opposite of `.multiply`.

It inverts the colors, performs a multiply, then inverts them again, resulting in a brighter image rather than a darker image.

```swift
struct ContentView: View {
    @State private var amount: CGFloat = 0.0

    var body: some View {
        VStack {
            ZStack {
                Circle()
                    .fill(Color.red)
                    .frame(width: 200 * amount)
                    .offset(x: -50, y: -80)
                    .blendMode(.screen)

                Circle()
                    .fill(Color.green)
                    .frame(width: 200 * amount)
                    .offset(x: 50, y: -80)
                    .blendMode(.screen)

                Circle()
                    .fill(Color.blue)
                    .frame(width: 200 * amount)
                    .blendMode(.screen)
            }
            .frame(width: 300, height: 300)

            Slider(value: $amount)
                .padding()
        }
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .background(Color.black)
        .edgesIgnoringSafeArea(.all)
    }
}
```



![](https://juliette-images.s3.us-east-2.amazonaws.com/public/swift008.png)



**Note:** The middle is not white, but a pale lilac. This is because  `Color.red`, `Color.green`, and `Color.blue` aren’t fully those colors. You're seeing adaptive colors that are designed to look good in both dark mode and light mode.

To see full effects, use this:

```swift
.fill(Color(red: 1, green: 0, blue: 0))
.fill(Color(red: 0, green: 1, blue: 0))
.fill(Color(red: 0, green: 0, blue: 1))
```



## Additional Effects:

`saturation()` adjusts how much color is used inside a View (between 0 (gray) and 1).

```swift
struct ContentView: View {
    @State private var amount: CGFloat = 0.0

    var body: some View {
        VStack {
            Image("pup")
                .resizable()
                .scaledToFit()
                .frame(width: 200, height: 200)
                .saturation(Double(amount))
                .blur(radius: (1 - amount) * 20)
            
            Slider(value: $amount)
                .padding()
        }
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .background(Color.black)
        .edgesIgnoringSafeArea(.all)
    }
}
```

This gives you a slider for blurred and colorless to sharp and colorful.

