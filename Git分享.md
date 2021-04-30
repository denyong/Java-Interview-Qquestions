# Git版本控制

​	

## 版本控制

**什么是版本控制？**

> 工程设计领域中使用版本控制管理工程蓝图的设计过程

**版本控制应具备的功能？**

> 1. 协同修改。多人并行不悖的修改服务器端的同一个文件
>
> 2. 版本管理。每一个版本不保存重复数据，以节约存储空间，提高运行效率
>
> 3. 权限控制。对团队中参与开发的人员进行权限控制
>
> 4. 历史记录。查看修改人、修改时间、修改内容、日志信息等
>
> 5. 分支管理。允许开发团队在工作过程中多条生产线同时推进任务，提高效率

**版本控制工具分类**

集中式版本控制工具： CVS、SVN、VSS……

![image-20210426111032608](C:\Users\dengyong\AppData\Roaming\Typora\typora-user-images\image-20210426111032608.png)

> 1. 开发者依赖中央服务器
> 2. 对网络的依赖性较强
> 3. 所有的人共享分支
> 4. 提交的文件会直接记录到中央版本库

分布式版本控制工具： Git、Mercurial、Bazaar……

![image-20210426111236588](C:\Users\dengyong\AppData\Roaming\Typora\typora-user-images\image-20210426111236588.png)

> 1. 没有中央服务器，开发人员都有本地仓库
> 2. 不依赖网络，没有网络时提交到本地的仓库
> 3. 开发这可以有自己独立的分支
> 4. 执行push操作才会到主要版本库

## Git简述

**什么是git?**

Git 像是把数据看作是对小型文件系统的一系列快照。 在 Git 中，每当你提交更新或保存项目状态时，它基本上就会对当时的全部文件创建一个快照并保存这个快照的索引。 为了效率，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 Git 对待数据更像是一个 **快照流**。

**几乎所有操作都是本地执行**

