# 2019/08/06
### 贡献代码 PR
  如何参与一个开源项目呢？比如人气极高的`bootstrap`项目，这是一个非常强大的CSS框架，你可以访问它的项目主页h`ttps://github.com/twbs/bootstrap`，点“`Fork`”就在自己的账号下克隆了一个`bootstrap`仓库，然后，从自己的账号下`clone`：
```git
git clone git@github.com:michaelliao/bootstrap.git
```
一定要从自己的账号下`clone`仓库，这样你才能推送修改。如果从`bootstrap`的作者的仓库地址`git@github.com:twbs/bootstrap.git`克隆，因为没有权限，你将不能推送修改。

Bootstrap的官方仓库`twbs/bootstrap`、你在GitHub上克隆的仓库`my/bootstrap`，以及你自己克隆到本地电脑的仓库，他们的关系就像下图显示的那样：

![pull request过程](/Users/jihu9/Downloads/1565094053549.jpg)

如果你想修复`bootstrap`的一个bug，或者新增一个功能，立刻就可以开始干活，干完后，往自己的仓库推送。

如果你希望`bootstrap`的官方库能接受你的修改，你就可以在GitHub上发起一个`pull request`。当然，对方是否接受你的`pull request`就不一定了。

#### 小结
- 在GitHub上，可以任意`Fork`开源仓库；

- 自己拥有`Fork`后的仓库的读写权限；

- 可以推送`pull request`给官方仓库来贡献代码。

---

### Gitee 码云
如果我们希望体验`Git`一般的速度，可以使用国内 的Git托管服务——[码云](https://gitee.com/)（[gitee.com](https://gitee.com/)）。

和`GitHub`相比，码云也提供免费的Git仓库。此外，还集成了代码质量检测、项目演示等功能。对于团队协作开发，码云还提供了项目管理、代码托管、文档管理的服务，*5人以下小团队免费*。

 码云的免费版本也提供私有库功能，只是**有5人的成员上限**。

使用码云和使用`GitHub`类似，我们在码云上注册账号并登录后，需要先上传自己的SSH公钥。选择右上角用户头像 -> 菜单“修改资料”，然后选择“SSH公钥”，填写一个便于识别的标题，然后把用户主目录下的`.ssh/id_rsa.pub`文件的内容粘贴进去：
![码云导入ssh key](https://upload-images.jianshu.io/upload_images/29581-5d8ef1720f104a63?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击“确定”即可完成并看到刚才添加的Key。

我们已经有了一个本地的git仓库（例如，一个名为learngit的本地库），如何把它关联到码云的远程库上呢？

首先，我们在码云上创建一个新的项目，选择右上角用户头像 -> 菜单“控制面板”，然后点击“创建项目”：
![创建仓库](https://upload-images.jianshu.io/upload_images/29581-c00ff6546a88daf3?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目名称最好与本地库保持一致：

然后，我们在本地库上使用命令`git remote add`把它和码云的远程库关联：

```git
git remote add origin git@gitee.com:liaoxuefeng/learngit.git
```

之后，就可以正常地用`git push`和`git pull`推送了！

如果在使用命令`git remote add`时报错：

```git
git remote add origin git@gitee.com:liaoxuefeng/learngit.git
fatal: remote origin already exists.
```
这说明本地库已经关联了一个名叫`origin`的远程库，此时，可以先用`git remote -v`查看远程库信息：

```git
git remote -v
origin	git@github.com:michaelliao/learngit.git (fetch)
origin	git@github.com:michaelliao/learngit.git (push)
```

可以看到，本地库已经关联了`origin`的远程库，并且，该远程库指向GitHub。

我们可以删除已有的GitHub远程库：

```git
git remote rm origin
```

再关联码云的远程库（注意路径中需要填写正确的用户名）：

```git
git remote add origin git@gitee.com:liaoxuefeng/learngit.git

```

此时，我们再查看远程库信息：

```git
git remote -v
origin	git@gitee.com:liaoxuefeng/learngit.git (fetch)
origin	git@gitee.com:liaoxuefeng/learngit.git (push)

```

现在可以看到，origin已经被关联到码云的远程库了。通过`git push`命令就可以把本地库推送到Gitee上。

有的小伙伴又要问了，一个本地库能不能既关联GitHub，又关联码云呢？

答案是肯定的，因为git本身是分布式版本控制系统，可以同步到另外一个远程库，当然也可以同步到另外两个远程库。

使用多个远程库时，我们要注意，git给远程库起的默认名称是`origin`，如果有多个远程库，我们需要用不同的名称来标识不同的远程库。

仍然以`learngit`本地库为例，我们先删除已关联的名为`origin`的远程库：

```git
git remote rm origin

```

然后，先关联GitHub的远程库：

```git
git remote add github git@github.com:michaelliao/learngit.git

```

注意，远程库的名称叫`github`，不叫`origin`了。

接着，再关联码云的远程库：

```
git remote add gitee git@gitee.com:liaoxuefeng/learngit.git

```

同样注意，远程库的名称叫`gitee`，不叫`origin`。

现在，我们用`git remote -v`查看远程库信息，可以看到两个远程库：

```git
git remote -v
gitee	git@gitee.com:liaoxuefeng/learngit.git (fetch)
gitee	git@gitee.com:liaoxuefeng/learngit.git (push)
github	git@github.com:michaelliao/learngit.git (fetch)
github	git@github.com:michaelliao/learngit.git (push)

```

如果要推送到GitHub，使用命令：

```git
git push github master

```

如果要推送到码云，使用命令：

```git
git push gitee master

```

这样一来，我们的本地库就可以同时与多个远程库互相同步：

```
┌─────────┐ ┌─────────┐
│ GitHub  │ │  Gitee  │
└─────────┘ └─────────┘
     ▲           ▲
     └─────┬─────┘
           │
    ┌─────────────┐
    │ Local Repo  │
    └─────────────┘

```

码云也同样提供了`Pull request`功能，可以让其他小伙伴参与到开源项目中来。




