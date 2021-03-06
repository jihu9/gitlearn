# gitlearn

- disp:learn git

---
# **day01 2019/07/30**

### 创建版本库
- 在工作目录下，初始化一个Git仓库，使用`git init`命令。

- 添加文件到Git仓库，分两步：

1.使用命令`git add` <file>，注意，可反复多次使用，添加多个文件；

2.使用命令`git commit -m <message>`，完成。

> **git commit**命令，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

---

### 时光机穿梭
- 要随时掌握工作区的状态，使用`git status`命令
```git
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
>**git status**命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，readme.txt被修改过了，但还没有准备提交的修改。

虽然Git告诉我们readme.txt被修改了，但如果能看看具体修改了什么内容，自然是很好的。比如你休假两周从国外回来，第一天上班时，已经记不清上次怎么修改的readme.txt，所以，需要用`git diff`这个命令看看：
```git
$ git diff readme.txt
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.
```
>**git diff**顾名思义就是查看difference，显示的格式正是Unix通用的diff格式，可以从上面的命令输出看到，我们在第一行添加了一个distributed单词。

#### **小结**
- 要随时掌握工作区的状态，使用`git status`命令。

- 如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容。

---
### 版本回退
不断对文件进行修改，然后不断提交修改到版本库里，就好比玩RPG游戏时，每通过一关就会自动把游戏状态存盘，如果某一关没过去，你还可以选择读取前一关的状态。有些时候，在打Boss之前，你会手动存盘，以便万一打Boss失败了，可以从最近的地方重新开始。

Git也是一样，每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为commit。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个commit恢复，然后继续工作，而不是把几个月的工作成果全部丢失。

实际工作中，我们脑子里怎么可能记得一个几千行的文件每次都改了什么内容，不然要版本控制系统干什么。版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用`git log`命令查看：

```git
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800
    append GPL
commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800
    add distributed
commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800
    wrote a readme file
```

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数：
```git
$ git log --pretty=oneline
1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master) append GPL
e475afc93c209a690c39c13a46716e8fa000c366 add distributed
eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0 wrote a readme file
```

Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交1094adb...（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个^比较容易数不过来，所以写成`HEAD~100`。

现在，我们要把当前版本append GPL回退到上一个版本add distributed，就可以使用`git reset`命令：

```git
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
```

现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的commit id怎么办？

在Git中，总是有**后悔药**可以吃的。
>当你用`$ git reset --hard HEAD^`回退到add distributed版本时，再想恢复到append GPL，就必须找到append GPL的commit id。

Git提供了一个命令`git reflog`用来记录你的每一次命令：
```git
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```
终于舒了口气，从输出可知，append GPL的commit id是1094adb，现在，你又可以乘坐时光机回到未来了。
#### **小结**


- `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。

- 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。

- 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

---
# **day02 2019/07/31**
### 工作区和暂存区
- **工作区**（Working Directory）:在电脑里能看到的目录,用于存放代码的文件夹
- **版本库**（Repository）:工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。
>Git的版本库里存了很多东西，其中最重要的就是称为**stage**（或者叫**index**）的**暂存区**，还有Git为我们自动创建的第一个分支**master**，以及指向**master**的一个指针叫`HEAD`。

