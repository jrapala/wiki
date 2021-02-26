# NSManagedObject

When creating a new Core Data entity, Xcode automatically generated a managed object class for us. We use that with a `@FetchRequest` to show data. Unfortunantly, there are a lot of optionals to unwrap.

## Solution to the Unwrapping Problem

Create an entity with some properties. Choose View > Inspectors > Data Model. The `Codegen` controls how Xcode generates the entity as a managed object class. By default, it is a Class Definition. If you change it to Manual/None, then you get full control over how the class is made.

In Editor, choose Create NSManagedObject Sublcass with "CoreDataProject" selected. Be sure to select the CoreDataProject folder. This coverts the generated code into the Swift files we need. We only care about the `Entity+CoreDataProperties.swift` file.

Here you will see where your optional problem stems from:

```swift
    @NSManaged public var title: String?
    @NSManaged public var director: String?
    @NSManaged public var year: Int16
```

Note that `@NSManaged` is not a property wrapper. It's older than that. It tells Swift to use Core Data to store the information.

You can remove the optionality, however you can still create an instance of your entity without providing values, due to how Core Data works. Instead, replace it with someting like this:

```swift
public var wrappedTitle: String {
    title ?? "Unknown Title"
}
```

These computed properties help us access the optional values safely.



## Conditional Saving

We save by using the `save()` method of `NSManagedObjectContext`. However we don't check if there are changes to be made. Apple suggests you check for uncommitted changes before calling `save()` to prevent Core Data from work it does't need to do.

### Checking for Changes

Every managed object has a `hasChanges` property. So do this:

```swift
if self.moc.hasChanges {
    try? self.moc.save()
}
```

