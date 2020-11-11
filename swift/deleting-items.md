# Deleting Items



## Using .onDelete()

`onDelete()` only exists on `ForEach`, so you need to use it when creating content:

```swift
struct ContentView: View {
    @State private var numbers = [Int]()
    @State private var currentNumber = 1
    
  	// The IndexSet is a parameter that contains sorted integers
    func removeRows(at offsets: IndexSet) {
        numbers.remove(atOffsets: offsets)
    }

    var body: some View {
        VStack {
            List {
                ForEach(numbers, id: \.self) {
                    Text("\($0)")
                }
                .onDelete(perform: removeRows)
            }

            Button("Add Number") {
                self.numbers.append(self.currentNumber)
                self.currentNumber += 1
            }
        }
    }
}
```



### Adding a Delete Button to a Navigation View

```swift
.navigationBarItems(leading: EditButton())
```

