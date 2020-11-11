# Showing and Hiding Views

## Using Sheets

```swift
struct ContentView: View {
    @State private var showingSheet = false
    
    var body: some View {
        Button("Show Sheet") {
            self.showingSheet.toggle()
        }
        .sheet(isPresented: $showingSheet){
            SecondView(name: "Juliette")
        }
    }
}

struct SecondView: View {
    var name: String
    
    var body: some View {
        Text("Hello, \(name)")
    }
}
```



### Dismiss a Sheet with a Button

You must tap into the environment:

```swift
struct ContentView: View {
    @State private var showingSheet = false
    
    var body: some View {
        Button("Show Sheet") {
            self.showingSheet.toggle()
        }
        .sheet(isPresented: $showingSheet){
            SecondView(name: "Juliette")
        }
    }
}

struct SecondView: View {
    var name: String
    @Environment(\.presentationMode) var presentationMode
    
    var body: some View {
        Button("Dismiss") {
            self.presentationMode.wrappedValue.dismiss()
        }
    }
}

```

