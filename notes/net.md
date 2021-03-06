# 计算机网络

### OSI 七层模型
> 应用层 表示层 会话层 传输层 网络层 数据链路层 物理层

		物理层：原始比特流传输
		数据链路层：物理寻址，同时将比特流转变为逻辑传输线路
		网络层：控制子网的运行，如逻辑编址、分组传输、路由选择
		传输层：向用户提供可靠的端到端的差错和流量控制，保证报文的正确传输

### TCP/IP 五层模型
> 应用层 传输层 网络层 数据链路层 物理层

### 常见协议及作用
- 传输层
		 TCP ：面向连接，提供可靠的传输，基于字节流
		  UDP ：提供面向事物的简单不可靠传输

- 应用层
		 HTTP ：超文本传输协议，无状态，是从 Web 服务器传送超文本到本地浏览器的传送协议
		 FTP ：文件传输协议

- 网络层
		 IP 
		  ICMP ：Internet 控制报文协议，用于在 IP 主机、路由器之间传递控制消息
		  IGMP ：Internet 网际管理协议，运行在主机和组播路由之间

- 网络接口层
		 ARP：正向地址解析协议，通过已知的 IP 寻找对应的主机的 MAC 地址
		 RARP：方向地址解析协议，通过 MAC 地址确定 IP 地址 

### 常见状态码
- 100 ：服务器已收到请求的一部分，正在等待其余部分

------------

- 200 ：服务器已成功处理了请求
- 201 ：已创建请求成功，并且服务器创建了新的资源
- 202 ：服务器已接受请求，但尚未处理

------------

- 300 ：多种选择针对请求，服务器可执行多种操作
- 301 ：永久移动请求的网页已永久移动到新的位置，即永久重定向
- 302 ：临时移动请求的网页暂时跳转到其他页面，即暂时重定向

------------

- 400 ：错误请求服务器，无法解析该请求
- 401 ：为授权请求，没有进行身份验证或验证未通过
- 403 ：禁止访问服务器，拒绝此请求
- 404 ：未找到服务器，找不到请求的网页
- 405 ：方法禁用，服务器禁用了请求中指定的方法
- 408 ：请求超时

------------

- 500 ：服务器内部错误
- 501 ：未实现，服务器不具备完成请求的功能
- 502 ：错误网关，服务器作为网关或代理，从上游服务器收到无效响应
- 503 ：服务器不可用
- 504 ：网关超时
- 505 ：服务器不支持请求中所用的 HTTP 版本

### TCP 和 UDP 的区别

		 TCP 面向连接， UDP 无连接
		 可靠性：TCP 通过握手、确认、重传保证可靠性
		 有序性：TCP 通过序列号保证消息报交付的有序性
		 速度：UDP 速度比 TCP 快
		 量级： TCP 属于重量级，头部占 20 个字节， UDP 头部占 8 个字节

### TCP 的三次握手
- #### 过程
第一次握手：建立连接时，客户端发送 SYN 包到服务器，并进入 SYN-SEND 状态，等待服务器确认
第二次握手：服务器收到 SYN 包，必须确认客户的 SYN 包，同时自己也发送一个 SYN 包，即 SYN + ACK 包，此时服务器进入 SYN-RECV 状态
第三次握手：客户端收到服务器的 SYN + ACK 包，向服务器发送确认包 ACK 包，此包发送完毕，客户端和服务端进入 ESTABLISHED 状态，完成三次握手

- #### 需要三次握手的原因
防止出现请求超时导致脏连接
> TCP 连接的核心思想：既要保证数据的可靠传输，又要提高传输的效率
TCP 连接的一方，由操作系统动态随机选取一个 32 位长的序列号（Initial Sequence Number），TCP 连接握手握的就是通信双方数据原点的序列号

> - 第一个包，即 A 给 B 的 SYN 包中途被丢，没有到达 B
	 A会周期性超时重传，直到收到 B 的确认
- 第二个包，即 B 给 A 的 SYN + ACK 包中途被丢，没有到达 A
	 B会周期性超时重传，直到收到 A 的确认
- 第三个包，即 A 给 B 发的 ACK 包中途被丢，没有到达 A
	 A发完 ACK ，单方面认为 TCP 为 ESTABLISHED 状态，而 B 显然认为 TCP 为 ACTIVE 状态
	 假定此时双方都没有数据发送，B 会周期性超时重传，直到收到 A 的确认，收到之后 B 也为 ESTABLISHED 状态，可以进行双向传包
	 假定此时 A 有数据发送， B 收到 A 的 Data + ACK ，自然会切换到 ESTABLISHED 状态，并接受 A 的Data
	 假定 B 有数据发送，数据发送不了，会一直周期性超时重传 SYN + ACK ，直到收到 A 的确认才可以发送数据


### TCP 的四次挥手
- #### 过程
		 第一次挥手， Client 发送一个 FIN ，用来关闭 Client 到 Server 的数据传输
		 第二次挥手， Server 收到 FIN 后，发送一个 ACK 给 Client ， Server 进入 CLOSE_WAIT 状态
		 第三次挥手， Server 发送一个 FIN 给 Client ，用来关闭 Server 到 Client 的数据传输， Server 进入 LAST_ACK 状态
		 第四次挥手，Client 收到 FIN 后，进入 TIME_WAIT 状态，接着发送一个 ACK 给 Server

