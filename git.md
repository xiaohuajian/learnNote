



[TOC]

## 设计思想

版本管理工具最主要的功能就是高效的管理各个版本（分支），那git 是如何做到高效的管理的呢？其分支的设计思想是什么？

git的设计思想就是通过记录每个版本和上一个版本之间的差异，把这个差异作为一个节点，第一次的提交为根节点，然后每次提交都会形成一个节点，这样循环往复就会形成一颗树，这棵树的形状就是该工程的提交全部记录。说明了，**git的核心就是记录差异 diff ；而其他的集中式的版本管理 svn 是记录快照**，每次提交都是通过记录所有文件的快照，然后通过比对快照然后记录每个文件的历史，差异。由于比较差异和记录快照这两种思想，在创建分支的时候，速度可以说差异巨大，因为在创建分支的时候本质上没有差异，但记录快照则需要把所有的文件都记录一遍，所以其耗时差异巨大，git几乎瞬间完成，svn 要花费很久根据项目的大小而论。



## 分支操作

### 分支创建 git branch

创建一个 testing 分支， 你需要使用 `git branch` 命令：

```
$ git branch branchName
eg: git branch testing
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

这会在当前所在的提交对象(即树节点)上创建一个指针。我们用上面的git 思想去理解这一过程就是此时git 记录一下当前提交对象（树节点）的信息，并和刚刚的节点进行差异比对，因为二者之间没有差异diff，所以它的工作量很少，瞬间就完成了。



![两个指向相同提交历史的分支。](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghja2rdojxj30m8097mx4.jpg)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

> 学会看上面这幅图？注意字符图的箭头方向，箭头为父节点，箭尾为子节点。这种关系也对应上面我说的树型关系图，每次的提交为一个节点，相邻的节点的差异就是两次提交的差异，他们之间形成了父子关系，循环往复组成一个树结构。

git 如何知道工作区间是哪个分支呢？ 是master 还是testing 。git 有一个名为 `HEAD` 的特殊指针，指向当前所在的本地分支（译注：将 `HEAD` 想象为当前分支的别名，比如 **git reset --hard HEAD , 代码回退到当前版本**）。

![HEAD 指向当前所在的分支。](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghja2yo0plj30m80czq2x.jpg)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 分支切换 git checkout

要切换到一个已存在的分支，你需要使用 `git checkout` 命令。 我们现在切换到新创建的 `testing` 分支去：

```
git checkout branchName
eg : $ git checkout testing
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

这样 `HEAD` 就指向 `testing` 分支了。

> 分支的创建和切换可以用 git checkout -b branchName 一个命令完成；

 如果说线上版本发现了bug（iss53），要去紧急修复它，需要新建一个分支并同时切换到那个分支上，你可以运行一个带有 `-b` 参数的 `git checkout` 命令：

```
$ git checkout -b iss53
Switched to a new branch "iss53"
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

它是下面两条命令的简写：

```
$ git branch iss53
$ git checkout iss53
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 分支合并 git merge

>  **git merge [--no-ff] [-m '合并注释'] branchName : 按照 --no-ff 模式进行合并**

解决完刚刚的bug，把iss53分支合并到主master分支上，用git merge 命令去执行。注意：说到合并，有两方参与，A 合并到 B 与 B 合并到 A ，是不一样的。A 合并到 B ，即把A 的代码拿来融合到 B ，过程结束之后，A 的内容没有改变， B的内容改变了 。对照上面，这时候，我的当前分支应该在 master 分支，把 iss53 分支的代码拿过来合并到master，结束之后，我的工作分支仍是 master 且master分支内容包含之前的master分支加 iss53 分支的内容。而 iss53分支的内容没有改变。

![一次典型合并中所用到的三个快照。](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghja37ok2yj30m80aljrf.jpg)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

merge 合并其实是结果合并，Git 会使用两个分支的末端所指的快照（`C4` 和 `C5`）以及这两个分支的工作祖先（`C2`），做一个简单的三方合并。Git 将此次三方合并的结果做了一个合并自动创建一个新的提交指向它。 Git 会自行决定选取哪一个提交作为最优的共同祖先，并以此作为合并的基础；

![一个合并提交。](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghja3c9yy8j30m808s0sm.jpg)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 解决合并冲突

