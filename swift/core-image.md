# Core Image

Core Image is Apple's framework for manipulating images. It doesn't integrate with SwiftUI very well, but it's a powerful tool.

You will need to use a different Image type. There are three other image types to work with, and we need all three:

- `UIImage`, which comes from UIKit. This is an extremely powerful image type capable of working with a variety of image types, including bitmaps (like PNG), vectors (like SVG), and even sequences that form an animation. `UIImage` is the standard image type for UIKit, and of the three it’s closest to SwiftUI’s `Image` type.
- `CGImage`, which comes from Core Graphics. This is a simpler image type that is really just a two-dimensional array of pixels.
- `CIImage`, which comes from Core Image. This stores all the information required to produce an image but doesn’t actually turn that into pixels unless it’s asked to. Apple calls `CIImage` “an image recipe” rather than an actual image.

Interoperability:

- We can create a `UIImage` from a `CGImage`, and create a `CGImage` from a `UIImage`.
- We can create a `CIImage` from a `UIImage` and from a `CGImage`, and can create a `CGImage` from a `CIImage`.
- We can create a SwiftUI `Image` from both a `UIImage` and a `CGImage`.





```swift
import SwiftUI
import CoreImage
import CoreImage.CIFilterBuiltins

struct ContentView: View {
    @State private var image: Image?

    var body: some View {
        VStack {
            image?
                .resizable()
                .scaledToFit()
        }
        .onAppear(perform: loadImage)
    }

    func loadImage() {
      // load image as a UIImage
    	guard let inputImage = UIImage(named: "Example") else { return }
      // convert to CIIamge for Core Image
    	let beginImage = CIImage(image: inputImage)
      
      let context = CIContext()
			let currentFilter = CIFilter.sepiaTone()
      currentFilter.inputImage = beginImage
			currentFilter.intensity = 1
      
      // get a CIImage from our filter or exit if that fails
			guard let outputImage = currentFilter.outputImage else { return }

			// attempt to get a CGImage from our CIImage
			if let cgimg = context.createCGImage(outputImage, from: outputImage.extent) {
		    // convert that to a UIImage
  		  let uiImage = UIImage(cgImage: cgimg)

    		// and convert that to a SwiftUI image
	    	image = Image(uiImage: uiImage)
			}
    }
}
```



Not all filters are easily usable in Swift. For example:

```swift
guard let currentFilter = CIFilter(name: "CITwirlDistortion") else { return }
currentFilter.setValue(beginImage, forKey: kCIInputImageKey)
currentFilter.setValue(2000, forKey: kCIInputRadiusKey)
currentFilter.setValue(CIVector(x: inputImage.size.width / 2, y: inputImage.size.height / 2), forKey: kCIInputCenterKey)

```