![工作区/缓存区/版本库关系图](https://upload-images.jianshu.io/upload_images/29581-3e5c9652233150b7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

把文件往Git版本库里添加的时候，是分两步执行的：
1. 用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；
2. 第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。
创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。
>可以简单理解为，需要提交的文件修改通通放到**暂存区**，然后，一次性提交暂存区的所有修改。每次修改，如果不用`git add`到**暂存区**，那就不会加入到`commit`中.

---

### 撤销修改

```git
$ git checkout -- readme.txt
```
命令`git checkout -- readme.txt`意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
- 一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
- 一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

命令`git reset HEAD <file>`可以把**暂存区**的修改撤销掉（unstage），重新放回**工作区**：
```git
$ git reset HEAD readme.txt
Unstaged changes after reset:
M	readme.txt
```
`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。
#### 小结

- 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。
- 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
- 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[day01中的版本回退]([https://www.jianshu.com/p/27f47a0ee348](https://www.jianshu.com/p/27f47a0ee348)
)一节，**不过前提是没有推送到远程库**。

---
### 删除文件

从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`：
```git
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master d46f35e] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```
现在，文件就从版本库中被删除了。

如果后面发现删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
```git
$ git checkout -- test.txt
```
`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“**一键还原**”。

#### 小结

命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容.

---
# **day03 2019/08/01**

### 远程仓库
#### 创建SSH Key

>创建**SSH Key**。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开`Shell`（Windows下打开`Git Bash`），创建SSH Key：

```git
$ ssh-keygen -t rsa -C "youremail@example.com"
```

>你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。
如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，**id_rsa.pub是公钥**，可以放心地告诉任何人。

---

### 创建远程库

1. 登陆GitHub，然后，在右上角找到**“Create a new repo”**按钮，创建一个新的仓库：
![创建新的仓库](https://upload-images.jianshu.io/upload_images/29581-b8faa661f0a6177d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 在Repository name填入`learngit`，其他保持默认设置，点击“**Create repository**”按钮，就成功地创建了一个新的Git仓库：
![创建新的git仓库](https://upload-images.jianshu.io/upload_images/29581-64a1b41608179844?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 根据`GitHub`的提示，在本地的learngit仓库下运行命令：
```git
$ git remote add origin git@github.com:you_name/learngit.git
```
请千万注意，把上面的`michaelliao`替换成你自己的GitHub账户名，否则，你在本地关联的就是我的远程库，关联没有问题，但是你以后推送是推不上去的，因为你的SSH Key公钥不在我的账户列表中。

4. 添加后，远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。下一步，就可以把本地库的所有内容推送到远程库上：
```git
$ git push -u origin master
Counting objects: 20, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (20/20), 1.64 KiB | 560.00 KiB/s, done.
Total 20 (delta 5), reused 0 (delta 0)
remote: Resolving deltas: 100% (5/5), done.
To github.com:michaelliao/learngit.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，`Git`不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。
5. 只要本地作了提交，就可以通过命令：
```git
$ git push origin master
```
把本地`master`分支的最新修改推送至`GitHub`，现在，你就拥有了真正的分布式版本库！

#### 小结
- 要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；

- 关联后，使用命令`git push -u origin master`第一次推送`master`分支的所有内容；

- 此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；

>分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！

---

### 克隆仓库
用命令`git clone`克隆一个本地库：
```git
$ git clone git@github.com:you_name/gitskills.git
Cloning into 'gitskills'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 3
Receiving objects: 100% (3/3), done.
```
注意把Git库的地址换成你自己的，然后进入gitskills目录看看，已经有`README.md`文件了

#### 小结
- 要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

- Git支持多种协议，包括`https`，但通过`ssh`支持的原生`git`协议速度最快。

---
# **day04 2019/08/02**

### 创建分支

创建dev分支，然后切换到dev分支：
```git
$ git checkout -b dev
Switched to a new branch 'dev'
```
`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：
```git
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```
然后，用`git branch`命令查看当前分支：
```git
$ git branch
* dev
  master
```
`git branch`命令会列出所有分支，当前分支前面会标一个*号。

然后，我们就可以在dev分支上正常提交，比如对`readme.txt`做个修改，加上一行：
>Creating a new branch is quick.

然后提交：
```git
$ git add readme.txt
$ git commit -m "branch test"
[dev b17d20e] branch test
 1 file changed, 1 insertion(+)
```
现在，`dev`分支的工作完成，我们就可以切换回`master`分支：
```git
$ git checkout master
Switched to branch 'master'
```
切换回 `master`分支后，再查看一个`readme.txt`文件，刚才添加的内容不见了！因为那个提交是在`dev`分支上，而`master`分支此刻的提交点并没有变：

![此时的分支状态](https://upload-images.jianshu.io/upload_images/29581-68f285076d3f5aea?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

### 合并分支

把`dev`分支的工作成果合并到`master`分支上：
```git
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```
`git merge`命令用于合并指定分支到当前分支。合并后，再查看`readme.txt`的内容，就可以看到，和`dev`分支的最新提交是完全一样的。

注意到上面的**Fast-forward**信息，Git告诉我们，这次合并是“**快进模式**”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。

当然，也不是每次合并都能**Fast-forward**，我们后面会讲其他方式的合并。

合并完成后，就可以放心地删除`dev`分支了：
```git
$ git branch -d dev
Deleted branch dev (was b17d20e).
```
删除后，查看`branch`，就只剩下`master`分支了：
```git
$ git branch
* master
```
因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。

#### 小结

Git鼓励大量使用分支：

- 查看分支：`git branch`

- 创建分支：`git branch <name>`

- 切换分支：`git checkout <name>`

- 创建+切换分支：`git checkout -b <name>`

- 合并某分支到当前分支：`git merge <name>`

- 删除分支：`git branch -d <name>`

---

### 解决冲突

Git告诉我们，`XXX`文件存在冲突，必须手动解决冲突后再提交。`git status`也可以告诉我们冲突的文件：
```git
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
我们可以直接查看`readme.txt`的内容：
```git
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```
Git用`<<<<<<<，=======，>>>>>>>`标记出不同分支的内容，我们修改如下后保存：

> Creating a new branch is quick and simple.

再提交：
```git
$ git add readme.txt
$ git commit -m "conflict fixed"
[master cf810e4] conflict fixed
```
现在，`master`分支和`feature1`分支变成了下图所示：

![此刻的分支结构状态](https://upload-images.jianshu.io/upload_images/29581-64ef8383ce9b76a5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

用带参数的`git log`也可以看到分支的合并情况：
```git
$ git log --graph --pretty=oneline --abbrev-commit
*   cf810e4 (HEAD -> master) conflict fixed
|\  
| * 14096d0 (feature1) AND simple
* | 5dc6824 & simple
|/  
* b17d20e branch test
* d46f35e (origin/master) remove test.txt
* b84166e add test.txt
* 519219b git tracks changes
* e43a48b understand how stage works
* 1094adb append GPL
* e475afc add distributed
* eaadf4e wrote a readme file
```
最后，删除feature1分支：
```git
$ git branch -d feature1
Deleted branch feature1 (was 14096d0).
```
#### 小结
当Git无法自动合并分支时，就必须首先**解决冲突。解决冲突后，再提交，合并完成**。

**解决冲突**就是**把Git合并失败的文件手动编辑为我们希望的内容**，再**提交**。

用`git log --graph`命令可以看到分支合并图。

---

# **day05 2019/08/03**

### 分支管理策略
合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在`merge`时生成一个新的`commit`，这样，从分支历史上就可以看出分支信息。

下面我们实战一下`--no-ff`方式的`git merge`：

首先，仍然创建并切换dev分支：
```git
$ git checkout -b dev
Switched to a new branch 'dev'
```
修改`readme.txt`文件，并提交一个新的`commit`：
```git
$ git add readme.txt
$ git commit -m "add merge"
[dev f52c633] add merge
 1 file changed, 1 insertion(+)
 ```
现在，我们切换回`master`：
```git
$ git checkout master
Switched to branch 'master'
```
准备合并dev分支，请注意`--no-ff`参数，表示禁用`Fast forward`：
```git
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
 ```
因为本次合并要创建一个新的`commit`，所以加上`-m`参数，把`commit`描述写进去。

合并后，我们用`git log`看看分支历史：
```git
$ git log --graph --pretty=oneline --abbrev-commit
*   e1e9c68 (HEAD -> master) merge with no-ff
|\  
| * f52c633 (dev) add merge
|/  
*   cf810e4 conflict fixed
...
```
可以看到，不使用`Fast forward`模式，`merge`后就像这样：

![分支结构图](https://www.liaoxuefeng.com/files/attachments/919023225142304/0)

- **团队合作的分支策略**

在实际开发中，我们应该按照几个基本原则进行分支管理：

1. `master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

2. 那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

3. 你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

所以，团队合作的分支看起来就像这样：
![团队合作开发分支结构图](https://www.liaoxuefeng.com/files/attachments/919023260793600/0)

#### 小结
*Git分支十分强大，在团队开发中应该充分应用。*

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而f`ast forward`合并就看不出来曾经做过合并。

---
### Bug分支

当你接到一个修复一个代号`101的bug`的任务时，很自然地，你想创建一个分支`issue-101`来修复它，但是，等等，当前正在dev上进行的工作还没有提交：
```git
$ git status
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt
```
并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该bug，怎么办？

幸好，Git还提供了一个`stash`功能，可以把当前工作现场“**`储藏`**”起来，等以后恢复现场后继续工作：
```git
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```
现在，用`git status`查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支：
```git
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git checkout -b issue-101
Switched to a new branch 'issue-101'
```
现在修复bug，需要把`“Git is free software ...”`改为`“Git is a free software ...”`，然后提交：
```git
$ git add readme.txt
$ git commit -m "fix bug 101"
[issue-101 4c805e2] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
 ```
修复完成后，切换到master分支，并完成合并，最后删除`issue-101`分支：
```git
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merged bug fix 101" issue-101
Merge made by the 'recursive' strategy.
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
 ```
太棒了，原计划两个小时的bug修复只花了5分钟！现在，是时候接着回到dev分支干活了！
```git
$ git checkout dev
Switched to branch 'dev'

$ git status
On branch dev
nothing to commit, working tree clean
```
工作区是干净的，刚才的工作现场存到哪去了？用`git stash list`命令看看：
```git
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
```
工作现场还在，Git把`stash`内容存在某个地方了，但是需要恢复一下，有两个办法：

1. 用`git stash apply`恢复，但是恢复后，`stash`内容并不删除，你需要用`git stash drop`来删除；

2. 用`git stash pop`，恢复的同时把`stash`内容也删了：
```git
$ git stash pop
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

Dropped refs/stash@{0} (5d677e2ee266f39ea296182fb2354265b91b3b2a)
```
再用`git stash list`查看，就看不到任何`stash`内容了：
```git
$ git stash list
```
你可以多次`stash`，恢复的时候，先用`git stash list`查看，然后恢复指定的`stash`，用命令：
```git
$ git stash apply stash@{0}
```

#### 小结
1. 修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

2. 当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场。

---

### 删除空文件夹/空目录

1. 使用`git rm -rf dir`命令删除文件夹或者空目录之后，本地还是会有空的目录存在，这时候空目录已经是*untracked*状态；
2. 使用使用`git clean -fd`命令删除untracked状态的空目录，再进行commit、push即可。