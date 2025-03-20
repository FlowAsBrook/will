---
title: git
date: 2016-08-05 22:00:30
tags: tools
categories: git
---

# architecture

<img src="/images/git/git flow.JPG" style="zoom:50%">

**command**

<img src="/images/git/git command.JPG" style="zoom:100%">

## 文件相关

- 提交单个文件
  
  ```shell
  git commit <file> -m "your comment" 
  git pull
  git push
  ```

- 提交多个文件，但是需要排除指定的文件
  
  - method a
  
  ```shell
  git add -u
  git reset -- main/dontcheckmein.txt
  然后进行commit、pull等操作
  ```
  
  - method b
  
  ```shell
  git add . ':!<file-to-exclude>'
  ```
  
- 比较文件，在不同版本的区别

  ```shell
  # uncommited file to HEAD
  git diff <path>
  
  # uncommited file to before last commit
  git diff HEAD^ -- <path>
  
  #last commit to before last commit
  git diff HEAD^ HEAD -- <path>
  
  #difference between HEAD and n-th grandparent
  git diff HEAD~n HEAD -- <path>
  
  #Another cool feature is whatchanged command
  git whatchanged -- <path>
  ```

- 删除远程仓库文件，但保留本地文件系统的文件

  ```shell
  # 文件
  git rm --cached path/to/file
  # 目录
  git rm -r --cached path/to/directory
  ```

- 撤销代码修改

  ```shell
  git restore <file>
  
  #  reset all tracked files
  git restore .
  ```

- 删除未被追踪的文件或目录

  ```shell
  # 查看将要删除的文件
  git clean -n
  
  # 删除未追踪的文件
  git clean -f
  
  # 删除未追踪的文件和目录
  git clean -fd
  
  # 删除未追踪的文件、目录和忽略的文件
  git clean -fdx
  ```

- 查看指定commit提交的文件信息

  ```shell
  git show --name-only <commit-hash>
  git show --name-status <commit-hash>
  ```

## fork 相关

Git fork后的分支，更新最新的源代码

```shell
sourcer为源项目代码
forker为fork之后的项目代码

1、找一个空的目录下签出 fork 后的代码
git clone https://github.com/sourcer/demo.git
查看 remote 信息
git remote -v

2、然后添加源项目地址（距离定义为 source）
git remote add source https://github.com/forker/demo.git
查看 remote 信息，可以看到新增两条信息
git remote -v

3、fetch 源项目
git fetch source

4、合并代码
git merge source/master

5、把合并最新的代码推送到你的fork项目上
git push origin master
```

## commit相关

```shell
# 查看每一行是哪次提交最后修改的
git blame filename 

# 列出文件的所有修改记录
git log -p filename

# 以单行列出commit信息
git log --oneline
```

### config

- config commit identity

  ```shell
  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"
  # Setting these values with --global applies them to all repositories on your machine. You can also set them at the repository level by omitting --global, in which case they will override the global settings for that specific project.
  # e.g.
  git config  user.email "jackblack369@163.com"
  git config  user.name "Wei Dong"
  
  # Git automatically converts CRLF to LF when committing and back to CRLF when checking out.
  git config --global core.autocrlf
  ```

- commit info

  ```
  <type>(<scope>): <subject>
  <body>
  <footer>
  ```

- Type

  > You can use your own commit types, but here are the most common use cases:

  - feat: a new feature, or change to an existing feature.

  - fix: Fixing a bug or known issue in code.

  - test: Adding additional tests for existing features.

  - chore: Updating build tools, like webpack, gulp, ascripts, etc.

  - docs: Update to documentation like README, wiki, etc.

- Scope

  > The scope of the commit can be kept as granular as required and is bound to change based on the complexity of the project. If you are starting off a project, it might not seem necessary at the beginning, although, it is highly recommended as it makes you think twice and harder about the changes that you are about to push.

