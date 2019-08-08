# 2019/08/08 gitflow 工作流程
`Git flow`是基于git之上的一种软件开发迭代模型。Git flow是使用git进行源代码管理的一套行为规范。
Git Flow重点解决的是由于源代码在开发过程中的各种冲突导致开发活动混乱的问题，提高开发效率。

![gitflow](http://www.wekri.com/assets/git-model@2x.png)

`Git Flow中的分支`
`Git Flow`模型中定义了**主分支**和**辅助分支**两类分支。其中主分支用于**组织与软件开发、部署相关的活动**；辅助分支组织为了**解决特定的问题而进行的各种开发活动**。

**主分支**
- master分支
- develop 分支

**辅助分支**

我们的开发模式旁边的主要分支机构掌握和发展，使用各种支持分支机构，以帮助团队成员之间的平行发展，便于跟踪的功能，准备生产版本，并协助快速修复现场生产问题。 与主分支不同，这些分支总是有有限的生命时间，因为它们最终将被移除。

- feature分支
- release分支
- hotfix分支

>**feature 分支**
>- 从develop分支检出
>- 必须合并回develop分支
>- 命名规范：除了master, develop, release-*, or hotfix- *

当开始一个新特征的开发时，从develop检出feature分支。Feature分支的本质是，只要特性处于开发阶段，它就会存在，将来会被合并会develop分支（为了即将发布的版本而明确地添加新特性），或者丢弃掉（如果是令人失望的实验）。

> Feature分支只存在于开发者本地，不能被提交到远程库
![feature branch](http://www.wekri.com/assets/fb@2x.png)


**创建feature**

Switched to a new branch “myfeature”
```git
$ git checkout -b myfeature develop
```
开发。。。

**完成feature**

完成的功能可以合并到`develop`分支，以明确将它们添加到即将发布的版本中：
```git
$ git checkout develop

$ git merge --no-ff myfeature

$ git branch -d myfeature

$ git push origin develop
```
**release分支**

从`develop`分支检出
必须合并回`develop`分支和`master`分支
命名规范:`release-*`
`release`分支是为发布新的产品版本而设计的。在这个分支上的代码允许做小的缺陷修正、准备发布版本所需的各项说明信息（版本号、编译时间等等）。通过在`release`分支上进行这些工作可以让`develop`分支空闲出来以接受新的`feature`分支上的代码提交，进入新的软件开发迭代周期。

当`develop`分支上的代码已经包含了所有即将发布的版本中所计划包含的软件功能，并且已通过所有测试时，我们就可以考虑准备创建`release`分支了。而所有在当前即将发布的版本之外的业务需求一定要确保不能混到`release`分支之内（避免由此引入一些不可控的系统缺陷）。

**创建一个release分支**

`Release`分支从`develop`分支检出。例如, 当前产品版本是1.1.5，我们有一个比较大的更新，`develop`分支已经做好发布准备了，我们新的版本号定位1.2 (而不是1.1.6 或 2.0)。所以，我们从develop分支检出release分支，命名为`release-1.2`：
```git
$ git checkout -b release-1.2 develop

$ ./bump-version.sh 1.2

$ git commit -a -m "Bumped version number to 1.2"
```
这个新的分支可能会存在一段时间，直到发布可能被推出。 在此期间，可以在此分支做一些小的错误修复（而不是开发分支）。而不能添加大的新功能。

**完成release分支**
当`release`分支准备发布时，需要执行一些操作。 首先，`release`分支被合并`master`分支（每往`master`提交一次，就是一个新的版本）。 接下来，对`master`的提交必须打tag，以便将来找到这个历史版本。 最后，`release`分支所做的更改需要重新合并到`develop`分支，以便将来的版本也包含这些错误修复。
```git
$ git checkout master

$ git merge --no-ff release-1.2

$ git tag -a 1.2
```
此时，已经发布完成，并打过了`tag`。

为了保存`release`分支所做的更改，需要把release分支合并回`develop`分支
```git
$ git checkout develop

$ git merge --no-ff release-1.2
```
这个操作可能会有冲突，合并冲突，提交就行了。

现在我们已经完成了，可以删除`release`分支了，因为我们不再需要它了：
```git
$ git branch -d release-1.2
```
**hotfix分支**：

- 从master检出
- 合并会develop和master分支
- 命名：hotfix-*
![hotfix branch](http://www.wekri.com/assets/hotfix-branches@2x.png)


`hotfix`分支非常像`release`分支，因为它们都意味着即将发布一个新的版本，尽管是未计划的。

当线上出现一个严重的bug，需要立即修复的时候，就需要从master分支上指定的tag版本派生hotfix分支来进行紧急修复工作。

这样做的显而易见的好处是不会打断正在进行的develop分支的开发工作，能够让团队中负责新功能开发的人与负责代码紧急修复的人并行的开展工作。

**创建hotfix**

hotfix分支从master检出。例如,当前线上运行的是1.2版本，出现了严重bug。而且develop分支还不够稳定。可以从master检出hotfix分支来修复bug：
```git
$ git checkout -b hotfix-1.2.1 master
$ ./bump-version.sh 1.2.1
Files modified successfully, version bumped to 1.2.1.
$ git commit -a -m "Bumped version number to 1.2.1"
```
修复bug。。。
```git
$ git commit -m "Fixed severe production problem"
```

**完成hotfix**

当完成修复，hotfix分支需要合并到master，也要合并到develop分支，以便下个版本也包含这次修复。这个和完成release分支完全相似。

- 合并到master并打tag
```git
$ git checkout master

$ git merge --no-ff hotfix-1.2.1

$ git tag -a 1.2.1
```
- 合并到develop分支
```git
$ git checkout develop

$ git merge --no-ff hotfix-1.2.1
```
>注意：有一个例外，如果此时存在release分支时，就需要将hotfix分支合并到release分支，而不是develop分支。其实当release分支完成的时候，这次修复也就被合并到develop分支了。（如果在develop分支的工作立即需要修正这个错误，而不能等到release分支完成，也可以将后hotfix分支合并到develop分支。）

- 最后，删除这个hotfix分支：
```git
$ git branch -d hotfix-1.2.1
```
#### 小结
1.主要分支
- **master**: 项目的主要分支，对外的第一门面。所有人浏览项目，使用项目，第一时间看到的是master。永远处在即将发布(production-ready)状态。
- **develop**: 处于功能开发的最前线的版本，查看develop分支就能知道下一个发布版本有哪些功能了。develop一开始是从master里分出来的，并且定期会合并到master里，每一次合并到master，表示我们完成了一个阶段的开发，产生一个稳定版。同样的，develop下也不建议直接开发代码，develop代表的是已经开发好的功能的回归版本。最后，在适当的时候，由合适的人，合并到master，作为下一个稳定版本。

- **feature**的作用是为每一个新功能从develop里创建出来的一个分支。例如小明和小白分别做两个不相干的功能，就应该分别创建两个分支，各自开发完以后，先后合并到develop里，这就叫做回归。

2.辅助分支
- **feature**: 开发新功能的分支, 基于 develop, 完成后 merge 回 develop
- **release**: 准备要发布版本的分支, 也基于 develop, 完成后 merge 回 develop 和 master
- **hotfix**: 修复 master 上的问题, 等不及 release 版本就必须马上上线. 基于 master, 完成后 merge回 master 和 develop
