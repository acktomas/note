
## 一、初始化
- git init
- git status: 查看状态
- git add file：把文件放到暂存区
- git commit -m "comments"：把暂存区的修改提交到分支
- git diff file:查看文件的修改内容
- git log: 查看历史记录
：--pretty=oneline: 简化输出信息
- git reset --hard HEAD^: 回退到上一个版本
- git reset --hard 版本号
- git reset HEAD <file>可以把暂存区的修改撤销掉（unstage），重新放回工作区：
- git reflog :显示历史命令
- git checkout -- file: 放弃工作区的修改。
一种是文件自修改后还没有被放到暂存区，撤销修改就回到和版本库一模一样的状态；
一种是已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
：-- 很重要，没有--，就变成了“切换到另一个分支”的命令。
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
