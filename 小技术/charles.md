##iOS端抓包

#### HTTP抓包
打开Charles程序
查看Mac电脑的IP地址，如192.168.1.7
打开iOS设置，进入当前wifi连接，设置HTTP代理Group，将服务器填为上一步中获得的IP，即192.168.1.7，端口填8888
iOS设备打开你要抓包的app进行网络操作
Charles弹出确认框，点击Allow按钮即可


#### HTTPS抓包
- iOS端与mac接入同一局域网wifi,在连接的wifi中选择http代理-->手动 填入192.168.1.101端口8888.<br>
- 在 iOS 设备上打开这个网址 http://charlesproxy.com/getssl 安装 Charles SSL 证书.<br>
- 在Charles的工具栏Proxy->SSL Proxying Settings切换到SSL Proxying选项卡.<br>选中Enable SSL Proxying，选完先别关掉，需要在上一步的SSL选项卡的Locations表单填写要抓包的域名和端口.<br>点击Add按钮，在弹出的表单中Host填写域名，比如填api.instagram.com，Port填443

##MAC端抓包
- Charles菜单栏切换到Proxy->MAC OS X Proxy