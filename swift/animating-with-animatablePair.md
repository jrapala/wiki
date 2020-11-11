# Animating Complex Shapes with AnimatablePair

`animatableData` is a property, which means it must always be one value.

It can however be an  `AnimatablePair` instead of a  `CGFloat`

An `AnimatablePair` are two values in a special wrapper. It is used in this Checkboard to animate from 4x4 to 8x16.



```swift
struct ContentView: View {
    @State private var rows = 4
    @State private var columns = 4

    var body: some View {
        Checkerboard(rows: rows, columns: columns)
            .onTapGesture {
                withAnimation(.linear(duration: 3)) {
                    self.rows = 8
                    self.columns = 16
                }
            }
    }
}

struct Checkerboard: Shape {
    var rows: Int
    var columns: Int
  
    public var animatableData: AnimatablePair<Double, Double> {
        get {
           AnimatablePair(Double(rows), Double(columns))
        }

        set {
            self.rows = Int(newValue.first)
            self.columns = Int(newValue.second)
        }
    }

    func path(in rect: CGRect) -> Path {
        var path = Path()

        // figure out how big each row/column needs to be
        let rowSize = rect.height / CGFloat(rows)
        let columnSize = rect.width / CGFloat(columns)

        // loop over all rows and columns, making alternating squares colored
        for row in 0..<rows {
            for column in 0..<columns {
                if (row + column).isMultiple(of: 2) {
                    // this square should be colored; add a rectangle here
                    let startX = columnSize * CGFloat(column)
                    let startY = rowSize * CGFloat(row)

                    let rect = CGRect(x: startX, y: startY, width: columnSize, height: rowSize)
                    path.addRect(rect)
                }
            }
        }

        return path
    }
}
```



The `animatableData` property that will be set with intermediate values as the animation progresses. Note that:

1. We have two properties that we want to animate, not one. So we use an `AnimatablePair`. 
2. Our `row` and `column` properties are integers, and SwiftUI can’t interpolate integers. So we use `Int(someDouble)` to convert.



You can animate more than two properties with the `EdgeInsets` type:

```swift
AnimatablePair<CGFloat, AnimatablePair<CGFloat, AnimatablePair<CGFloat, CGFloat>>>
```

You can dig into this with `newValue.second.second.first`

