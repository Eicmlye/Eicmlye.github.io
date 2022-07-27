---
layout:         post
title:          "Git 使用指南"
subtitle:   
post-date:      2022-07-25
update-date:    2022-07-27
author:         "Eicmlye"
header-img:     "img/em-post/20220725-GitTutorial.jpg"
catalog:        true
tags:
    - Git
---

## 0. 术语

未追踪文件 (untracked files): 新建的、从未加入过暂存区的文件.

未加入暂存区的文件 (unstaged files): 已加入过暂存区并被修改了的文件, 或分支中原有的并被修改了的文件.

忽略的文件 (ignored files): .gitignore 中包含的文件.

## 1. 安装初始化

### 1.1. 设置用户名和邮箱

    $ git config --global user.name "USER_NAME"
    $ git config --global user.email "USER_EMAIL"

### 1.2. 设置远程仓库 SSH Key

在 `C:/User/USER_NAME` 目录中查看是否有 `.ssh` 目录. 若无, 执行

    $ ssh-keygen -t rsa -C "USER_EMAIL"

以创建 SSH Key.

**注意:** 切勿泄露私钥 `id_rsa`.

## 2. 本地版本库初始化

    $ cd REPO_PATH
    $ mkdir REPO_NAME
    $ cd REPO_NAME
    $ git init
    Initialized empty Git repository in /REPO_PATH/REPO_NAME/.git/

