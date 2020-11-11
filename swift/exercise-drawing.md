# Exercise - Drawing

1. Create an `Arrow` shape made from a rectangle and a triangle – having it point straight up is fine.
2. Make the line thickness of your `Arrow` shape animatable.

```swift
import SwiftUI

struct ContentView: View {
    @State private var arrowThickness: CGFloat = 50
    @State private var colorCycle = 0.0

    var body: some View {
        VStack(spacing: 0) {
            ArrowTop()
            ArrowBottom(thickness: arrowThickness)
        }
        .frame(width: 200, height: 500)
        .onTapGesture {
            withAnimation {
                self.arrowThickness = CGFloat.random(in: 10...100)
            }
        }
        .foregroundColor(.purple)
    }
}

struct ArrowTop: Shape {
    func path(in rect: CGRect) -> Path {
        var path = Path()
        
        path.move(to: CGPoint(x:rect.midX, y:rect.midY))
        path.addLine(to: CGPoint(x: rect.minX, y: rect.maxY))
        path.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY))
        path.addLine(to: CGPoint(x: rect.midX, y: rect.midY))
        

        return path
    }
}

struct ArrowBottom: Shape {
    var thickness: CGFloat
    
    var animatableData: CGFloat {
        get { thickness }
        set { self.thickness = newValue }
    }
    
    func path(in rect: CGRect) -> Path {
        var path = Path()
        
        path.move(to: CGPoint(x:rect.midX - thickness / 2, y:rect.minY))
        path.addLine(to: CGPoint(x: rect.midX - thickness / 2, y: rect.maxY))
        path.addLine(to: CGPoint(x: rect.midX - thickness / 2 + thickness, y: rect.maxY))
        path.addLine(to: CGPoint(x: rect.midX - thickness / 2 + thickness, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.midX - thickness / 2, y: rect.minY))
        
        return path
    }
}



struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

