# Core Data

Core Data is Apple's framework for working with databases, first introduced with iPhoneOS 3. It integrates well with SwiftUI.

## What is Core Data?

1. An object graph and persistence framework. (Lets us define objects and read and write them from permanent storage). Much more advanced than Codable and UserDefaults.
2. It is capable of sorting and filtering data.
3. There's essentially no limit to how much data it can store.
4. It implements data validation, lazy loading, undo, redo, and more.



## Using Core Data

When adding Core Data to a project, it add an `.xcdatamodeld` file. This describes your data model. There is also extra code in `AppDelegate.swift` and `SceneDelegate.swift`.



## How to Set Up Core Data

1. Create a **persistent container**, which loads and saves data from storage
2. Inject it into the SwiftUI environment via a fetch request.



Xcode does this for us automatically in the template.



## Updating the Model

We define types as "entities" and properties as "attributes"



## Fetching Data

A FetchRequest takes the entity you want to query and how you want the results to be sorted.

Here we fetch the `Student` entity, apply no sorting, and place it into the `students` property which has a type of `FetchedResults<Student>`

```swift
@FetchRequest(entity: Student.entity(), sortDescriptors: []) var students: FetchedResults<Student>

```

It can now be used like this:

```swift
    var body: some View {
        VStack {
            List {
                ForEach(students, id: \.id) { student in
                    Text(student.name ?? "Unknown")
                }
            }
        }
    }
```

Note the optional. CoreData will **always** generate optional Swift properties.



## Adding Data

First, we need a property to store data, called a **managed object context**.

```swift
@Environment(\.managedObjectContext) var moc
```

Then, add data to it:

```swift
Button("Add") {
    let firstNames = ["Ginny", "Harry", "Hermione", "Luna", "Ron"]
    let lastNames = ["Granger", "Lovegood", "Potter", "Weasley"]

    let chosenFirstName = firstNames.randomElement()!
    let chosenLastName = lastNames.randomElement()!

  	// Create a Student object, and attach it to the moc
  	// Then, assign values 
    let student = Student(context: self.moc)
		student.id = UUID()
		student.name = "\(chosenFirstName ?? "") \(chosenLastName ?? "")"
  
  	// Save it
	  try? self.moc.save()
}
```



## Ensuring Core Data Objects are Unique Using Constraints

**Constraints** exist to allow us to make sure that one attribute is always unique. No sense to have two separate contacts that share the same email address. Constraints let Core Data resolve duplicates so only one piece of data gets written. 

You can add constraints in the Data Model Inspector (under View). 

In this code, if the Wizard name is constrained, adding the same name multiple times results in an error.

```swift
struct ContentView: View {
    @Environment(\.managedObjectContext) var moc

    @FetchRequest(entity: Wizard.entity(), sortDescriptors: []) var wizards: FetchedResults<Wizard>

    var body: some View {
        VStack {
            List(wizards, id: \.self) { wizard in
                Text(wizard.name ?? "Unknown")
            }

            Button("Add") {
                let wizard = Wizard(context: self.moc)
                wizard.name = "Harry Potter"
            }

            Button("Save") {
                do {
                    try self.moc.save()
                } catch {
                    print(error.localizedDescription)
                }
            }
        }
    }
}
```

To actually save the change, you need to update `SceneDelegate.swift`:

```swift
import CoreData
```

And add to `willConnectTo`, under `let context`:

```swift
context.mergePolicy = NSMergeByPropertyObjectTrumpMergePolicy

```

Swift will now merge duplicate objects based on their properties. Each time you save a duplicate, Swift collapses it down to a single row.





## Filtering @FetchRequest using NSPredicate

Providing an `NSPredicate` to the `@FetchRequest` controls which results we see. Essentially, it's a filter.

To show the `name` and `universe` attributes in a `Ship` entity:

