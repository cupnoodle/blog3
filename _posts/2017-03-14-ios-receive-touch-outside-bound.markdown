---
layout: post
title:  "iOS - Receive touch event outside bounds of UIView"
date:   2017-03-14 10:20:10
permalink: "ios-receive-touch-event-outside-bounds-of-uiview"
---


An interesting problem I met during work, the previous developer placed a child UIButton outside of its parent UIView bounds, hence the tap on the UIButton is not registered. I troubleshooted 15 minutes for this problem and found the correct keyword to google for in Stack Overflow.

Writing this down in case my future self needs to reference it.

```objectivec
//override this in a UIView Custom Class

// check if any sub element inside view is outside bounds of the view
// if yes, expand the hitable area of view with its sub element
// http://stackoverflow.com/questions/11770743/capturing-touches-on-a-subview-outside-the-frame-of-its-superview-using-hittest
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event
{
  if (!self.clipsToBounds && !self.hidden && self.alpha > 0) {
    for (UIView *subview in self.subviews.reverseObjectEnumerator) {
      CGPoint subPoint = [subview convertPoint:point fromView:self];
      UIView *result = [subview hitTest:subPoint withEvent:event];
      if (result != nil) {
        return result;
      }
    }
  }
  
  return nil;
}
```

Source : [http://stackoverflow.com/questions/11770743/capturing-touches-on-a-subview-outside-the-frame-of-its-superview-using-hittest](http://stackoverflow.com/questions/11770743/capturing-touches-on-a-subview-outside-the-frame-of-its-superview-using-hittest)


p/s: I have moved to a new blog [https://fluffy.es](https://fluffy.es) , you can learn more iOS development stuff there. Also my [free ebook about Auto Layout is here](https://fluffy.es/making-sense-of-auto-layout/).