如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，那就有了冲突，因为git 不知道应该保留哪个？只好都保留下来，让你来决定。于是，我们就看到了如下内容所示：

```
<<<<<<< HEAD:index.html  //这个head 就是你的当前工作分支
<div id="footer">contact : email.support@github.com</div>
=======                // 中间这是分割线
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html   //另一个合并的分支
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

在你解决了所有文件里的冲突之后，对文件使用 `git add` 命令来将其标记为冲突已解决。 一旦暂存这些原本有冲突的文件，Git 就会将它们标记为冲突已解决。然后commit 这些文件。最后运行 git status 查看当前分支状态；

### 合并cherry-pick

两个分支A 、B；想把B中的提交合并到A中，可以借助androidStudio完成，先把分支切到A，然后通过项目git history，然后把分支指向B分支到某个提交，右键然后cherry pick 即可到A分支。参考 ：https://jingyan.baidu.com/article/5225f26b2c7576a7fa0908bc.html或者命令行，百度一下。



## 远程仓库 git remote

**管理远程仓库包括了解如何添加远程仓库、移除无效的远程仓库、管理不同的远程分支并定义它们是否被跟踪**等等。然后所有和远程仓库相关的操作可以通过 git remote. **远程仓库默认名字 “origin” 与分支名字 “master” 一样**，在 Git 中并没有任何特别的含义一样。 同时 “master” 是当你运行 `git init` 时默认的起始分支名字，原因仅仅是它的广泛使用，“origin” 是当你运行 `git clone` 时默认的远程仓库名字。 如果你运行 `git clone -o booyah`，那么你默认的远程分支名字将会是 `booyah/master`。

### 查看远程仓库

如果想查看你已经配置的远程仓库url，可以运行 `git remote` 命令。 它会列出你指定的每一个远程仓库名字简写。 如果你已经克隆了自己的仓库，那么至少应该能看到 origin ——这是 Git 给你克隆的仓库的默认名字：

```
$ git remote
origin
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

你也可以指定选项 `-v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。

```
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
```

那么，仓库简写名称为origin，对应的url为 https：//github.com/.... 如果想要查看更多的仓库远程信息 ,可以用 

```
$ git remote show  [shortname]
 
git remote show origin
```



```
$ git remote show origin
* remote origin
  Fetch URL: git@github.com:huburt-Hu/NewbieGuide.git
  Push  URL: git@github.com:huburt-Hu/NewbieGuide.git
  HEAD branch: master
  Remote branches:
    dev    tracked
    master tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)![点击并拖拽以移动]
```

 这个命令列出了当你在特定的分支上执行 `git push` 会自动地推送到哪一个远程分支。 它也同样地列出了哪些远程分支不在你的本地，哪些远程分支已经从服务器上移除了，还有当你执行 `git pull` 时哪些分支会自动合并。



### 添加远程仓库

我在之前的章节中已经提到并展示了如何添加远程仓库的示例，不过这里将告诉你如何明确地做到这一点。 运行 `git remote add <shortname> <url>` 添加一个新的远程 Git 仓库，同时指定一个你可以轻松引用的简写：

```
sjh@DESKTOP-QFQQSSM MINGW64 /d/androidProject/temp
$ git init
Initialized empty Git repository in D:/androidProject/temp/.git/

sjh@DESKTOP-QFQQSSM MINGW64 /d/androidProject/temp (master)
$ git remote add remoteUrl git@github.com:huburt-Hu/NewbieGuide.git

sjh@DESKTOP-QFQQSSM MINGW64 /d/androidProject/temp (master)
$ git remote
remoteUrl
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

这里，我们可以把一个本地存在的仓库，通过git remote add 命令来和远程库进行关联，这里我用了 shortName 为 remoteUrl ,然后再运行 git remote 就得到了remoteUrl ；

### 从远程仓库中抓取与拉取

```
$ git fetch [remote-name]
```

这个命令会访问远程仓库，从中拉取所有你还没有的数据。（这里的remote-name 等于上面的shortname）

如果你使用 `clone` 命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简写。 所以，`git fetch origin` 会抓取克隆（或上一次抓取）后新推送的所有工作。 必须注意 `git fetch` 命令会将数据拉取到你的本地仓库——它并不会自动合并或修改你当前的工作。 当准备好时你必须手动将其合并入你的工作。

