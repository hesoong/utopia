---
title: git使用
date: 2019-09-03
updated: 2019-11-15
toc: true
tags: 
- git
- 笔记
---
## git 常用操作
---

### stash

```shell
git pull        #down代码
git stash       #备份
git stash save [<message>] #备份及添加备份信息
git stash pop   #还原代码
```

### branch

```shell
git checkout -b abc         #git创建分支abc
	git checkout master     #切换到master分支
git branch -d 'branchName'  #删除本地分支
git status                  #查看分支中的代码目录
git add TowerLampDll.dll    #添加待提交代码TowerLampDll.dll到git
git status                  #查看提交的代码；（绿色表示准备提交的，红色表示可选择提交的）
git reset HEAD <file>       #恢复已add的文件为未提交的状态
git pull <origin> <activemq_local> #git更新远程分支代码到本地,<origin>:远程库名 <activemq_local>：待更新远程分支名
git branch -a | grep -v -E 'master|remotes'|xargs git branch -d #批量删除本地分支
    grep: 
        -v 取反（not）
        -E 用分隔符连接(or)
git remote show origin  #  查看远程库的一些信息，及与本地分支的信息
git remote prune origin #  删除本地库中这些相比较远程库中已经不存在的分支
```

### commit

```shell
git status #查看更改的文件，红色表示有改动，绿色表示准备提交到本地仓库中文文件
git diff # 用于比较两次修改的差异
git commit	#进入vi编辑器编辑注释， 编辑好后用:wq命令提交
git push -u origin abc # push本地仓库代码到远程'abc'分支
git log #可查看git提交版本编号
#然后在浏览器中进入git lab 提交merge请求
git config commit.template myTemplate.template	#运行模板
# 修改提交信息
git commit --amend # 进入编辑器修改
git commit --amend 'update information' # 直接更改为新输入的提交信息
```
### restore
```sh
git restore # git restore命令是撤销的意思，也就是把文件从缓存区撤销，回到未被追踪的状态。
```
### revert

```shell
git reset HEAD # 已经add 将文件回滚到上次add的状态
git checkout -- 'fileName'#文件还未add到缓存队列
git checkout . # 回滚所有修改的文件
git checkout . && git clean -df # 回滚所有修改、删除、新增的文件
```

### cherry-pick

```shell
git cherry-pick <commit No.>    # commit编号
git cherry-pick No1..No2        # N1,N2两次commit编号
```

### rebase

```shell
git checkout -b testbranch  # 切换到待rebase的分支（假设该分支已推到远程）
git rebase master
    git rebase --continue   # 解决冲突后继续rebase
    git rebase --skip       # 跳过
    git rebase --abort      # 返回rebase前的状态
git push --force-with-lease origin feature # 将rebase后的代码同步到远程分支
```

### merge

```shell
git pull #更新代码
git checkout -b 'newBranchName#新建一个本地分支
git merge 'target' #merge target分支，target待合并的分支，git会自动merge部分代码，未能merge的需要手动merge
git status #查看merge后的状态

for example：
bms@bms MINGW64 /c/test (master)
$ git pull
Already up-to-date.

bms@bms MINGW64 /c/test (master)
$ git checkout -b activemq_local
Switched to a new branch 'activemq_local'

bms@bms MINGW64 /c/test (activemq_local)
$ git merge activemq
Auto-merging test-commons/src/main/java/com/song/commons/model/NeClass.java
Auto-merging test-commons/src/main/java/com/song/commons/model/ITProtocol.java
Auto-merging test-collector/src/main/resources/connectors.xml
CONFLICT (content): Merge conflict in test-collector/src/main/resources/connectors.xml
Automatic merge failed; fix conflicts and then commit the result.

bms@bms MINGW64 /c/test (activemq_local|MERGING)
$ git status
On branch activemq_local
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Changes to be committed:

modified:   connectors/test-buildin-connectors/build.gradle
new file:   connectors/test-buildin-connectors/src/main/java/com/song/test/collector/connections/ActiveMQConnection.java
new file:   test-collector-data/src/main/java/com/song/test/networkentity/target/ActiveMQTarget.java
new file:   test-collector/src/main/resources/config/indicators/indicator.activemq.xml
new file:   test-collector/src/main/resources/config/query-templates/web-server/query.activemq.xml
modified:   test-commons/src/main/java/com/song/commons/model/ITProtocol.java
modified:   test-commons/src/main/java/com/song/commons/model/NeClass.java

Unmerged paths:
  (use "git add <file>..." to mark resolution)

both modified:   test-collector/src/main/resources/connectors.xml

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
  
# 此时在ide中将both modified (冲突文件)解决
# 然后git add . 
# git merge --continue
.
.
.
```

## git删除用户名密码

```yaml
# tortoriseGit --> settings --> GIt--> Edit systemwide gitconfig  打开文件
                                            
# 或者 直接打开文件：GIT_HOME/mingw64/etc/gitconfig 删除其中credential一项                            
[http]
	sslCAInfo = D:/Program Files/Git/mingw64/ssl/certs/ca-bundle.crt
	sslBackend = openssl
[diff "astextplain"]
	textconv = astextplain
[filter "lfs"]
	clean = git-lfs clean -- %f
	smudge = git-lfs smudge -- %f
	process = git-lfs filter-process
	required = true
[credential]
	helper = manager
```
## git status 不能显示中文

问题描述：
```shell
> git status
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        "content/posts/Windows\347\254\224\350\256\260.md"
        "content/posts/gradle\347\254\224\350\256\260.md"
```
* 原因： 默认情况下，中文文件名在工作区状态输出，中文不能正确显示，而是显示为八进制的字符编码
* 解决办法： 将git配置文件`core.quotepath`项设置未false。 `quotepath`表示引用路径，加上`--global` 表示全局
```sh
git config --global core.quotepath false
```
## git 不能使用正则（仅适用于终端使用了zsh的情况）
问题描述：
```shell
# 使用正则匹配不能添加文件
> git add *.java
```
解决方法：在`.zshrc`文件中添加`setout nonomatch`即可
```shell
echo "setout nonomatch" >> ~/.zshrc
source ~/.zshrc
```

## git 修改远程Repo Url
```shell
# 方式1 -- 直接设置 适用于新的repo 之前没有设置过url
git remote add origin 
# 方式2 -- 先删除 在设置
git remote rm origin 
git remote add origin [url]
```