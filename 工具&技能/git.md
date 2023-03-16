# git

## 基本配置

### 用户信息

- 设置
  - git config --global user.name "name"
  - git config --global user.email "email"

- 查看
  - git config --global user.name
  - git config --global user.email

### 命令别名

- 在用户文件夹下新建.bashrc文件，在里面输入
- alias newname='oldname'

 

## 本地仓库

### 获取

- 在任意位置创建一个空目录（文件夹）作为本地仓库
- 右键打开git bash
- 执行git init
- 创建成功后可以看到文件夹下隐藏的.git目录

## 基础操作

 ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/20221125101245.png)

- git add 工作区->暂存区
  - git add xxx 添加单个文件可以用|来添加多个文件
  - git add . 添加全部文件
  - 在.gitignore文件中设置不需要git管理的文件
    - 可以写全名，也可以如*.a这种匹配
    - ！lib.a lib不能被忽略，要求管理
    - xxx/ 忽略一个文件夹
    - xxx/*.a 忽略文件夹中的一类文件,但不忽略子文件夹中的
    - xxx/\**/\*.a 全部忽略
    - /xxx忽略根目录下文件/文件夹
- git commit 暂存区->本地仓库
  - git commit -m ‘注释’
- git status 检查文件状态
- git log检查仓库状态（提交记录）
  - 已配置git-log显示更直观简介

- git reset --hard commitID 版本回退
  - id可以通过git-log查看
  - git reflog 可以查看已经删除了的提交记录

- git中选择文字就可自动复制，按鼠标中键粘贴

## 分支

### 操作

- git branch 查看分支（也可以用git-log）
- gti branch xxx 新建分支
- git branch -d 删除分支（-D强制删除）
  - 切换到别的分支才能删除当前分支
- git checkout xxx 切换分支

  - git checkout -b xxx 如果分支不存在则会自动创建

  - 一次只能对一个分支进行操作
  - 不同分支可能处于不同的版本
  - head->指向当前的分支
- git merge xxx 把其它分支合并到当前的分支上 
- git rebase xxx 另一种合并分支的方法，把一个分支直接移动到另一个分支的末端，使得并行开发的任务看起来像线性先后开发的
  - 把当前分支移动到xxx目标分支上


### head指针

- 默认情况下head指针指向当前分支的最新节点
- `git checkout HEAD^`表示向上移动一个节点
  - ~n 表示向上移动n个节点
- 移动分支指向：`git brunch -f master HEAD~3`
  - 将master分支以到HEAD上3个点的位置
- 撤销提交：`git reset HEAD^1`
  - `git revert`:用于对已经上传的修改进行撤销，即创建一个与修改前内容完全相同的新分支

### 分支冲突

- 比如两个分支对同一文件同一处进行了 不同修改，那么合并时就会失败

  ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/20221125111646.png)

  - git会在文件中保存两个文件的共同信息，需要手动选择
  - 对文件修改完成，解决冲突后再次add 并commit

- 命名规范

  - master线上分支（主分支）
  - develop开发分支  
  - feature新功能分支
  - hotfix修复分支（修复bug）
  - feature hotfix用后可以删除

  ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/20221125112416.png)

## 远程仓库

### 配置ssh公钥

- 生成  ssh-keygen -t rsa
- 获取cat ~/.ssh/id_rsa.pub
- 把公钥输入网站
- 测试连接 ssh -T git@github.com

###  推送

- 添加远程仓库 git remote add xxx（名字随便起，通常用origin） xxx(ssh地址)
- git remote 查看远程仓库

- 推送 git push (-f强制覆盖) (--set-upstream 推送到远端并建立起和远端分支的关联关系) origin(远端名称) master(选择本地分支)(:远端分支名称(可选))

  - 如git push --set-upstream origin master:master进行绑定 

  - 如果已经关联，可以只用git push
  - 用git branch -vv可以查看绑定状态

​		

### 获取

- 克隆: git clone ssh地址 name（不显式指出的话会自动按照仓库名称指定）
  - 会生成名称为name的文件夹
  - 第一次获取仓库使用

- 抓取：git fetch (远端名称)（分支名称）

  - 抓取指令是将仓库里的更新都抓取到本地，不进行合并。

  - 更新的是远程分支，而不是本地正在使用的分支，还要进行合并操作。
  - git merge origin/master合并。
  - 不指定分支时会抓取所有分支。

- 拉取：git pull (远端名称)（分支名称）
  - 将修改拉取到本地并自动进行合并，等同于fetch+merge

### 解决合并冲突

- 产生：A,B修改同一文件的同一处，A先修改完后push到远程仓库，而B修改后已经提交到本地仓库，由于远程库已经发生了更改，因此要先拉取远程仓库合并后再推送，在合并过程中就会发生冲突。
- 解决冲突后再add，commit即可
  - 可以直接 git commit (不-m)然后直接wq

## ide中使用（待补充）

