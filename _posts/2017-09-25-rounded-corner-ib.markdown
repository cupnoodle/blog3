---
layout: post
title:  "How to view rounded corners, borders in Interface Builder"
date:   2017-09-25 10:20:10
permalink: "rounded-corners-and-stuff-in-interface-builder"
---



Are you tired of pressing compile and wait every time to see minor UI changes ?

Starting from Xcode 6, you can build custom UI and have it rendered real time in Storyboard / XIB / Interface builder.

We are going to create an UIView with rounded corners and borders in this post. First, create a UIView subclass, I name it as 'RoundedCornerView'  
![New file](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post32/newFile.png)
![Subclass UIView](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post32/subclassUIView.png)

And add the following **cornerRadius** attribute to the newly created subclass file.  

**RoundedCornerView.swift**
```swift
import UIKit

class RoundedCornerView: UIView {

  // if cornerRadius variable is set/changed, change the corner radius of the UIView
  @IBInspectable var cornerRadius: CGFloat = 0 {
    didSet {
      layer.cornerRadius = cornerRadius
      layer.masksToBounds = cornerRadius > 0
    }
  }
  
}
```

The above snippet initialize cornerRadius variable to 0 and any change on its value (didSet) will update the view layer cornerRadius and masksToBounds boolean if the cornerRadius is larger than 0.

The **@IBInspectable** keyword in front of the variable lets you set the value of cornerRadius in Interface builder!

Since @IBInspectable is based on **User-defined runtime attributes**, the variable types it support are also same as the runtime attributes : Boolean, Numbers (NSNumber, double, CGFloat etc), String, CGPoint, CGSize, CGRect, UIColor (Not CGColor), UIImage and also NSRange.  

![Supported variable type](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post32/typeSupported.png)
  
  
Select your desired view in interface builder, set the class to **RoundedCornerView**.
![Setting class to RoundedCornerView](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post32/assignRoundedCornerView.png)

Now select Attribute Inspector tab, you should see the corner radius attribute there, Xcode even auto convert your camelcased variable name to proper format!  
![Attribute Inspector](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post32/attributeInspector.png)
  
You can now set the corner radius of the view in Interface Builder, but the view still looks square after setting the corner radius to 40, what did we miss? 🤔

You need to add **@IBDesignable** on top of the class definition to tell Xcode that it needs to render the view properties directly in the Interface builder.

**RoundedCornerView.swift**
```swift
import UIKit

@IBDesignable
class RoundedCornerView: UIView {
...
}
```

After adding `@IBDesignable` , open the interface builder again, you should see a rounded corner view now :  
![Muh rounded corners](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post32/muhRoundedCorner.png)  

Praise rounded corners, now you can do the same for borders : 

**RoundedCornerView.swift**
```swift
import UIKit

@IBDesignable
class RoundedCornerView: UIView {
  
  // if cornerRadius variable is set/changed, change the corner radius of the UIView
  @IBInspectable var cornerRadius: CGFloat = 0 {
    didSet {
      layer.cornerRadius = cornerRadius
      layer.masksToBounds = cornerRadius > 0
    }
  }
  
  @IBInspectable var borderWidth: CGFloat = 0 {
    didSet {
      layer.borderWidth = borderWidth
    }
  }
  
  @IBInspectable var borderColor: UIColor? {
    didSet {
      layer.borderColor = borderColor?.cgColor
    }
  }
  
}

```
  
<br>  
  
**Result** :   
![Rounded corner with borders](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post32/finishedView.png)

You can download the [sample Swift project here](https://git.tzhongyan.com/soulchild/roundedCorner/archive/master.zip)

p/s: I have moved to a new blog (https://fluffy.es) , you can learn more iOS development stuff there. Also my [free ebook about Auto Layout is here](https://fluffy.es/making-sense-of-auto-layout/).

