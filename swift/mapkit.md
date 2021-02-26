# MapKit

**Views vs View Controllers**: generally, a *view* is one piece of layout, such as some text, a button, or an image, and a *view controller* is one screen of content. However, as you progress, you can have many view controllers on one screen.

## Adding a MapView

MapKit gives us `MKMapView` which gives us a view so we need to use a `UIViewRepresentable`. 

We need to write a `makeUIView` and `updateUIView` method.

```swift
import MapKit

struct MapView: UIViewRepresentable {
    func makeUIView(context: UIViewRepresentableContext<MapView>) -> MKMapView {
        let mapView = MKMapView()
        return mapView
    }

    func updateUIView(_ view: MKMapView, context: UIViewRepresentableContext<MapView>) {
    }
}
```

`UIViewControllerRepresentable` and `UIViewRepresentable` both include type aliases (right-click -> Jump to Definition to see the `typealias`).

```swift
// Definition
typealias Context = UIViewRepresentableContext<Self>
```



To use the MapView:

```swift
MapView()
    .edgesIgnoringSafeArea(.all)
```



## Add a Coordinator

Use a coordinate to pass data to and from SwiftUI.

You'll need a nested class that inherits from `NSObject`, to conform to the protocol or view/view controller works with:

```swift
class Coordinator: NSObject, MKMapViewDelegate {
    var parent: MapView

    init(_ parent: MapView) {
        self.parent = parent
    }
}

func makeCoordinator() -> Coordinator {
    Coordinator(self)
}
```

And add to the `makeUIView()`:

```swift
mapView.delegate = context.coordinator
```



## Changing Region

Add to the Coordinator class:

```swift
func mapViewDidChangeVisibleRegion(_ mapView: MKMapView) {
    print(mapView.centerCoordinate)
}
```

This is called whenever the map view changes its visible region.

 

## Adding Coordinates

Coordinates would be model data so add to `makeUIView()`:

```swift
func makeUIView(context: Context) -> MKMapView {
    let mapView = MKMapView()
    mapView.delegate = context.coordinator

    let annotation = MKPointAnnotation()
    annotation.title = "London"
    annotation.subtitle = "Capital of England"
    annotation.coordinate = CLLocationCoordinate2D(latitude: 51.5, longitude: 0.13)
    mapView.addAnnotation(annotation)

    return mapView
}
```



To customize markers, add to the Coordinator class:

```swift
func mapView(_ mapView: MKMapView, viewFor annotation: MKAnnotation) -> MKAnnotationView? {
    let view = MKPinAnnotationView(annotation: annotation, reuseIdentifier: nil)
    view.canShowCallout = true
    return view
}
```

Frameworks such as UIKit and MapKit have simple identifiers called **reuse identifiers**. 

We used `nil` here, which is fine for now, but you'll need it when adding more pins.



## Fetch User's Location

Need to add `“Privacy - Location When In Use Usage Description”,` key



**Add class that fetches user's location:**

```swift
import CoreLocation

class LocationFetcher: NSObject, CLLocationManagerDelegate {
    let manager = CLLocationManager()
    var lastKnownLocation: CLLocationCoordinate2D?

    override init() {
        super.init()
        manager.delegate = self
    }

    func start() {
        manager.requestWhenInUseAuthorization()
        manager.startUpdatingLocation()
    }

    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
        lastKnownLocation = locations.first?.coordinate
    }
}
```



**Use it:**

```swift
struct ContentView: View {
    let locationFetcher = LocationFetcher()

    var body: some View {
        VStack {
            Button("Start Tracking Location") {
                self.locationFetcher.start()
            }

            Button("Read Location") {
                if let location = self.locationFetcher.lastKnownLocation {
                    print("Your location is \(location)")
                } else {
                    print("Your location is unknown")
                }
            }
        }
    }
}
```

