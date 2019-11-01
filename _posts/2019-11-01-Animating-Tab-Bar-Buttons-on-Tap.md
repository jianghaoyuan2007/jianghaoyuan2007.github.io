---
layout: post
title: 【译】Animating tab bar buttons on tap
---

If you use the Twitter app on iOS you might have noticed that tapping the buttons in the tab bar makes them bounces.

如果你在 iOS 上使用 Twitter 应用，你或许已经注意到按下在 UITabBar 上面的按钮会使得它们回弹。

![Twitter 效果](https://blog.kulman.sk/animating-tab-bar-buttons/twitter-animation.gif){: .center-image }

This is a very subtle animation that I really like so I decided to do the same for the tab bar in the app I currently work on.

这是一个非常巧妙的动画，我非常喜欢，所以我决定给我当前正在开发 App 的 UITabBar 做一个同样的效果。

The first step is to find the right place to insert the bounce animation into. When you tap a button in the tab bar, the `tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem)` method of the `UITabBarController` gets called. You cannot override this method in extension, so you have to create a new subclass.

首先是找到一个合适的地方把回弹动画加进去。当你点击 UITabBar 上面的按钮，UITabBarController 的  `tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem)` 方法会被调用。在扩展中你不能覆盖这个方法，所以你必须创建一个新的子类。

This method gives you the selected `UITabBarItem` but you need to get to the actual view and its image. I found out that the tab bar contains (at least in my case) a background subview and then subviews corresponding to the tab bar buttons, so when a tab bar button at index N is tapped, its subview is at N+1.

上面的这个方法会把选中的 UITabBarItem 对象给你，但你却需要获得的是实际的 UIView 和对应的 UIImage。我发现 UITabBar 包含一个背景子视图和 UITabBarButton 相关的子视图，所以当下标为 N 的 UITabBarButton 被按下的时候，它对应的子视图的下标是 N + 1。

```swift
class AnimatedTabBarController: UITabBarController {
    override func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem) {
        // find index if the selected tab bar item, then find the corresponding view and get its image, the view position is offset by 1 because the first item is the background (at least in this case)
        guard let idx = tabBar.items?.index(of: item), tabBar.subviews.count > idx + 1, let imageView = tabBar.subviews[idx + 1].subviews.compactMap ({ $0 as? UIImageView }).first else {
            return
        }
        // animate the imageView
    }
}
```

To create a bounce animation we can use `CAKeyframeAnimation` and animate the `transform.scale` key path. Basically, you need to make the image bigger, then slightly smaller and original size again. This is the animation I use

为了创建一个回弹动画，我们可以使用 CAKeyframeAnimation 并且根据 transform.scale 来作动画。从根本上说，你需要先让 UIImage 变大，然后稍微调小一些，最后再调成原始的尺寸。下面的代码就是这个动画。

```swift
let bounceAnimation = CAKeyframeAnimation(keyPath: "transform.scale”)
bounceAnimation.values = [1.0, 1.4, 0.9, 1.02, 1.0]
bounceAnimation.duration = TimeInterval(0.3)
bounceAnimation.calculationMode = CAAnimationCalculationMode.cubic
```
It is more visible as the one in the Twitter app, you can tweak the values to get the animation you like best.

它过于明显像在 Twitter 应用上的那个动画，你可以对这个值进行微调来获得你最喜欢的动画。

![时间模仿效果](https://blog.kulman.sk/animating-tab-bar-buttons/tw-animation.gif){: .center-image }

The resulting class code looks like this

最后的实现代码如下

```swift
class AnimatedTabBarController: UITabBarController {
    private var bounceAnimation: CAKeyframeAnimation = {
        let bounceAnimation = CAKeyframeAnimation(keyPath: “transform.scale”)
        bounceAnimation.values = [1.0, 1.4, 0.9, 1.02, 1.0]
        bounceAnimation.duration = TimeInterval(0.3)
        bounceAnimation.calculationMode = CAAnimationCalculationMode.cubic
        return bounceAnimation
    }()
    override func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem) {
        // find index if the selected tab bar item, then find the corresponding view and get its image, the view position is offset by 1 because the first item is the background (at least in this case)
        guard let idx = tabBar.items?.index(of: item), tabBar.subviews.count > idx + 1, let imageView = tabBar.subviews[idx + 1].subviews.flatMap { $0 as? UIImageView }.first else {
            return
        }
        imageView.layer.add(bounceAnimation, forKey: nil)
    }
}
```

## See also
*  [Changing UIApplication base class](https://blog.kulman.sk/change-uiapplication-class/) 
*  [Creating iOS context menu with highlight and dim](https://blog.kulman.sk/creating-context-menu-with-highlight/) 
*  [Changing UIAlertAction text color](https://blog.kulman.sk/changing-uialertaction-text-color/) 
*  [Editing macOS app About dialog](https://blog.kulman.sk/editing-macos-app-about-dialog/) 
*  [Using CocoaPods to just build frameworks for use elsewhere](https://blog.kulman.sk/using-pods-to-just-build-frameworks/) 