在 Git 中的绝大多数操作都只需要访问本地文件和资源，一般不需要来自网络上其它计算机的信息。 `

**Git 保证完整性**

Git 中所有的数据在存储前都计算校验和，然后以校验和来引用。 Git 用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）。 这是一个由 40 个十六进制字符（0-9 和 a-f）组成的字符串，基于 Git 中文件的内容或目录结构计算出来。 SHA-1 哈希看起来是这样：

```
24b9da6552252987aa493b52f8696cd6d3b00373
```

Git 中使用这种哈希值的情况很多，你将经常看到这种哈希值。 实际上，Git 数据库中保存的信息都是以文件内容的哈希值来索引，而不是文件名。

**Git 一般只添加数据**

几乎所有的 Git 操作都是往数据库中 **添加** 数据。 很难使用 Git 从数据库中删除数据，也就是说 Git 几乎不会执行任何可能导致文件不可恢复的操作。 一旦你提交快照到 Git 中， 就难以再丢失数据，特别是如果你定期的推送数据库到其它仓库的话。

**三种状态**

Git 有三种状态，你的文件可能处于其中之一： **已提交（committed）**、**已修改（modified）** 和 **已暂存（staged）**。

- 已修改表示修改了文件，但还没保存到数据库中。
- 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。
- 已提交表示数据已经安全地保存在本地数据库中。

这会让我们的 Git 项目拥有三个阶段：工作区、暂存区以及 Git 目录

## Git基本使用

### 获取与创建项目

**git init**

将一个目录转变成一个 Git 仓库，这样你就可以开始对它进行版本管理了

**git clone**

是一个封装了其他几个命令的命令。 它创建了一个新目录并切换到新的目录，然后 `git init` 来初始化一个空的 Git 仓库， 然后为你指定的 URL 添加一个（默认名称为 `origin` 的）远程仓库（`git remote add`），再针对远程仓库执行 `git fetch`，最后通过 `git checkout` 将远程仓库的最新提交检出到本地的工作目录。

### 快照基础

**git add**

将内容从工作目录添加到暂存区（或称为索引（index）区），以备下次提交。 当 `git commit` 命令执行时，默认情况下它只会检查暂存区域，因此 `git add` 是用来确定下一次提交时快照的样子的。 

命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

**git status**

显示工作区及暂存区域中不同状态的文件。 其中包含了已修改但未暂存，或已经暂存但没有提交的文件。 在一般的显示形式中，它会给你一些如何在这些暂存区之间移动文件的提示。

**git diff**

当需要查看任意两棵树的差异时，可以使用 `git diff` 命令。 此命令可以查看你工作环境与你的暂存区的差异（`git diff` 默认的做法），你暂存区域与你最后提交之间的差异（`git diff --staged`），或者比较两个提交记录的差异（`git diff master branchB`）。

**git difftool**

当你不想使用内置的 `git diff` 命令时。`git difftool` 可以用来简单地启动一个外部工具来为你展示两棵树之间的差异

**git commit**

`git commit` 命令将所有通过 `git add` 暂存的文件内容在数据库中创建一个持久的快照，然后将当前分支上的分支指针移到其之上。

**git reset**

`git reset` 命令主要用来根据你传递给动作的参数来执行撤销操作。 它可以移动 `HEAD` 指针并且可选的改变 `index` 或者暂存区，如果你使用 `--hard` 参数的话你甚至可以改变工作区。 如果错误地为这个命令附加后面的参数，你可能会丢失你的工作，所以在使用前你要确定你已经完全理解了它。

**git rm**

`git rm` 是 Git 用来从工作区，或者暂存区移除文件的命令。

**git mv**

`git mv` 命令是一个便利命令，用于移到一个文件并且在新文件上执行`git add`命令及在老文件上执行`git rm`命令。

**git clean**

`git clean` 是一个用来从工作区中移除不想要的文件的命令。 可以是编译的临时文件或者合并冲突的文件

### 分支与合并

**git branch**

`git branch` 命令实际上是某种程度上的分支管理工具。 它可以列出你所有的分支、创建新分支、删除分支及重命名分支。

**git checkout**

`git checkout` 命令用来切换分支，或者检出内容到工作目录。

**git merge**

`git merge` 工具用来合并一个或者多个分支到你已经检出的分支中。 然后它将当前分支指针移动到合并结果上。

**git log**

`git log` 命令用来展示一个项目的可达历史记录，从最近的提交快照起。 默认情况下，它只显示你当前所在分支的历史记录，但是可以显示不同的甚至多个头记录或分支以供遍历。 此命令通常也用来在提交记录级别显示两个或多个分支之间的差异。

**git stash**

`git stash` 命令用来临时地保存一些还没有提交的工作，以便在分支上不需要提交未完成工作就可以清理工作目录。

**git tag**

`git tag` 命令用来为代码历史记录中的某一个点指定一个永久的书签。 一般来说它用于发布相关事项

### 项目分享与更新

**git fetch**

`git fetch` 命令与一个远程的仓库交互，并且将远程仓库中有但是在当前仓库的没有的所有信息拉取下来然后存储在你本地数据库中。

**git pull**

`git pull` 命令基本上就是 `git fetch` 和 `git merge` 命令的组合体，Git 从你指定的远程仓库中抓取内容，然后尝试将其合并进你所在的分支中。

**git push**

`git push` 命令用来与另一个仓库通信，计算你本地数据库与远程仓库的差异，然后将差异推送到另一个仓库中。 它需要有另一个仓库的写权限，因此这通常是需要验证的。

**git remote**

`git remote` 命令是一个是你远程仓库记录的管理工具。 它允许你将一个长的 URL 保存成一个简写的句柄，例如 `origin` ，这样你就可以不用每次都输入他们了。 你可以有多个这样的句柄，`git remote` 可以用来添加，修改，及删除它们。

### 补丁

**git cherry-pick**

`git cherry-pick` 命令用来获得在单个提交中引入的变更，然后尝试将作为一个新的提交引入到你当前分支上。 从一个分支单独一个或者两个提交而不是合并整个分支的所有变更是非常有用的。

**git rebase**

`git rebase` 命令基本是是一个自动化的 `cherry-pick` 命令。 它计算出一系列的提交，然后再以它们在其他地方以同样的顺序一个一个的 `cherry-picks` 出它们。

**git revert**

`git revert` 命令本质上就是一个逆向的 `git cherry-pick` 操作。 它将你提交中的变更的以完全相反的方式的应用到一个新创建的提交中，本质上就是撤销或者倒转。

git init               初始化git仓库
git remote add 添加远程地址
git clone           克隆远程代码库
git add              将文件添加的git索引库
git commit        提交到本地仓库
git status           显示工作区文件状态
git fetch             获取远程版本(不与本地合并)
git blame file     追踪改变
git  citool           图形化提交
git rebase          分支合并 
git tag               标签管理
git restore path 放弃修改
git checkout-index 从暂存区拷贝文件至工作区
git pull                       获取远程代码                     
git push                     推送到远程代码库
git merge                  合并分支
git diff                       查看文件的变化
git stash                    将文件添加至暂存区
git unstash                将文件移除暂存区
git log                       查看提交历史
git show commitId   查看具体提交版本号的内容
git reset                    删除修改并回退HEAD
git revert                  回滚修改不回退HEAD
git cherry-pick         合并指定的提交版本号
git branch                查看分支
git checkout (-b)      创建并切换至该分支

## Git文件管理机制

**.git文件夹解释**

![image-20210427163816887](C:\Users\dengyong\AppData\Roaming\Typora\typora-user-images\image-20210427163816887.png)

hooks：                         存放一些shell脚本
Info\exclude：             存放仓库的一些信息
logs：                            保存所有更新的引用记录
objects：		               存放所有的git对象
refs\heads：		         保存当前最新的一次提交的哈希值
COMMIT_EDITMSG：  保存最新提交的一次Commit Message
description：	            保存仓库的描述信息
config：                         仓库的配置文件
index：             	 	    暂存区（stage）,二进制文件
FETCH_HEAD：             版本链接(从远程仓库取下来的分支的末端版本)
HEAD：                 	     保存ref引用，能够找到下一次commit的前一次哈希值
ORIG_HEAD\HEAD:      保存指针的前一个状态
packed-refs:                  保存更新一个引用

**Git版本管理机制**

![image-20210427163944993](C:\Users\dengyong\AppData\Roaming\Typora\typora-user-images\image-20210427163944993.png)

Git 把数据看作是小型文件系统的一组快照。
每次提交更新时 Git 都会对当前 的全部文件制作一个快照并保存这个快照的索引。
为了高效，如果文件没有修改， Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。所以 Git 的 工作方式可以称之为快照流。

## Git基本原理

**Git对象**

Git 是一个内容寻址文件系统，听起来很酷。但这是什么意思呢？ 这意味着，Git 的核心部分是一个简单的键值对数据库（key-value data store）。 你可以向 Git 仓库中插入任意类型的内容，它会返回一个唯一的键，通过该键可以在任意时刻再次取回该内容。

Blob对象

blob对象是用来存储文本内容的。即把一个包含具体文本内容的文件作为一个blob对象存储在git系统中。

初始化一个git仓库

```
git init
Initialized empty Git repository in C:/Users/dengyong/Desktop/demo/.git/
```

通过命令创建一个文本文件，并生成一个blob对象

```
echo 'test conten' | git hash-object -w --stdin
04b35ded9a6e06accbb72875d2b1f05ccbb26afb

