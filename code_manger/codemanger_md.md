# svn

####xcode和CornerStone初始化新建工程初始化步骤:
```
1. 在svn中创建好代码仓库,并设置好对应的Group和user权限
2. 在CornerStone添加HTTP Server类型的代码仓库
3. 选中代码仓库,点击右上角的check out同步working copies
   设置中的版本号一定选择1.7

```
####在working copies目录中创建xcode工程
```
1. xcode创建工程,路径放在working copies目录中
2. 在CornerStone中把xcshareddata xcuserdata开头的文件夹
   中显示?号的右键ignore,显示A的直接右键delete
3. 在xcode中设置取消断点等与上述文件夹有关的操作,回到
   CornerStone看到生成的xcshareddata xcuserdata相关的
   文件夹显示?的右键ignore.
4. commit其余的目录文件
```

####在工程路径下创建相关目录结构
```
1. 把创建好的目录拖入xcode
2. 在CornerStone中右键delete显示!号的文件
3. 回到xcode删除显示红色的文件,在xcode或者CornerStone
   中commit目录
```

####在工程中添加静态库目录(包括.a文件)
```
1. 把静态库目录拖拽到工程中,xcode中应显示A或者?,若无在
   finder确认文件是否添加
2. 用命令行进入到.a文件当前路径,手动添加svn add xx.a
3. 在CornerStone中查看文件为A状态后,commit
```
####回退版本
```
1. svn update
1. svn merge -r 8:5 .
2. svn ci -m "回退到第五个版本"
```
======================================================
# Git


####创建本地 共享版本库
```
1. 在新建'git'下执行git init --bare
2. 加入忽略文件.gitignore(没有的话github搜索.gitignore)
3. 在新建'经理'下执行git clone 'git'路径
4. 配置一个⽤户名和邮箱
    git config (—-global) user.name "why"
    git config (—-global) user.email "why@163.com"
5. 用Xcode创建工程在'经理'路径 commit到本地代码仓库
6. push到本地共享版本库('git'是看不到push的代码)
7. 在新建'张三'目录下执行git clone 'git'路径
8. 已经看到'张三'路径下的Xcode工程
```

####个人开发(命令⾏行的演示)
```
一. 初始化一个GIT的代码仓库
git init

二. 配置一个⽤户名和邮箱
git config (—-global) user.name "why"
git config (—-global) user.email "why@163.com"

三.开始开发
1.初始化项目
touch main.m
git add main.m : 将代码提交到暂缓区
git commit -m "注释" : 提及到本地代码仓库

2.查看文件的状态(git status)
 红色:新添加的⽂文件或者修改的文件,该⽂件在⼯作区,还没有被添加到暂缓区
 绿色:⽂件被添加到暂缓区中,但是没有被提交到代码仓库

3.开始开发
touch person.h person.m
git add . : 将在工作区的所有的文件一起添加到暂缓区
git commit -m "注释"
修改⽂件
修改person.h : 文件也会显⽰在⼯作区
git add . : 将修改的文件添加到暂缓区
git commit -m “将修改的内容提交到本地仓库”

4.给GIT命令起别名
git config (--global) alias.st "status"
git config (—-global) alias.ci "commit -m"

5.删除文件
git rm ⽂件名
git commit -m "删除⽂文件"

6.查看所有的版本
b90aae3817bbae3fc90a1502934c4c12eb91e5ee : git的版本,SHA1⽣生成的40位哈希值.保证每⼀个版本号的唯⼀性
git log : 退出查看,点击键盘q
git reflog
给查看版本号起别名,执行下列命令后 git lg查看版本号
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

7.版本回退
git reset --hard HEAD : 回到当前版本
git reset --hard HEAD^ : 回到上⼀一个版本
git reset —-hard HEAD^^ : 回到上上个版本
git reset —-hard HEAD~100 : 回到前100个版本
git reset —-hard 版本号前七位 : 回到特定的版本

8.下载到代码仓库、推送到共享版本库
git pull：下载远程仓库的最新信息到本地仓库
git push：将本地的仓库信息推送到远程仓库

```
