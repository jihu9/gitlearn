# 2019/08/04

### Feature分支

- 开发一个新feature，最好新建一个分支；
```git
$ git checkout -b feature-vulcan
Switched to a new branch 'feature-vulcan'
```
- 如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

### 多人协作
当你从远程仓库克隆时，实际上Git自动把本地的`master`分支和远程的`master`分支对应起来了，并且，远程仓库的默认名称是o`rigin`。

要查看远程库的信息，用`git remote`：
```git
$ git remote
origin
```
或者，用`git remote -v`显示更详细的信息：
```git
$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```
上面显示了可以抓取和推送的`origin`的地址。如果没有推送权限，就看不到`push`的地址。

### 推送分支
推送分支，*就是把该分支上的所有本地提交推送到远程库*。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：
```git
$ git push origin master
```
如果要推送其他分支，比如`dev`，就改成：
```git
$ git push origin dev
```
但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

- `master`分支是主分支，因此要时刻与远程同步；

- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；

- `bug`分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；

- `feature`分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

>总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，视你的心情而定！

### 抓取分支
多人协作时，大家都会往master和dev分支上推送各自的修改。

现在，模拟一个你的小伙伴，可以在另一台电脑（注意要把SSH Key添加到GitHub）或者同一台电脑的另一个目录下克隆：
```git
$ git clone git@github.com:michaelliao/learngit.git
Cloning into 'learngit'...
remote: Counting objects: 40, done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 40 (delta 14), reused 40 (delta 14), pack-reused 0
Receiving objects: 100% (40/40), done.
Resolving deltas: 100% (14/14), done.
```
当你的小伙伴从远程库`clone`时，默认情况下，你的小伙伴只能看到本地的master分支。不信可以用`git branch`命令看看：
```git
$ git branch
* master
```
现在，你的小伙伴要在dev分支上开发，就必须创建远程`origin`的dev分支到本地，于是他用这个命令创建本地dev分支：
```git
$ git checkout -b dev origin/dev
```
现在，他就可以在dev上继续修改，然后，时不时地把dev分支`push`到远程：
```git
$ git add env.txt

$ git commit -m "add env"
[dev 7a5e5dd] add env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt

$ git push origin dev
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 308 bytes | 308.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
   f52c633..7a5e5dd  dev -> dev
```

你的小伙伴已经向`origin/dev`分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送：
```git
$ cat env.txt
env

$ git add env.txt

$ git commit -m "add new env"
[dev 7bd91f1] add new env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt

$ git push origin dev
To github.com:michaelliao/learngit.git
 ! [rejected]        dev -> dev (non-fast-forward)
error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送：
```git
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> dev
```
`git pull`也失败了，原因是没有指定本地dev分支与远程`origin/dev`分支的链接，根据提示，设置`dev`和`origin/dev`的链接：
```git
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```
再`pull`：
```git
$ git pull
Auto-merging env.txt
CONFLICT (add/add): Merge conflict in env.txt
Automatic merge failed; fix conflicts and then commit the result.
```
这回`git pull`成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的解决冲突完全一样。解决后，提交，再`push`：
```gt
$ git commit -m "fix env conflict"
[dev 57c53ab] fix env conflict

$ git push origin dev
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 621 bytes | 621.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
   7a5e5dd..57c53ab  dev -> dev
   ```
因此，多人协作的工作模式通常是这样：

   - 首先，可以试图用`git push origin <branch-name>`推送自己的修改；

   - 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；

   - 如果合并有冲突，则解决冲突，并在本地提交；

   - 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

   - 如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to origin/<branch-name> <branch-name>`。

   >这就是多人协作的工作模式，一旦熟悉了，就非常简单。


   #### 小结
   - 查看远程库信息，使用`git remote -v`；

   - 本地新建的分支如果不推送到远程，对其他人就是不可见的；

   - 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；

   - 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；

   - 建立本地分支和远程分支的关联，使用`git branch --set-upstream origin/branch-name branch-name`；

   - 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。
---
### rebase
Git有一种称为`rebase`的操作，有人把它翻译成“**变基**”。
从实际问题出发，看看怎么把分叉的提交变成直线。