"text conten"为文本内容；
git hash-object：git底层命令，可以根据传入的文本内容返回表示这些内容的键值。
–w：使hash-object命令存储数据，否则仅返回键值而不存储内容；
–stdin：指定从标准输入设备如键盘来读取内容，若不用这个参数则需要指定一个要存储的文件的路径；
04b35ded9a6e06accbb72875d2b1f05ccbb26afb是命令返回的40位的十六进制的键值，称为SHA-1码
```

通过键值查看对象内容

```
git cat-file -p 04b35ded9a6e06accbb72875d2b1f05ccbb26afb
test conten

git cat-file：git底层命令，用来查看二进制文件的内容；
-p：让命令输出键值指向的文本内容；
```

查看该键值的对象类型

```
git cat-file -t 04b35ded9a6e06accbb72875d2b1f05ccbb26afb
blob

-t：让命令输出键值的类型
```

查看对象的文件位置

```
ll objects/
total 0
drwxr-xr-x 1 dengyong 197121 0  4月 27 20:02 04/
drwxr-xr-x 1 dengyong 197121 0  4月 27 16:51 info/
drwxr-xr-x 1 dengyong 197121 0  4月 27 16:51 pack/
```

创建一个test.txt文件并写入内容 version 1

```
echo 'version 1' > test.txt

git hash-object -w test.txt
83baae61804e65cc73a7201a7252750c76066a30

git cat-file -p 83baae61804e65cc73a7201a7252750c76066a30
version 1
```

往test.txt文件写入内容 version 2【覆盖之前的内容】

```
echo 'version 2' > test.txt

git hash-object -w test.txt
1f7a7a472abf3dd9643fd615f6da379c4acb3e3a

git cat-file -p 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a
version 2
```

往test.txt文件新增内容 version 3【追加内容】

```
echo 'version 3' >> test.txt

