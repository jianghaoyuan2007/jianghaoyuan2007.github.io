---
layout: post
title: 如何在 macOS 上快速切换应用
---

现今，不论是办公，还是生活。我们越来越离不开计算机了。我们在做一件事情的时候会打开许多的应用程序，而不是像以前只需要打开单一的应用就可以了。也就是说，我们现在用计算机去完成一项任务，频繁地进行应用程序切换变得非常普通的一件事情。那么，我们曾经是否想过去优化这个频繁的操作呢？

![DJ84](http://www.artnet.com/Magazine/reviews/banai/Images/banai2-28-2.jpg){: .center-image }

<br/>

### 常用方法

-------------

#### 第一种：可以通过在 Dock 上点击对应的应用程序的图标的方式：

![Switch-Between-Apps-Click](http://opbeqriq7.bkt.clouddn.com/Switch-Between-Apps-Click.gif){: .center-image }

<br/>

#### 第二种：可以通过 ⌘ 和 TAB 两个键的方式：

![Switch-Between-Apps-CMD+TAB](http://opbeqriq7.bkt.clouddn.com/Switch-Between-Apps-CMD+TAB.gif){: .center-image }

<br/>

其实，这两张方法都有一个共同的好处，那就是简单。因为是操作系统对其默认的方式，所以不需要配置任何内容。但它们的缺点也是非常地明显，那就是操作起来效率比较低。如果使用的电脑屏幕较大或者连接了额外的显示器，那么这种方式就显得笨拙。而且这类的操作非常地频繁，就会导致大多的操作全部耗在这上面了。

那么，既然问题已经显而易见了。我们该如何优化这个操作呢？
<br/>

### Slate 

-------------

#### 什么是 Slate ?

A window management application (replacement for Divvy/SizeUp/ShiftIt)

这段文字来自 Slate 的 GitHub Repository 描述。简单说来就是一个管理窗口的应用程序。并且可以代替 Divvy 、SizeUp 和 ShiftIt 的应用程序。窗口管理这类问题，其实也早已经有人关注到了。并且也找到了解决方案。上面的提到的这些应用程序都可以解决类似的问题。

#### 功能概述

* 高度地自定义
* 绑定按键：
	* 移动以及或重置窗口大小
	* 直接聚焦窗口
	* 激活预设的布局
	* 创建、删除和激活当前窗口的状态的快照
* 为不同的显示器设置默认布局配置会激活，当配置被删除时
* 窗口暗示：一种直觉的更改去更改窗口的聚焦
* 【Beta】一个更好，更加自定义，应用切换器

#### 安装 Slate

***安装说明***

注意：你必须启用 Accessibility API 功能。通过选取苹果菜单 >“系统偏好设置”，点按“隐私”，点按“辅助功能”，然后选择该应用的复选框。若要移除应用访问 Mac 的权限，请取消选择复选框。您还可以将应用拖到面板中以进行添加，然后选择该应用的复选框。
<br/>

***直接下载***

* [.dmg](http://slate.ninjamonkeysoftware.com/Slate.dmg)
* [.tar.gz](http://slate.ninjamonkeysoftware.com/versions/slate-latest.tar.gz)

#### 配置 Slate

Slate 使用了一个 `.slate` 文件去配置，它在当前用户的 home 目录。 Slate 一运行就加载这个配置。 你可以通过在状态栏上的菜单选项中的 `Load Config` 去重新载入配置文件。

注意：如果当前用户的 home 目录中没有 ".slate" 文件存在的话，默认的配置文件将会被使用。

***Configuration is split into the following directives:***

* `config` (for global configurations)

* `alias` (to create alias variables)

* `layout` (to configure layouts)

* `default` (to default certain screen configurations to layouts)

* `bind` (for key bindings)

* `source` (to load configs from another file)

注意： `#` 是一个注释字符。一切在 `#` 后面的内容都将被忽略。

*对于我们来说，上面的指令只需要用到 `alias` 和 `bind` 就足够了。所以下面仅对这两种指令进行说明。详细的指令用法请参考[这里](https://github.com/jigish/slate/blob/master/README.md#configuring-slate)。*

***alias 指令的用法***

`alias` 的指令遵循以下形式：
    alias name value

当你设置了一个 `alias` ，你可以在任何一个指令中引用它（在它定义的后面）通过引用像 `${name}` 的方式。

例如：

	alias Chrome 'Google Chrome'

会允许你使用 `${Chrome}` 去作为一引用到 `Google Chrome` 。	

***bind 指令的用法***

`bind` 的指令遵循以下其中一种形式（可能通过任意数量的空格分隔）

bind key:modifiers operation parameter+

bind key:modal-key operation parameter+

***Key***

key 关联键盘上的按键。 可以查看一个完整的 `Allowed Keys` 的列表。例如： `s` 是简单的 `s` ，而 `1` 在数字键盘上是 `pad1` 。可以使用的 `key` 如下：

* '
* ,
* -
* .
* /
* 0
* 1
* 2
* 3
* 4
* 5
* 6
* 7
* 8
* 9
* ;
* =
* `
* a
* b
* backslash
* c
* caps
* d
* delete
* down
* e
* end
* esc
* f
* f1
* f10
* f11
* f12
* f13
* f14
* f15
* f16
* f17
* f18
* f19
* f2
* f20
* f3
* f4
* f5
* f6
* f7
* f8
* f9
* g
* h
* help
* home
* i
* j
* k
* l
* left
* m
* mute
* n
* o
* p
* pad*
* pad+
* pad-
* pad.
* pad/
* pad0
* pad1
* pad2
* pad3
* pad4
* pad5
* pad6
* pad7
* pad8
* pad9
* pad=
* padClear
* padEnter
* pageDown
* pageUp
* q
* r
* return
* right
* s
* space
* t
* tab
* u
* up
* v
* w
* x
* y
* z
* [
* ]

***Modifiers***

Modifier 是一个逗号或者分号分隔标准修饰符按键的列表。可用的 Modifier 如下：

* Control: ctrl

* Option/Alt: alt

* Command: cmd

* Shift: shift

***Operation***

Operation 定义了被聚焦的窗口实际做的是事情。可用的 Operation 如下：

* move (Move/Resize the window any which way: move topLeftX;topLeftY sizeX;sizeY screen)
* resize (Resize the window (keeping top-left the same): resize x y anchor)
* push (Push the window to the edge of the screen: push direction style)
* nudge (Nudge the window in any direction: nudge x y)
* throw (Throw the window to any screen's origin: throw screen style)
* corner (Move/Resize the window into a corner: corner direction style)
* shell (Execute a shell command: shell options 'command')
* hide (Hide one or more applications: hide applications)
* show (Show one or more applications: show applications)
* toggle (Toggle one or more applications: toggle applications)
* chain (Chain multiple operations to one binding: chain opAndParams1 opAndParams2 ...)
* sequence (Activate a sequence of operations in one binding: sequence opAndParams1 separator opAndParams 2 ...)
* layout (Activate a layout: layout name)
* focus (Focus a window in a direction or from an application: focus direction app)
* snapshot (Create a snapshot of your current window locations: snapshot name options)
* delete-snapshot (Delete a snapshot: delete-snapshot name options)
* activate-snapshot (Activate a snapshot: activate-snapshot name options)
* hint (Show Window Hints (similar to Link Hints in Vimium except for Windows): hint characters)
* grid (Show a Grid to one-off resize and move windows: grid options)
* relaunch (Relaunch Slate: relaunch)
* undo (Undo an Operation: undo)
* switch ([Beta] A Better Application Switcher: switch)
<br/>

### 改善切换方式

-------------

现在，我们可以使用 `Slate` 来优化我们的切换方式。

#### 自定义 Slate

1. 使用 `Vim` 来编辑我们的 `Slate` 配置文件。

	在命令行工具中输入 *`vim ~/.slate`* ，然后按下回车。

2. 打开后，我们先利用 `alias` 去设置我们的应用别名。

	例如： *`alias Chrome 'Google Chrome'`*

3. 然后，我们再利用 `bind` 去设置我们的绑定操作。

	例如： *`bind g:alt;shift focus ${Chrome}`*

4. 按照使用说明，如果我需要将这个配置文件重新载入的话，那么我们需要通过在状态栏上的菜单选项中的 `Load Config` 去重新载入配置文件。

*[这里](https://gist.github.com/jianghaoyuan2007/783643040693a17f33b65782b12fb0bc)是我当前的 `Slate` 的配置文件，已经对常用的程序对其进行了设置。下面我们看一下优化后的效果。

![Switch-Between-Apps-Slate](http://opbeqriq7.bkt.clouddn.com/Switch-Between-Apps-Slate.gif){: .center-image }
<br/>

### 结束语

-------------

快速地切换不同的应用程序使得我们工作更有效率，同时也避免了没用信息的干扰。具体实现快速切换的方式有很多，今天介绍的只是众多中的一种。而且这种方式也需要不断地改进，来成为我们最有利的帮手。每个人都有不同的习惯，让我们打造属于各自的舒适人生吧！
<br/>

### 参考链接

-------------

- [jigish/slate - GitHub](https://github.com/jigish/slate "jigish/slate: A window management application (replacement for Divvy/SizeUp/ShiftIt)")