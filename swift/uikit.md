# UIKit

Many parts of Apple's frameworks, such as MapKit, Safari, etc.., don't have SwiftUI wrapper yet.

You need to know how to wrap their code for use with SwiftUI.



## Parts of UIKit

- `UIView` is the parent class of all views in the layouts. So, labels, buttons, text fields, sliders, and so on – those are all views.
- `UIViewController` is designed to hold all the code to bring views to life. Just like `UIView`, `UIViewController` has many subclasses that do different kinds of work.
- **delegation** is a design pattern that decides where work happen. So, when it came to deciding how our code should respond to a text field’s value changing, we’d create a custom class with our functionality and make that the delegate of our text field.

SwiftUI blends `UIView` and `UIViewController` into a single `View` protocol.



## Using UIKit's Photo Picker 

UIKit uses ``UIImagePickerController `, `UINavigationControllerDelegate`, and `UIImagePickerControllerDelegate` for the picker. The delegates need to be wrapped.

1. Create a struct that conforms to the `UIViewControllerRepresentable` protocol. This builds on `View`. This view's body is the controller itself.

   ```swift
   struct ImagePicker: UIViewControllerRepresentable {
       typealias UIViewControllerType = UIImagePickerController
   }
   ```

2. To conform to the protocol, we need to implement two methods: `makeUIViewController()` and `updateUIViewController()`. Cause the error and fix the stubs to auto-generate code. You can now delete the `typealias` line.

   ```swift
   struct ImagePicker: UIViewControllerRepresentable {
       func makeUIViewController(context: UIViewControllerRepresentableContext<ImagePicker>) -> UIImagePickerController {
           code
       }
   
       func updateUIViewController(_ uiViewController: UIImagePickerController, context: UIViewControllerRepresentableContext<ImagePicker>) {
           code
       }
   }
   ```

3. We don't actually need to use `updateUIViewController()`, so delete the `code`:

   ```swift
   struct ImagePicker: UIViewControllerRepresentable {
       func makeUIViewController(context: UIViewControllerRepresentableContext<ImagePicker>) -> UIImagePickerController {
           code
       }
   
       func updateUIViewController(_ uiViewController: UIImagePickerController, context: UIViewControllerRepresentableContext<ImagePicker>) {}
   }
   ```

4. Update the `makeUIViewController()`. The view controller is now wrapped. The `ImagePicker` is a valid SwiftUI view.

   ```swift
   struct ImagePicker: UIViewControllerRepresentable {
       func makeUIViewController(context: Context) -> UIImagePickerController {
           let picker = UIImagePickerController()
           return picker
       }
       
       func updateUIViewController(_ uiViewController: UIImagePickerController, context: Context) {}
   }
   ```

5. Add the view:

   ```swift
   struct ContentView: View {
       @State private var image: Image?
       @State private var showingImagePicker = false
   
       var body: some View {
           VStack {
               image?
                   .resizable()
                   .scaledToFit()
   
               Button("Select Image") {
                  self.showingImagePicker = true
               }
           }
           .sheet(isPresented: $showingImagePicker) {
               ImagePicker()
           }
       }
   }
   ```

6. The picker will work, but in order to select an image, we need to assign the selection from the `UIImagePickerController`. To do this, you need to use **coordinators**.



### Adding Coordinators

Don't confuse these with UIKit coordinators. The are very different.

SwiftUI coordinators act as delegates for UIKit view controllers.

1. Add these to the `ImagePicker` struct. The `makeCoordinator()` method is called automatically. Our `ImagePicker` now has a coordinator.

   ```swift
   class Coordinator {
   }
   
   func makeCoordinator() -> Coordinator {
       Coordinator()
   }
   ```

2. Update `makeUIViewController()` to use the coordinator as the delegate if something happens.

   ```swift
       func makeUIViewController(context: Context) -> UIImagePickerController {
           let picker = UIImagePickerController()
           picker.delegate = context.coordinator
           return picker
       }
   ```

3. You will need to fi the `Coordinator` class to inherit from `NSObject` and conform to the other protocols:

   ```swift
   class Coordinator: NSObject, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
     ...
   
   ```

   `NSObject` is the parent class for almost everything in `UIKit`. It allow Objective-C to ask the object what functionality it supports at runtime. 

   `UIImagePickerControllerDelegate` adds the funcationality for detecting when a user selects an image. It has two methods: one for selecting an image and one for hitting Cancel. If no image is selected, it cancels automatically.

   `UINavigationControllerDelegate` detects when the user moves between screens in an image picker.

4. Add the property wrappers we'll need:

   ```swift
       @Binding var image: UIImage?
       @Environment(\.presentationMode) var presentationMode
   ```

5. Tell the coordinator what it's parent is:

   ```swift
       class Coordinator: NSObject, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
           var parent: ImagePicker
   
           init(_ parent: ImagePicker) {
               self.parent = parent
           }
       }
   ```

6. Pass the `ImagePicker` `struct` into the coordinator:

   ```swift
   func makeCoordinator() -> Coordinator {
       Coordinator(self)
   }
   ```

7. In the `Coordinater` class, start typing `didFinishPicking` and autocomplete the following:

   ```swift
   func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
       code            
   }
   ```

8. Replace the `code` with the following to find the selected image and dismiss.

   ```swift
   if let uiImage = info[.originalImage] as? UIImage {
       parent.image = uiImage
   }
   
   parent.presentationMode.wrappedValue.dismiss()
   ```

9. In the `ContentView` add state, a load image function, and pass the state into the picker:

   ```swift
   struct ContentView: View {
       @State private var image: Image?
       @State private var showingImagePicker = false
     	// add inputImage state
       @State private var inputImage: UIImage?
   
       var body: some View {
           VStack {
               image?
                   .resizable()
                   .scaledToFit()
   
               Button("Select Image") {
                  self.showingImagePicker = true
               }
           }
         	// run loadImage when sheet is dismissed
           .sheet(isPresented: $showingImagePicker, onDismiss: loadImage) {
             	// pass inputImage into ImagePicker
               ImagePicker(image: self.$inputImage)
           }
       }
       
       func loadImage() {
         	// if inputImage has a value, assign a new image to the image property
           guard let inputImage = inputImage else { return }
           image = Image(uiImage: inputImage)
       }
   }
   
   ```

   

## Saving a Photo to a Photo Library

You'll need to add “Privacy - Photo Library Additions Usage Description” to the `Info.plist`.

```swift
    func loadImage() {
        guard let inputImage = inputImage else { return }
        image = Image(uiImage: inputImage)
      	// Add this method to write out a picture
      	// Parameters for what method to call after saving completes, then succeeds or fails
      	// --> Useful if incorrect permissions are provided
      	// The fourth parameter is a UIKit context, to help identify one save operation from 						another
        UIImageWriteToSavedPhotosAlbum(inputImage, nil, nil, nil)
    }
```



To add a "Save Finished!" alert after save, add this class somewhere outside of `ContentView`:

```swift
class ImageSaver: NSObject {
    func writeToPhotoAlbum(image: UIImage) {
        UIImageWriteToSavedPhotosAlbum(image, self, #selector(saveError), nil)
    }

    @objc func saveError(_ image: UIImage, didFinishSavingWithError error: Error?, contextInfo: UnsafeRawPointer) {
        print("Save finished!")
    }
}
```

With that in place we can now use it from SwiftUI, like this:

```swift
let imageSaver = ImageSaver()
imageSaver.writeToPhotoAlbum(image: inputImage)
```

