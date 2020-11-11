# Storing user settings with UserDefaults

iOS gives us several ways to read and write user data.



## UserDefaults

Allows us to store small amounts of user data directly in our app. It is **stringly typed**.

Shoot for no more than 512KB of data (which is a lot).

```swift
struct ContentView: View {
    @State private var tapCount = UserDefaults.standard.integer(forKey: "Tap")

    var body: some View {
        Button("Tap count: \(tapCount)") {
            self.tapCount += 1
	          UserDefaults.standard.set(self.tapCount, forKey: "Tap")
        }
    }
}
```



Break down:

- `UserDefaults.standard` is the built-in instance that is attached to our app.
- There is a `set()` method that accepts any kind of data.
- Attach a string to this data. e.g. "Tap". This key is case-sensitive.



We get back 0 if the key doesn't exist, so make sure you can distinguish 0 the default value from 0 being a value you set.



## Codable

A protocol specifically used for archiving and unarchiving data (converting objects into plain text and back again.). So, it can be turned into something like JSON and back.

```swift
struct User: Codable {
    var firstName: String
    var lastName: String
}
```

We still need to tell Swift when to archive and what to do with the data.

We can use the `JSONEncoder` type which coverts Codable to JSON.

If Codable sees an optional property, it will only try to unarchive it if it exists in the source data.



### To Convert to JSON

```swift
...
@State private var user = User(firstName: "Taylor", lastName: "Swift")

Button("Save User") {
    let encoder = JSONEncoder()

    if let data = try? encoder.encode(self.user) {
        UserDefaults.standard.set(data, forKey: "UserData")
    }
}
...
```

`data` is of type `Data` and can store any kind of data--strings, images, zip files, etc..



## To Decode JSON

```swift
struct ContentView: View {
    var body: some View {
        Button("Decode JSON") {
            let input = """
            {
                "name": "Taylor Swift",
                "address": {
                    "street": "555, Taylor Swift Avenue",
                    "city": "Nashville"
                }
            }
            """
            let data = Data(input.utf8)
            let decoder = JSONDecoder()
            if let user = try? decoder.decode(User.self, from: data) {
              	// This prints 555, Taylor Swift Avenue
                print(user.address.street)
            }
        }
    }
}

struct User: Codable {
    var name: String
    var address: Address
}

struct Address: Codable {
    var street: String
    var city: String
}
```

