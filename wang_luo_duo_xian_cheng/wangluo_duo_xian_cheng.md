网络笔记

socket连接和http连接的区别
简单说，你浏览的网页（网址以http://开头)都是http协议传输到你的浏览器的, 而http是基于socket之上的。socket是一套完成tcp，udp协议的接口。
HTTP协议：简单对象访问协议，对应于应用层  ，HTTP协议是基于TCP连接的

tcp协议：    对应于传输层

ip协议：     对应于网络层 
TCP/IP是传输层协议，主要解决数据如何在网络中传输；而HTTP是应用层协议，主要解决如何包装数据。

Socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口（API），通过Socket，我们才能使用TCP/IP协议。

http连接：http连接就是所谓的短连接，即客户端向服务器端发送一次请求，服务器端响应后连接即会断掉；

socket连接：socket连接就是所谓的长连接，理论上客户端和服务器端一旦建立起连接将不会主动断掉；但是由于各种环境因素可能会是连接断开，比如说：服务器端或客户端主机down了，网络故障，或者两者之间长时间没有数据传输，网络防火墙可能会断开该连接以释放网络资源。所以当一个socket连接中没有数据的传输，那么为了维持连接需要发送心跳消息~~具体心跳消息格式是开发者自己定义的

我们已经知道网络中的进程是通过socket来通信的，那什么是socket呢？socket起源于Unix，而Unix/Linux基本哲学之一就是“一切皆文件”，都可以用“打开open –> 读写write/read –> 关闭close”模式来操作。我的理解就是Socket就是该模式的一个实现，socket即是一种特殊的文件，一些socket函数就是对其进行的操作（读/写IO、打开、关闭），这些函数我们在后面进行介绍。
 
Socket连接与HTTP连接

我们在传输数据时，可以只使用（传输层）TCP/IP协议，但是那样的话，如果没有应用层，便无法识别数据内容，如果想要使传输的数据有意义，则必须使用到应用层协议，应用层协议有很多，比如HTTP、FTP、TELNET等，也可以自己定义应用层协议。WEB使用HTTP协议作应用层协议，以封装HTTP文本信息，然后使用TCP/IP做传输层协议将它发到网络上。
1)Socket是一个针对TCP和UDP编程的接口，你可以借助它建立TCP连接等等。而TCP和UDP协议属于传输层 。
  而http是个应用层的协议，它实际上也建立在TCP协议之上。 

 (HTTP是轿车，提供了封装或者显示数据的具体形式；Socket是发动机，提供了网络通信的能力。)

 2）Socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口（API），通过Socket，我们才能使用TCP/IP协议。Socket的出现只是使得程序员更方便地使用TCP/IP协议栈而已，是对TCP/IP协议的抽象，从而形成了我们知道的一些最基本的函数接口。

下面是一些的重要的概念，特在此做摘抄和总结。

一。什么是TCP连接的三次握手

第一次握手：客户端发送syn包(syn=j)到服务器，并进入SYN_SEND状态，等待服务器确认；
第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN_RECV状态；
第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=k+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。

握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始传送数据。理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连接之前，TCP 连接都将被一直保持下去。断开连接时服务器和客户端均可以主动发起断开TCP连接的请求，断开过程需要经过“四次握手”（过程就不细写了，就是服务器和客户端交互，最终确定断开）

二。利用Socket建立网络连接的步骤

建立Socket连接至少需要一对套接字，其中一个运行于客户端，称为ClientSocket ，另一个运行于服务器端，称为ServerSocket 。

套接字之间的连接过程分为三个步骤：服务器监听，客户端请求，连接确认。

1。服务器监听：服务器端套接字并不定位具体的客户端套接字，而是处于等待连接的状态，实时监控网络状态，等待客户端的连接请求。

2。客户端请求：指客户端的套接字提出连接请求，要连接的目标是服务器端的套接字。为此，客户端的套接字必须首先描述它要连接的服务器的套接字，指出服务器端套接字的地址和端口号，然后就向服务器端套接字提出连接请求。

3。连接确认：当服务器端套接字监听到或者说接收到客户端套接字的连接请求时，就响应客户端套接字的请求，建立一个新的线程，把服务器端套接字的描述发给客户端，一旦客户端确认了此描述，双方就正式建立连接。而服务器端套接字继续处于监听状态，继续接收其他客户端套接字的连接请求。