```swift
import CoreData
import SwiftUI

struct ContentView: View {
    @Environment(\.managedObjectContext) var moc
    @FetchRequest(entity: Ship.entity(), sortDescriptors: [], predicate: NSPredicate(format: "universe == 'Star Wars'")) var ships: FetchedResults<Ship>

    var body: some View {
        VStack {
            List(ships, id: \.self) { ship in
                Text(ship.name ?? "Unknown name")
            }

            Button("Add Examples") {
                let ship1 = Ship(context: self.moc)
                ship1.name = "Enterprise"
                ship1.universe = "Star Trek"

                let ship2 = Ship(context: self.moc)
                ship2.name = "Defiant"
                ship2.universe = "Star Trek"

                let ship3 = Ship(context: self.moc)
                ship3.name = "Millennium Falcon"
                ship3.universe = "Star Wars"

                let ship4 = Ship(context: self.moc)
                ship4.name = "Executor"
                ship4.universe = "Star Wars"

                try? self.moc.save()
            }
        }
    }
}
```



### Different Ways to Use NSPredicate

- `%@` syntax which means "insert some data here":

  ```swift
  NSPredicate(format: "universe == %@", "Star Wars")
  ```

- You can use comparisons such as `==`, `>`, or `<`:

  ```swift
  // Return attribute values that start with letters before F
  NSPredicate(format: "name < %@", "F")) var ships: FetchedResults<Ship>
  ```

- `IN` checks whether it's part of an array of options:

  ```swift
  NSPredicate(format: "universe IN %@", ["Aliens", "Firefly", "Star Trek"])
  ```

- `BEGINSWITH` and `CONTAINS` examines parts of strings:

  ```swift
  // Returns items that start with a capital E
  NSPredicate(format: "name BEGINSWITH %@", "E"))
  
  // Returns items that contain a capital E
  NSPredicate(format: "name CONTAINS %@", "E"))
  ```

- To ignore case is the above example:

  ```swift
  NSPredicate(format: "name BEGINSWITH[c] %@", "e"))
  ```

- `NOT` can be used to inverse behavior:

  ```swift
  // Returns items that do not start with an e
  NSPredicate(format: "NOT name BEGINSWITH[c] %@", "e"))
  ```

- For dynamic queries, use `%K` for a key name without quotes:

  ```swift
  NSPredicate(format: "%K BEGINSWITH %@", filterKey, filterValue)
  ```

- `AND` can be used to join predicates

- Importing `NSCompoundPredicate` from `Core Data` lets you build a predicate out of smaller ones.



## Dynamically Filtering @FetchRequest with SwiftUI

Example of how this works.

1. Add a new entity: `Singer`. Give it a `firstName` and `lastName` attribute (strings). Change the data model to `Manual/None` and create an `NSManagedObject Subclass`. 

2. Add these two properties to `Singer+CoreDataProperties.swift`:

   ```swift
   var wrappedFirstName: String {
       firstName ?? "Unknown"
   }
   
   var wrappedLastName: String {
       lastName ?? "Unknown"
   }
   ```

3. Add a view that lets us change the way things are filtered. Add some examples and options to change a  `@State private var lastNameFilter = "A"` state.

4. Create a new Swift view called `FilteredList`. Add:

   ```swift
   // Stores the fetch request. Doesn't create it though. We don't know what we're searching for.
   var fetchRequest: FetchRequest<Singer>
   ```

5. Add an init:

   ```swift
   // This runs a fetch request against the current managed object context, inherited from the ContentView.
   init(filter: String) {
       fetchRequest = FetchRequest<Singer>(entity: Singer.entity(), sortDescriptors: [], predicate: NSPredicate(format: "lastName BEGINSWITH %@", filter))
   }
   ```

6. Add a view:

   ```swift
   var body: some View {
       List(fetchRequest.wrappedValue, id: \.self) { singer in
           Text("\(singer.wrappedFirstName) \(singer.wrappedLastName)")
       }
   }
   ```

