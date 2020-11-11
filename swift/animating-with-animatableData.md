# Animating Simple Shapes with animatableData

Here is a Trapezoid that changes shape on every click:



```swift
struct ContentView: View {
    @State private var insetAmount: CGFloat = 50

    var body: some View {
        Trapezoid(insetAmount: insetAmount)
            .frame(width: 200, height: 100)
            .onTapGesture {
					    withAnimation {
				        self.insetAmount = CGFloat.random(in: 10...90)
					    }
            }
    }
}

struct Trapezoid: Shape {
    var insetAmount: CGFloat

    func path(in rect: CGRect) -> Path {
        var path = Path()

        path.move(to: CGPoint(x: 0, y: rect.maxY))
        path.addLine(to: CGPoint(x: insetAmount, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.maxX - insetAmount, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY))
        path.addLine(to: CGPoint(x: 0, y: rect.maxY))

        return path
   }
}
```

The `withAnimation` doesn't do anything though.

As soon as `self.insetAmount` is set to a new random value, it will immediately jump to that value and pass it directly into `Trapezoid` – it won’t pass in lots of intermediate values as the animation happens. This is why our trapezoid jumps from inset to inset; it has no idea an animation is even happening.



To fix this, add a new computed property to the `Trapezoid` struct:

```swift
var animatableData: CGFloat {
    get { insetAmount }
    set { self.insetAmount = newValue }
}
```

Behind the scenes, SwiftUI is now keeping track of the changing value over time as part of the animation.

As the animation progresses, SwiftUI will set the `animatableData` property of our shape to the latest value, and it’s down to us to decide what that means – in our case we assign it directly to `insetAmount`, because that’s the thing we want to animate.

Remember, SwiftUI evaluates our view state before an animation was applied and then again after. It can see we originally had code that evaluated to `Trapezoid(insetAmount: 50)`, but then after a random number was chosen we ended up with (for example) `Trapezoid(insetAmount: 62)`. So, it will interpolate between 50 and 62 over the length of our animation, each time setting the `animatableData` property of our shape to be that latest interpolated value – 51, 52, 53, and so on, until 62 is reached.