三。HTTP链接的特点

HTTP协议即超文本传送协议(Hypertext Transfer Protocol )，是Web联网的基础，也是手机联网常用的协议之一，HTTP协议是建立在TCP协议之上的一种应用。

HTTP连接最显著的特点是客户端发送的每次请求都需要服务器回送响应，在请求结束后，会主动释放连接。从建立连接到关闭连接的过程称为“一次连接”。

四。TCP和UDP的区别

1。TCP是面向链接的，虽然说网络的不安全不稳定特性决定了多少次握手都不能保证连接的可靠性，但TCP的三次握手在最低限度上（实际上也很大程度上保证了）保证了连接的可靠性；而UDP不是面向连接的，UDP传送数据前并不与对方建立连接，对接收到的数据也不发送确认信号，发送端不知道数据是否会正确接收，当然也不用重发，所以说UDP是无连接的、不可靠的一种数据传输协议。

2。也正由于1所说的特点，使得UDP的开销更小数据传输速率更高，因为不必进行收发数据的确认，所以UDP的实时性更好。

 


```objc
// 3.1设置请求头, 告诉系统从什么地方开始下载
// 3.1.1获取已经下载数据的大小
NSFileManager *manager = [NSFileManager defaultManager];
NSDictionary *dict = [manager attributesOfItemAtPath:[@"minion_02.mp4" cacheDir] error:nil];
NSInteger fileSize = [dict[NSFileSize] integerValue];
// 3.1.2根据已经下载的大小生成请求头参数值
// 只要设置HTTP请求头的Range属性, 就可以实现从指定位置开始下载
NSString *range = [NSString stringWithFormat:@"bytes=%zd-", fileSize];
[request setValue:range forHTTPHeaderField:@"Range”];
// 2.创建REQUEST
// 注意: 如果需要设置请求体或者其他请求参数, 必须使用NSURLRequest的子类
NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
// 注意点: 必须大写
request.HTTPMethod = @"POST";
// 注意: 只要是POST请求, 系统内部会自动添加?
request.HTTPBody = [@"username=520it&pwd=520it&type=JSON" dataUsingEncoding:NSUTF8StringEncoding];
// 设置超时时间
request.timeoutInterval = 10;
// 设置请求头
[request setValue:@"iOS 9.0" forHTTPHeaderField:@"User-Agent”];

// 3.设置请求头
// 设置请求头的目的: 告诉服务器是上传文件, 而不是传递普通参数
NSString *field = [NSString stringWithFormat:@"multipart/form-data; boundary=%@", kBoundary];
[request setValue:field forHTTPHeaderField:@"Content-Type”];


// 注意: 如果将数据放到request的HTTPBody中, 会被uploadTaskWithRequest方法忽略
// 所以需要上传的数据, 需要放到fromData:中
//    request.HTTPBody = data;

/***********************************************************/


// 设置请求头
[request setValue:[NSString stringWithFormat:@"%zd", data.length] forHTTPHeaderField:@"Content-Length"];
NSURLSessionUploadTask *task = [session uploadTaskWithRequest:request fromData:data completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
    NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
}];

HTTP协议规定：1个完整的HTTP响应中包含以下内容
请求头：包含了对客户端的环境描述、客户端请求信息等
GET /minion.png HTTP/1.1   // 包含了请求方法、请求资源路径、HTTP协议版本
Host: 120.25.226.186:32812   // 客户端想访问的服务器主机地址
User-Agent: Mozilla/5.0  // 客户端的类型，客户端的软件环境
Accept: text/html, */*   // 客户端所能接收的数据类型
Accept-Language: zh-cn   // 客户端的语言环境
Accept-Encoding: gzip   // 客户端支持的数据压缩格式
请求体：客户端发给服务器的具体数据，比如文件数据(POST请求才会有)
响应头：包含了对服务器的描述、对返回数据的描述
HTTP/1.1 200 OK            // 包含了HTTP协议版本、状态码、状态英文名称
Server: Apache-Coyote/1.1   // 服务器的类型
Content-Type: image/jpeg   // 返回数据的类型
Content-Length: 56811   // 返回数据的长度
Date: Mon, 23 Jun 2014 12:54:52 GMT  // 响应的时间
响应体：服务器返回给客户端的具体数据，比如文件数据

```