7. Add the list to ContentView:

   ```swift
   FilteredList(filter: lastNameFilter)
   ```



**Bonus:** More Dynamic Version:

```swift
struct FilteredList<T: NSManagedObject, Content: View>: View {
    var fetchRequest: FetchRequest<T>
    var singers: FetchedResults<T> { fetchRequest.wrappedValue }

    // this is our content closure; we'll call this once for each item in the list
    let content: (T) -> Content

    var body: some View {
        List(fetchRequest.wrappedValue, id: \.self) { singer in
            self.content(singer)
        }
    }

    init(filterKey: String, filterValue: String, @ViewBuilder content: @escaping (T) -> Content) {
        fetchRequest = FetchRequest<T>(entity: T.entity(), sortDescriptors: [], predicate: NSPredicate(format: "%K BEGINSWITH %@", filterKey, filterValue))
        self.content = content
    }
}
```

```swift
FilteredList(filterKey: "lastName", filterValue: lastNameFilter) { (singer: Singer) in
    Text("\(singer.wrappedFirstName) \(singer.wrappedLastName)")
}
```



## One-to-many relationships with Core Data, SwiftUI, and @**FetchRequest**

An example using candy.  Each type of candy was invented in a single country, but each country can have invented many types of candy.

1. Create a `Candy` entity with a `name` attribute (string). 

2. Create a `Country` entity with a `fullName` and `shortName` attrbte (strings). Add a constraint for `shortName`. 

3. With `Country` selected, press `+` under the `Relationships` table. Call it `candy`. Change the destination to `Candy`. In the data model inspector, change `Type` to `To Many`.

4. With Candy selected, press `+` under the `Relationships` table. Call it `origin`. Change the destination to `Country`. Set the inverse to `candy`.

5. Press `Cmd+S` to save changes.

6. Select both `Candy` and `Country` and set their Codegen to `Manual/None`. Create an `NSManagedObject Subclass` for both entities. Save.

7. Note that the new `Country` class has a `candy` property of `NSSet`, an Objective-C data type equivalent to `Set`, but can't be used with `ForEach`. 

8. Add convenience wrappers:

   ```swift
   // Candy class
   public var wrappedName: String {
       name ?? "Unknown Candy"
   }
   
   // Country class
   public var wrappedShortName: String {
       shortName ?? "Unknown Country"
   }
   
   public var wrappedFullName: String {
       fullName ?? "Unknown Country"
   }
   ```

9. Convert the candy set to a sorted array:

   ```swift
   // This is more complicated because this is an array of custom types -- can't just used sorted()
   public var candyArray: [Candy] {
       let set = candy as? Set<Candy> ?? []
       return set.sorted {
           $0.wrappedName < $1.wrappedName
       }
   }
   ```

10. Update your `ContentView`:

    ```swift
    @Environment(\.managedObjectContext) var moc
    @FetchRequest(entity: Country.entity(), sortDescriptors: []) var countries: FetchedResults<Country>
    
    VStack {
        List {
            ForEach(countries, id: \.self) { country in
                Section(header: Text(country.wrappedFullName)) {
                    ForEach(country.candyArray, id: \.self) { candy in
                        Text(candy.wrappedName)
                    }
                }
            }
        }
    
        Button("Add") {
            let candy1 = Candy(context: self.moc)
            candy1.name = "Mars"
            candy1.origin = Country(context: self.moc)
            candy1.origin?.shortName = "UK"
            candy1.origin?.fullName = "United Kingdom"
    
            let candy2 = Candy(context: self.moc)
            candy2.name = "KitKat"
            candy2.origin = Country(context: self.moc)
            candy2.origin?.shortName = "UK"
            candy2.origin?.fullName = "United Kingdom"
    
            let candy3 = Candy(context: self.moc)
            candy3.name = "Twix"
            candy3.origin = Country(context: self.moc)
            candy3.origin?.shortName = "UK"
            candy3.origin?.fullName = "United Kingdom"
    
            let candy4 = Candy(context: self.moc)
            candy4.name = "Toblerone"
            candy4.origin = Country(context: self.moc)
            candy4.origin?.shortName = "CH"
            candy4.origin?.fullName = "Switzerland"
    
            try? self.moc.save()
        }
    }
    ```

    

