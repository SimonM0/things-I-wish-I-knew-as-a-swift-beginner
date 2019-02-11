![Swift 4.2](https://img.shields.io/badge/Swift-4.2-orange.svg)
# Things I wish I knew as a swift beginner
Here's a list of things I wish I'd known as a swift beginner, so far the list mainly contains iOS centered swift things because I'm developing for iOS, but this will change as the list grows. Coming from a front end background, I find alot of things I want to do are the same, but different.

![Same Same](https://media.giphy.com/media/C6JQPEUsZUyVq/giphy.gif)

## Contents
1. [Text shadow slows animation](#1-text-shadow-slows-animation)
2. [UITextField field padding](#2-uitextfield-field-padding)
3. [Use SnapKit instead of Autolayout](#3-use-snapkit-instead-of-autolayout)
4. [How to set a background image](#4-how-to-set-a-background-image)
5. [What the equivalent of Javascripts setTimeOut function is in swift](#5-what-the-equivalent-of-javascripts-settimeout-function-is-in-swift)
6. [How to easily cast firebase data](#6-how-to-easily-cast-firebase-data)
7. [How to pretty format arrays and objects in the console](#7-how-to-pretty-format-arrays-and-objects-in-the-console)
8. [How to use reduce in swift](#8-how-to-use-reduce-in-swift)
9. [You can also use logical operator in a reduce](#9-you-can-also-use-logical-operator-in-a-reduce)
10. [Swift is more than just iOS development ;) (5, 7, 8 are the only swift-specific things)](#10-swift-is-more-than-just-ios-development--5-7-8-are-the-only-swift-specific-things)
11. [What the swift equivalent of a void function](#11-what-the-swift-equivalent-of-a-void-function-is)

## 1. Text shadow slows animation
Rendering animations that has or overlaps text with text shadow on will suffer a performance impact. The solution I found was to rasterize the UILabel's layer.
### Solution
There is an option to rasterize the layer, effectively mitigating the lag
```swift
class MyViewController : UIViewController {
    let label: UILabel = {
        let label = UILabel()
        label.frame = CGRect(x: 150, y: 200, width: 200, height: 20)
        label.text = "Hello World!"
        label.textColor = .black
        label.layer.shadowColor = UIColor.black.cgColor
        label.layer.shadowOpacity = 1;
        label.layer.shadowRadius = 5;
        label.layer.shadowOffset = CGSize(width: 0, height: 2)
        return label
    }()

    override func loadView() {
        let view = UIView()
        view.backgroundColor = .white

        // Rasterize the layer using shouldRasterize and
        // set the rasterizationScale for retina screens
        label.layer.shouldRasterize = true;
        label.layer.rasterizationScale = 2
        
        view.addSubview(label)
        self.view = view
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
**Disclaimer: There's nothing wrong with Autolayout. SnapKit uses it under the hood as pointed out by [/u/fear731](https://www.reddit.com/user/fear731).**  
But personally I find [SnapKit's](https://github.com/SnapKit/SnapKit) syntax more convienient, shorter and easier to understand when I come back to it.

Also Autolayout is backed by the system and should be studied first no matter what. As suggested by [/u/and_roman](https://www.reddit.com/user/and_roman). Which I agree with as I first learn Autolayout before using SnapKit so just diving into SnapKit without have ever used AutoLayout might make things more complicated.
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
A better way of doing this was recommended by [/u/halleys_comet69](https://www.reddit.com/user/halleys_comet69) How elegant and succinct.
```
DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
    print("This is delayed.")
}
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

## 7. How to pretty format arrays and objects in the console
I was using `print` alot of the time to output data. But actually, there's also a command called `dump`. `dump` will format the object for you.
```swift
// Example output of print
[['a'], ['b']]

// Example output of dump
[
  ['a'],
  ['b']
]

```

## 8. How to use reduce in swift
Reduce in swift isn't hard, it's just a different syntax as pointed out by [/u/and_roman](https://www.reddit.com/user/and_roman). I wasted quite alot of time because the stuff I found had rather convoluted examples. So here's a simple example of concatenating hello world in Javascript and how the same thing can be done in Swift.
```javascript
// Javascript version
const helloWorldArray = ["Hello", "World"];
const joinedString = helloWorldArray
  .reduce(
    (endResult, value) => `${endResult} ${value}`,
    "Result:"
  );
console.log(joinedString);
```
```swift
// Swift version
let helloWorldArray = ["Hello", "World"]
let joinedString = helloWorldArray
    .reduce("Result: ") { "\($0) \($1)" }
print(joinedString)
```

Here is a better example by [/u/DonaldPShimoda](https://www.reddit.com/user/DonaldPShimoda). The `(1)` is the initial value of the reduction.
```swift
let numbers = [3, 5, 2, 5, 6, 2, 4]
let product = numbers.reduce(1) { (previousNumber, currentNumber) in previousNumber * currentNumber }
print(product)
```

## 9. You can also use logical operator in a reduce
I learnt this off [/u/anymbryne](https://www.reddit.com/user/anymbryne). Here's the same example as above. Very cool stuff! ❤️
```swift
let numbers = [3, 5, 2, 5, 6, 2, 4]
let product = numbers.reduce(1, *)
print(product)
```

## 10. Swift is more than just iOS development ;) (5, 7, 8 are the only swift-specific things)
[/u/Terrible_Umpire](https://www.reddit.com/user/Terrible_Umpire) Pointed this one out to me 😙

## 11. What the swift equivalent of a void function is and how to type it
I had trouble typing void functions in the beginnning, it wasn't as easy to find an example as I had hoped. So just putting this here for convienience.
```javascript
// Javascript
const logHelloWorld = () => console.log('Hello World');
```
```swift
// Swift
let logHelloWorld: (() -> Void) = {
    () in
    print("Hello World")
}
```
