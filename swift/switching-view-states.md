# Switching View States

```swift
enum LoadingState {
    case loading, success, failed
}

struct ContentView: View {
  var loadingState = LoadingState.loading
 
  var body: some View {
    Group {
      if loadingState == .loading {
        LoadingView()
      } else if loadingState == .success {
        SuccessView()
      } else if loadingState == .failed {
        FailedView()
      }
    }
  }
}

struct LoadingView: View {
    var body: some View {
        Text("Loading...")
    }
}

struct SuccessView: View {
    var body: some View {
        Text("Success!")
    }
}

struct FailedView: View {
    var body: some View {
        Text("Failed.")
    }
}
```