However, things are more complicated when it comes to `candy`. This is an `NSSet`, which could contain anything at all, because Core Data hasn’t restricted it to just instances of `Candy`.

So, to get this thing into a useful form for SwiftUI we need to:

1. Convert it from an `NSSet` to a `Set<Candy>` – a Swift-native type where we know the types of its contents.
2. Convert that `Set<Candy>` into an array, so that `ForEach` can read individual values from there.
3. Sort that array, so the candy bars come in a sensible order.

Swift actually lets us perform steps 2 and 3 in one, because sorting a set automatically returns an array. However, sorting the array is harder than you might think: this is an array of custom types, so we can’t just use `sorted()` and let Swift figure it out. Instead, we need to provide a closure that accepts two candy bars and returns true if the first candy should be sorted before the second.

So, please add this computed property to `Country` now:

```swift
public var candyArray: [Candy] {
    let set = candy as? Set<Candy> ?? []
    return set.sorted {
        $0.wrappedName < $1.wrappedName
    }
}
```

That completes our Core Data classes, so now we can write some SwiftUI code to make all this work.

Open ContentView.swift and give it these two properties:

```swift
@Environment(\.managedObjectContext) var moc
@FetchRequest(entity: Country.entity(), sortDescriptors: []) var countries: FetchedResults<Country>
```

Notice how we don’t need to specify anything about the relationships in our fetch request – Core Data understands the entities are linked, so it will just fetch them all as needed.

As for the body of the view, we’re going to use a `List` with two `ForEach` views inside it: one to create a section for each country, and one to create the candy inside each country. This `List` will in turn go inside a `VStack` so we can add a button below to generate some sample data:

```swift
VStack {
    List {
        ForEach(countries, id: \.self) { country in
            Section(header: Text(country.wrappedFullName)) {
                ForEach(country.candyArray, id: \.self) { candy in
                    Text(candy.wrappedName)
                }
            }
        }
    }

    Button("Add") {
        let candy1 = Candy(context: self.moc)
        candy1.name = "Mars"
        candy1.origin = Country(context: self.moc)
        candy1.origin?.shortName = "UK"
        candy1.origin?.fullName = "United Kingdom"

        let candy2 = Candy(context: self.moc)
        candy2.name = "KitKat"
        candy2.origin = Country(context: self.moc)
        candy2.origin?.shortName = "UK"
        candy2.origin?.fullName = "United Kingdom"

        let candy3 = Candy(context: self.moc)
        candy3.name = "Twix"
        candy3.origin = Country(context: self.moc)
        candy3.origin?.shortName = "UK"
        candy3.origin?.fullName = "United Kingdom"

        let candy4 = Candy(context: self.moc)
        candy4.name = "Toblerone"
        candy4.origin = Country(context: self.moc)
        candy4.origin?.shortName = "CH"
        candy4.origin?.fullName = "Switzerland"

        try? self.moc.save()
    }
}
```

Make sure you run that code, because it works really well – all our candy bars are automatically sorted into sections when the Add button is tapped. Even better, because we did all the heavy lifting inside our `NSManagedObject` subclasses, the resulting SwiftUI code is actually remarkably straightforward – it has no idea that an `NSSet` is behind the scenes, and is much easier to understand as a result.

**Tip:** If you don’t see your candy bars sorted into sections after pressing Add, make sure you haven’t removed the `mergePolicy` change from the `willConnectTo` method in `SceneDelegate`. As a reminder, it should be this: `context.mergePolicy = NSMergeByPropertyObjectTrumpMergePolicy`.