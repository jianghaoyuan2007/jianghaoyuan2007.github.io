---
layout: post
title: 开发项目复盘
---

前段时间参加了极客时间推出的《算法训练营》的课程。其中反复被强调的一个词——复盘。在搜索引擎上查了一下才知道这个分析过程是那么的重要和有价值。所以，我借此也将“复盘”应用到具体的实践中，不断地提高自己和更多地理解复盘。

## CocoaPods 相关问题
如果将`Podfile.lock`放入版本控制当中的话，常常会因为项目各个成员所依赖的第三方库版本不同而导致文件冲突和需要不停地进行解决冲突和执行`pod install`等相关操作，特别麻烦。
根据以上情况所产生的问题，建议使用以下方法来解决：
```ruby
# pod 'JCTagListView', '~> 2.0.0' 
pod 'JCTagListView', '2.0.0' # 这种写法表示只使用 `2.0.0` 版本。
```
## UIStackView
对于一般的线性布局建议尽可能多使用`UIStackVIew`，因为使用这种方式的布局的好处是效率高和更加灵活。一般来说，熟练使用`UIStackView`的话，要比使用约束的方式去布局要快3~5倍，而且布局思路也比较清晰。当`UIStackView`里面的任意视图方式改变（如隐藏），`UIStackView`都会自动去处理相关的约束，非常灵活和方便。
但也并不是说`UIStackView`就因此变得无比的强大，它也有其自己的局限性。在实际开发过程中，一定要具体情况具体分析，不要过度地依赖某项技术，要灵活多变。
在各种场景下的使用。

## Storyboard
在`Storyboard`的使用上需要处处小心。因为它最容易导致的问题就是内容过于庞大所带来的`Xcode`的操作卡顿。可以尝试多使用`Storyboard Reference`技术，将分散的`Storyboard`聚合在一起。也要不停地进行模块化，随时检查该`Storyboard`是否已经过于庞大了。

## Storyboard Container View
`Container View`可以说是`Storyboard`中独特的一项技能。如果使用好了的话，可以将一个复杂的页面结构调整的非常清晰。也可以最大范围地使用`MVC`设计模式（以`UIViewController`为单位，而不是以`UIView`）。

## Storyboard Segue
作为`Storyboard`的特性之一，`UIStoryboardSegue`可以省掉页面跳转不必要的胶水代码。也可以自定义`UIStoryboardSegue`来实现一些简单的动画效果。
* [Dismissing a Modal View Using a Storyboard Segue](http://jeffreysambells.com/2014/02/19/dismissing-a-modal-view-using-a-storyboard-segue)

## Storyboard Object
可以借用`Storyboard Object`功能将`UIViewController`庞大的代码分散到其他的代码文件中。

## 自定义弹出框
如果是使用模态的方式去展示，那么一点要记住`UIModalPresentationStyle`要使用`UIModalPresentationOverFullScreen`或与之相关的类型。

## 模型解析处理利器
使用[JSONExport](https://github.com/Ahmed-Ali/JSONExport)和[YYModel](https://github.com/ibireme/YYModel)来处理模型的序列化与反序列化。

## Xcode Snippets
曾经见过`Swift Talk`中，看见`Chris`很快就把一大串代码给打完了，当时特别好奇是怎么做到的，有一天才发现是使用了`Xcode Snippets`这项技术。对于经常写的代码，配置好了相关的 Snippets，那么开发速度会加快很多。

## 大众点评吸顶效果
模仿大众点评主页的吸顶效果。

## 优雅地使用 UITableView
借鉴[RETableViewManager](https://github.com/romaonthego/RETableViewManager)来实现一个简易的`UITableView`。

## 优化 JCTagListView
给  [JCTagListView](https://github.com/lijingcheng/JCTagListView)  添加自动布局固有尺寸的能力。学习其内部实现。自定义`UICollectionViewCell`的能力。

## 图片拉伸
设置`UIImage`的拉伸处理。

## 组合模式
使用组合模式取代`超级父类`。给`UIViewController`添加扩展功能。

## IBInspectable
对`UIView`添加一些属性，使其可以在`Interface Builder`中直接设置，不需要添加额外的代码。

## 网络请求
网络请求简易的封装。

## UICollectionView
对于基本的 Layout 大概的设置。

## UIPageViewController
可将多个`UIViewController`放置在一起的容器。

## 持续集成 + Git flow
如何使用了持续集成，可以避免线上的代码是有问题。

## 其它内容
代码分块策略。
 [Lookin](https://cdn.lookin.work/release/Lookin-0-9-3.zip) 








