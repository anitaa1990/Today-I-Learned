# How to change color of image in Swift 4

Let's say we need to change the image of a button when it is clicked, how do we do that?

There is an extension we can make use of and use the function ```tintImage``` to specify the color we would like to change the image.

```
  extension UIImage {

      func tintImage(color: UIColor) -> UIImage {

          UIGraphicsBeginImageContext(self.size)
          guard let context = UIGraphicsGetCurrentContext() else { return self }
          guard let cgImage = cgImage else { return self }

          // flip the image
          context.scaleBy(x: 1.0, y: -1.0)
          context.translateBy(x: 0.0, y: -size.height)

          // multiply blend mode
          context.setBlendMode(.multiply)

          let rect = CGRect(x: 0, y: 0, width: size.width, height: size.height)
          context.clip(to: rect, mask: cgImage)
          color.setFill()
          context.fill(rect)

          // create uiimage
          guard let newImage = UIGraphicsGetImageFromCurrentImageContext() else { return self }
          UIGraphicsEndImageContext()

          return newImage
      }

  }
```

And it can be used like this:
```
  let image = btnClose.imageView?.image?.tintImage(color: UIColor(red: 103/255, green: 58/255, blue: 183/255, alpha: 1))
  btnClose.setImage(image, for: .normal)
```
