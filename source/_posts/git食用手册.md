---
title: git食用手册
date: 2022-02-24 12:14:04
tags: 
- 前端 
- git
---
#### 前言
近期在跟小伙伴合作开发，期间频繁使用到了一些git命令以及学习到一些分支管理流程，记录一下。
<!--more-->

#### 基础命令
* `git init`
  初始化空的新git仓库。
* `git clone {url}`
  克隆仓库到本地。
* `git add`
  暂存改动,可以提供参数暂存某一文件的改动，也可提供参数`.`暂存所有改动。
* `git commit`
  提交改动，可以提供参数`--all`来一次性提交所有暂存的改动，可继续提供字符串作为改动的说明。
* `git push`
  将确认的改动推送到远端仓库对应分支。
* `git branch`
  * 默认是列出本地所有分支。
  * `git branch {name}`可在本地创建新分支。
  * `git branch --delete {name}`可在本地删除对应分支。
* `git checkout`
  * 默认是检查当前分支。
  * `git checkout {name}`切换到对应分支。
  * `git checkout -b {name}`切换到对应分支,当没有该分支时会自动创建一个新分支。
* `git switch {name}`
  * 切换到对应分支，如果不存在该分支会报错。
  * `git switch -c {name}`切换到对应分支,当没有该分支时会自动创建一个新分支。
* `git stash`
  会将所有的改动（暂存的和未暂存的）全部保存起来，然后将分支恢复到改动前的状态。
  * `git stash pop`会把保存的改动应用到当前分支。
  * `git stash drop`删除保存，一般是保存的改动以及没用时使用。
* `git log`
  会显示所有提交的信息（版本号、作者、时间、message），且能通过参数显示更加详细的信息。
* `git show`
  显示最近一次提交的信息及改动，能通过提供参数显示特定版本号的信息及改动，并且能通过提供参数进行一些数据统计。
* `git rest {version}`
  * 回退到对应版本,`git reset`的回退有三种模式：`mixed（默认）`,`soft`,`hard`
  * `git reset --mixed {version}`默认模式，会将`commit`信息和暂存区的信息回退，保留代码。
  * `git reset --soft {version}`只会将`commit`信息回退。
  * `git reset --hard {version}`会将`commit`信息、暂存区信息、代码全部回退。

#### 部分场景的工作流程
##### 多人合作开发分支管理
案例：远端仓库主分支为`master`，且已克隆到本地，下述操作均在本地仓库。
1. 首先`git pull`拉取远端仓库的最新版本。
2. `git checkout -b dev`在本地创建新分支`dev`并切换过去。
3. 在本地分支`dev`上进行改动。
4. `git add`、`git commit`、`git push`一条龙，此举会在远端仓库创建dev分支并将改动推送上去。
5. 打开仓库页面提交`Pull Request`，源分支为`dev`，目标分支为`master`。
6. `git branch --delete dev`删除本地`dev`分支。
7. 等待其他人的更新并重复步骤1。

##### 合作开发时忘了先`git pull`拉取最新版本代码
案例：情况与上面一致，但是在开发时忘了先拉取最新版本的代码,本地存在`master`、`dev`两个分支，且当前处在`dev`分支。
1. `git stash`保存所有改动,且分支恢复到改动之前，即与`master`一致的状态。
2. `git checkout master`切换到`master`分支。
3. `git branch --delete dev`删除`dev`分支。
4. `git pull`拉取`master`分支的最新改动。
5. `git stash pop`取出之前的所有改动。
6. 如果有冲突就解决冲突，解决冲突后`git add`合并冲突改动。
7. 然后就能继续创建本地新分支并进行开发的流程。
   
#### 后记
以上即是我在合作开发中学到的`git`的一些使用方法。当然`git`强大的功能还不止于此，但是那些操作略显复杂，还没有碰到具体场景的我理解起来可能会有误差，待以后碰到应用场景时再作记录。

#### 参考文献
[[Git]执行git stash pop时的冲突解决](https://blog.csdn.net/jy692405180/article/details/78520251)
[易百git教程](https://www.yiibai.com/git/git_stash.html)