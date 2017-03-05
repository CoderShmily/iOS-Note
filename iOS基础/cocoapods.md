# CocoaPods

---

```objc
安装cocoapods


 
建议先更新下gem
sudo gem update --system

gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
// https://gems.ruby-china.org/
gem sources -l

// 新版本要有问题试试

//  sudo gem install cocoapods应该会提示下错误
// ERROR:  While executing gem ... (Errno::EPERM)   Operation not permitted - /usr/bin/update_rubygems
试试 gem update -n /usr/local/bin --system

sudo gem install -n /usr/local/bin cocoapods

pod repo remove master 
pod repo add master https://gitcafe.com/akuandev/Specs.git
pod repo update

// du -sh * 查看下载进度
pod setup

pod --version

卸载CocoaPods
sudo gem uninstall cocoapods
```

```objc

查找框架
pod search AFNetworking

// 工程目录下创建Podfile
touch Podfile

// 内容格式
platform :ios, "8.0" 		8.0代表兼容的ios版本 不指定会用最新的框架
pod 'SVProgressHUD' 		框架名用'' 或者""都行  
pod 'JSONKit', '~> 1.4' 	不指定版本,默认下载最新版

platform :ios, "8.0"        
pod 'Masonry'

初次下载使用
pod install --no-repo-update

修改后更新用
pod update --no-repo-update

更新本地框架库
pod repo update

不集成到工程，只下载
pod install --no-integrate
```
<a href="#1">查找框架</a>  
<a name="1">锚点1跳转处</a>
```objc
1. 如果提示关于gem的东西找不到:
sudo gem uninstall --all
sudo gem install cocoapods

2. brew update失败可以用 /usr/local/bin/brew update --force


3. 执行sudo gem install cocoapods的时候提示错误
ERROR:  SSL verification error at depth 1: unable to get local issuer certificate (20)
ERROR:  You must add /C=IL/O=StartCom Ltd./OU=Secure Digital Certificate Signing/CN=StartCom Certification Authority to your local trusted store
ERROR:  SSL verification error at depth 2: self signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=US/O=GeoTrust Inc./CN=GeoTrust Global CA)

其实只要是使用gem的都会又ssl证书问题。 下面说下用的新路径
gem sources --remove https://ruby.taobao.org/
gem sources -a https://gems.ruby-china.org/
gem sources -l

sudo gem install -n /usr/local/bin cocoapods

然后就会执行成功了，不过此刻会卡住，其实是在下载文件，你可以用下面的方法查看下载的文件大小。
cd ~/.cocoapods/
du -sh *
到此就可以说是解决了这个问题了。
```