如果你有一个分支设置为跟踪一个远程分支，可以使用 `git pull` 命令来自动的抓取然后合并远程分支到当前分支。 这对你来说可能是一个更简单或更舒服的工作流程；默认情况下，`git clone` 命令会自动设置本地 master 分支跟踪克隆的远程仓库的 master 分支（或不管是什么名字的默认分支）。 运行 `git pull` 通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。

从这里看 fetch 和 pull 有一些差别，一般的，我们会用pull 进行抓取并合并，相当于是 pull = fetch + merge，同时fetch 好像并不常用。

### 仓库重命名

如果想要重命名引用的名字可以运行 `git remote rename` 去修改一个远程仓库的简写名。 例如，想要将 `pb` 重命名为 `paul`，可以用 `git remote rename` 这样做：

```
$ git remote rename pb paul
$ git remote
origin
paul
```

值得注意的是这同样也会修改你的远程分支名字。 那些过去引用 `pb/master` 的现在会引用 `paul/master`。

 ### 远程分支

**远程引用是对远程仓库的引用（指针），包括分支、标签等等。** 你可以通过 `git ls-remote`远程跟踪分支是远程分支状态的引用。**它们是你无法移动的本地引用。**一旦你进行了网络通信， Git 就会为你移动它们以精确反映远程仓库的状态。请将它们看做书签， 这样可以提醒你该分支在远程仓库中的位置就是你最后一次连接到它们的位置。

假如远程仓库和本地仓库都创建好了，但没有进行关联，这时候需要先进行关联才能推送，否则我怎么知道应该往哪里推呢？

git remote add origin  git@github.com:xiaohuajian/test.git 

#### 推送push

`git push origin localBranchName[:rometeBrachName]`

推送本地的 localBranchName 分支代码到远程分支 rometeBrachName；远程分支名可以不写，就推送到默认为和本地分支名称相同的分支。 这时候远程仓库如果没有该分支对应的分支，就会在仓库创建一个相同名称的分支；

————————————————————————————————————————————————————————————

当你想要公开分享一个分支时，需要将其推送到有写入权限的远程仓库上。命令为：

```
git push (remote) (branch)

eg: git push origin v1  // 把分支v1 推送到origin仓库里
```

 你也可以运行 `git push origin v1:v1Fix`，它会做同样的事——也就是说“推送本地的 `v1` 分支，将其作为远程仓库的 `v1Fix` 分支” 可以通过这种格式来推送本地分支到一个命名不相同的远程分支。

#### 跟踪分支

从一个远程跟踪分支检出一个本地分支会自动创建所谓的“跟踪分支”（它跟踪的分支叫做“上游分支”）。 跟踪分支是与远程分支有直接关系的本地分支。 **说白了就是服务器分支的本地映射**，那么什么操作会创建跟踪分支？

`git checkout -b <branch> <remote>/<branch>`

`git checkout --track <remote>/<branch>` 

`git checkout localBranchName //这里的localbranchName要和服务器的分支名称相同 否则操作不成功 ` 

```
git checkout -b feature/message origin/feature/message 把远程分支检出一个本地分支并建立关联
```



##### 查看跟踪分支提交情况

如果想要查看设置的所有跟踪分支，可以使用 `git branch` 的 `-vv` 选项。 这会将所有的本地分支列出来并且包含更多的信息，如每一个分支正在跟踪哪个远程分支与本地分支是否是领先、落后或是都有

```
$ git branch -vv
  iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
  master    1ae2a45 [origin/master] deploying index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
  testing   5ea463a trying something new
```

以看到 `iss53` 分支正在跟踪 `origin/iss53` 并且 “ahead” 是 2，意味着本地有两个提交还没有推送到服务器上。 也能看到 `master` 分支正在跟踪 `origin/master` 分支并且是最新的。 接下来可以看到 `serverfix` 分支正在跟踪 `teamone` 服务器上的 `server-fix-good` 分支并且领先 3 落后 1， 意味着服务器上有一次提交还没有合并入同时本地有三次提交还没有推送。 最后看到 `testing` 分支并没有跟踪任何远程分支。

- Ahead 表示本地有提交但没有上传到服务端
- behind 表示服务端有更新，本地还没有合并服务端的代码



