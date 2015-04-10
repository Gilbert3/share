git的配置文件有三级
项目级
```
.git/config
git config user.name ‘JianYong'
```
用户级
```
~/.gitconfig
git config —global user.name ‘JianYong'
```
系统级
```
/etc/gitconfig
git config —system user.name ‘JianYong’
```
需要配置自己的私钥. 在/home/***/.ssh/id_rsa文件, 此文件的权限必须是0600.
初始化的git配置
用户名&邮箱
```
user.name
user.email
```
是否忽略文件权限变化
```
core.filemode
```
编辑器的配置
```
编辑器 core.editor 
合并工具 merge.tool
```
diff工具 
```
git config --global diff.tool vimdiff
git config --global difftool.prompt No
```
着色
```
git config --global  color.ui true
git branch/diff/status 着色
git config --global color.status auto
git config --global color.diff auto
git config --global color.branch auto
```
git忽略文件
```
git config core.excludesfile
.gitignore
.git/info/exclude
```

1、如果在服务器已经部署你的公钥了， 那么你可以尝试从服务器上checkout代码了
```
git clone git@***.git
git clone git@***.git  dirname // [将代码检出到dirname目录下]
```
2、我检出的代码是在master(主干)上的, 如何去创建（进入）自己的分支?
```
git branch 分支名    git checkout 你想要切换的分支名
git checkout –b  new_branch  创建新的分支new_branch并切换到new_branch
```
3、合并其他远程分支上的改动
```
git  fetch  获取远程所有有改动的分支
git merge origin/相应分支
git pull origin 相应分支
```
  4.如何完成分支间的切换
  首先保证当前分支上没有改动， 才能进行切换
```
git  checkout  想要切换的分支
```
  5.我想知道本次就那些文件进行过修改（我想确定分支上有没有修改）
```
git status
```
6.我的分支上有改动，但是我还想切换分支， 但我又不想提交我的改动
  git stash 将改动储藏起来
```
1. 想要看我的储藏列表 git stash list
2. 想要应用储藏的内容 git stash apply –index(索引位置)
3. 想要查看我具体储藏了那些内容 git stash list -p
4. 删除储藏列表  gitstash drop  储藏名
5. 想要查看此次储藏的涉及到那些文件？ git stash show stash:{0}
```
 查看名为stash{0}缓存的文件列表

7.我修改之后，想放弃此次修改
```
git checkout xx_filename  
```
8、我想对比一下， 看看我到底进行了那些操作？
```
git diff // 没有用git add
git difff origin/master  #跟远程的master对比
git diff origin/***_branch #跟远程***_branch对比
git diff –cached // 已经使用过git add
```
15.将自己的改动推到远程
```
git push origin 自己的分支名
```
16.如果在git merge或git pull 时， 发生冲突， 如果这些冲突是自己的原因产生的， 那么自己解决， 但是如果不是自己的原因， 那可以查看文件的日志(命令如下), 找到冲突的文件曾被谁更改过， 之后一起解决冲突
```
git log [–p ] 文件名  【-p可省略， 加上-p显示的是详细信息】
```
    还有一种就是， 如果冲突的文件中没有新内容， 并且你可以找到一个最新的版本
    例如： index.php在master上面是最新的， 而在本地冲突了， 但是你不想解决这个冲突， 可以执行以下操作（慎用， 执行完之后你自己的代码没有保留也没有备份）
```
git add index.php
git reset HEAD index.php 【慎用】
git checkout origin/master index.php   这样以后index.php就跟远程的master分支上一致了。
```
16(1): git reset 回退以前某个版本
```
git reset是指将当前head的内容重置，不会留log信息。
git reset HEAD filename  从暂存区中移除文件
git reset —hard origin/master 将代码重置到origin/master
git reset –hard HEAD~3  会将最新的3次提交全部重置，就像没有提交过一样。
git reset –hard commit (38679ed) 回退到 38678ed 版本
根据–soft –mixed –hard，会对working tree和index和HEAD进行重置:
git reset –mixed：此为默认方式，不带任何参数的git reset，即时这种方式，它回退到某个版本，只保留源码，回退commit和index信息
git reset –soft：回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可
git reset –hard：彻底回退到某个版本，本地的源码也会变为上一个版本的内容
```

> 开发过程中的代码 --mixed 上一次的代码还在, git diff 查看 本地缓冲区 --soft  上一次的代码还在, git
> diff --cached 查看 本地代码库 --hard 上一次的代码不在, 不能恢复

17.每天合并master分支， 合并其他成员的修改， 对于大的项目新建一个分支开发
18.想让同事帮你的时候
```
git checkout –b 你的本地分支名 origin/你的远程分支
```
19. 比较两个分支的差异
```
git diff master...version_no/branch (3个点可以比较任意不同)
git diff A..B   B里面有而A没有的改变(2个点)
```
20. 将git commit 编辑器改为vim
```
 1.  编辑.git/config文件。在core中添加editor = vim
```
如此以后在使用git的时候就自动使用vim作为编辑器
2.  除去git，系统中有其他的也会调用编辑器，可以使用一下命令来全局配置编辑器的选择：
```
update-alternatives --config editor

git config --global core.editor vim
sudo update-alternatives --config editor
```
21. 忽略在库中的文件的改变
```
git update-index --assume-unchanged database.php
```
22. 获取当前版本号
```
git log --pretty -1 | awk '/^commit/{print $2}'
```
Git Push默认设置
```
git push.default
```


•参考文件：


> - [progit][progit] – 
> - [看日记学 git][看日记学 git] 
> - http://blogimg.chinaunix.net/blog/upfile2/100827121906.pdf
