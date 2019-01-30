# Swift Beginners List of quirks
This is a list of things I wish I'd known as a beginner to swift. Coming from a front end background, I find alot of things I want to do are the same, but different.

![Same Same](https://media.giphy.com/media/C6JQPEUsZUyVq/giphy.gif)

## 1. UITableViewController slows animation
Rendering animations that overlap tables will cause the frame rate to drop because of the composition of table and cells.
### Solution
There is an option to rasterize the layer, effectively mitigating the lag
```swift
class YourTableViewController: UITableViewController {
 override func viewDidLoad() {
    super.viewDidLoad()
    tableView.layer.shouldRasterize = true;
    tableView.layer.rasterizationScale = 2
  }
}
```

## 2. UITextField field padding
Sometimes you want to add padding to a text field and that's not as straight forward as you'd think.
### Solution
Extend UITextField and override the bounding boxes
```swift
extension UITextField {
    override open func editingRect(forBounds bounds: CGRect) -> CGRect {
        return bounds.insetBy(dx: 10, dy: 0)
    }

    override open func placeholderRect(forBounds bounds: CGRect) -> CGRect {
        return bounds.insetBy(dx: 10, dy: 0)
    }

    override open func textRect(forBounds bounds: CGRect) -> CGRect {
        return bounds.insetBy(dx: 10, dy: 0)
    }
}
```
