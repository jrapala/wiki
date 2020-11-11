# Metal

Most iOS devices render at 120fps. It is powered by Core Animation and offers great performance out of the box.

This code works well, even though is renders 100 views:

```swift
struct ColorCyclingCircle: View {
    var amount = 0.0
    var steps = 100

    var body: some View {
        ZStack {
            ForEach(0..<steps) { value in
                Circle()
                    .inset(by: CGFloat(value))
                    .strokeBorder(self.color(for: value, brightness: 1), lineWidth: 2)
            }
        }
    }

    func color(for value: Int, brightness: Double) -> Color {
        var targetHue = Double(value) / Double(self.steps) + self.amount

        if targetHue > 1 {
            targetHue -= 1
        }

        return Color(hue: targetHue, saturation: 1, brightness: brightness)
    }
}
```



```swift
struct ContentView: View {
    @State private var colorCycle = 0.0

    var body: some View {
        VStack {
            ColorCyclingCircle(amount: self.colorCycle)
                .frame(width: 300, height: 300)

            Slider(value: $colorCycle)
        }
    }
}
```



However it will struggle if you use gradients instead:

```swift
.strokeBorder(LinearGradient(gradient: Gradient(colors: [
    self.color(for: value, brightness: 1),
    self.color(for: value, brightness: 0.5)
]), startPoint: .top, endPoint: .bottom), lineWidth: 2)
```



To fix this, we can add this modifier. This tells SwiftUI to render the contents of the view in an off-screen image before putting it back onto the screen. This is powered by Metal, which is Apple’s framework for working directly with the GPU for extremely fast graphics.

```swift
// ColorCyclcingCircle body
var body: some View {
    ZStack {
        // existing code…
    }
    .drawingGroup()
}
```



**Note:** Use this only when necessary.

