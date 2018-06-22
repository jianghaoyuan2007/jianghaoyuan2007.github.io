---
layout: post
title: 与 iPhone 屏幕相关的参数说明
---

到目前为止，Apple 已经发布了很多款的 iPhone，最新的也就是 iPhone X。在 iPhone X 之前也有很多版本的 iPhone，它们有着不同的尺寸。在它们屏幕相关的参数中有一定的相关性。以下的内容是对其中一些参数的记录。

## iPhone 不同型号屏幕信息

--- 

iPhone 不同型号屏幕信息如下表所示：

| 型号 | 屏幕尺寸 | 逻辑分辨率 | 倍率 | 像素分辨率 | DPI | PPI |
|:-------------|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|
 iPhone 4 <br/> iPhone 4S | 3.5-inch | 320 x 480 points | @2x | 640 x 960 px | 163 | 326 |
| iPhone 5<br/> iPhone 5S<br/> iPhone 5C<br/> iPhone SE | 4-inch | 320 x 568 points | @2x | 640 x 1136 px | 163 | 326 |
| iPhone 6<br/> iPhone 6S<br/> iPhone 7<br/> iPhone 8 | 4.7-inch | 375 x 667 points | @2x | 750 x 1334 px | 163 | 326 |
| iPhone 6 Plus<br/> iPhone 6S Plus<br/> iPhone 7 Plus<br/> iPhone 8 Plus | 5.5-inch | 414 x 736 points | @3x | 1242 x 2208 px (Virtual)<br/>1080 x 1920 px (Physical) | 154 | 401 |
| iPhone X | 5.8-inch | 375 x 812 points | @3x | 1125 x 2436 px | 154 | 458 |

## 相关参数

--- 

### 屏幕尺寸

例如：Phone X 为 5.8 英寸，这个 5.8 英寸指的是屏幕对角线长度。

1英寸（inch）=2.54厘米（cm）

### 逻辑分辨率

>
> The default logical coordinate space is measured using points.
>

对于开发者来说，去布局和计算所使用的就是这个逻辑分辨率。

例如：iPhone X 的逻辑分辨率为 375 x 812 points。

### 像素分辨率

例如：iPhone X 的像素分辨率为 1125 x 2436 px。

### 倍率

>
> This value reflects the scale factor needed to convert from the default logical coordinate space into the device coordinate space of this screen. The default logical coordinate space is measured using points. For Retina displays, the scale factor may be 3.0 or 2.0 and one point can represented by nine or four pixels, respectively. For standard-resolution displays, the scale factor is 1.0 and one point equals one pixel.
>

一般来说，存在**像素分辨率 = 逻辑分辨率 * 倍率**这样的关系。

例如：iPhone X 的倍率为 @3x。

### PPI（Pixels Per Inch）

PPI 是像素密度的单位。屏幕上每英寸可以显示的像素点的数量，即屏幕像素密度。

计算方法：物理屏幕对角线的像素分辨率除以物理屏幕对角线的尺寸。

计算公式：$$ PPI = \frac{\sqrt{Width^2 + Height^2}}{Inch} $$

例如，iPhone 8 的 PPI ： $$ PPI = \frac{\sqrt{750^2 + 1134^2}}{4.7} \approx 325.61 $$ （326 PPI）

iPhone X 的 PPI ： $$ PPI = \frac{\sqrt{1125^2 + 2436^2}}{5.8} \approx 462.63  $$  （**458 PPI**）

**iPhone 8 的 PPI 计算结果跟表格上的一致，但 iPhone X 的 PPI 计算结果是怎么了？**

~~我个人的猜测是 iPhone X 有一个齐刘海，占用了一定的空间把整体的平均值给拉下来了。但下面的 DPI 却没有受影响，好奇怪。~~

这个问题我暂时不清楚，以后我会再查阅相关资料。

### DPI (Dots Per Inch)

DPI 是点密度的单位。DPI 越高，每英寸内的墨点就越多，你打印出来的东西就会越清晰锐利。

计算方法：物理屏幕对角线的逻辑分辨率除以物理屏幕对角线的尺寸。

计算公式：$$ DPI = \frac{\sqrt{Width^2 + Height^2}}{Inch} $$

例如，iPhone 8 的 DPI ： $$ DPI = \frac{\sqrt{375^2 + 667^2}}{4.7} \approx 162.81 $$  （163 DPI）

iPhone X 的 DPI ： $$ DPI = \frac{\sqrt{1125^2 + 2436^2}}{5.8} \approx 154.21  $$  （154 DPI）


## iPhone 8 Plus 为什么会有两种不同的像素分辨率？

--- 

正如上面的表格所示，iPhone 8 Plus 有两种像素分辨率：Physical 和 Virtual。为什么会如此的奇怪呢？

### Physical 像素分辨率

iPhone 8 Plus 真机的像素分辨率是 1080 * 1920 px。正如之前总结的——一般来说，存在**像素分辨率 = 逻辑分辨率 * 倍率**这样的关系。那为什么到这就不行了呢？iPhone 8 Plus 为什么不把像素分辨率做成 1242 x 2208 px （**(414 * 3) x (736 * 3) px**）呢？

有一些人推测的，之所以没有做成 1242 x 2208 px 的分辨率在真机上，是处于成本和耗电量。我觉得这是有些道理的。

### Virtual 像素分辨率

按照上面的公式，iPhone 8 Plus 也确实提供了 1242 x 2208 px 的分辨率，只是没有做成物理像素分辨率。

```objective-c

CGSize size = [UIScreen mainScreen].currentMode.size;   // {1242, 2208}

```

上面这段代码在 iPhone 8 Plus 上面会得到 `{1242, 2208}` 。这样也就是说对于开发者来说，可以认为是上面公式得出的分辨率了。

### 这两种不同的像素分辨率是如何在一起工作的？

在继续分析的过程之前，可以先看一张图片。这张图片描述了从 Points 到 Physical Device 的整个过程。

![The Ultimate Guide to iPhone Resolutions, by PaintCode](http://opbeqriq7.bkt.clouddn.com/ultimate-guide-to-iphone-resolutions-2018.jpg){: .center-image }

iPhone 8 Plus 与其它 iPhone 在整个过程中只有一处不同，就是多了一个 Physical Pixels 的环节。这个环节的主要目的也很清楚，就是有些设备的像素分辨率要比上一步正常渲染出来的低，在显示到设备之前需要对其进行 downsampled 。这样就能和设备上所提供的分辨率支持保持一致了。

这样看来，iPhone 8 Plus 并非是真正的 @3x 的倍率。

这一步是系统去做的，对于开发者来说没有影响。只要将 iPhone 8 Plus 完全当成一个被 @3x 的就可以了。

## 结束语

---

了解了上面的内容，基本就可以准备开始做不同 iPhone 屏幕的适配了。

## 参考链接

---

* [The Ultimate Guide To iPhone Resolutions](https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions)

* [List of iOS devices](https://en.wikipedia.org/wiki/List_of_iOS_devices)

* [scale - Apple Developer Documentation](https://developer.apple.com/documentation/uikit/uiscreen/1617836-scale?language=objc)

* [iPhone屏幕尺寸、分辨率及适配](https://blog.csdn.net/phunxm/article/details/42174937)




