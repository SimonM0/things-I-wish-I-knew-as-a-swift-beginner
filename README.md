![Swift 4.2](https://img.shields.io/badge/Swift-4.2-orange.svg)
# Things I wish I knew as a swift beginner
Here's a list of things I wish I'd known as a swift beginner. Coming from a front end background, I find alot of things I want to do are the same, but different.

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

## 3. Use [SnapKit](https://github.com/SnapKit/SnapKit) instead of Autolayout
**Disclaimer: There's nothing wrong with Autolayout.**  
But personally I find [SnapKit's](https://github.com/SnapKit/SnapKit) syntax more convienient, shorter and easier to understand when I come back to it.
### Comparison
```swift
/**
 * Autolayout
 */
textView.translatesAutoresizingMaskIntoConstraints = false
textView.topAnchor.constraint(equalTo: self.topAnchor, constant: 0).isActive = true
textView.leftAnchor.constraint(equalTo: self.leftAnchor, constant: 10).isActive = true
textView.rightAnchor.constraint(equalTo: self.anotherTextView.leftAnchor).isActive = true

/**
 * Snap Kit
 */
textView.snp.makeConstraints {
  make in
  make.top.equalTo(self.snp.top)
  make.left.equalTo(self.snp.left).offsetBy(10)
  make.right.equalTo(self.anotherTextView.snp.left)
}
```

## 4. How to set a background image
In this example I am using SnapKit, but you can use Autolayout if you wanted to.
### Solution
```swift
class YourViewController: UIViewController {
  let backgroundImage = UIImageView(
    image: UIImage(named: "your-background-image")
  )

  init() {
    super.init(nibName: nil, bundle: nil)
    self.setupBackground()
  }

  /**
    * This sets up the background for the screen
    */
  fileprivate func setupBackground() {
    view.addSubview(backgroundImage)
    backgroundImage.contentMode = UIView.ContentMode.scaleAspectFill
    backgroundImage.snp.makeConstraints {
      make in
      make.margins.equalTo(view)
    }
  }
}
```

## 5. What the equivalent of Javascripts setTimeOut function is in swift
I use setTimeOut in javascript sometimes to delay things. It pretty easy to do in Swift, but the code is a little different.
```swift
  Timer.scheduledTimer(
    withTimeInterval: 1,
    repeats: false,
    block: {
      timer in
      // ...Your code goes here
    }
  )
```

## 6. How to easily cast firebase data
If you're only storing string values in your firebase database then this can help you to convert the return snapshop to a dictionary.
```swift
  self.ref?
      .child("your/data/path")
      .observeSingleEvent(
        of: .value,
        with: {
          (snapshot) in
             guard let data = snapshot.value as? [String : String] else {
                return
             }
             // data["yourProp"]
          }
      )
```