- reference [7 Rules for Writing a Good Commit Message](https://hackernoon.com/7-rules-for-writing-a-good-commit-message)

### amend

> In Git, the --amend option is used with the git commit command to modify the most recent commit. It allows you to make changes to the last commit without creating a new one, which is helpful for fixing small mistakes such as a missing file, a typo in the commit message, or an incorrect change.

- **Example 1: Fixing a Commit Message**

  ```shell
  # Suppose you made a commit but realized there was a typo in the message:
  git commit -m "Fixing a bugg in login"
  
  # To correct the message, This will update the last commit with the corrected message.
  git commit --amend -m "Fixing a bug in login"
  ```

- **Example 2: Adding Missing Changes**

  ```shell
  # You commit some changes but forgot to include a file:
  git commit -m "Add login functionality"
  
  # Later, you realize that you forgot to add auth.js. To fix this:
  # 1.	Stage the missing file:
  git add auth.js
  
  # 2.	Amend the commit:
  git commit --amend
  ```

  Git will open an editor to let you modify the commit message if needed. Save and close the editor to complete the process.

**Important Notes**

1. **Avoid amending commits that have already been pushed**

Amending changes the commit’s hash, which can lead to problems if others have already pulled the original commit.

2. **Use in Local Development**

`git commit --amend` is best used in local branches before pushing changes to a shared repository.

## 回滚

- method 1: 重置当前分支的 HEAD 指针以及工作目录和暂存区的内容到指定的提交，擦除了目标提交之后的所有提交历史。

  ```shell
  git reset --hard <commit-id>
  ```

- Method 2: 只想回滚到指定提交，并保留后续提交历史

  ```shell
  git revert <commit-id>
  ```

- 回滚远程分支

  - 本地代码回滚到上一版本 `git reset --hard HEAD~1`
  - 回滚到指定版本commitId  `git reset --hard commitId`


加入-f参数，强制提交，远程端将强制跟新到reset版本

>  git push -f

## 分支

```shell
# 查看分支提交历史
git log --oneline --graph --decorate

# 查看分支对应的远程分支
git branch -vv

# 更改当前分支对应的远程分支
git branch -u origin/develop

# 重命名分支
git branch -m new-branch-name

# 备份分支
git branch bk-dev HEAD
```

- 拉取远程分支

  ```shell
  # First, fetch the remote branches:
  git fetch origin
  
  # Next, checkout the branch you want. In this case, the branch we want is called “branchxyz”.
  git checkout -b branchxyz origin/branchxyz**
  ```

- 新建分支

  ```shell
  # To create a new branch from a branch you do NOT have checked out:
  git branch new_branch from_branch
  
  # To create a new branch from the branch you DO have checked out:
  git branch new_branch
  
  # To create *and check out* a new branch from the branch you DO have checked out:
  git checkout -b new_branch
  
  # To create *and check out* a new branch from a branch you do NOT have checked out:
  git checkout -b new_branch from_branch
  
  # Create a new branch from that commit by using the commit hash
  git checkout -b new-branch-name commit-id
  
  # To rename a branch
  git branch -m old_name new_name
  ```

- 推送新分支到远程

  ```shell
  # 1. 将develop分支推送到远程
  git push origin new_branch:new_branch
  
  # 2. 建立本地至上游（远程）分支的链接
  git branch --set-upstream-to=origin/new_branch new_branch
  
  # 如果要推送到的远程分支 origin/new_branch 与本地分支 new_branch 名字相同，那可以使用
  git branch --set-upstream-to origin new_branch
  ```

- 删除分支

  ```shell
  # delete branch locally (如果要删除这个分支的话，需要跳转至其他分支后进行删除操作)
  git branch -d localBranchName
  
  # delete branch remotely
  git push origin --delete remoteBranchName
  
  # If someone else has already deleted the branch, you just do below
  git fetch -p
  ```

## tag

- 拉取并切换指定tag 分支

  ```shell
  git fetch --all --tags
  git tag  # list all tag
  git checkout <tag_name>

- 对指定commit进行标记tag

  ```shell
  git tag <tag_name> <commit_hash>
  git push origin <tag_name>
  
  # push remote upstream repo
  git push upstream <tag_name>
  # 该命令会自动触发upstream repo的github workflow
  ```

- 根据tag创建分支

  ```shell
  git checkout -b <new_branch_name> <tag_name>
  ```

## conflict

- 查看冲突文件

  ```shell
  # Highlight conflicts within files
  git diff
  
  # List conflict files directly
  git diff --name-only --diff-filter=U
  
  # Check merge conflicts interactively
  git mergetool
  ```


## 暂存区

- 查看暂存内容的差异：

  ```shell
  git diff --cached
  ```

- 重置暂存区（对于所有文件）：

  ```shell
  git reset
  ```


## stash

在Git中，`stash`命令用于暂存当前未提交的代码修改，以便在后续时刻再次应用它们。它可以暂存已修改但还未添加到暂存区的文件，也可以暂存已经添加到暂存区但还未提交的文件。

以下是`stash`命令的使用方式：

1. 暂存当前修改：(这个命令将当前的修改暂存起来，让你的工作目录变成干净的状态。)

   ```shell
   git stash
   ```

2. 恢复暂存的修改：

   ```shell
   git stash apply
   ```

   这个命令将之前暂存的修改应用到当前分支上。如果你有多个stash，可以使用`git stash apply stash@{n}`来指定恢复某个特定的stash。

3. 删除stash：

   ```shel
   git stash drop
   ```

   这个命令会删除最近的一个stash。

4. 列出所有stash：

   ```SHELL
   git stash list
   ```

5. 应用并删除stash：

   ```shell
   git stash pop
   ```

   这个命令相当于`git stash apply`和`git stash drop`的组合，它会将最近的一个stash应用到当前分支，并将其删除。

6. 暂存特定的文件：

   ```bash
   git stash push path/to/file
   ```

   这个命令可以将指定的文件暂存起来，而不是暂存整个工作目录。

7. 给stash添加描述：

   ```BASH
   git stash save "stash message"
   ```

## rebase

[git rebase 用法详解与工作原理](https://waynerv.com/posts/git-rebase-intro/)

[How to Rebase a Fork](https://johneverettcase.com/git-how-to-rebase-a-fork)

1. **更新本地主分支（通常是 master 或 main）:** 获取原始仓库的最新更改并合并到你的本地主分支：

   ```shell
   git checkout master  # 切换到你的本地主分支
   git pull upstream master  # 从原始仓库拉取最新更改到本地主分支
   ```

   或

   ```shell
   git fetch upstream
   ```

   

2. **切换到你的分支并进行 rebase:** 切换到你的分支并将其 rebase 到主分支上：

   - 如果是新分支

   ```shell
   git checkout <新分支>
   git rebase master  # 将新分支 rebase 到你的本地主分支
   ```

   - 如果是已有分支

   ```shell
   git checkout <已有分支>
   git rebase upstream/master
   ```

   

3. **解决冲突（如果有）:** 如果在 rebase 过程中出现冲突，你需要手动解决冲突，然后使用 `git add` 标记为已解决。

   - 展示冲突代码（可省略）

   ```shell
   git diff --name-only --diff-filter=U
   ```

   - 解决代码冲突
     - IDE手动解决
     - vimdiff解决
     - meld解决
   - 解决之后

   ```shell
   git add .  # resolve conflict, always use add & commit 🚨
   git commit -m "xxx"
   git rebase --continue
   ```

   - 终止rebase

   ```shell
   git rebase --abort
   ```

   

4. **推送更改到远程仓库:** 推送你的更改到远程仓库：

   - 如果是新分支

   ```shell
   git push origin <分支> --force  # 强制推送到你的 fork 仓库的新分支上
   或
   git push -f origin <分支>
   ```

   请注意：使用 `--force` 参数进行强制推送时，请确保你了解它的影响。它可能会覆盖远程分支的历史记录，因此务必小心操作。

   - 如果是已有分支

   ```shell
   git push
   ```

5. **创建 Pull Request:** 在你的 fork 仓库中，从新分支创建一个 Pull Request（PR），将你的更改合并到原始仓库的目标分支

## squash/reword commit

> 提前备份待操作的分支 git branch bk-dev HEAD

1. merge the last 3 commits into a single commit.

   ```shell
   git rebase -i HEAD~3
   ```

2. replace all those `pick` with `squash` (or simply `s`) apart from the first one.

   <img src="https://www.freecodecamp.org/news/content/images/2023/03/Screenshot-2023-03-22-at-11.28.12.png" style="zoom:40%">

   **Note:** `pick` or `p` will only use those commits, but `squash` or `s` will use them and combine them all together.

   The first commit is the one you will combine them into without losing your changes.

   > 注意，一般需要 squash的是后面的commit，需要保证第一条（最早的）commit 信息不被 squash

3. After doing that, save the file and close it. Git will open up another editor where you can see the new commit message it generates for you.

   <img src="https://www.freecodecamp.org/news/content/images/2023/03/Screenshot-2023-03-22-at-11.36.02.png"   style="zoom:40%;"/>

   You can get rid of all of them and add your custom message:

   <img src="https://www.freecodecamp.org/news/content/images/2023/03/Screenshot-2023-03-22-at-11.37.45.png" style="zoom:44%;">

    Or you can leave it as is. If you add a new commit message, save the file and close it.

4. Pushing changes, You should run git push to add a new commit to the remote origin. If you have already pushed your commits, then you should force push them using the git push command with — force flag (suppose, the name of remote is origin, which is by default)

   ```shell
   git push --force origin HEAD
   ```

> 中途如需终止rebase，运行 git rebase --abort



5. 其它结合

   ```
   - 先进行 `git rebase upstream/v1` 可以确保你的分支是基于最新的上游代码。
   
   - 然后进行 `git rebase -i HEAD~2` 让你可以在最新代码的基础上修改你的提交。
   
     这个顺序可以减少冲突的可能性，因为你先将你的代码基于最新的上游代码，然后再修改你自己的提交。
   ```

   

## remote

[docs.github.com](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork)

- **初始化仓库**

1. List the current configured remote repository for your fork.

   ```shell
   $ git remote -v
   > origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
   > origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
   ```

2. Specify a new remote *upstream* repository that will be synced with the fork.

   ```shell
   git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
   ```

3. Verify the new upstream repository you've specified for your fork.

   ```shell
   $ git remote -v
   > origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
   > origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
   > upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (fetch)
   > upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (push)
   ```

- **同步代码**

1. Change the current working directory to your local project.

2. Fetch the branches and their respective commits from the upstream repository. Commits to `BRANCHNAME` will be stored in the local branch `upstream/BRANCHNAME`.

   ```shell
   $ git fetch upstream
   > remote: Counting objects: 75, done.
   > remote: Compressing objects: 100% (53/53), done.
   > remote: Total 62 (delta 27), reused 44 (delta 9)
   > Unpacking objects: 100% (62/62), done.
   > From https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY
   >  * [new branch]      main     -> upstream/main
   
   # 指定fetch某个分支
   git fetch <remote> <branch>
   ```

3. Check out your fork's local default branch - in this case, we use `main`.

   ```shell
   $ git checkout main
   > Switched to branch 'main'
   ```

4. Merge the changes from the upstream default branch - in this case, `upstream/main` - into your local default branch. This brings your fork's default branch into sync with the upstream repository, without losing your local changes.

   ```shell
   $ git merge upstream/main
   > Updating a422352..5fdff0f
   > Fast-forward
   >  README                    |    9 -------
   >  README.md                 |    7 ++++++
   >  2 files changed, 7 insertions(+), 9 deletions(-)
   >  delete mode 100644 README
   >  create mode 100644 README.md
   ```

   If your local branch didn't have any unique commits, Git will perform a fast-forward. For more information, see [Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) in the Git documentation.

   ```shell
   $ git merge upstream/main
   > Updating 34e91da..16c56ad
   > Fast-forward
   >  README.md                 |    5 +++--
   >  1 file changed, 3 insertions(+), 2 deletions(-)
   ```

   If your local branch had unique commits, you may need to resolve conflicts. For more information, see "[Addressing merge conflicts](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts)."

## submodule

> 对于项目中包含子项目的git项目
>
> reference
>
> https://www.git-scm.com/book/en/v2/Git-Tools-Submodules
>
>  https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97

- 如果是首次克隆，初始化代码需要 `git clone --recurse-submodules https://github.com/chaconinc/MainProject`

  或如果首次没有使用`--recurse-submodules`，那后面可以通过 `git submodule init`和`git submodule update`，进行子模块的拉取更新


- 如何要对子模块的代码也用克隆地址

  ```shell
  git config -f .gitmodules -e    # opens editor, update URLs for your forks`
  git submodule sync
  ```

- 后期从远程仓库更新submodule代码

  ```shell
  git submodule update --remote
  ```

- 在已有项目添加子模块

  ```shell
  # add the submodule
  git submodule add https://github.com/example/submodule.git submodule-dir
  
  # fetch and checkout the specific tag
  cd submodule-dir
  git fetch --tags
  git checkout <xxx>
  cd ..
  
  # check submodule
  git submodule status
  ```

# cherry pick

If the new commits (commit-4, commit-5, commit-6) are in the feat-alpha branch and you want to cherry-pick them into a clean branch based on upstream/main, here’s how to do it:

**1. Fetch the Latest Changes from Upstream**

First, ensure you have the latest changes from the upstream repository:

```shell
git fetch upstream
git checkout main
git merge upstream/main
```

**2. Create a Clean Branch Based on upstream/main**

Create a new branch starting from the updated upstream/main. This branch will hold only the cherry-picked commits.

```shell
git checkout -b new-feature-branch upstream/main
```

**3. Cherry-Pick Commits from feat-alpha**

```shell
# Locate the hash IDs of commit-4, commit-5, and commit-6 in the feat-alpha branch using:
git log feat-alpha --oneline

# Once you have the commit hashes, cherry-pick them into the new branch:
git cherry-pick <commit-4-hash>
git cherry-pick <commit-5-hash>
git cherry-pick <commit-6-hash>
```

**4. Resolve Any Conflicts** (optional)

If there are conflicts during the cherry-pick process:

​	1.	Git will stop and list the conflicting files.

​	2.	Open the conflicting files and manually resolve the conflicts.

​	3.	After resolving the conflicts, mark them as resolved:

```shell
git add <file>
git cherry-pick --continue
```

If you encounter multiple conflicts, repeat this process for each one.

If you decide to abort the cherry-pick entirely:

```shell
git cherry-pick --abort
```

**5. Push the Clean Branch**

Once all desired commits are cherry-picked and conflicts are resolved, push the new branch to your forked repository:

```shell
git push origin new-feature-branch
```

**Why This Approach Works**

​	1.	**Selective Commits**: Cherry-picking ensures only the necessary commits from feat-alpha are included.

​	2.	**Conflict Management**: Conflicts, if any, are resolved once during cherry-picking.

​	3.	**Clean PR**: The new branch has a clear history, free from irrelevant or duplicate commits.

This ensures a smooth and conflict-free pull request to the upstream repository.

# 其它

## 合并策略

warning: 不建议在没有为偏离分支指定合并策略时执行pull操作。  
您可以在执行下一次pull操作之前执行下面一条命令来抑制本消息：

```shell
git config pull.rebase false # 合并（缺省策略）  
git config pull.rebase true # 变基  
git config pull.ff only # 仅快进
```

您可以将 "git config" 替换为 "git config --global" 以便为所有仓库设置  
缺省的配置项。您也可以在每次执行 pull 命令时添加 --rebase、--no-rebase，  
或者 --ff-only 参数覆盖缺省设置。

## 推送本地离线项目到远程github

```shell
mkdir my_project
cd my_project
touch .gitignore
git init
git add .
git commit -m "Initial commit"
git remote add origin youruser@yourserver.com:/path/to/my_project.git
git push origin master
```

## 更改https协议进行推送

> 如果之前是使用ssh协议进行推送，现改为http协议

1. 查看当前Git项目的远程地址

   ```shell
   git remote -v
   ```

   该命令会列出所有已配置的远程仓库地址，并显示它们的读取和写入访问URL。示例输出如下所示：

   ```shell
   origin  https://github.com/username/repository.git (fetch)
   origin  https://github.com/username/repository.git (push)
   ```

   其中，`origin`是远程仓库的名称，`https://github.com/username/repository.git`是远程仓库的地址，`(fetch)`和`(push)`分别表示读取和写入访问URL。

2. 将Git仓库的URL替换为HTTP协议的URL

   ```shell
   git remote set-url origin http://gitlab.xxx.com/username/repository.git
   ```

3. 然后使用以下命令进行Git push操作

后记：需要注意的是，使用HTTP协议进行Git push操作的速度可能会比使用SSH协议慢一些，因为HTTP协议需要建立TCP连接、发送HTTP请求、接收HTTP响应等过程。同时，HTTP协议的安全性也比SSH协议稍低，因此在安全性要求较高的情况下，建议使用SSH协议进行Git push操作。

## Git修改.gitignore不生效	

- 在git中，如果想忽略某个文件，不让这个文件提交到版本库中，可以修改根目录中的.gitignore文件

  但有时候把某些目录或者文件加入忽略规则，发现并未生效

- 未生效原因：.gitignore只能忽略那些原来没有被追踪(track)的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的

- 解决方案：先把本地缓存删除（改变成未track状态），然后提交。

```
git rm -r --cached <要忽略的具体文件或者目录> 或者 git rm -r --cached . 
git add .
git commit -m "update .gitignore"
```

# github

## https

- 使用 https 协议拉取项目代码

```shell
git config credential.helper cache
git config credential.helper store

# global (optional)
git config --global credential.helper cache
git config --global credential.helper store
```

## ISSUES

## search

`searchKeyWord is:issue is:closed repo:Alamofire/Alamofire` 

这条搜索，searchKeyWord是搜索关键字, `is:issue` 表示我们要搜索 issue， `is:closed` 表示已经关闭的 issue， `repo:Alamofire/Alamofire` 表示我们只搜索这个仓库范围的 issue

## workflow

> GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline.

- [runner host](https://docs.github.com/en/actions/using-github-hosted-runners/using-github-hosted-runners/about-github-hosted-runners#supported-runners-and-hardware-resources)

- home

  ```shell
  # 情况一
  runs-on: ubuntu-latest
  
  $HOME=/home/runner/work/<projectName>/<projectName>
  
  # 情况二
  runs-on: ubuntu-latest
  container: dingodatabase/dingo-eureka:rocky9
  
  $HOME=/__w/<projectName>/<projectName>
  ```

- disk usage

  ```shell
  # 情况一：
  runs-on: ubuntu-latest
  
  Filesystem      Size  Used Avail Use% Mounted on
  /dev/root        72G   47G   26G  65% /
  tmpfs           3.9G   84K  3.9G   1% /dev/shm
  tmpfs           1.6G  1.1M  1.6G   1% /run
  tmpfs           5.0M     0  5.0M   0% /run/lock
  /dev/sda16      881M   59M  761M   8% /boot
  /dev/sda15      105M  6.1M   99M   6% /boot/efi
  tmpfs           794M   12K  794M   1% /run/user/1001
  
  # 情况二：
  runs-on: ubuntu-latest
  container: dingodatabase/dingo-eureka:rocky9
  
  Filesystem      Size  Used Avail Use% Mounted on
  overlay          73G   60G   14G  82% /
  tmpfs            64M     0   64M   0% /dev
  shm              64M     0   64M   0% /dev/shm
  /dev/root        73G   60G   14G  82% /__w
  tmpfs           1.6G  1.2M  1.6G   1% /run/docker.sock
  tmpfs           3.9G     0  3.9G   0% /proc/acpi
  tmpfs           3.9G     0  3.9G   0% /proc/scsi
  tmpfs           3.9G     0  3.9G   0% /sys/firmware
  ```

  

- event


- env

  ```shell
  GITHUB_OUTPUT
  GITHUB_STATE
  GITHUB_ENV
  ```

  [Passing information between jobs](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/passing-information-between-jobs)

## github cli

**install**

```shell
sudo dnf install 'dnf-command(config-manager)'
sudo dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo
sudo dnf install gh --repo gh-cli

# 或 直接下载二进制文件进行安装
https://github.com/cli/cli/releases
```

**security**

- create/check gh_token

  ```shell
  # github网站设置
  settings/Developer Settings/Personal access tokens (classic)
  ```


- senario

  ```shell
  # senario 1 在git push的时候，输入password的时候，直接复制上面的tokens即可
  
  # senario 2 在workflow中使用
  gh secret set SECRET_NAME
  # or
  gh secret set SECRET_NAME < secret.txt
  ```


- use

  ```shell
  # login
  gh auth login
  
  # set
  gh secret set DOCKERHUB_USERNAME 
  gh secret set DOCKERHUB_TOKEN 
  gh secret set GH_TOKEN 
  
  # check
  gh secret list
  ```

  



