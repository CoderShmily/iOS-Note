# CocoaPods

---
```
安装cocoapods

sudo gem install cocoapods
 
建议先更新下gem
sudo gem update --system

gem sources --remove https://rubygems.org/
gem sources -a http://ruby.taobao.org/
gem sources -l

pod repo remove master 
pod repo add master https://gitcafe.com/akuandev/Specs.git
pod repo update

// du -sh *
pod setup

pod --version

卸载CocoaPods
sudo gem uninstall cocoapods
```
```
查找框架
pod search AFNetworking

Podfile
platform :ios, “8.0” 		8.0代表兼容的ios版本
pod ‘Reachability’ 		框架名用’’ 或者””都行  
pod 'JSONKit', '~> 1.4' 	不指定版本,默认下载最新版

初次下载使用
pod install --no-repo-update

修改后更新用
pod update --no-repo-update

更新框架
pod repo update
```
```

如果提示关于gem的东西找不到:
sudo gem uninstall --all
sudo gem install cocoapods
```