- #### TIME_WAIT 存在的意义
> 由于TCP连接是双向的，所以在关闭连接的时候，两个方向各自都需要关闭。先发FIN包的一方执行的是主动关闭，后发送FIN包的一方执行的是被动关闭。主动关闭的一方会进入TIME_WAIT状态，并且在此状态停留2MSL时长。

确保有足够的时间让对方收到 ACK 包

- #### 出现大量 TIME_WAIT 的原因
短时间内有大量的请求

### 拥塞控制
> 防止过多的数据注入到网络中，这样可以使网络中的路由器或链路不致过载（通过拥塞窗口处理网络拥塞现象的一种机制）

四种算法：慢开始 拥塞避免 快重传 快恢复


### TCP 流量控制
> 对发送方发送速率的控制，称之为流量控制

- 如何控制
接收方每次收到数据包，可以在发送确定报文的时候，同时告诉发送方自己的缓存区还剩多少是空闲的，我们也把缓存区额剩余大小称之为接收窗口大小，用变量win来表示接收窗口的大小
发送方收到之后，便会调整自己的发送速率，也就是调整自己发送窗口的大小，当发送方收到接收窗口的大小为0时，发送方就会停止发送数据，防止出现大量丢包情况的发生

- 如何恢复
当停止发送报文时，同时会开启一个定时器，每隔一段时间就发个测试报文去询问接收方，打听是否可以继续发送数据了，如果可以，接收方就告诉他此时接收窗口的大小；如果还是0，则发送方再次刷新启动定时器

- 和**拥塞控制**的区别
拥塞控制往往是一种全局的，防止过多的数据注入到网络中，而TCP连接的端点只要不能收到对方的确认信息，猜想在网络之中发生了拥塞，但并不知道发生在何处，因此，流浪控制通常指点对点通信量的控制，是端到端的问题

### HTTP
- #### 在浏览器输入 URL ，按下回车经历的过程
		 利用 DNS 进行域名解析
		 发起 TCP 三次握手
		 建立连接后发起 http 请求
		 服务器响应请求，浏览器得到 html 代码
		 浏览器解析 html 代码，并请求 html 中的资源
		 浏览器对页面进行渲染呈现给用户

- #### HTTP 协议中常见方法
		 GET ：向指定的资源发出“显示”请求，使用 get 方法应该只用在读取数据
		 POST ：向指定资源提交数据，请求服务器进行处理。数据被包含在请求体中
		 HEAD ：与 get 方法一样，都是向服务器发出指定资源的请求，只不过服务器将不传回资源的本文部分。
		 PUT ：向指定资源位置上传其最新内容
		 DELETE ：请求服务器删除 REQUEST_URL 所标识的资源
		 TRACE ：回显服务器收到的请求，主要用于测试或诊断
		 OPTIONS ：这个方法可使服务器传回该资源所支持的所有HTTP请求方法。用'*'来代替资源名称，向Web服务器发送OPTIONS请求，可以测试服务器功能是否正常运作。

- #### GET 和 POST 的区别
		 GET 将信息放在 URL 上，POST 放在报文体中
		 GET 请求符合幂等性和安全性
		 GET 可以被缓存、被存储

- #### session 和 cokkie 的区别
		 Cookie 数据存放在客户的浏览器上， Session 存放在服务器上
		 Session 相对于 Cookie 来说更安全
		 考虑减轻服务器的负担，应当使用 Cookie
		 如果 Cookie 被禁用可以考虑 URL 重写

### HTTPS
> 在应用层和传输层之间加了 SSL 层
SSL 采用身份验证和数据加密保证网络通信的安全和数据的完整性

- HTTP 存在的问题
		 使用明文进行通信，内容可能被窃取
		 不验证通信双方的身份，通信方的身份可能遭遇伪装
		 无法验证报文的完整性，报文可能会遭遇篡改

- 两种加密方式
		 对称密钥加密：加密和解密使用的同一密钥
		 非对称加密：公开密钥所有人都可以获得，通信发送方获得接收方的公开密钥之后，就可以使用公开密钥进行加密，接收方收到通信内容后使用私有密钥解密。

- HTTPS 采用的加密方式
HTTPS 采用混合的加密机制，使用非对称密钥加密用于传输对称密钥来保证传输过程的安全性，之后使用对称密钥加密进行通信来保证通信过程的效率

- HTTPS 的缺点
		 需要进行加密解密等过程，因此速度会更慢
		 需要支付证书授权高昂的费用


### HTTP 2.0
> HTTP/2.0 将报文分成 HEADERS 帧和 DATA 帧，它们都是二进制格式的。
在通信过程中，只会有一个 TCP 连接存在，它承载了任意数量的双向数据流（Stream）

- 优势
HTTP/1.1 的首部带有大量信息，而且每次都要重复发送
HTTP/2.0 要求客户端和服务器同时维护和更新一个包含之前见过的首部字段表，从而避免了重复传输；不仅如此，HTTP/2.0 也使用 Huffman 编码对首部字段进行压缩








