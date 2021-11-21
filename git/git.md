# 目录

[TOC]

## git

***世界上最先进的分布式版本控制系统***

### Standing on Shoulders of Giants

站在巨人的肩膀上

###  生成ssh密钥

```properties
ssh-keygen -t rsa -C "你的GitHub注册邮箱"
```

### 设置淘宝镜像

```bash
npm config set registry https://registry.npm.taobao.org
# 或者安装cnpm
npm install -g cnpm --registry=http://registry.npm.taobao.org	#安装淘宝的cnpm 管理器
cnpm -v	# 查看cnpm版本
```

### 初始化一个git仓库

```bash
git init # 进入一个空白的仓库  初始化生成一个有关仓库配置的.git的隐藏文件
```

*![git-repo](https://cdn.qingweico.cn/blog/5ef2eb7b14195aa594c0631f.jpg)*

### 配置全局变量

```bash
git config --global user.name "name"
git config --global user.email "email"
```

### 将文件添加到暂存区

```bash
git add .     # 添加该目录下所有文件 
git add 文件   # 可以一次添加多个文件 空格隔开
```

### 将文件提交到本地仓库（master分支）

```bash
git commit -m '本次提交有关的描述'
```

### 查看当前本地仓库的状态（master分支）

```bash
git status
```

### 查看上次的修改

```bash
git diff 文件 # 只有添加到版本库的文件才可以看到文件所作的修改
```

### 版本回退

```bash
git log # 查看从近到远的提交日志
```

*![1588945995013](https://cdn.qingweico.cn/blog/5ef2eb1814195aa594c05330.png)*

commit之后的是版本号

```bash
git reset --hard HEAD^ # 回到上一个版本 HEAD代表当前的版本
```

```bash
git reset --hard 版本号前几位即可# 从当前版本回到未来的版本
```

```bash
git reflog    # 查看历史所有的命令
```

#### 对文件的所有修改必须先add到暂存区（stage） 之后使用commit提交才会对相应的文件作出修改

#### 对于两次修改同一个文件再使用add提交到暂存区会合并两个文件

### 撤销修改

- 文件还没有add到暂存区

  ```bash
  git checkout -- 文件 # 撤销在工作区对文件所作的任何修改 回到上一次的add或commit时的文件状态 
  ```

- 文件已经add到暂存区

  ```bash
  git reset HEAD 文件 # 将对暂存区的修改回退到工作区 需要重新add文件到暂存区
  ```

### 删除文件

先把文件add到暂存区然后commit到master分支

```bash
rm 文件  # 把文件从工作区中删除
```

```bash
git status # 查看本地仓库的状态 会显示哪些文件被删除
```

![1588929929326](https://cdn.qingweico.cn/blog/5ef2eb1814195aa594c0532d.png)

由于工作区的文件已被删除,暂存区此时也是干净的,而`git status` 此时会提示工作区和master本地仓库会不一致,要么重新使用命令`git rm` 把master分支中文件删掉再`git commit` 此时该文件就真的没有了,要么使用命令`git checkout -- 文件` 用版本库中的文件覆盖工作区的文件（没有加覆盖就是还原）

### 关联远程github仓库

```bash
git remote add origin git@github.com:github的用户名/仓库名.git
```

```bash
git push -u origin master # 第一次将本地master分支的内容全部推送到远程
```

```bash
git push # 关联远程仓库的master分支后git push 不用加任何参数
```

### 管理分支

```bash
git checkout -b dev # 创建并切换分支
git switch -c dev   # git最新版本提供  创建并切换分支
```

```bash
git branch dev  # 创建分支
git checkout dev# 切换分支
```

#### 查看当前分支

```bash
git branch # 带有*的就是当前分支
```

#### 切换到主分支

```bash
git checkout master
git switch master # 最新版本的切换分支命令
```

#### 合并俩个分支

```bash
git merge dev # 用于合并指定分支到当前分支
```

#### 删除dev分支

```bash
git branch -d dev
```

```bash
 git remote set-url origin https://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@github.com/qingweico/back-end-notes.git
```

