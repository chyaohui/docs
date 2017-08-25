# GIT的常用操作
## 0、写在前面
作为一名开发者，熟悉使用 git 代码管理工具是一项必备的基本技能。git 相较 SVN 而言，其优点不言而喻。git 的功能非常强大，其包括的操作命令也非常的多，但是从实用性而言，很多命令可能我们一辈子也用不到，这里我只记录一下自己经常使用的 git 命令，熟练使用了这些命令，其实已经可以完全得心应手的使用 git 工具了。我所使用的开发环境是在 CentOS6.5 系统，下边的操作命令都是在 CentOS6.5 上进行实验通过的。

## 一、GIT的初始化及配置
### 1.git安装
- yum方式安装: 
```bash
$ yum install git -y
```
- apt方式安装: 
```bash
$ apt install git -y
```
- 源码方式安装: 
```bash
$ ./configure && make && make install
```

### 2.git全局配置
```bash
$ git config --global user.name "liwei0526vip"
$ git config --global user.email "liwei0526vip@163.com"
```
通过`git config`命令的`--global`参数设置了git的用户名和用户邮箱，默认情况下这台机器上所有的git仓库都会使用这个配置，当然也可以对某个仓库指定具体不同的用户名和用户邮箱

### 3.初始化仓库
仓库，英文名repository。可以简单理解成一个目录，这个目录里面的所有文件都可以被git管理起来，每个文件的修改、删除，git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以"还原"。
```bash
$ mkdir learngit
$ cd learngit
$ git init
Initialized empty Git repository in /root/learngit/.git/
$ ls -a
.  ..  .git
```
可以发现当前目录下多了一个`.git`的目录，这个目录是git来跟踪管理版本库的，千万不要手动修改这个目录里面的文件，否则就把git仓库给破坏了

### 4.关于文件变动跟踪
首先这里再明确一下，所有的版本控制系统，其实只能跟踪文本文件的改动，比如 TXT 文件，网页，所有的程序代码等等， Git 也不例外。版本控制系统可以告诉你每次的改动，比如在第5行加了一个单词 "Linux" ，在第8行删了一个单词"Windows"。而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，也就是只知道图片从100KB改成了120KB，但到底改了什么，版本控制系统不知道，也没法知道。

### 5.关于编码
因为文本是有编码的，如果没有历史遗留问题，强烈建议使用标准的`UTF-8`编码，所有语言使用同一种编码，既没有冲突，又被所有平台所支持。

## 二、代码提交
### 1.查看repo状态
```bash
$ git status
```

### 2.添加文件到缓存区
```bash
$ git add file1.txt
$ git add file2.txt
```

### 3.提交到版本库
```bash
$ git commit -m "message"    # 一定要写提交信息, 便于后续查看版本信息
```

## 三、管理修改
### 1.丢弃工作区的修改
```bash
$ git checkout -- file.txt
```
### 2.丢弃暂存区的修改
```bash
$ git reset HEAD file.txt    # 丢弃暂存区的修改, 完毕后工作区内容未撤销
```
### 3.撤销提交(版本回退)
```bash
$ git reset --hard HEAD^
```
### 4.管理修改的举例
- 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
- 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。
- 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

### 5.查看工作区与版本库的差异
```bash
$ git diff HEAD -- file.txt
```
### 6.查看提交版本日志
```bash
$ git log --pretty=oneline    # --pretty选项更清晰显示
```

## 四、远程库操作
### 1.克隆远程库
```bash
$ git clone http://gitlab.staff.sina.com.cn/liwei42/gitver
```
### 2.添加远程库(给本地库)
```bash
$ git remote add origin git@gitlab.staff.sina.com.cn:liwei42/gitver.git
```
### 3.推送远程库
```bash
$ git push -u origin master    # 第一次推送加'-u'选项, 关联本地与origin的master分支
```
### 4.拉取远程库
```bash
$ git pull <远程主机名> <远程分支名>:<本地分支名>
```
在默认模式下，取回远程主机某个分支的更新，再与本地的指定分支合并。git pull是git fetch后跟git merge的缩写。

比如，要取回origin主机的next分支，与本地的master分支合并，需要写成下面这样:
```bash
$ git pull origin next:master    # 如果要与当前分支合并, 则冒号后面的部分可以省略
```
相当于:
```bash
$ git fetch origin
$ git merge origin/next
```
手动建立追踪关系:
```bash
$ git branch --set-upstream master origin/next
```
上面命令指定master分支追踪origin/next分支。如果当前分支与远程分支存在追踪关系，git pull就可以省略远程分支名
```bash
$ git pull origin    # 如果当前分支只有一个追踪分支，连远程主机名都可以省略
```

## 五、分支管理
### 1.创建分支
```bash
$ git branch b1
```
### 2.切换分支
```bash
$ git checkout b1    # 切换到b1分支
$ git checkout -b b2 # 创建并切换到b2分支
```
### 3.合并分支
```bash
$ git merge dev           # 合并dev分支到当前分支
$ git merge dev master    # 合并dev分支到master分支(可能当前分支并没有在master上)
$ git --no-ff merge dev   # 合并时不使用ff方式, 这样会在合并分支上保留提交的版本信息
```

## 六、分支策略
### 1.主分支master
代码库应该有一个、且仅有一个主分支。所有提供给用户使用的正式版本，都在这个主分支上发布。
### 2.开发分支develop
主分支只用来分布重大版本，日常开发应该在另一条分支上完成。这个分支可以用来生成代码的最新隔夜版本（nightly）。如果想正式对外发布，就在Master分支上，对Develop分支进行"合并"
### 3.临时性分支
- 功能(feature)分支
- 预发布(release)分支
- 修补bug(fixbug)分支

### 4.功能分支
它是为了开发某种特定功能，从Develop分支上面分出来的。开发完成后，要再并入Develop。
### 5.预发布分支
它是指发布正式版本之前(即合并到Master分支之前)，我们可能需要有一个预发布的版本进行测试。预发布分支是从Develop分支上面分出来的，预发布结束以后，必须合并进Develop和Master分支。它的命名，可以采用release-*的形式。
### 6.修补bug分支
软件正式发布以后，难免会出现bug。这时就需要创建一个分支，进行bug修补。修补bug分支是从Master分支上面分出来的。修补结束以后，再合并进Master和Develop分支。它的命名，可以采用fixbug-*的形式



