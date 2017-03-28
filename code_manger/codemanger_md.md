# svn

####xcode和CornerStone初始化新建工程初始化步骤:
```objc
1. 在svn中创建好代码仓库,并设置好对应的Group和user权限
2. 在CornerStone添加HTTP Server类型的代码仓库
3. 选中代码仓库,点击右上角的check out同步working copies
   设置中的版本号一定选择1.7 (最高版本)

```
####在working copies目录中创建xcode工程
```objc
1. xcode创建工程,路径放在working copies目录中
2. 在CornerStone中把xcshareddata xcuserdata开头的文件夹
   中显示?号的右键ignore,显示A的直接右键delete
3. 在xcode中设置取消断点等与上述文件夹有关的操作,回到
   CornerStone看到生成的xcshareddata xcuserdata相关的
   文件夹显示?的右键ignore.
4. commit其余的目录文件
``` 

####在工程路径下创建相关目录结构
```objc
1. 把创建好的目录拖入xcode
2. 在CornerStone中右键delete显示!号的文件
3. 回到xcode删除显示红色的文件,在xcode或者CornerStone
   中commit目录
```

####在工程中添加静态库目录(包括.a文件)
```objc
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
---
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

####个人开发(命令行的演示)
```
一、 配置(可以跳过)
1. 配置一个⽤户名和邮箱
git config (—-global) user.name "why"
git config (—-global) user.email "why@163.com"

2. 给GIT命令起别名
git config (--global) alias.st "status"
git config (—-global) alias.ci "commit -m"

二、 开始开发
1.初始化项目,在工程目录下执行git初始化
git init 

2.查看文件的状态(git status)
 红色:新添加的⽂文件或者修改的文件,该⽂件在⼯作区,还没有被添加到暂缓区
 绿色:⽂件被添加到暂缓区中,但是没有被提交到代码仓库

3.开始开发
git add main.m : 将指定文件提交到暂缓区
// git add . : 将在工作区的所有的文件一起添加到暂缓区
git commit -m "注释"    将修改的内容提交到本地仓库（会有对应的版本号）

4.配置远程仓库
git remote 查看远程仓库
git remote add origin https://xxx.git 添加远程仓库
git push origin master   提交到远程的master分支,继续输入账户，密码，用SSH方式时，要添加公钥私钥

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

9. release打包（tag），tag只是一条记录，不是版本号
git tag 查看打包
git tag -a '0.0.1' -m "打标签" // git tag '0.0.1'
git push --tags 把本地所有的tag都提交到远程仓库
// git push origin 0.0.1 指定的tag提交到远程仓库
git tag -d 0.0.1 删除指定的tag
git push :0.0.1 删除远程的指定tag

10. git push 403 错误 原因：本地缓存了用户名和密码
重新设置用户名和密码
git remote set-url origin https://用户名@XXXtest.git 会让你输入这次对应的用户名的密码

11. 本地私有库实现方案
创建本地代码 git初始化
pod spec create 库名字
修改spec文件 （  s.summary 字符串长度小于 s.description， license 删除多余， soure字段对应 ：git => "" ， source_files 对应的classes目录与库相对应)
进入项目pod init 
修改 Podfile  pod '库名字', :path => '找到库对应的本地spec路径'
pod install

12. 本地私有库实现方案优化
1. pod lib create XXX
2. 拖入代码到 Classes文件夹, 并删除ReplaceMe.m，执行pod install
3. 在宿主工程中, 直接测试, 没有问题后, 直接由外界以本地库的形式使用

13. 远程私有库

1. 创建私有库 CSPrivateSpec
2. pod repo add CSPrivateSpec https://git.coding.net/codershmily/CSPrivateSpec.git 输入用户密码
3. 如果提示Permission denied (publickey)， ssh-keygen -t rsa -C "953442978@qq.com" 将新生成的公钥添加到https://coding.net
4. mkdir Test  cd Test  pod lib create myLib 输入相关的参数
5. 添加需要的库文件到相应文件夹，替换Test/myLib/myLib/Classes/ 目录下的文件
6. 创建lib私有库，不要添加文件
6. pod lib lint 本地验证 （根据提示修改myLib.podspec的s.description长度大于s.summary，把lib信息添加到s.homepage, s.source)
7. 修改完 git add .    git commit -m "XX"
8. git remote add origin https://git.coding.net/codershmily/lib.git
9. git push origin master 
10. git tag '0.1.0' tag号与myLib.podspec 的s.version要一致
11. git push --tags
12. pod spec lint 远程验证，根据错误修改直到成功
13. pod repo push CSPrivateSpec myLib.podspec
14. pod search myLib 就可以找到了显示在CSPrivateSpec repo
15. Podfile 开始处添加
source 'https://git.coding.net/codershmily/CSPrivateSpec.git' 
source 'https://github.com/CocoaPods/Specs.git' 为了添加其他人的库比如AFNetworking
```



## Github 创建一个iOS-的repository以后 
#### 1.create a new repository on the command line
```
echo "# iOS-" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:CoderShmily/iOS-.git
git push -u origin master
```
#### 2.push an existing repository from the command line
```
git remote add origin git@github.com:CoderShmily/iOS-.git
git push -u origin master
```
