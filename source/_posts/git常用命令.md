---
title: git常用命令
date: 2018-03-05 20:37:16
type: tags
tags: 
  - git
categories:
  - git
toc: true
comments: true
---
## 配置
### 查看已经存在的配置
```
git config --list
```
### 配置用户名和邮箱
```
git config --global user.name "Tocy"
git config --global user.email "zyvj@qq.com"
```
### 让git显示颜色
```
$ git config --global color.ui true
```
<!--more-->
## 初始化
### 服务器端
```
git --bare init

在初始化远程仓库时最好使用 git --bare init   而不要使用：git init,如果使用了git init初始化，则远程仓库的目录下，也包含work tree，当本地仓库向远程仓库push时,   如果远程仓库正在push的分支上（如果当时不在push的分支，就没有问题）,
那么push后的结果不会反应在work tree上,  也即在远程仓库的目录下对应的文件还是之前的内容，必须得使用git reset --hard才能看到push后的内容.
```
```
//切换仓库权限
chown -R git:git test.git
```
### 实际的工程目录
```
git init
```

## 提交更新
### 添加文件
```
git add <file>

//可以添加多个文件或目录
```
### 提交
```
git commit -m "相关信息"

//将add之后的文件提交到版本库
//不add的文件修改，直接提交是不会提交的
```
### 查看状态
```
git status
```
### 比较文件
```
git diff <file>
```
## 远程
### 关联远程库（先有本地，后有远程库）
```
git remote add origin git@server-name:path/repo-name.git
```
### 第一次推送
```
git push -u origin master
```
### 推送
```
git push origin master
```
### 从远程库克隆（先创建远程库，后克隆到本地）
```
git clone git@github.com:michaelliao/gitskills.git
//或使用http协议
git clone https://github.com/michaelliao/gitskills.git
```
### 多人协作（包括分支）
1. 查看远程库信息，使用git remote -v；
2. 本地新建的分支如果不推送到远程，对其他人就是不可见的；
3. 从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
4. 在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
5. 建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
6. 从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。
## 分支
### 查看分支
```
git branch

//列出所有分支，带*表示当前分支
```
### 创建分支
```
git branch <name>
```
### 切换分支
```
git checkout <name>
```
### 创建并切换分支
```
git checkout -b <name>
```
### 合并分支（合并某分支到当前分支）
```
git merge <name>
```
### 删除分支
```
git branch -d <name>

//强行删除分支（分支没有合并）
git branch -D <name>
```
### 分支策略
禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
```
git merge --no-ff -m "merge with no-ff" dev
```
### bug分支
- 保存工作现场
```
git stash
```
- 查看工作现场
```
git stash list
```
- 恢复工作现场
```
//先恢复，再删除
git stash apply
git stash drop

//恢复并删除
git stash pop
```
- 恢复指定的工作现场
```
git stash apply stash@{0}
//或者
git stash pop stash@{0}
```

## 回退撤销
### 查看历史
```
git log
commit d131f99ca0fd1314a90cdbdf47becd040ae87701 (HEAD -> master, origin/master)
Author: zhuliangxuan <zhuliangxuan@zstax.com>
Date:   Fri Jan 19 15:54:47 2018 +0800

    add controller test

commit 5e466e10a6fbdd3e98d1552565fb58cb0406de5a
Author: zlxubuntu <zlxubuntu@qq.com>
Date:   Fri Jan 19 15:34:43 2018 +0800

    demo springboot

commit deb24037df78ae2a84edd335dac1b11e651f7c61
Author: zlxubuntu <zlxubuntu@qq.com>
Date:   Fri Jan 19 15:25:59 2018 +0800

    add a jar

commit 32055fb26951d00a689c0d4cead2db652fee0086
Author: zhuliangxuan <zhuliangxuan@zstax.com>
Date:   Fri Jan 19 15:14:53 2018 +0800

    test commit


git log --pretty=oneline
d131f99ca0fd1314a90cdbdf47becd040ae87701 (HEAD -> master, origin/master) add controller test
5e466e10a6fbdd3e98d1552565fb58cb0406de5a demo springboot
deb24037df78ae2a84edd335dac1b11e651f7c61 add a jar
32055fb26951d00a689c0d4cead2db652fee0086 test commit

```
### 回退版本
```
//回退上一个版本
git reset --hard HEAD^

//回退上100个版本
git reset --hard HEAD~100

//回退到指定版本
git reset --hard <版本号>

版本号没必要写全，前几位就可以了，Git会自动去找
```
### 查看命令历史
```
git reflog

d131f99 (HEAD -> master, origin/master) HEAD@{0}: reset: moving to d131f
5e466e1 HEAD@{1}: reset: moving to HEAD^
d131f99 (HEAD -> master, origin/master) HEAD@{2}: commit: add controller test
5e466e1 HEAD@{3}: pull origin: Fast-forward
deb2403 HEAD@{4}: pull: Fast-forward
32055fb HEAD@{5}: commit (initial): test commit
查看命令历史，以便确定要回到未来的哪个版本
```
### 撤销工作区的修改
```
git checkout -- readme.txt

命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
总之，就是让这个文件回到最近一次git commit或git add时的状态。
```
### 撤销暂存区的修改
```
git reset HEAD <file>

//回到4的状态
git checkout -- <file>
放弃工作区的更改
```
### 删除文件
#### 正常删除
文件提交到git之后，先从磁盘删除，再从版本库删除
```
rm 2.text
git rm 2.text
git commit -m "delete file"
```
#### 误删除
文件提交到git之后，从磁盘删除，需要恢复磁盘删除的文件
```
rm 2.text
git checkout -- 2.text
```
## github
### 设置ssh key
- 1.在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：
```
$ ssh-keygen -t rsa -C "youremail@example.com"
```
你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。
如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
- 2.登陆GitHub，打开“Account settings”，“SSH Keys”页面,点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容
- 3.GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。
## 解决冲突
### 冲突的发生
```
分支1对a文件进行了修改并提交
主分支对a文件在同一处可能冲突的地方进行了修改
将分支1合并到主分支时，冲突发生
```
### 冲突的解决
```
编辑 a
git add a
git commit -m "解决冲突"
git branch -d 分支1
```
### 查看分支合并图
```
git log --graph --pretty=oneline --abbrev-commit
```
## 标签管理
Git的标签是版本库的快照，其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。
### 创建标签
```
//切换到分支，创建标签
git tag <name>

//指定标签信息
git tag -a <name> -m "message"

//用PGP签名标签
git tag -s <name> -m "message"
```
### 查看标签
```
//列出所有标签
git tag

//查看某个标签
git show <name>
```
### 使用标签
- 命令git push origin <tagname>可以推送一个本地标签；

- 命令git push origin --tags可以推送全部未推送过的本地标签；

- 命令git tag -d <tagname>可以删除一个本地标签；

- 命令git push origin :refs/tags/<tagname>可以删除一个远程标签。