在和远程分支同步后，我们对`hello.py`这个文件做了两次提交。用`git log`命令看看：
```git
$ git log --graph --pretty=oneline --abbrev-commit
* 582d922 (HEAD -> master) add author
* 8875536 add comment
* d1be385 (origin/master) init hello
*   e5e69f1 Merge branch 'dev'
|\  
| *   57c53ab (origin/dev, dev) fix env conflict
| |\  
| | * 7a5e5dd add env
| * | 7bd91f1 add new env
...
```
注意到Git用(`HEAD -> master`)和(`origin/master`)标识出当前分支的`HEAD`和远程`origin`的位置分别是`582d922 add author`和`d1be385 init hello`，本地分支比远程分支快两个提交。

现在我们尝试推送本地分支：
```git
$ git push origin master
To github.com:michaelliao/learngit.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
很不幸，失败了，这说明有人先于我们推送了远程分支。按照经验，先`pull`一下：
```git
$ git pull
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:michaelliao/learngit
   d1be385..f005ed4  master     -> origin/master
 * [new tag]         v1.0       -> v1.0
Auto-merging hello.py
Merge made by the 'recursive' strategy.
 hello.py | 1 +
 1 file changed, 1 insertion(+)
 ```
再用`git status`看看状态：
```git
$ git status
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```
加上刚才合并的提交，现在我们本地分支比远程分支超前3个提交。

用`git log`看看：
```git
$ git log --graph --pretty=oneline --abbrev-commit
*   e0ea545 (HEAD -> master) Merge branch 'master' of github.com:michaelliao/learngit
|\  
| * f005ed4 (origin/master) set exit=1
* | 582d922 add author
* | 8875536 add comment
|/  
* d1be385 init hello
...
```
对强迫症童鞋来说，现在事情有点不对头，提交历史分叉了。如果现在把本地分支push到远程，有没有问题？
>
有！
什么问题？
不好看！
有没有解决方法？
有！
>

这个时候，`rebase`就派上了用场。我们输入命令`git rebase`试试：
```git
$ git rebase
First, rewinding head to replay your work on top of it...
Applying: add comment
Using index info to reconstruct a base tree...
M	hello.py
Falling back to patching base and 3-way merge...
Auto-merging hello.py
Applying: add author
Using index info to reconstruct a base tree...
M	hello.py
Falling back to patching base and 3-way merge...
Auto-merging hello.py
```
输出了一大堆操作，到底是啥效果？再用`git log`看看：
```git
$ git log --graph --pretty=oneline --abbrev-commit
* 7e61ed4 (HEAD -> master) add author
* 3611cfe add comment
* f005ed4 (origin/master) set exit=1
* d1be385 init hello
...
```
原本分叉的提交现在变成一条直线了！这种神奇的操作是怎么实现的？其实原理非常简单。我们注意观察，发现Git把我们本地的提交“挪动”了位置，放到了`f005ed4 (origin/master) set exit=1`之后，这样，整个提交历史就成了一条直线。`rebase`操作前后，最终的提交内容是一致的，但是，我们本地的`commit`修改内容已经变化了，它们的修改不再基于`d1be385 init hello`，而是基于`f005ed4 (origin/master) set exit=1`，但最后的提交`7e61ed4`内容是一致的。

这就是`rebase`操作的特点：把分叉的提交历史“**整理**”成一条直线，**看上去更直观**。缺点是**本地的分叉提交已经被修改过**了。

最后，通过`push`操作把本地分支推送到远程：
```git
Mac:~/learngit michael$ git push origin master
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 576 bytes | 576.00 KiB/s, done.
Total 6 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
To github.com:michaelliao/learngit.git
   f005ed4..7e61ed4  master -> master
   ```
再用`git log`看看效果：
```git
$ git log --graph --pretty=oneline --abbrev-commit
* 7e61ed4 (HEAD -> master, origin/master) add author
* 3611cfe add comment
* f005ed4 set exit=1
* d1be385 init hello
...
```
远程分支的提交历史也是一条直线。
####   小结
- `rebase`操作可以把本地未`push`的分叉提交历史整理成直线；

- `rebase`的目的是使得我们在**查看历史提交的变化时更容易**，因为分叉的提交需要三方对比。
