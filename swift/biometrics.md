# Biometrics

Uses the Objective-C API.

First, add a “Privacy - Face ID Usage Description” key to you plist. Touch ID does not need one.

```swift
import SwiftUI
import LocalAuthentication

struct ContentView: View {
    @State private var isUnlocked = false
    
    func authenticate() {
        let context = LAContext()
        var error: NSError?

        // check whether biometric authentication is possible
        if context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error) {
            // it's possible, so go ahead and use it
            let reason = "We need to unlock your data."

            context.evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, localizedReason: reason) { success, authenticationError in
                // authentication has now completed
                DispatchQueue.main.async {
                    if success {
                        // authenticated successfully
                        self.isUnlocked = true
                    } else {
                        // there was a problem
                    }
                }
            }
        } else {
            // no biometrics
        }
    }
    
    var body: some View {
        VStack {
            if self.isUnlocked {
                Text("Unlocked")
            } else {
                Text("Locked")
            }
        }
        .onAppear(perform: authenticate)
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```



The `authenticate()` method requires four steps:

1. Create instance of `LAContext`, which allows us to query biometric status and perform the authentication check.
2. Ask that context whether it’s capable of performing biometric authentication – this is important because iPod touch has neither Touch ID nor Face ID.
3. If biometrics are possible, then we kick off the actual request for authentication, passing in a closure to run when authentication completes.
4. When the user has either been authenticated or not, our completion closure will be called and tell us whether it worked or not, and if not what the error was. This closure will get called away from the main thread, so we need to push any UI-related work back to the main thread.