git hash-object -w test.txt
d2a22b76d77aed216728332470d3673d5b582055
```

查看objects/文件夹内对象个数

```
ll objects/
total 0
drwxr-xr-x 1 dengyong 197121 0  4月 27 20:02 04/
drwxr-xr-x 1 dengyong 197121 0  4月 27 20:09 1f/
drwxr-xr-x 1 dengyong 197121 0  4月 27 20:09 83/
drwxr-xr-x 1 dengyong 197121 0  4月 27 20:10 d2/
drwxr-xr-x 1 dengyong 197121 0  4月 27 16:51 info/
drwxr-xr-x 1 dengyong 197121 0  4月 27 16:51 pack/
```

查看当前test.txt文本的内容

```
git cat-file -p d2a22b76d77aed216728332470d3673d5b582055

version 2
version 3
```

把test.txt内容退回到上个版本

```
git cat-file -p 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a > test.txt
```

再次查看当前test.txt文本的内容

```
cat test.txt
version 2
```

**tree对象**

文本通过sha-1键值存储在git系统中，sha-1键值只是存储文本的内容，并没有存储文件名，而且sha-1码是40位十六进制的数字，查找文本内容非常麻烦，因此tree对象可以存储blob对象和子tree对象，使得查找更加方便。

GIT系统通过tree对象和blob对象存储系统中所有的目录以及目录下的文本，类似于文件夹以及文件夹下的文件；因此一个tree对象可以包含若干个子tree对象和若干个blob对象，子tree对象含有一个指向blob对象或子tree对象的sha-1指针。

建立索引文件index

```
git update-index --add --cacheinfo 100644  83baae61804e65cc73a7201a7252750c76066a30 test.txt

此处test.txt文件已经在数据库中，因此要通过plumbing命令update-index创建一个索引文件;并且把test.txt文件的"version 1"内容键值(第一版本)存储到暂存区和索引文件中。

命令中参数含义如下：

100644表示为文件模式。blob对象的文件模式一般都为100644，即普通文本
100755表示可执行文件
120000表示符号链接。
–cacheinfo是必须参数，因为要添加的文件并不在当前目录下而在数据库中。
–add是必须参数，因为该键值对是首次加入到暂存区。
```

查看索引文件index的内容

```
git ls-files --stage 

100644 83baae61804e65cc73a7201a7252750c76066a30 0       test.txt
sha-1码后面的数字0表示文件的状态，当有冲突时不为0，其他一般状态为0.
```

基于当前索引文件index建立tree对象

```
git write-tree 

d8329fc1cc938780ffdd9f94e0d364e0ea74f579
```

验证其是否是一个tree对象，并查看其内容

```
git cat-file -t d8329fc1cc938780ffdd9f94e0d364e0ea74f579
tree
git cat-file -p d8329fc1cc938780ffdd9f94e0d364e0ea74f579
100644 blob 83baae61804e65cc73a7201a7252750c76066a30    test.txt
```

首先新建另外一个tree对象，该tree对象存储第二版本的test.txt和一个新文件new.txt.

```
git update-index --cacheinfo 100644 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a test.txt
创建新文件
echo 'new file' > new.txt
git hash-object -w new.txt
fa49b077972391ad58037050f2a75f74e3671e92
基于当前索引文件index建立tree对象
git update-index --add --cacheinfo 100644 fa49b077972391ad58037050f2a75f74e3671e92 new.txt
查看当前索引文件index内容
git ls-files --stage
100644 fa49b077972391ad58037050f2a75f74e3671e92 0       new.txt
100644 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a 0       test.txt
```



在此之前得把tree对象d8329fc1cc938780ffdd9f94e0d364e0ea74f579加入索引文件index内

```
git read-tree --prefix=bak d8329fc1cc938780ffdd9f94e0d364e0ea74f579
```

查看当前索引文件index内容

```
git ls-files --stage
100644 83baae61804e65cc73a7201a7252750c76066a30 0       bak/test.txt
100644 fa49b077972391ad58037050f2a75f74e3671e92 0       new.txt
100644 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a 0       test.txt
```

基于当前索引文件index新建tree对象，即完成子tree对象加入tree对象内.

```
git write-tree 

3c4e9cd789d88d8d89c1073707c3585e41b0e614
```

查看tree对象内容

```
git cat-file -p 3c4e9cd789d88d8d89c1073707c3585e41b0e614
040000 tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579    bak
100644 blob fa49b077972391ad58037050f2a75f74e3671e92    new.txt
100644 blob 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a    test.txt

思考：为什么最新的tree包含一个tree对象和两个blob对象，而不是两个tree对象呢？

