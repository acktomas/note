
## 一、初始化
- git init
- git status: 查看状态
- git add file：把文件放到暂存区
- git commit -m "comments"：把暂存区的修改提交到分支
- git diff file:查看文件的修改内容
- git log: 查看历史记录
：--pretty=oneline: 简化输出信息
- git log --graph --pretty=oneline --abbrev-commit: 查看分支的合并情况
- git reset --hard HEAD^: 回退到上一个版本
- git reset --hard 版本号
- git reset HEAD <file>可以把暂存区的修改撤销掉（unstage），重新放回工作区：
- git reflog :显示历史命令
- git checkout -- file: 放弃工作区的修改。
一种是文件自修改后还没有被放到暂存区，撤销修改就回到和版本库一模一样的状态；
一种是已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
：-- 很重要，没有--，就变成了“切换到另一个分支”的命令。
- git checkout -b dev: -b参数表示创建并切换到分支dev，相当于以下命令
```git
git branch dev
git checkout dev
```
- git branch: 无分支名参数时，会列出所有分支，当前分支前面标一个*号，有分支名参数就切换到分支。
- git branch -d branchName: 删除分支branchName.
- git branch -D name:强行删除分支
- git merge dev: 合并dev分支到当前分支。

### 删除文件
    git add test.txt
    git commit -m "add test.txt"
    rm test.txt
    git rm test.txt   从版本库中删除该文件
    git commit -m "remove test.txt"
    git checkout -- test.txt 从版本库中恢复到工作区。
## 小结
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。
## 远程仓库
1. 创建SSH Key
在windows用户目录下，生成.ssh目录，生成id_rsa（私钥）和id_rsa.pub（公钥）文件
2. 登陆GitHub，“Add SSH Key",填上任意Title，在key文本框里粘贴id_rsa.pub文件的内容。
3. Git支持SSH协议。
### 添加远程仓库
让远程与本地仓库进行远程同步，GitHub上的仓库既可以作为备份，也可以让其他人通过该仓库来协作
1. “Create a new repo"创建一个新的仓库（比如：note）
2. 远程与本地仓库关联
```git
git remote add origin git@github.com:acktomas/note.git
```
origin 是远程库的名称，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库
3. 把本地库的所有内容推送到远程库上：
```git
git push -u origin master
```
把当前分支master推送到远程，由于远程库是空的，第一次推送<code>master</code>分支时，加上<code>-u</code>参数，git不但会把本地的master分支内容推送到远程新的master分支，还会把本地的master分支和远程的master分支关联起来，以后的推送或者拉取时就可以简化命令。
4. 以后把本地master分支的最新修改推送到GitHub
```git
git push origin master
```
#### ssh警告
当你第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告：
```
The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
RSA key fingerprint is xx.xx.xx.xx.xx.
Are you sure you want to continue connecting (yes/no)?
```
这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。

Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：

Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
这个警告只会出现一次，后面的操作就不会有任何警告了。

如果你实在担心有人冒充GitHub服务器，输入yes前可以对照GitHub的RSA Key的指纹信息是否与SSH连接给出的一致。

#### 小结
要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；

关联后，使用命令git push -u origin master第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；
### 从远程库克隆
git clone git@github.com:acktomas/notes.git
## 分支管理
### bug分支
1. git stash：把当前工作现场“储藏”起来，等以后恢复现场后继续工作。
2. 确定要在哪个分支上修复bug，假定需要在master上
    git checkout master
3. git checkout -b issue-101
4. 修复
git add read.py
git commit -m "fix bug 101"
5. git checkout master
6. git merge --no-ff -m "merged bug fix 101" issue-101
7. git checkout dev
8. git stash list
9. git stash 内容恢复： git stash apply或git stash pop
10. git stash pop
11. git stash list
12. git stash apply stash@{0}
#### 推送分支
推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

$ git push origin master
如果要推送其他分支，比如dev，就改成：

$ git push origin dev
但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

- master分支是主分支，因此要时刻与远程同步；

- dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；

- bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；

- feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，视你的心情而定！
## 标签管理
### 创建标签
git tag V1.0
#### 查找历史提交的commit id
git log --pretty=oneline --abbrev-commit  :
#### 给某个commit id 打标签
git tag v0.9 f52c633
#### 查看标签信息
git show <tagname>
#### 还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
####  注意：
标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。
- 命令git push origin <tagname>可以推送一个本地标签；
- 命令git push origin --tags可以推送全部未推送过的本地标签；
- 命令git tag -d <tagname>可以删除一个本地标签；
- 命令git push origin :refs/tags/<tagname>可以删除一个远程标签。