![image-20200425160753678](https://i.loli.net/2020/04/25/4CfHGhDbkqYno5v.png)



看到有分支 【behind 1】，表明本地需要pull 服务端代码

重点注意的一点是这些数字的值来自于你从每个服务器上最后一次抓取的数据。 这个命令并没有连接服务器，它只会告诉你关于本地缓存的服务器数据。 如果想要统计最新的领先与落后数字，需要在运行此命令前抓取所有的远程仓库，可以执行:

**`git fetch --all; git branch -vv`**

#### 拉取

当 `git fetch` 命令从服务器上抓取本地没有的数据时，它并不会修改工作目录中的内容。 它只会获取数据然后让你自己合并。 然而，有一个命令叫作 `git pull` 在大多数情况下它的含义是一个 `git fetch` 紧接着一个 `git merge` 命令。如果有一个像之前章节中演示的设置好的跟踪分支，不管它是显式地设置还是通过 `clone` 或 `checkout` 命令为你创建的，`git pull` 都会查找当前分支所跟踪的服务器与分支， 从服务器上抓取数据然后尝试合并入那个远程分支。

**git pull = git fetch  + git merge 二者效果一样，原理不一样**; 

由于 `git pull` 的魔法经常令人困惑所以通常单独显式地使用 `fetch` 与 `merge` 命令会更好一些; 

Git  fetch origin ; git merge origin/master —— 抓取所有的远程分支，然后合并远程master分支代码到当前本地分支

```
git diff origin develop  拉取某个分支之前，想看看区别
Git fetch origin develop  把远程develop分支先抓取
git merge origin develop  把远程develop 进行本地分支合并
```



#### 删除分支

**1. 删除远程分支**

通过远程分支做完所有的工作, 且将其合并到了远程仓库的 `master` 分支（或任何其他稳定代码分支）, 可以运行：

```console
$ git push origin --delete serverfix [To https://github.com/schacon/simplegit]
 - [deleted]         serverfix
```

基本上这个命令做的只是从服务器上移除这个指针。 Git 服务器通常会保留数据一段时间直到垃圾回收运行，所以如果不小心删除掉了，通常是很容易恢复的。

**2. 删除本地分支**

如果合并完之前的某个分支，此时该分支已经没有用了，这时候可以运行

```
git branch -d【D】 branchName  
//删除分支 （当我们临时拉了一个分支修复完bug后，可以删除）
-d 假如要删除的分支没有合并到当前分支，删除失败
-D 无论要删除的分支是否合并到当前分支，均删除
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

## 工作流



## stash 贮存

会处理工作目录的脏的状态——即跟踪文件的修改与暂存的改动——然后将未完成的修改保存到一个栈上， 而你可以在任何时候重新应用这些改动（甚至在不同的分支上）。

> 运用场景：当你在项目的一部分上已经工作一段时间后，所有东西都进入了混乱的状态， 而这时你想要切换到另一个分支做一点别的事情。 问题是，你不想仅仅因为过会儿回到这一点而为做了一半的工作创建一次提交。 针对这个问题的答案是 `git stash` 命令

常用的命令：

- git stash ：贮藏修改，让当前工作目录是干净的；

- git stash list ：查看当前缓存的集合；

  ```console
  $ git stash list
  stash@{0}: WIP on master: 049d078 added the index file
  stash@{1}: WIP on master: c264051 Revert "added file_size"
  stash@{2}: WIP on master: 21d80a5 added number to log
  ```

- git stash apply/pop 【stash@{2}】 ：指定某个贮藏到当前工作区; 如果不指定一个贮藏，Git 认为指定的是最近的贮藏 （apply 是运用之后还会在贮藏堆栈种保留这个索引，pop 会丢弃）

- git stash drop 【stash@{2}】：移除的贮藏的名字来移除堆栈中的索引；

- git stash clear ： 清除所有的贮藏历史



## 重置揭秘

当我们在开发时，想回到某个提交版本，或者说我们想丢弃工作区的所有改动，回到上一次的提交。这时就要用到重置功能reset 和checkout。

### 三棵树

理解 `reset` 和 `checkout` 的最简方法，就是以 Git 的思维框架（将其作为内容管理器）来管理三棵不同的树（文件集合）

Git 作为一个系统，是以它的一般操作来管理并操纵这三棵树的：

| 树                | 用途                                 |
| :---------------- | :----------------------------------- |
| HEAD              | 上一次提交的快照，下一次提交的父结点 |
| Index             | 预期的下一次提交的快照               |
| Working Directory | 沙盒                                 |

#### HEAD

HEAD 是当前分支引用的指针，它总是指向该分支上的最后一次提交。这表示 HEAD 将是下一次提交的父结点。 通常，理解 HEAD 的最简方式，就是将它看做 **该分支上的最后一次提交** 的快照。

#### Index 索引

索引是你的 **预期的下一次提交**。 我们也会将这个概念引用为 Git 的“暂存区”

#### 工作目录

 你可以把工作目录当做 **沙盒**。在你将修改提交到暂存区并记录到历史之前，可以随意更改

#### 工作流程

![reset-workflow](https://git-scm.com/book/en/v2/images/reset-workflow.png)

#### reset \checkout 

reset \checkout 命令会操作这三颗树，工作区文件经过add ，就到了暂存区index，暂存区文件经过commit 就会到HEAD ，形成一个指针记录。由于我们利用第三方集成工具，一般都是直接提交，工具会帮助我们 一次性把add 和commit 操作都做掉，所以也不存在说考虑 已经add 但没有commit 的情况，直接就引用 **-- hard** 参数即可。 

`git reset --hard HEAD`  回到上一次commit 后的代码；

`git reset --hard commitId`  回到commitId 提交时候后的代码；

我们可以这样去针对不同的场景做一些事情，比如我们的代码比较乱，想重新开发，直接就回到最近一次的情况直接运行 `git reset --hard HEAD`；我记得有一次git 拉代码时，卡死了，重启电脑，之前的代码都没了，但是有记录，就直接运行 `git reset --hard HEAD` 回到最近，一下就解决了。

`git checkout banchName `  git 会先检查当前工作区是否有内容变动，如有该命令就执行不成功，需要我们先处理改动. 这时候我们可以commit 该内容，或者丢弃改动，运行 

`git checkout  branchName -- fileName(带路径)` 

>  git checkout feature/optimizeCode  --   /Users/shijianhua/Documents/androidProject/haduCompany/ktakilat/app/src/main/java/com/pendanaan/kta/ui/fragment/amortize/AmortizeBillFragment.java

这时候就会丢弃该文件的修改；

上面提到了checkout 的对提交commit 及 file 两个层面的，显然reset 也有两个，之前介绍的reset是对commit ，对file 层面语法如下：`git reset fileName(带路径)` ；其实reset 对file层面就是**取消暂存文件**，我们知道对一个file 要提交先经过add ，在commit ；如果一个file add 之后，我们想恢复之前的状态，可以运行reset 命令，但是我们经常用工具androidStudio，一般的工具会把这两个命令合二为一，这时候reset 对单独的file 其实没啥意义了。

> **reset 会把当前分支的对应提交id 移到指定的branch 分支的对应提交，并且HEAD指针也会跟着移动；而checkout 不会，但是HEAD 指针会改变其指向，变为目的分支的对应提交。**

上面的所说的就是其根本，要明白这两个根本区别就好了。

![reset和checkout](https://www.git-scm.com/book/en/v2/images/reset-checkout.png)

## 变基 rebase

### 示例说明



​	![](https://www.git-scm.com/book/en/v2/images/interesting-rebase-3.png)

在 `master` 分支上 接下来你决定将 `server` 分支中的修改整合进来。

`git rebase <baseBranch>  <topicBranch>`  将分支server变基到master 上，运行命令：

```
git rebase master server
```

![变基后](https://www.git-scm.com/book/en/v2/images/interesting-rebase-4.png)

将 `server` 中的修改变基到 `master` 上，然后就可以快进合并主分支 `master` 了。

```console
$ git checkout master （执行上面的命令后，会切换到server 分支上，所以要切换回来）
$ git merge server 【-m ‘注释信息’】
```

后面就可以删掉server 分支了。最后为

![合并后分支](https://git-scm.com/book/en/v2/images/interesting-rebase-5.png)

注意这时候master 和 server 都在c10‘ ，而不是在c9’ 。

> 思路是首先找到这两个分支（即当前分支 `server`、变基操作的目标基底分支 `master`） 的最近共同祖先 `C2`，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件， 然后将当前分支指向目标基底 `C3`, 最后以此将之前另存为临时文件的修改依序应用

使用变基的注意事项：**如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，那么不要执行变基。**

假如说有的代码存在你想要变基的分支，你最好只控制自己的代码，而不是帮别人管理分支。因为别人想的和你并不一样。上面的那就话就是这个意思。

### 变基 vs. 合并

至此，你已在实战中学习了变基和合并的用法，你一定会想问，到底哪种方式更好。 在回答这个问题之前，让我们退后一步，想讨论一下提交历史到底意味着什么。

有一种观点认为，仓库的提交历史即是 **记录实际发生过什么**。 它是针对历史的文档，本身就有价值，不能乱改。 从这个角度看来，改变提交历史是一种亵渎，你使用 *谎言* 掩盖了实际发生过的事情。 如果由合并产生的提交历史是一团糟怎么办？ 既然事实就是如此，那么这些痕迹就应该被保留下来，让后人能够查阅。

另一种观点则正好相反，他们认为提交历史是 **项目过程中发生的事**。 没人会出版一本书的第一版草稿，软件维护手册也是需要反复修订才能方便使用。 持这一观点的人会使用 `rebase` 及 `filter-branch` 等工具来编写故事，怎么方便后来的读者就怎么写。

现在，让我们回到之前的问题上来，到底合并还是变基好？希望你能明白，这并没有一个简单的答案。 Git 是一个非常强大的工具，它允许你对提交历史做许多事情，但每个团队、每个项目对此的需求并不相同。 既然你已经分别学习了两者的用法，相信你能够根据实际情况作出明智的选择。

## 提交历史

回顾下提交历史 `git log`命令完成。然后在这个命令上接一些参数，实现我们想要的功能，下面就列举出常用的命令参数。

选项说明

| 选项              | 说明                                                         |
| :---------------- | :----------------------------------------------------------- |
| `-p`              | 按补丁格式显示每个提交引入的差异。                           |
| `--stat`          | 显示每次提交的文件修改统计信息。                             |
| `--shortstat`     | 只显示 --stat 中最后的行数修改添加移除统计。                 |
| `--name-only`     | 仅在提交信息后显示已修改的文件清单。                         |
| `--name-status`   | 显示新增、修改、删除的文件清单。                             |
| `--abbrev-commit` | 仅显示 SHA-1 校验和所有 40 个字符中的前几个字符。            |
| `--relative-date` | 使用较短的相对时间而不是完整格式显示日期（比如“2 weeks ago”）。 |
| `--graph`         | 在日志旁以 ASCII 图形显示分支与合并历史。                    |
| `--pretty`        | 使用其他格式显示历史提交信息。可用的选项包括 oneline、short、full、fuller 和 format（用来定义自己的格式）。 |
| `--oneline`       | `--pretty=oneline --abbrev-commit` 合用的简写。              |

一般的，常用的选项有 --online 、--graph 、--pretty ；

然后用上这些选项命令后，还可以限制输出，然后限制输出的选项有 ：

在 [限制 `git log` 输出的选项](https://www.git-scm.com/book/zh/v2/ch00/limit_options) 中列出了常用的选项

| 选项                  | 说明                                       |
| :-------------------- | :----------------------------------------- |
| `-<n>`                | 仅显示最近的 n 条提交。                    |
| `--since`, `--after`  | 仅显示指定时间之后的提交。                 |
| `--until`, `--before` | 仅显示指定时间之前的提交。                 |
| `--author`            | 仅显示作者匹配指定字符串的提交。           |
| `--committer`         | 仅显示提交者匹配指定字符串的提交。         |
| `--grep`              | 仅显示提交说明中包含指定字符串的提交。     |
| `-S`                  | 仅显示添加或删除内容匹配指定字符串的提交。 |

```
git log --oneline -3 //显示最近的3条提交日志
$ git log --since=2.weeks 
git log --pretty="%h - %s" --author='Junio C Hamano' --since="2008-10-01" \
   --before="2008-11-01" --no-merges -- t/
```

更多的日志过滤 参考 ：[连接]([https://www.git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2#limit_options](https://www.git-scm.com/book/zh/v2/Git-基础-查看提交历史#limit_options))







