---
layout: post
title: Git详细解释
categories: [Git]
description: 处理使用Git时遇到的特殊问题时的常用命令
keywords: Git,Github
---

## Git仓库处理命令可视化说明

仓库处理常用命令
```sh
git clone       #克隆远程仓库到本地
git pull        #从远程拉取项目到本地仓库和工作台
git fetch       #从远程拉取项目到本地仓库
git push        #将本地仓库上传至远程
git commit      #将暂存区更改提交到仓库
git checkout    #将仓库下载到工作台
git reset       #获取上一个Commit仓库
```

![一图看懂Git仓库处理命令](/img/assets/Git/onepic_git.png)


## 仓库分支操作、合并分支与处理冲突

多人协作时，需要每个人创建个人分支，完成工作后再合并至主分支，这样才能让协作过程有序而高效。

```sh
git branch    #获取分区列表(带*的为目前所在分支)

git branch <分支名称>    #添加分支

git checkout <分支名称>    #单纯切换到某个分支

git merge <分支名称>    #合并分支

git branch -D <分支名称>    #删除分支
```

## Git回滚与项目撤回

如果有时出于某些原因不得不将项目回滚或撤回，这也是git的核心能力。

每一次提交(Commit)都会被git获取到，通过`git checkout`回滚到本仓库的上一次Commit。

也可以用`git checkout <文件地址>`将单个文件回滚到上一次Commit。

但如果回滚到上一次Commit还不够，就需要`git reset HEAD^`将仓库恢复到再上一次Commit的状态，这时再`git checkout <文件地址>`就可以把文件回滚到更早的状态。

但要当心此时若`git checkout`可能会让整个项目回滚到更早的状态。

## 多设备协同时冲突问题解决方案

在从Github上拉取单个分支到多台设备上时，若项目发生了更改，其他设备需要用`git fetch`将仓库与云端同步才能进行Commit。

倘若一台设备上原本的项目的一个分支发生了更改没有同步，另一台设备在该分支同步了，这台设备就需要让自己的分支与云端先与合并再上传。
```sh
git merge origin/master
```

如果想舍弃该设备的分支，使用云端上的分支，就需要强行pull把分支下载下来。
```sh
git fetch --all
git reset --hard origin/master
git pull
```

本地项目回滚后，若其他成员上传后，本地再上传至云端可能会显示冲突。

可以用`git branch <分支名>`新建一个分支，Commit后用`git push -u origin <分支名>`上传，交由其他项目成员合并。

或者先`git merge origin/master`在本地合并后再`git push`。

如果你想把自己目前的本地项目原封不动的上传上去，就需要`git push -u origin <分支名> -f`强制上传。