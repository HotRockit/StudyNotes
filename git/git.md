## 1. 版本控制分类

### 1.1 本地版本控制

记录文件每次的更新，可以对每个版本做一个快照，适合个人使用

<img src="./img/本地版本控制.png" style="zoom:50%;" />

### 1.2 集中版本控制

所有的版本数据都放在服务器上，协同开发者从服务器上同步更新或者上传自己的修改，**典型代表SVN**

<img src="./img/集中版本控制.png" style="zoom:50%;" />

用户本地只有以前同步的数据，**如果不联网的话，是看不到以往的版本数据的**。而且所有的版本数据都在同一个服务器上，如果服务器坏掉了，那么之前的数据都没了，当然可以通过数据备份来解决这个问题。

### 1.3 分布式版本控制

所有的版本信息都同步到每一个用户手里，所以可以在本地查看历史版本记录，可以离线在本地提交，只需要在联网的时候push到服务器上，由于每个用户那里都拥有所有的版本数据，所以在服务器坏掉之后，都可以利用任何一个用户的数据来恢复服务器。但增加了本地存储空间的占用。**典型代表Git**

<img src="./img/分布式版本控制.png" style="zoom:50%;" />

> 拓展

两个用户都修改了代码库里面的文件A，这时如果两个人都把修改push到服务端上，两个人都会看到对方的修改的。

## 2. Git配置

```bash
git config -l 查看git所有配置
git config --system --list 查看git系统配置  文件路径：git安装目录/etc/gitconfig
git config --global --list 查看git本地配置  文件路径：本机用户目录/.gitconfig
git config --global user.name 用户名   设置用户名
git config --global user.email 邮箱    设置邮箱
```

**有时希望项目提交的时候使用别的用户名和邮箱，而且只使用一次，这时候在设置的时候就不需要--global选项，这个选项设置是以后每一次提交都使用这个配置。**

## 3. Git工作原理

Git本地有三个工作区域：**工作区**(Work Directory)，就是平时存放代码的位置，**暂存区**(State/Index)，用于临时存放改动，事实上，它只是一个文件，保存即将提交的文件列表信息，**本地仓库**(Repository或Git Directory)，就是安全存放数据的位置，这里有你所提交的所有版本的数据，其中Head指向最新放入仓库的版本。还有一个**远程仓库**(Remote Directory)，就是托管代码的服务器。

<img src="./img/git工作原理.png" style="zoom:50%;" />

<img src="./img/git原理.png" style="zoom:50%;" />

git管理的文件有三种状态

1. 已修改(Modified)
2. 已暂存(Staged)
3. 已提交(Committed)

<img src="./img/git处理流程.png" style="zoom:50%;" />

## 4. Git项目创建

1. 创建全新的仓库

   ```bash
   # 在当前目录新建一个仓库
   git init
   ```

2. 克隆远程仓库

   ```bash
   # 克隆远程仓库
   git clone 仓库地址
   ```

## 5. Git 文件操作

1. Untracked：未跟踪，此文件在目录里，但是没有加入到git仓库里面，所以不参与版本控制，通过git add将状态变为Staged
2. Unmodified：文件已经入库，但是未修改，也就是版本库里面的文件快照和本地文件内容一致，这种文件有两种去处，如果修改了，状态变为Modified，如果使用git rm 移出版本库，就成为Untracked文件
3. Modified：文件已修改，但是仅仅是修改，没有其它操作。这样的文件有两个去处，通过git add加入到staged状态，使用git checkout 则丢弃修改，返回到Unmodified状态
4. Staged：暂存状态，执行git commit 则将修改同步到库中，这时仓库里的文件和本地文件又变为一致，所以本地1文件状态又变为Unmodified状态，执行git reset HEAD filename 取消暂存，状态变为Modified

```bash
# 查看指定文件状态，如果不加文件名，表示查看所有文件状态
git status filename
# 添加指定文件到暂存区，如果使用.表示提交所有文件
git add filename 
# 提交暂存区的内容到本地仓库
git commit -m "描述内容"
```

> 忽略文件

**把不需要提交的文件排除在外**

在主目录创建".gitignore"文件，文件规则如下

1. 忽略文件中的空行以及以#开头的行
2. 可以使用linux通配符
3. 如果名称的最前面有一个！，表示例外规则，将不被忽略
4. 如果名称的最前面是一个/，表示要忽略的文件在此目录下，而子目录中的文件不忽略
5. 如果名称的最后面是一个/，表示要忽略的是此目录下该名称的子目录，而非文件（默认文件和目录都忽略）

```bash
*.txt 所有以.txt结尾的文件全部忽略
!lib.txt   把lib.txt文件除外
/tmp  表示忽略temp的上级目录，但是temp目录不会被忽略
build/ 忽略build目录下的所有文件
doc/*.txt 会忽略doc目录下的.txt文件，但是不会忽略doc子目录下的.txt文件
```

## 6. Git分支

<img src="./img/git分支.png" style="zoom:50%;" />

```bash
# 列出本地所有分支
git branch
# 列出所有远程分支
git branch -r
# 新建一个分支，但依然停留在当前分支
git branch 分支名字
# 新建一个分支，并切换到该分支
git checkout -b 分支名
# 合并指定分支到当前分支
git merge 分支名
# 删除分支
git branch -d 分支名
# 删除远程分支
git push origin --delete 分支名
git branch -dr 分支名 
```

如果一个文件在两个分支里面都被修改了，这个时候要合并两个分支就会发生冲突。

**master主分支应该很稳定，主要用来发布新版本，一般不在这个分支上工作，工作一般在dev分支上，工作完之后，可以把dev分支上的代码merge到master分支上来**

