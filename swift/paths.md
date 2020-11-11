# Paths

A `Path` type can be used for drawing custom shapes.

A triangle:

```swift
var body: some View {
    Path { path in
        path.move(to: CGPoint(x: 200, y: 100))
        path.addLine(to: CGPoint(x: 100, y: 300))
        path.addLine(to: CGPoint(x: 300, y: 300))
        path.addLine(to: CGPoint(x: 200, y: 100))
    }
	  .fill(Color.blue)
}
```

This uses exact coordinates so better to use `GeometryReader` to design according to our device.



A `CGPoint` is part of `Core Graphics` which gives us a selection of basic types:

- `CGPoint` lets us reference X/Y coordinates
- `CGSize` gives us heights and widths
- `CGRect` gives us rectangular frames
- `CGFloat` is a number



## Adding a Fill

```swift
.fill(Color.blue)
```



## Using a Stroke

```swift
.stroke(Color.blue, lineWidth: 10).opacity(0.25)
```



Rounded corners:

```swift
.stroke(Color.blue, style: StrokeStyle(lineWidth: 10, lineCap: .round, lineJoin: .round))
```



To prevent a stroke from spilling outside the screen:

```swift
Circle()
    .strokeBorder(Color.blue, lineWidth: 40)
```



## Building a Shape

Shapes are built with Paths. There is a `Shape` protocol with a single required method: given the following rectangle, what path do you want to draw? 

The `CGRect` gives properties like `minX` and `midY`

```swift
struct Triangle: Shape {
    func path(in rect: CGRect) -> Path {
        var path = Path()

        path.move(to: CGPoint(x: rect.midX, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.minX, y: rect.maxY))
        path.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY))
        path.addLine(to: CGPoint(x: rect.midX, y: rect.minY))

        return path
    }
}


Triangle()
    .fill(Color.red)
    .frame(width: 300, height: 300)
```



### An Arc

```swift
struct Arc: Shape {
    var startAngle: Angle
    var endAngle: Angle
    var clockwise: Bool

    func path(in rect: CGRect) -> Path {
        var path = Path()
        path.addArc(center: CGPoint(x: rect.midX, y: rect.midY), radius: rect.width / 2, startAngle: startAngle, endAngle: endAngle, clockwise: clockwise)

        return path
    }
}

Arc(startAngle: .degrees(0), endAngle: .degrees(110), clockwise: true)
    .stroke(Color.blue, lineWidth: 10)
    .frame(width: 300, height: 300)
```

**Notes**

- This will appear 90 to 200 degress, not 0 to 110
- 0 degrees is not straight up, but directly to your right
- Shapes measure their coordinates from bottom-left rather than top-left



To make this the way nature intended, add some adjustments:

```swift
struct Arc: Shape {
    var startAngle: Angle
    var endAngle: Angle
    var clockwise: Bool

    func path(in rect: CGRect) -> Path {
        let rotationAdjustment = Angle.degrees(90)
        let modifiedStart = startAngle - rotationAdjustment
        let modifiedEnd = endAngle - rotationAdjustment

        var path = Path()
        path.addArc(center: CGPoint(x: rect.midX, y: rect.midY), radius: rect.width / 2, startAngle: modifiedStart, endAngle: modifiedEnd, clockwise: !clockwise)

        return path
    }
}

```



## Using .strokeBorder with an Arc

Circles also conform to the `InsettableShape` protocol so you need to adjust our previous `Arc` code to user .strokeBorder:

```swift
struct ContentView: View {
    var body: some View {
        Arc(startAngle: .degrees(-90), endAngle: .degrees(90), clockwise: true)
            .strokeBorder(Color.blue, lineWidth: 40)
    }
}

struct Arc: InsettableShape {
    var startAngle: Angle
    var endAngle: Angle
    var clockwise: Bool
    var insetAmount: CGFloat = 0
    
    func path(in rect: CGRect) -> Path {
        let rotationAdjustment = Angle.degrees(90)
        let modifiedStart = startAngle - rotationAdjustment
        let modifiedEnd = endAngle - rotationAdjustment

        var path = Path()
        path.addArc(center: CGPoint(x: rect.midX, y: rect.midY), radius: rect.width / 2 - insetAmount, startAngle: modifiedStart, endAngle: modifiedEnd, clockwise: !clockwise)

        return path
    }
    
    func inset(by amount: CGFloat) -> some InsettableShape {
        var arc = self
        arc.insetAmount += amount
        return arc
    }
}
```

