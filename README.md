# gitlearn
learn git
---
# day01 2019/07/30

## 创建版本库
- 在工作目录下，初始化一个Git仓库，使用`git init`命令。

- 添加文件到Git仓库，分两步：

1.使用命令`git add` <file>，注意，可反复多次使用，添加多个文件；

2.使用命令`git commit -m <message>`，完成。

> git commit命令，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

## 时光机穿梭
- 要随时掌握工作区的状态，使用`git status`命令
``` git
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```