解答：这是因为第二个tree对象0155eb4并没有增加到索引文件index内，而是把第一个tree对象d8329f直接增加到第二tree对象文件内。
```

**commit对象**

committer对象用来保存提交信息，提交信息包括基于当前索引文件生成的tree对象、作者、提交者信息、提交时间以及提交备注等内容；每次提交都会产生一个committer对象。

创建committer对象

```
echo 'first commit' | git commit-tree d8329f
9b102f2939b213750a80ef780d2c70c2c4ad3d4a

first commit为committer对象内备注信息
commit-tree为创建committer对象的底层命令
d8329f 为tree对象d8329fc1cc938780ffdd9f94e0d364e0ea74f579的缩写。
返回该committer对象的键值sha-1码
```

查看committer对象80516b024108ecc079e39f1c0cedc6502f666b12的内容

```
git cat-file -p 9b102f2939b213750a80ef780d2c70c2c4ad3d4a
tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579
author dengyong <13661341683@163.com> 1619578550 +0800
committer dengyong <13661341683@163.com> 1619578550 +0800

first commit

注意：每次commit都会基于当前索引文件index新建tree对象，那么当前索引文件是在上次提交的基础上更新来的，所以每次提交产生的committer对象有前后关系或者称为父子关系。
```

手动创建tree对象和committer对象，可以在创建committer对象的时候加上此关系

```
echo 'second commit' | git commit-tree 0155eb4 -p 9b102f29
670320b51b3bc1d6360d19f291e1cea2b5d88ab4

echo 'third commit' | git commit-tree 3c4e9cd -p 670320
720ed579113b8e70a4131c6c4326c8e9f9d728ac

注：0155eb4表示tree对象0155eb4229851634a0f03eb265b69f5a2d56f341的缩写，-p表示前一个committer对象
```

至此把三个tree对象分别放到三个committer对象中，通过命令查看committer对象的内容

```
git log --stat 720ed5
commit 720ed579113b8e70a4131c6c4326c8e9f9d728ac
Author: dengyong <13661341683@163.com>
Date:   Wed Apr 28 11:31:17 2021 +0800

    third commit

 bak/test.txt | 1 +
 1 file changed, 1 insertion(+)

commit 670320b51b3bc1d6360d19f291e1cea2b5d88ab4
Author: dengyong <13661341683@163.com>
Date:   Wed Apr 28 11:25:42 2021 +0800

    second commit

 new.txt  | 1 +
 test.txt | 2 +-
 2 files changed, 2 insertions(+), 1 deletion(-)
 
 注意：此处无法通过git log命令查看committer对象，而只能通过git log –stata查看。 这是因为所有对象的创建都是使用底层命令，而非普通命令创建的GIT历史。这些底层命令也是执行普通命令git add和git commit命令后，git系统所执行的操作
```

## GitFlow应用

**分支策略**

从master 分支 checkout 出来 stage开发分支
2.开发人员从 stagecheckout 自己的新功能分支 feature
3.新功能开发完成,单元测试后合并 feature 分支到 stage 分支
4.该功能联调完成后, 将对应的 feature 分支合并到 test 分支
5.测试完成后将 test 分支合并到 proview 分支
6.preview环境测试没问题后，合并到 master 分支, 打 tag 上线
7.线上 bug 需要修复时, 从 master checkout 对应 hotfix 分支
8.修复完成后合并到 test 分支进行测试, 测试没有问题, 合并的 master 打 tag 进行上线修复

**分支解释**

master   发布版分支，这个分支只能从别的分支上合并过
           来，是稳定版本的分支，发布时在该分支添加tag
preview 预发布版本分支，在该分支的代码已经趋于成熟
           待测试通过后，合并到master分支。
test        测试分支，前后端开发完成并联调通过后，由
           stage分支合并提交到测试分支
stage     开发分支，各开发同学功能开发完成并通过单元
           测试后，由feature分支合并，并与前端同学联调
feature  功能分支，开发新功能时，新建功能分支，本地
          开发环境测试通过后，提交到该功能分支
hotfix    线上bug修复分支，该分支从master checkout，修复完成后
          提交到hotfix分支，并通过stage->test->proview-master上线

![image-20210427161153510](C:\Users\dengyong\AppData\Roaming\Typora\typora-user-images\image-20210427161153510.png)

## 参考文档

[0]: https://git-scm.com/book/en/v2	"官网文档"

## 推荐工具

Zshell工具：

[1]: http://zsh.sourceforge.net/	"Zshell工具"
