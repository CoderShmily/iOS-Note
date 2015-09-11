
## 一、音频播放

1.音效播放（短时间的音频文件）

1> AudioServicesCreateSystemSoundID
2> AudioServicesPlaySystemSound

2.音乐播放（长时间的音频文件）

1> AVAudioPlayer
只能播放本地的音频文件
>MPMusicPlayerController

```objc

AVAudioPlayer对象的url属性只读,所以每播放一首歌曲都要创建一个对象,可以用字典把每个对象存储起来
AVAudioPlayer对象局部变量不行,需要放到字典数组中,或定义属性引用


```


2> AVPlayer
能播放本地、远程的音频、视频文件
基于Layer显示，得自己去编写控制面板

3> MPMoviePlayerController
能播放本地、远程的音频、视频文件
自带播放控制面板（暂停、播放、播放进度、是否要全屏）

4> MPMoviePlayerViewController
能播放本地、远程的音频、视频文件
内部是封装了MPMoviePlayerController
播放界面默认就是全屏的
如果播放功能比较简单，仅仅是简单地播放远程、本地的视频文件，建议用这个

5> DOUAudioStreamer
能播放远程、本地的音频文件
监听缓冲进度、下载速度、下载进度


## 二、视频播放

- iOS提供了MPMoviePlayerController、MPMoviePlayerViewController两个类，可以用来轻松播放视频和网络流媒体/网络音频
- 提示：网络音频同样使用此控制器播放
- YouTobe就是用MPMoviePlayerController实现的
- MPMoviePlayerViewController只能全屏播放视频
上述两个类都定义在了MediaPlayer框架中

1.音乐播放中2> 3> 4>

2.VLC

3.FFmpeg

kxmovie(可以咨询王红元)

Vitamio




#### MPMoviePlayerController支持的格式



```
MPMoviePlayerController
继承自NSObject
内部有个view可以展示视频内容
将该视图添加其他控制器的view上，即可显示视频内容
MPMoviePlayerController可以播放的视频格式包括：
H.264、MPEG-4等
支持的文件扩展名包括：avi,mkv,mov,m4v,mp4等

可以从苹果官网：http://support.apple.com/kb/HT1425下载一些用来测试的视频文件，文件都比较小

提示：MPMoviePlayerController并不支持所有的视频格式，如果要播放不支持的视频格式，需要借助第三方框架进行解码，如VLC
https://github.com/videolan/vlc

```
#### MPMoviePlayerController的使用

```
# 加载视频资源(注意，如果url为nil同样可以加载)

NSAssert(self.url, @"URL不能为空");
[[MPMoviePlayerController alloc] initWithContentURL:self.url];
// 显示
[self.view addSubview:self.moviePlayer.view];
通过设置AutoresizingMask属性可以在横竖屏转换时自动调整视图大小
// 播放
[self.moviePlayer play];
// 全屏
[self.moviePlayer setFullscreen:YES animated:YES];

# MPMoviePlayerController的播放状态是通过通知中心监听的

```

#### 常用监听通知事件
```
// 状态变化
MPMoviePlayerPlaybackStateDidChangeNotification
// 播放结束
MPMoviePlayerPlaybackDidFinishNotification
// 退出全屏
MPMoviePlayerDidExitFullscreenNotification
// 截屏完成
MPMoviePlayerThumbnailImageRequestDidFinishNotification
// 截屏方法
requestThumbnailImagesAtTimes:timeOption:

```

##三、录音

1.AVAudioRecorder


- 设置录制音频的质量

```objc
// 创建录音配置信息的字典
NSMutableDictionary *setting = [NSMutableDictionary dictionary];
// 音频格式
setting[AVFormatIDKey] = @(kAudioFormatAppleIMA4);
// 录音采样率(Hz) 如：AVSampleRateKey==8000/44100/96000（影响音频的质量）
setting[AVSampleRateKey] = @(8000.0);
// 音频通道数 1 或 2
setting[AVNumberOfChannelsKey] = @(1);
// 线性音频的位深度  8、16、24、32
setting[AVLinearPCMBitDepthKey] = @(8);
//录音的质量
setting[AVEncoderAudioQualityKey] = [NSNumber numberWithInt:AVAudioQualityHigh];
```