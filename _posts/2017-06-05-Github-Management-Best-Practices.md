---
layout: post
title: GitHub 管理基础实践
---

代码的开源对整个软件行业有着深远的影响。一方面，它是技术发展方向的一种指引；另一方面，它也促使软件生产有了较高的质量保证。[GitHub](https://github.com) 是一个开源的平台，在这里你可以发现很多好的开源项目。同时，你在这里也可以学习到一个高质量的开源项目是如何分布式协作完成的。

![github.com](http://opbeqriq7.bkt.clouddn.com/github.png){: .center-image }

<br/>

### 理解 GitHub Flow

在 GitHub 的官方指南当中，有一篇文章 *[Understanding the GitHub Flow](https://guides.github.com/introduction/flow/)* 对如何正确使用 GitHub 进行软件开发做了很好的讲述。

下面是对其中的部分内容进行总结：	

#### Create a branch

![Create a branch](http://opbeqriq7.bkt.clouddn.com/github-flow-create-a-branch.png){: .center-image }

在对现有工程进行开发维护（新增功能/更新功能/修复问题/变更功能/移除功能）的时候，要基于 *`master`* 分支创建一个新的分支。这样的好处是可以保证拥有与 *`master`* 分支有同样的环境，并且保证在对所创建分支上的操作不会影响其他的分支，当然也包括 *`master`* 分支。

>
> 分支是 Git 的核心，所有的 GitHub Flow 也基于分支之上。对于 *`master`* 分支有一个重要的要求：一切在 *`master`* 分支上的代码总是可部署的。
>
> 所有从 *`master`* 创建出来的分支，不论是功能的开发，还是问题的修复。它们的命名都应该具有描述性的（比如：`refactor-authentication`，`user-content-cache-key`，`make-retina-avatars`等）。其他人一看到分支的名称就知道是做什么的。
>

#### Add commits

![Add commits](http://opbeqriq7.bkt.clouddn.com/github-flow-add-commits.png){: .center-image }

一旦你的分支被创建了，你可以在其上进行更改。不论何时你添加、编辑和删除一个文件，都可以创建一个 `commit` 到这个分支上。这个添加 `commit` 的过程追踪了你的进度在这个特性分支上的工作。

`commit` 同样也创建了对于你工作的一个透明历史，使得别人可以了解你的工作。每个 `commit` 也都关联了一条信息，这条信息描述了为什么要这样改。从一个更远的角度去看，每个 `commit` 都可以认为是一个更改的分割单元。如果发现了一个问题，这使得你可以去回滚所更改的内容。或者你可以回到起头，朝着另外一个方向重新去做。

>
> `commit` 信息是非常重要的。Git 追踪你的更改，然后显示它们作为提交，一旦它们被提交到服务器上。
>
> 通过书写简单清晰的提交信息，可以使得更加简单为别人追随以及提供反馈。
>

#### Open a Pull Request

![Open a Pull Request](http://opbeqriq7.bkt.clouddn.com/github-flow-open-a-pull-request.png){: .center-image }

`Pull Request` 发起了关于你的 `commit` 的讨论。你可以在开发的任何时候创建一个 `Pull Request` 。不论你是否有代码要  `commit`，你有一些想法要去展示，还是你在一个问题卡住的时候，你都可以创建一个`Pull Request`。

你还可以去 @mention 指定的人或者团队去获取反馈，不论他们是否下班了，还是与你有很大的时差。

>
> 对于**贡献到开源项目**和**管理更改到共享的仓库**，`Pull Request` 是非常有用的。
>

#### Discuss and review your code

![Discuss and review your code](http://opbeqriq7.bkt.clouddn.com/github-flow-discuss-and-review-your-code.png){: center-image }

一旦你的 `Pull Request` 被创建起来，指定的人员或者团队会审核你的更改，会对你的更改提出一些问题或者评论。或许你的代码风格与项目的指南所要求的不一致，变更的内容缺少对应的单元测试。也或许一切看起来非常不错，井然有序。当然，你也可以继续 `push` 你的代码到这个分支上。

>
> `Pull Request` 的评论使用了 Markdown 语言。所以,你可以内置图片和 emoji 表情，预先格式化好的文本，以及其他轻量的格式。
>

#### Deploy

![Deploy](http://opbeqriq7.bkt.clouddn.com/github-flow-deploy.png){: center-image }

一旦你的 `Pull Request` 审核和测试通过，你可以部署你的更改到生产环境去验证。如果你的分支出现了问题，你可以通过部署当前的 `master` 分支上的内容到生产环境来进行回滚。

#### Merge

![Merge](http://opbeqriq7.bkt.clouddn.com/github-flow-merge.png){: center-image }

现在你修改的内容在生产环境上已经验证通过了，可以把它们合并到 `master` 分支上了。

一旦合并，`Pull Request` 会存储一条更改历史记录到你的代码上。由于它们是可搜索的，它们可以让任何人及时回头去理解为什么以及怎样做了这个决定。

>
> 通过包含固定的关键字和代码到你的 `Pull Request` 文本中，可关联对应的 `issue` 。当你的 `Pull Request` 被合并之后， 相关的 `issue` 就会被关闭掉。比如，输入短语 `Close #32` 会关闭仓库中编号为 `32` 的 `issue` 。更多信息可以查看[这里](https://help.github.com/articles/closing-issues-via-commit-messages)。
>

通过上面的内容，使我们在 GitHub 的初步使用上有了一些了解。

<br/>

### Github Management Best Practices

在著名的 iOS 开源项目 [AFNetworking](https://github.com/AFNetworking/AFNetworking) 中，有一个 [fastlane](https://github.com/AFNetworking/fastlane) 的 `repo`。这个项目主要是使用 [fastlane](https://github.com/fastlane/fastlane)  对 `AFNetworking` 进行自动化 `CI` 和 `CD` 管理的。

里面提到了该如何有效地管理 GitHub ，我觉得这个内容对以后的软件开发有很大帮助。所以，下面是我对[这些](https://github.com/AFNetworking/fastlane#github-management-best-practices)内容进行了简单的整理。

#### 实践理论

下面是 `AFNetworking` 如何管理的：

* 任何更改应该被关联在 `changelog` 中的应该使用 `Pull Request` 去进行合并。

* `Pull Request` 的 `title` 应该简洁，将准备显示在 `changelog` 中。这就意味着 `repo` 的管理员需要适当地为你修改 `Pull Request` 的 `title`， 使它更加有意义。

* 所有的 `release` 应该被 `GitHub milestone` 来管理。不仅这提供了一个[良好文档](https://github.com/AFNetworking/AFNetworking/issues?q=milestone%3A2.6.2)的地方为用户获得一个清晰的预览什么发布了，而且也为 `changelog` 的生成提供了主要的组件。这个 `milestone` 的名称要和计划的 `tag` 名称一致。

* 所有的问题合并应该使用 `GitHub Label` 被分组在以下五类中的一类。这允许 `changelog` 以多个逻辑分离，就像[这样](https://github.com/AFNetworking/AFNetworking/blob/master/CHANGELOG.md#262-11062015)。你可以看到一个 `AFNetworking` 以[这种](https://github.com/AFNetworking/AFNetworking/issues?q=milestone%3A2.6.2+is%3Aclosed)方式发布管理的例子。
	* Added
	* Updated
	* Changed
	* Fixed
	* Removed

#### 扩展阅读

* [Managing your work with issues](https://help.github.com/articles/managing-your-work-with-issues/)

	You can manage your work on GitHub by creating issues to track ideas, enhancements, tasks, or bugs.

* [Tracking the progress of your work with milestones](https://help.github.com/articles/tracking-the-progress-of-your-work-with-milestones/)

	You can track your work on GitHub by creating milestones with associated issues and pull requests.

* [Release](https://help.github.com/categories/releases/)
	
	Releases are GitHub's way of packaging and providing software to your users. You can think of it as a replacement to using downloads to provide software.

* [Collaborating with issues and pull requests](https://help.github.com/categories/collaborating-with-issues-and-pull-requests/)

	Pull requests let you tell others about changes you've pushed to a repository on GitHub. Once a pull request is opened, you can discuss and review the potential changes with collaborators and add follow-up commits before the changes are merged into the repository.

<br/>


### 参考链接
- [Understanding the GitHub Flow](https://guides.github.com/introduction/flow "Understanding the GitHub Flow")

- [Github Management Best Practices](https://github.com/AFNetworking/fastlane#github-management-best-practices "Github Management Best Practices")

- [Managing your work with issues](https://help.github.com/articles/managing-your-work-with-issues "Managing your work with issues")

- [Tracking the progress of your work with milestones](https://help.github.com/articles/tracking-the-progress-of-your-work-with-milestones "Tracking the progress of your work with milestones")

- [Collaborating with issues and pull requests](https://help.github.com/categories/collaborating-with-issues-and-pull-requests "Collaborating with issues and pull requests")