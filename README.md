<img src=top.png width=960/>

# Pixel - Engine • Editor

![](preview.gif)


![CocoaPods](https://img.shields.io/cocoapods/v/PixelEngine.svg) Engine

![CocoaPods](https://img.shields.io/cocoapods/v/PixelEditor.svg) Editor

[![](https://sourcerer.io/fame/muukii/muukii/Pixel/images/0)](https://sourcerer.io/fame/muukii/muukii/Pixel/links/0)[![](https://sourcerer.io/fame/muukii/muukii/Pixel/images/1)](https://sourcerer.io/fame/muukii/muukii/Pixel/links/1)[![](https://sourcerer.io/fame/muukii/muukii/Pixel/images/2)](https://sourcerer.io/fame/muukii/muukii/Pixel/links/2)[![](https://sourcerer.io/fame/muukii/muukii/Pixel/images/3)](https://sourcerer.io/fame/muukii/muukii/Pixel/links/3)[![](https://sourcerer.io/fame/muukii/muukii/Pixel/images/4)](https://sourcerer.io/fame/muukii/muukii/Pixel/links/4)[![](https://sourcerer.io/fame/muukii/muukii/Pixel/images/5)](https://sourcerer.io/fame/muukii/muukii/Pixel/links/5)[![](https://sourcerer.io/fame/muukii/muukii/Pixel/images/6)](https://sourcerer.io/fame/muukii/muukii/Pixel/links/6)[![](https://sourcerer.io/fame/muukii/muukii/Pixel/images/7)](https://sourcerer.io/fame/muukii/muukii/Pixel/links/7)

Image editor and engine using CoreImage

> ⚠️ Currently, API is not stable. It may change in the future.

## Features

**Currently accepting PRs that impement these features.**

### Performance

* [x] ✈️Pretty Good
* [ ] 🚀Blazing Fast (🌀Anyone help us!)

### Adjustment

* [x] Crop
* [ ] Straighten (🌀Anyone help us!)
* [ ] Perspective (🌀Anyone help us!)

### Filter

#### Presets

* [x] ColorCube (Look Up Table)
  * [ ] Intensity

> ⚠️ Currently, Pixel does not contain LUT.
> Demo app has sample LUTs.

And also, here is [interesting article](https://medium.com/the-bergen-company/recreating-vsco-filters-in-darkroom-291114051a0e)

#### Edits

* [x] Brightness
* [x] Contrast
* [x] Saturation
* [x] Highlights
* [x] Shadows
* [x] Temperature
* [x] GaussianBlur
* [x] Vignette 
* [ ] Color (Shadows / Highlights)
* [x] Fade
* [x] Sharpen
* [x] Clarity
* [ ] HLS (🌀Anyone help us!)

#### Other

* [Restore editing](#restore-editing)
* [Customize Control-UI](#customize-control-ui)

## Requirements

* Swift 4.2 (Xcode10+)
* iOS 10+

## Getting Started

Demo.app contains the sample code.
Please check out `Sources/Demo/EditorViewController.swift`.

### Create instance of PixelEditViewController

```swift
let image: UIImage

let controller = PixelEditViewController(image: image)
```

### Show

* as Modal

⚠️ Currently
We need to wrap the controller with `UINavigationController`.
Because, `PixelEditViewController` needs `UINavigationBar`.

```swift
let controller: PixelEditViewController

let navigationController = UINavigationController(rootViewController: controller)

self.present(navigationController, animated: true, completion: nil)
```

* as Push

We can push the controller in UINavigationController.

```swift
let controller: PixelEditViewController
self.navigationController.push(controller, animated: true)
```

### Setup Delegate

`PixelEditViewController` has delegate protocol called `PixelEditViewControllerDelegate`.

```swift
public protocol PixelEditViewControllerDelegate : class {
  func pixelEditViewController(_ controller: PixelEditViewController, didEndEditing editingStack: SquareEditingStack)
  func pixelEditViewControllerDidCancelEditing(in controller: PixelEditViewController)
}
```

💡`PixelEditViewController` does not know how to dismiss or pop by itself.
So we need to control `PixelEditViewController` outside.

Basically, it's like following code, recommend dismiss or pop in methods of delegate.

```swift
extension EditorViewController : PixelEditViewControllerDelegate {

  func pixelEditViewController(_ controller: PixelEditViewController, didEndEditing editingStack: SquareEditingStack) {

    self.navigationController?.popToViewController(self, animated: true)
  }

  func pixelEditViewControllerDidCancelEditing(in controller: PixelEditViewController) {
  
    self.navigationController?.popToViewController(self, animated: true)
  }

}
```

### Render Image


```swift
let editingStack: SquareEditingStack

let image = editingStack.makeRenderer().render(resolution: .full)
```

### Restore editing

We can take current editing as instance of `EditingStack` from `PixelEditViewController.editingStack`.

If we want to restore editing after closed `PixelEditViewController`, we use this.

```swift
let editingStack = controller.editingStack
// close editor

// and then when show editor again
let controller = PixelEditViewController(editingStack: editingStack)
```

### Add ColorCubeFilters

We can use LUT(LookUpTable) with CIColorCubeFilter.

LUT is like this (Dimension is 64)

<img src="neutral-lut.png" />

```swift
import PixelEngine

let lutImage: UIImage

let filter = FilterColorCube(
  name: "Filter Name",
  identifier: "Filter Identifier",
  lutImage: lutImage,
  dimension: 64
)

let storage = ColorCubeStorage(filters: [filter])
let controller = PixelEditViewController(image: image, colorCubeStorage: storage)
```

And also, if we don't specify colorCubeStorage, use `default`.

```swift
// set
ColorCubeStorage.default.filters = filters

// get
ColorCubeStorage.default.filters
```

## Customize Control-UI

We can customize UI for control area.

<img src="customize.png" width=375/>

### Customize Built-In Control-UI using override

There is `Options` struct in PixelEditor.
We can create options that fit our usecases.

So, If we need to change ExposureControl, override ExposureControlBase class.
Then set that class to Options.

```swift
let options = Options.default()
options.classes.control.brightnessControl = MyExposureControl.self
```

It's like using custom Cell in UICollectionView.
If you have any better idea for this, please tell us💡.
And also Built-In UI may need expose some properties to customize from subclassing.

### Customize whole Control-UI

We can also customize whole UI.

Override `options.classes.control.rootControl`, then build UI from scratch.

## Localization

Strings in UI can be localized with `L10n`.

```swift
import PixelEditor

PixelEditor.L10n.done = "保存"

// or
PixelEditor.L10n.done = NSLocalizedString...
```

## Installation

### CocoaPods

Pixel is available through [CocoaPods](https://cocoapods.org). To install
it, simply add the following line to your Podfile:

```ruby
pod 'PixelEngine'
pod 'PixelEditor'
```

### Carthage

For [Carthage](https://github.com/Carthage/Carthage), add the following to your `Cartfile`:

```ogdl
github "muukii/Pixel"
```

## Contributing

If you need more features, please open issue or submit PR!
Muukii may not know the approach to take for implementing them, So your PR will be very helpful.

### Development

To develop Pixel, setup environment of development with following code.

```ogdl
$ pod install
$ open Pixel.xcworkspace
```

## Author

Muukii (muukii.app@gmail.com)

## License

Pixel is available under the MIT license. See the LICENSE file for more info.

