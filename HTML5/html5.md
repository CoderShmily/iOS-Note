## HTML5

### HTML: HyperText Markup Language 超文本标记语言
背景:属于移动互联网,网页的5.0版本
2014年定制完HTML5的标准,历时8年    
万维网联盟（W3C）
### 移动先行 

为什么要用:
跨平台,编写的UI界面支持所有平台的浏览器
不能完成一些单独的功能 拍照,访问相册


框架
编辑器

### 现成的HTML5框架
- sencha-touch
- phoneGap
- jQuery mobile
- bootstrap

### 手机APP的开发模式
与系统无缝兼容 开发成本高
- 原生(纯OC)  
- 纯HTML5
- 原生+HTML5

### http://www.w3school.com.cn

### 开发工具
- Android (eclipse/MyEclipse/android studio)
- iOS (Xcode)
- HTML5 
    - (eclipse/MyEclipse)
    - Dreamwave (Dreamwave/fireworks/flash 三剑客)
    - WebStrom

Web开发新时代
- Web1.0
- Web2.0
- Web3.0
    - 主流技术 HTML5+CSS3
    - HTML5亮点 Canvas/HTML5音视频/Web存储/Geolocaton/Workers多线程处理
    - CSS3亮点
    

- 标签选择器 div{}
- 类选择器 .name{}
- id选择器 #name{}
- 并列选择器  (逻辑或) 选择器1,选择器2{}
- 复合选择器 (逻辑与) 选择器1选择器2{}
- 后代选择期 选择器1 选择器2
- 直接后代选择器 选择器1 > 选择器2
- 相邻兄弟选择器 选择器1 + 选择器2
- 属性选择器 选择器1[选择器2] 选择器1[选择器2][选择器3]
选择器1[选择器2 = "xxx"];
- 伪类

CSS 书写样式
CSS有3种书写形式
行内样式：（内联样式）直接在标签的style属性中书写
```
<body style="color: red;">
```
页内样式：在本网页的style标签中书写
```
<!--可修改本文章的文本格式-->
<style>
    .test {
        color: red;
    }
    h1{
     color: red;
    }
</style>

```
外部样式：在单独的CSS文件中书写，然后在网页中用link标签引用
```
<link rel="stylesheet" href="index.css">
```

# JS (Netspace设计)
用于浏览器客户端的脚本语言
脚本语言:解析(不是编译)

数据类型:<br>
number(整数 小数)<br>
object(null)<br>
boolean<br>
string('')