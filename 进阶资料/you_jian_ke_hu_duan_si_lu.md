# 邮件客户端思路

iOS mailcore2 学习 （一）


公司移动办公ios版需要加入邮件功能，思考后决定基于mailcore2来完成。

   先简单了解下pop3和smtp协议：

       pop3协议规定了邮件客户端与pop3服务器之间的通信规则。一共定义了12条pop3命令供邮件客户端来检索和获取电子邮箱中的邮件信息。下面简单描述下这些命令（ <SP>代表空格，<CRLF>代表回车和换行）。

       user<SP>username<CRLF>  邮件客户端与pop3服务器建立连接后的第一条命令，向服务器通知帐 户名，不过，貌似任意用户名，服务器都会返回＋OK。

       pass<SP>password<CRLF>  用于向服务器传递密码。

       apop<SP>name,digest<CRLF> 用于替换user和pass命令，以md5数字摘要的形式向pop3邮件服务器提交帐户密码。

       stat<CRLF>    查询邮箱中的统计信息，＋OK 21 5678952

       uidl<SP>index<CRLF>    查询第index封邮件的唯一标识号，+OK 562481

       list<SP>(index)<CRLF>  查询第index或所有邮件的信息，返回邮件index size的信息。

       retr<SP>index<CRLF>      获取指定index邮件的全部信息。整封完整邮件。

       dele<SP>index<CRLF>    给第index封邮件设置删除标识。

       rset<CRLF>   清除所有邮件的删除标识。

       top<SP>index<SP>n<CRLF>  获取第index封邮件的邮件头＋n行邮件体的信息。

       noop<CRLF>  检测pop3客户端与pop3服务器的连接情况。

       quit<CRLF>  结束邮件接收过程。



   mailcore2与pop3协议相关的API：

       MCOPOPSession封装了pop3客户端与pop3服务器之间的通讯底层细节。

- (MCOPOPOperation *)checkAccountOperation 封装了命令user和pass，完成向邮件服务器进行认证的过程

- (MCOPOPOperation *)deleteMessagesOperationWithIndexes:(MCOIndexSet *)indexes 封装了命令dele，给邮件设置删除标识。

- (MCOPOPOperation *)disconnectOperation 封装了命令quit，结束邮件接收过程。

     - (MCOPOPFetchHeaderOperation *)fetchHeaderOperationWithIndex:(unsigned int)index 封装了命令top，n＝0的情况。

     - (MCOPOPFetchMessageOperation *)fetchMessageOperationWithIndex:(unsigned int)index 封装了命令retr，获取指定邮件的全部信息。

- (MCOPOPFetchMessagesOperation *)fetchMessagesOperation 封装了命令stat，list和uidl，返回所有邮件的index，size，和uidl信息。



   smtp协议规定了邮件客户端与smtp服务器之间，以及两台smtp服务器之间的通讯规则。虽然smtp协议一共定义了18条命令，但是通常只需用到其中的6条命令，

   ehlo<SP>domain<CRLF> domain表示邮件发送者的主机名。邮件服务器返回的信息中包含支持的auth方式。

   auth<SP>(para)<CRLF> 指定认证方式。接下来要输入base64编码的username和pwd了。

   mail<SP>FROM:address<CRLF> 指定发件人的邮箱地址

   rept<SP>TO:address<CRLF> 指定收件人的邮箱地址

   data<CRLF> 表示数据区域开始，这个命令后面的所有内容都将被当成邮件内容，直到遇到"<CRLF>.<CRLF>",则表示邮件内容结束。

   quit<CRLF> 结束会话。

   mailcore2与smtp协议相关的api：

       MCOSMTPSession 封装了邮件客户端与smtp服务器之间的通讯规则：

- (MCOSMTPOperation *)checkAccountOperationWithFrom:(MCOAddress *)from 封装了ehlo与auth命令，完成与smtp服务器的认证。

- (MCOSMTPSendOperation *)sendOperationWithData:(NSData *)messageData 封装了data命令，messagedata就是data命令后面的内容。收件人信息从messageData中提取。

- (MCOSMTPSendOperation *)sendOperationWithData:(NSData *)messageData from:(MCOAddress *)from recipients:(NSArray *)recipients 封装了data命令，指定收件人。



   接下来就是关于邮件内容的解析了，首先得了解邮件的格式。

   邮件内容的基本格式和细节由RFC822文档和MIME协议定义。

   RFC邮件格式包含邮件头和邮件体两个主要的部分，邮件体部分只能表述一段普通的文本，无法表达图片等二进制数据，且当收到“.”符号的单行时，“<CRLF>.<CRLF> ”后面的数据会被丢弃掉。

   但是可以通过某种编码方式将二进制数据编码成可打印的ascii字符后再作为RFC822格式的内容。邮件阅读程序收到这种邮件后解码成二进制数据。这样就巧妙的用RFC格式文档传递二进制数据了。

   但是邮件阅读程序必须知道二进制数据编码成ascii的编码方式以及这些二进制数据在邮件内容中的起始位置。这些问题催生了mime协议的诞生，mime定义了如何在邮件体部分表达出丰富多样的数据内容。

   MIME邮件就是采用了MIME协议的邮件，它新增了一些自己的头字段，MIME－version指定mime的版本，content－type指定邮件体的mime类型及每段的分隔符，解决了二进制数据在邮件内容中的起始位置问题，content-transfer-encoding告知邮件阅读程序二进制数据编码方法。

   MIME邮件由mime消息组成。mime消息也包含头和体两部分，头和体之间用空格分开，多个mime消息体之间采用某种分隔符标识进行分隔，它们之间的组合类型和分隔符标识由它门的组合mime消息的头来描述。

   mime消息头：

      1.content－type    字段内容以“主类型／子类型;boundarty="分隔符”的形式出现。主类型：text,image,multipart等，multipart主类型表示mime组合消息。mime组合消息有三种组合关系：混合，关联，选择，混合（multipart/mixed）表示消息体中有文本，附件等组成。关联（multipart／related）表示关联组合类型，如html代码内嵌引用的图片等资源。选择类型（multipart/alternative）表示该邮件内容既可以用纯文本表示，也可以用html格式表示，如果邮件阅读程序不支持html文本的话，就可以用纯文本表示。

    2.content-transfer-encoding     表示二进制内容编码的方式。

   3.content－disposition     指定邮件阅读程序处理数据内容的方式。inline直接处理，attachment当附件处理，

   4.content-ID    出现在消息类型为"multipart/related"组合消息内，表示该消息的一个唯一标识号，该标识号会在html格式正文中被引用，content－id：451265.jpg,则html正文中的引用方式为：<img src＝“cid：451265.jpg” >

   5.content-location    为内嵌资源设置一个URI地址，html正文中可以直接使用该地址。

   6.content－base，为内嵌资源设置一个基准路径。



   了解了邮件格式后，下面就可以开始正式学习使用mailcore2来构建邮件客户端了。