远程库初始化和库关联见 [7.1. 节](#71-关联-github-远程库).

## 3. 修改版本库

**注意:**
1. 避免使用 Windows 自带的记事本软件编辑文本文件, 建议使用 VS Code 或 Atom.
2. `git` 命令 (`git init` 和 `git clone` 除外) 只能在工作目录为 Git 版本库时才能使用, 否则报错

        fatal: not a git repository (or any of the parent directories)

### 3.1. 添加、修改文件

将要添加到库中的文件移动到库目录内, 或将库中要修改的文件修改后, 执行

    $ git add FILE_NAME

使更新后的文件 `FILE_NAME` 进入暂存区.

**注意:**
必须将 `FILE_NAME` 先移入 `REPO_NAME` 或其子目录, 再使用 `git add`, 否则报错

    fatal: pathspec 'FILE_NAME' did not match any files

### 3.2. 删除文件

将要删除的文件先移出库或直接删除, 然后执行

    $ git rm FILE_NAME

使修改进入暂存区.

## 4. 发布修改

执行

    $ git commit -m "MESSAGE"

以发布暂存区的修改为最新版本库.

## 5. 版本库状态

### 5.1. 查询当前修改状态

    $ git status

1. 若显示

        On branch main
        Nothing to commit, working tree clean

    则表明当前版本库未经修改, 与最近一次发布时状态相同.
2. 否则, 应显示

        On branch main
        Changes not staged for commit:
            ...
        Changes to be committed:
            ...
        Untracked files:
            ...

    以下修改不会被 `git commit` 发布:
    - 第一处省略号表示仍在工作区、但库中存在同名文件的修改;
    - 最后一处省略号表示仍在工作区、且库中不存在同名文件的修改;

    以下修改在 `git commit` 执行后会发布至最新版本库中:
    - 第二处省略号表示已提交至暂存区的修改.

### 5.2. 查询修改内容

    $ git diff FILE_NAME

若修改内容超出命令行一页范围, 可以:
- 按 `ENTER` 键逐行打印
- 按空格键逐页打印
- 按上、下方向键向前、向后移行
- 按左、右方向键向后、向前翻页
- 按 `Q` 键退出打印 (也可能是 `Ctrl + Q` , `Shift + Q` , `ESC` 或 `Win + Q` )

### 5.3. 操作回退

- 撤销工作区中文件 `FILE_NAME` 的所有修改, 将其状态返回至最新版本库中的状态, 可执行

        $ git checkout -- FILE_NAME

- 将暂存区中文件 `FILE_NAME` 的所有修改退回到工作区, 可执行

        $ git reset HEAD FILE_NAME

### 5.4. 版本修改历史

执行

    $ git log

后, 将由近到远显示各次修改的信息

    commit MD5_ID (HEAD->main)
    Author: USER_NAME <USER_EMAIL>
    Date:   DATE TIME TIMEZONE

        MESSAGE

参数 `--pretty=oneline` 将改变打印格式为

    MD5_ID (HEAD->main) MESSAGE

参数 `--abbrev-commit` 将 `MD5_ID` 统一输出前7位.

参数 `--graph` 将输出分支合并图.

### 5.5. Git 操作历史记录

执行

    $ git reflog

后, 将显示若干行

    MD5_ID (HEAD->main) HEAD@{NUM}: COMMAND: MESSAGE

### 5.6. 版本回退

Git 中, `HEAD` 为当前版本指针, 向前1个版本为 `HEAD^`, 向前 n 个版本为 `HEAD~n`.

1. 要回退到前一版本, 可执行

        $ git reset --hard HEAD^

2. 要回退或在回退后要重新恢复到指定版本, 可执行

        $ git reset --hard MD5_ID

    其中 `MD5_ID` 可在 `git reflog` 的执行结果中查找.

## 6. 分支管理

`main` 等指针是分支的首元指针, 指向一个分支的最新发布的修改; `HEAD` 则是工作指针, 指向当前工作所处的分支的首元指针 (如 `main` ), `HEAD`指向的指针所指向的修改称为工作修改.

### 6.1. 查询版本库分支信息

执行

    $ git branch

以获取当前版本库的分支列表

      BRANCH_NAME1
    * BRANCH_NAME2
      ...
      BRANCH_NAME_K

其中标*的为当前分支.

### 6.2. 创建分支

创建分支的本质, 是创建一个指向工作修改的新指针, 并将 `HEAD` 指向这个新指针.

执行

    $ git branch BRANCH_NAME

以创建分支 `BRANCH_NAME` , 执行

    $ git switch BRANCH_NAME

以将 `HEAD` 移动到**已有**分支 `BRANCH_NAME` 上. 上述操作可合并为

    $ git switch -c BRANCH_NAME

### 6.3. 合并、删除分支

执行

    $ git merge BRANCH_NAME

以将分支 `BRANCH_NAME` 合并到当前分支中. 合并默认使用 Fast-forward 方式, 但用这种方式合并, 会使此后被删除的分支的合并记录丢失.

参数 `--no-ff -m MESSAGE` 可禁用 Fast-forward 方式, 实际将创建一个 commit , 因此需要同时带上 `-m MESSAGE` .

执行

    $ git branch -d BRANCH_NAME

以删除分支 `BRANCH_NAME` .

## 7. 远程库管理

### 7.1. 关联 GitHub 远程库

将 [1.2.](#12-设置远程仓库-ssh-key) 中的公钥 `id_rsa.pub` 内容复制到 GitHub 账号 SSH Key, 并将 `USER_EMAIL` 设置为 GitHub 账号的**可见**邮箱. 然后执行相应操作.

#### 7.1.1. 本地库没有对应的远程库时

在 GitHub 账号中创建同名版本库 `REPO_NAME`, 不要加入任何默认文件 (包括开源协议、`.gitignore` 文件和 `README` 文件等). 执行

    $ git remote add origin https://github.com/GITHUB_USER_NAME/REPO_NAME.git

或

    $ git remote add origin git@github.com:GITHUB_USER_NAME/REPO_NAME.git

可将本地库与远程库关联 (分别使用 `https` 协议和 `ssh` 协议传输), 其中 `origin` 是远程库的默认别名.

#### 7.1.2. 新建项目创建版本库时

在 GitHub 账号中创建版本库 `REPO_NAME`, 加入 `README` 文件和开源协议. 将工作目录移至存放版本库的目录后, 执行

    $ git clone https://github.com/GITHUB_USER_NAME/REPO_NAME.git

或

    $ git clone git@github.com:GITHUB_USER_NAME/REPO_NAME.git

本地将克隆远程库 `REPO_NAME` 的 `main` 分支内容 (分别使用 `https` 协议和 `ssh` 协议传输), 并将该本地目录初始化为**与对应远程库关联的**本地 Git 版本库, 远程库别名默认为 `origin` .

### 7.2. 多人协作

#### 7.2.1. 远程库信息

执行

    $ git remote

以查看远程库信息, 参数 `-v` 可显示详细信息

    $ git remote -v
    origin git@github.com:GITHUB_USER_NAME/REPO_NAME.git (fetch)
    origin git@github.com:GITHUB_USER_NAME/REPO_NAME.git (push)

若无推送权限, 则不会显示相应的可推送地址.

#### 7.2.2. 抓取分支

- 本地无远程库对应内容时, 首先使用 `git clone` 抓取主分支到本地.

        $ git clone git@github.com:GITHUB_USER_NAME/REPO_NAME.git

    此时本地只能看到 `main` 分支. 若要在分支 `BRANCH_NAME` 中开发, 应在本地创建分支 `BRANCH_NAME` , 可执行

        $ git switch -c BRANCH_NAME_LOCAL REPO_NAME/BRANCH_NAME

    创建与远程分支 `REPO_NAME/BRANCH_NAME` 关联的本地分支 `BRANCH_NAME_LOCAL` .

- 本地库已与远程库关联, 且有未关联的待发布分支 `BRANCH_NAME_LOCAL` 时, 若需发布到远程库的 `BRANCH_NAME` 分支上, 但其它人已经更新过该分支, 则应先重新关联分支

        $ git branch --set-upstream-to=REMOTE_REPO_NAME/BRANCH_NAME

    然后将更新的内容用 `git pull` 拉取到本地进行合并, 解决冲突后推送

        $ git push REMOTE_REPO_NAME BRANCH_NAME_LOCAL

#### 7.2.3. 推送分支

执行

    $ git push -u REMOTE_REPO_NAME BRANCH_NAME_LOCAL

可将本地分支 `BRANCH_NAME_LOCAL` 同步到其在远程库 `REMOTE_REPO_NAME` 中关联的分支.

### 7.3. 分支布局与处理

理想的多人协作分支场景应当是

    main    o----------o------------o----o-----
             \        /            /    /
    draft     o---o--o------------o----o-------
              |\    /            /    /
    user1     | o--o-------o----o----/---------
              \           /         /
    user2       o--------o---------o-----------

主分支 `main` 应当尽可能稳定, 只用于发布 Release 版本. 各协作者在各自的分支上工作, 完成后将工作合并到组长的分支或 `draft` 分支.

#### 7.3.1. Bug 分支处理与保护现场

Bug 处理具有临时性、紧急性的特点. Git 的工作区和暂存区是各分支共用的. 因此, 当临时需要处理 bug 而切换至 bug 分支时, 手头上的工作未完成, 需要先保存现场. 下面的操作将已追踪的文件保存到 stash list 中.

    $ git stash
    Saved working directory and index state WIP on USER_BRANCH_NAME: MD5_STASH MESSAGE

若有暂时不便加入暂存区的未追踪文件, 可使用参数 `--include-untracked` 或 `-a`.

**【注意**: 参数 `-u` 会将含有“未追踪且未忽略的文件”的文件夹整个删除, 应避免使用. 参见[该链接](http://web.archive.org/web/20140310215100/http://blog.icefusion.co.uk:80/git-stash-can-delete-ignored-files-git-stash-u/). **】**

此时工作区和暂存区文件都会保存到 WIP 的位置, 工作区恢复到未修改的状态, 可以创建 Bug 分支, 修复后提交到 `main` 分支.

    $ git switch -c issue-ID
    ...
    $ git add .
    $ git commit -m "fixed bug-ID"
    [issue-ID MD5_ISSUE] fixed bug-ID
    $ git switch main
    $ git merge --no-ff -m "merged bug fix ID" issue-ID
    $ git branch -d issue-ID

现在可以切换回原分支, 并恢复工作现场了.

    $ git switch USER_BRANCH_NAME
    $ git stash list
    stash@{STASH_NUM}: WIP on USER_BRANCH_NAME: MD5_STASH MESSAGE

执行

    $ git stash apply stash@{STASH_NUM}

恢复到指定的工作现场. 执行

    $ git stash drop stash@{STASH_NUM}

删除指定的工作现场记录.

注意到此时工作现场仍存在 bug, 需要将刚才提交的 bug 修复复制到当前分支, 实际将创建新的 commit.

    $ git cherry-pick MD5_ISSUE

## 参考文献

<div id="liaoGit">[1] 廖雪峰. <a href="https://www.liaoxuefeng.com/wiki/896043488029600">Git 教程</a>. </div>
