# Git 的使用

本文主要内容源自[廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)以及 [git-scm 网站](https://git-scm.com/doc)

## Git 的基本原理、构成

![git-repo](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)

- 工作区（Working Directory）：一个文件夹就是一个工作区
- 仓库 / 版本库（Repository）：隐藏式文件夹 /.git，可使用$ git init 在某个文件夹（即工作区）内生成仓库 
  - stage（又叫 index）：暂存区
  - master：一个默认的分支，可以自己创建其他的分支
  - HEAD：指针，指向需要 git 的分支，可以指向 master，也可以指向自己创建的其他分支
  - [可以参见本视频](http://github.liaoxuefeng.com/sinaweibopy/video/master-and-dev-ff.mp4)
- 文件的添加，分为两步：①工作区的文件 add 到 stage 里面 ②stage 的文件 commit 到 master 里面
- 文件如果不推送到远程，就只能在本地可见

## 添加文件

1. 注意：
   1. 不要使用微软自带的记事本记录txt文档
   2. 一开始需要使用$ cd 文件夹名，让自己进入工作区
2. 命令`$ git add file.txt` ，先麻烦也可以用` $ git add --all `推送所有文件按
3. 命令`$ git commit -m "本次提交的说明，方便日后查找（最好用英文）"`
4. git add 和 git commit 的区别：add 可以多次添加不同的文件；commit 可以一次性全部提交

```git
s@S MINGW64 ~/learngit (master)
$ git add readme.txt

s@S MINGW64 ~/learngit (master)
$ git commit -m "wrote a readme file"
[master (root-commit) 55d5c63] wrote a readme file
 1 file changed, 3 insertions(+)
 create mode 100644 readme.txt
```


## 查看文件的状态（是否已提交）

1. 使用`\$ git status` 查看文件现在的修改是否被提交
   1. 若出现 Changes not staged for commit:，则说明文件被修改后，修改未被添加到 stage 中
   2. 若出现 Untracked files:，则说明该文件还未被添加到 stage 中
2. 可以使用`$ git diff file.txt `**查看修改被修改的地方**
3. 使用`$ git add file.txt `添加
   1. 若此时使用`$ git status` 查看，会出现Changes to be committed:，说明此时修改已被添加，只剩下 commit 了
4. 使用`$ git commit -m "对文件的说明" `提交即可
   1. 使用`$ git status `查看仓库的状态，若没有需要 commit 的修改时，工作目录是干净的，会出现 nothing to commit (working directory clean) 


> 可以使用$ ls 来查看被 git 的文件夹的内容

## 版本回退

1. `$ git log` 可以用来查看各个版本的信息

   使用`$ git log --pretty=oneline` 可以让信息展示更加简洁，`$ git log --pretty=oneline --abbrev-commit` 可以进一步简化 commit id，让其只显示前七位字符

   P.S. 版本回退后，使用`$ git log `无法看到那个被放弃的版本，这时，**需要使用`$ git reflog `**

2. `$ git reset --hard `用来进行版本的回退

   1. `$ git reset --hard HEAD~1 `

      HEAD 指向的是当前的版本，回退到当前版本之后的第一个过往版本就用 ~1，之后第100个就用 ~100

   2. `$ git reset --hard commit_id`

      commit_id 就是`$ git log` 显示的那一大堆数字字母组合，用 commit_id 的前几个字符（一般是7个）就可以了，无需更多字符（因为 Git 会自行搜索）

3. `$ git reflog `可以用来查看命令历史

   可以查看自己之前进行过的操作，也可以用来查看commit_id

4. `$ cat file.txt` 可以用来查看文件内容

## 使用分支

### 创建分支

1. 使用`$ git checkout -b` 分支名 创建分支，`$ git checkout -b `分支名 相当于**合并了创建分支的命令**`$ git branch 分支名 `和**切换分支的命令**`$git checkout 分支名 `

   ```
   # Switch to a branch "serverfix"
   $ git checkout serverfix

   # Create a new branch "serverfix" and switch to it
   $ git checkout -b serverfix
   ```

   若想在本地创建远程的某分支，可以使用 `$ git checkout -b 分支名 origin/分支名` 这个命令，但是本地此时不能存在该分支

2. 在新分支里进行操作 —— 例如修改文件、git add、 git commit、git log……

3. 可以使用`$ git branch` 来查看仓库内一共有多少分支

4. *使用新分支的场合：*

   1. *对仓库内文件的修改工作一时之间完不成，所以就对原先仓库内的文件和自己之后的修改分别进行备份，这两个备份就是仓库的 master 和自己的新分支*
   2. 团队开发，每个人有自己的分支，最后合并到一个分支上

### 合并分支

1. 使用`$ git checkout 分支名` 切换到想要被合并的分支下，使用`$git merge 分支名` 合并分支

   > 若因为`$ git merge` 后忘接分支名，而出现`s@S MINGW64 ~/learngit (master|MERGING)`，则可以使用git checkout 先切换到其他分支，在切换回来的时候，就不再出现`(master|MERGING)`了

2. 如果不需要该分支，可以是使用`$git branch -d 分支名` 来删除该分支

### 分支发生冲突

1. 使用`$ git merge 分支名` 时，若因为冲突而合并失败，会显示：

   ```
   Auto-merging file.txt
   CONFLICT (content): Merge conflict in file.txt
   Automatic merge failed; fix conflicts and then commit the result.
   ```

   *也可以使用`$git status` 查看冲突的文件是什么* 

2. 再使用`$ git diff file.txt` 查看冲突是什么，然后自己手动在文件中修改

3. 使用`$ git add file.txt` 和`$ git commit -m "注释"` 提交修改

### 查看分支详情

1. 可以使用`$ git log --graph` 查看分支的具体合并情况，

2. 使用`$ git log --graph --pretty=oneline --abbrev-commit` 查看分支的合并情况大纲

3. git 默认使用 fast-forward 模式（即 --ff 参数）进行合并，这种模式下，若分支被删除，则再也无法通过`$ git log --graph --pretty=oneline --abbrev-commit` 查看到原先的分支信息

   **除非使用 --no-ff 参数**，例如` \$ git merge --no-ff -m "merge with no-ff" 分支名`

## 撤销修改

1. 当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`$ git checkout -- file`。

2. 当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`$ git reset HEAD file`，第二步按上一条操作。

   `$ git reset`的更多介绍见[超链接](https://git-scm.com/docs/git-reset)

3. 已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库

## 删除文件

1. 使用`$ rm file.txt `可以直接在文件管理器中删除文件。此时可以使用` $ git status `查看 git 版本与本地文件的冲突，解决方法如下：
   1. 使用 `$ git rm file.txt` 删除 git 版本内的文件，并且使用`$ git commit -m "注释"` 来 commit 文件
   2. 使用 `$ git checkout -- file.txt` 恢复本地文件 

## 暂存文件

**常用场景：**团队合作时，若修复自己工作范围内的线上 bug，最好自己创建一个分支，免得影响团队工作

> 自己真的不太理解这个功能的用处何在，可能是由于自己现在没有工作经历的缘故吧

1. 修改文件并且将其`$ git add file.txt `到版本库中后，使用`$ git stash` 将其暂存起来

   > 需要注意的是，不先执行 git add 将文件放进版本库，就无法执行 git stash

2. 这时再去查看文件，就会发下文件呈现出修改之前的样子，乐意使用`$ git stash list `查看暂存文件

3. 想要恢复到暂存文件，使用命令 `$ git stash pop` 即可

## 远程仓库同步

### 使用 GitHub 作为远程仓库

#### 将本地内容推送到 GitHub

在 GitHub 上搭建的**免费仓库是公开的**，步骤如下：

1. [创建 SSH Key](https://help.github.com/articles/connecting-to-github-with-ssh/)。GitHub 可支持多个电脑连接一个 GitHub 账号，GitHub 不仅支持 SSH 协议，还支持 HTTPS 协议[^https 协议]。

   > SSH 的好处是省去每次登陆时的账号密码输入（有点类似与 chrome 浏览器的保存账号密码），而且下载速度快
   >
   > 授权认证的方法：OAUTH、OPENID、SAML、CAS

2. 将文件夹的内容推送到 GitHub 的仓库中。

   1. 首先使用 `$ cd ~/文件夹名 `来到想要同步的文件夹下，以便进一步操作

   2. 再使用`$ git remote add origin git@github.com:账号名/仓库名.git` 将文件夹关联到到 GitHub 的账号上

      > origin 是 GitHub 的远程仓库的默认名，最好不要修改，便于他人理解

   3. 初次使用时，需要使用`$ git push -u origin master `将文件夹的内容都推送到 GitHub

      > 由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令

   4. 后续更新文件夹内容时，首先使用`$ git add file.txt `和`$ git commit -m "注释"` 更新本地的 git 设置

   5. 最后，使用`$ git push origin 分支名 `将本地修改后的 git 分支 推送到 GitHub 上就可以了

#### 多人协作

1. 用`git push origin branch-name`推送自己的修改

      > 可以用`git remote -v`查看远程库信息

2. 如果推送失败，并出现以下：

      ```
      error: failed to push some refs to 'git@github.com:TAN-S/learngit.git'
      hint: Updates were rejected because the tip of your current branch is behind
      hint: its remote counterpart. Integrate the remote changes (e.g.
      hint: 'git pull ...') before pushing again.
      hint: See the 'Note about fast-forwards' in 'git push --help' for details.
      ```

      就说明远程的版本比本地版本更新，因此二者存在冲突，此时，需要在 push 之前，使用  [\$ git pull origin 分支名 ](https://git-scm.com/docs/git-pull) 试图合并

      > 如果`git pull`提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream branch-name origin/branch-name`

3. 如果合并时产生了冲突，就需要解决冲突后再在本地 add、commit

4. 最后，再 push 推送

#### 将 GitHub 的内容 clone 到本地

1. 首先选择一个文件夹，用来放置被 clone 的内容

2. 使用`$ git clone git@github.com:用户名/仓库名.git `

   > 在 GitHub 上面点击 Clone or download，会出现 Clone with SSH 

### [自建 Git 服务器](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000)

## 标签的使用

1. 为什么要使用标签 tag？

   虽然 git 会自动生成 commit id，但是这个 id 非常不好记，使用 tag 标签可以便于查找版本

   <u>tag 和 commit id</u> 之间的关系就像 <u>域名和 IP 地址</u> 之间的关系

2. 查看已有的标签：`$ git tag`

3. 创建标签`$ git tag 标签名 commit id `，若没有后接 commit id，则默认是给最近的一个版本打标签，常见参数如下：

   1. `-a`：指定标签名 
   2. `-m`：指定注释、说明
   3. 例如：`$ git tag -a v0.1 -m "version 0.1 released" 3628164`

   > 可使用`$ git log --pretty=oneline --abbrev-commit`查看 commit id

4. 可以用`git show 标签名 `查看该标签名所代表的版本的情况

5. 推送标签名到远程：

   1. 推送某一个标签名：`$ git push origin 标签名`
   2. 推送全部标签名：`$git push origin --tags`

6. 删除标签名：

   1. 删除本地标签名：`$ git tag -d 标签名`，例如`$ git tag -d v0.1`
   2. 删除远程标签名：若标签已推送至远程，则需先删除本地标签，再使用`git push origin :refs/tags/标签名`删除远程标签

## Git 的其他配置

1. [忽略某些格式的文件](https://github.com/github/gitignore)：在工作区的根目录下生成`.gitignore`文件，并将其提交到 git 中，之后，就无法添加`.gitignore`中的文件格式至 git，除非使用 -f 参数，例如：`$ git add -f file`

   也可以对`.gitignore`文件进行版本管理哟~

2. 给命令起别名：使用`$ git config --global alias.别名 原名`进行设置。例如`$ git config --global alias.st status`就是将 status 的别名设置为 st，这时，`$ git status`与`$ git st`等价

   1. 如果原名是由好几个单词组成的，可以将它们用引号括起来，例如`$ git config --global alias.unstage 'reset HEAD'`中 reset HEAD 就用引号括起来了
   2. 别名的设置放在`.git/config`文件中，可直接在该文件中对别名的配置进行添加、删减

   ​

------

[^https 协议]: 类似于https://github.com/michaelliao/gitskills.git