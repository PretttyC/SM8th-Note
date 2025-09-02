# Web和HTTP

Web（World Wide Web，万维网）的应用层协议是HTTP(HyperText Transfer Protocol, 超文本传输协议)。  

HTTP有两部分组成：HTTP客户端和HTTP服务端。

```plantuml
@startmindmap
* 术语

** Web页面(Web page)或Web文档
***_:\
Web页面由Web对象(object)组成。
多数Web页面由一个HTML基本文件以及其它引用对象组成。;

** Web对象(object)
***_:\
如HTML文件、JPEG图片、javascript文件、css样式表文件、视频文件等。
每个对象通过URL来进行引用。;

** URL
***:\
URL格式：
===\tprotocol :// hostname[:port] / path / [;parameters][?query]#fragment
===\t协议://主机:端口/路径名?查询参数#锚点
如：URL http://www.someSchool.edu/someDepartment/picture.gif中，www.someSchool.edu是主机名，/someDepartment/picture.gif是路径名;

** Web浏览器
***_ Web浏览器实现了HTTP的客户端

** Web服务器
***_ Web服务器实现了HTTP的服务端。Web服务器用于存储Web对象，每个对象通过URL寻址。

** RTT(Round-Trip Time, 往返时间)
***_ 一个短分组从客户端到服务器再返回客户端所花费的时间

* HTTP的版本
** HTTP/1.0
***_ HTTP的初始版本，[RFC 1945]
** HTTP/1.1
***_ 2020年为止，大部份使用HTTP/1.1，[RFC 7230]
** HTTP/2
***_ [RFC 7540]
** HTTP/3
***_ [RFC 9114]

* HTTP使用的传输层协议
** HTTP/1.0、HTTP/1.1与HTTP/2基于TCP协议
** HTTP/3使用基于UDP的QUIC协议

@endmindmap
```

## URI、URL、URN

### URI
URI（Uniform Resource Identifier，统一资源标识符）是一个总称，相当于“资源的身份证号码+住址”。它的作用是唯一标识某个资源，比如一张图片、一本书或一个网页。  

URI组成：

![URI组成.png](../images/URI组成.png)

authority由3部分组成：  
* User info：可选，可能包含用户名和密码（可选），由冒号 (:) 分隔，后跟 @ 符号。
* Host：域名或者IP地址。
* Port：指向域或 IP 地址的端口号。

URI举例：
* mailto:hey.john@website.com
* file:website.com/pathtofile/intro.pdf
* https://john@website.com:5052/path/to/page

URI包含两种具体形式：URL(Uniform Resource Locator, 统一资源定位符) 和URN(Uniform Resource Name, 统一资源名称)。  

三者关系：  
* URI = URL + URN：URI是广义的“资源标识”，包含定位（URL）和命名（URN）两种方式。
* URL和URN互补：URL解决“怎么找到资源”，URN解决“怎么唯一标识资源”。
* 实际使用：日常说的“网址”通常是URL，但像磁力链接（如 magnet:?xt=urn:btih:...）则是URN的一种应用。

![URI_URL_URN.png](../images/URI_URL_URN.png)

### URL
URL（Uniform Resource Locator，统一资源定位符）是URI的子集，相当于“资源的地址”。它告诉你去哪里能找到这个资源，比如一个具体的网址。  

URL标准格式：  

![URL组成.png](../images/URL组成.png)

URL完整格式：  

![URL完整组成.png](../images/URL完整组成.png)

* scheme：通信协议，包括HTTP、HTTPS、FTP、MAILTO、IRC、FILE等。
* authority包含两部分：  
 
  host：主机，服务器（计算机）域名系统（DNS），主机名或IP地址。  
  port：端口号，可选，省略时是默认端口，如HTTP的默认端口是80，HTTPS 的默认端口是 443。  
* path：路径，用来表示主机上的一个目录或者文件地址。
* query：查询的参数，多个参数由 “&” 隔开，每个参数的名和值用“=”号隔开。如：age=18。
* fragment：信息片段，在给定页面内跳转的目标。

URL举例：  
* http://website.com/path/to/page
* https://website.com/path/here?name=html#head
* http://website.com:5500/path/here

### URN
URN（Uniform Resource Name，统一资源名称）也是URI的子集，相当于“资源的身份证号码”。它只负责给资源一个唯一的名字，不关心资源的位置。  

URN组成：  

![URN组成.png](../images/URN组成.png)

* scheme：方案规范（**urn:**）不区分大小写。
* NID：名称空间标识符，可以包括字母、数字和连字符 (-)，后跟冒号 (:)。
* NSS：特定于命名空间的字符串。NSS 可能包含 ASCII 字母、数字、标点符号和特殊字符。

一些特定的NID编号：  

| NID                                             | 含义             |
|-------------------------------------------------|----------------|
| ISBN(International Standard. Books Number)      | 图书的唯一标识符       |
| ISSN(International Standard Serial Number)      | 期刊的唯一标识符       |
| UUID(Universally Unique Identifier)             | 计算机系统内的唯一信息标识符 |
| ISAN(International Standard Audiovisual Number) | 电影等视听作品的唯一编号   |

URL举例：  
* urn:website:language:html:head
* urn:isan:0000-0000-2CEA-0000-1-0000-0000-Y
* urn:ISSN:0167-6423

## HTTP是无状态协议 {id="http_is_stateless_protocol"}

HTTP协议的无状态性是指协议本身不具备记忆能力，
每个请求和响应之间相互独立，服务器不会记录客户端的历史请求信息;

```plantuml
@startmindmap
* 无状态协议(stateless protocol)
** 请求独立性
***_ 每次HTTP请求都是全新的交互
** HTTP协议头部（Header）和主体（Body）中不包含会话状态信息，服务器仅根据当前请求内容生成响应
@endmindmap
```

## HTTP连接的持续性{id="http_persistence"}

```plantuml
@startmindmap
* HTTP连接的持续性
** 非持续连接
***_:\
===每个请求/响应对都由一个单独的TCP连接发送。
===HTTP/1.0 默认使用非持续连接
===通过非标准扩展，客户端可在请求头添加 **Connection: Keep-Alive 字段向服务器发起长连接请求。若服务器支持该扩展，则会在响应头中返回 Connection: Keep-Alive** 确认启用长连接;
*** 非持续连接的缺点
****:\
===- 必须为每一个对象的请求建立和维护一个连接，每个连接在客户端和服务器中都要占用一定的资源，这样服务器负担大。
===- 每一个对象的响应时间为两倍的RTT：即一个RTT用于创建TCP，一个RTT用于请求和接收对象;

** 持续连接(长连接)
***_:\
===所有的请求/响应对都由相同的TCP连接发送。
===HTTP/1.1 及后续版本默认使用持续连接，也可配置成非持续连接(Connection: close);
*** 长连接优点
****:\
===- 保持连接：服务器在发送响应后保持TCP连接打开。一个完整的web页面或者多个web页面可以用单个TCP连接进行传送
===- 使用流水线：多个对象的请求可以采用流水线式发送，允许客户端在未收到前一个响应时发送后续请求，进一步降低延迟
===- 配置超时时间：每个连接可以配置超时事件，超时服务器就关闭该连接;

* 发起TCP连接的三次握手
** 三次握手过程
***_ 第一次握手
**** 客户端向服务端发起
***_ 第二次握手
**** 服务器返回一个小TCP报文端以做出确认和响应
***_ 第三次握手
**** 客户端再向服务端返回确认(和客户端向服务端发送的第一个请求一起发送)
** 请求并接收一个HTML文件的时间
***_ 总的响应时间为两个RTT(两次握手为一个RTT)加上传输HTML文件的时间
@endmindmap
```

## HTTP报文格式 {id="http_message_format"}

HTTP报文使用ASCII编码

HTTP报文除实体主体(entity body)外由多行组成，每行由一个回车(CR)和换行结束(LF)。  
最后一行之后再附加一个回车换行。

### HTTP请求报文 {id="http_request"}

请求报文的第一行叫做“请求行(request line)”  
请求行后面的行称为“首部行(header line)”  
最后是可选的“实体主体(entity body)“  

HTTP请求报文例子：

![HTTP请求报文例子.png](../images/HTTP请求报文例子.png)

HTTP请求报文的通用格式：

![HTTP请求报文的通用格式.png](../images/HTTP请求报文的通用格式.png) {width="500"}

```plantuml
@startmindmap
* HTTP请求报文
** 请求行
***_ 请求行由3部分组成
**** 方法字段
***** GET
******_ 请求一个对象
***** POST
******_ 常用于提交表单
***** HEAD
******_ 类似于GET。服务器收到HEAD请求后只返回html的head，而不返回html的body。常用于开发者调试。(爬虫可以根据HEAD请求来建立索引)
***** PUT(HTTP/1.1 新增)
******_ 可用于用户上传对象到服务器。
***** DELETE(HTTP/1.1 新增)
******_ 可用于用户删除服务器上的对象。
**** URL字段
*****_ 可以是相对路径(依赖请求头中Host字段补全完整地址)，如/index.html；也可以是绝对路径；如：http://example.com/index；可以带查询参数，如：/search?q=test
**** HTTP版本字段
*****_ 向服务器声明客户端支持的最高协议版本。客户端通过此版本号告知服务器“我能理解的协议特性”

** 首部行
*** Host: www.example.com:80
****_ 表示对象所在的主机：域名和端口。端口号可省略（HTTP默认80，HTTPS默认443）
****_ 虽然TCP连接已经在通信双方建立了，但是此首部行信息仍有必要，Web代理高速缓存使用此信息
*** Connection: close
****_ 浏览器告诉服务器不要使用长连接，要求服务器发送完被请求的对象后关闭此连接
*** User-agent: Mozilla/5.0
****_ User-agent用来指明用户代理(即浏览器)的类型。Mozilla/5.0表示Firefox。(服务器可根据此字段给不同类型的用户代理发送不同的对象)
*** Accept-language: fr
****_ Accept-language表示用户想要的对象的语言版本。如fr表示法语

** 实体主体
*** 使用GET方法时，实体主体为空。使用POST方法时才使用实体主体。
@endmindmap
```

### HTTP响应报文 {id="http_response"}

响应报文的第一行叫做“状态行(status line)”  
状态行后面的行称为“首部行(header line)”  
最后是可选的“实体主体(entity body)“  

HTTP响应报文例子：

![HTTP响应报文例子.png](../images/HTTP响应报文例子.png)

HTTP响应报文的通用格式：

![HTTP响应报文的通用格式.png](../images/HTTP响应报文的通用格式.png) {width="500"}

```plantuml
@startmindmap
* HTTP响应报文
** 状态行
***_ 状态行由3部分组成
**** 协议版本字段
*****_ 响应报文实际使用的协议版本。服务器可能选择低于客户端请求版本的协议（例如客户端声明 HTTP/2.0，但服务器返回 HTTP/1.1）
**** 状态码和状态信息
***** 200 OK
******_ 请求成功
***** 301 Moved Permanently
******_ 请求的对象被转移了，新的URL定义在响应头“Location”中。客户端软件将自动获取新的URL。
***** 400 Bad Request
******_ 400 状态码属于 4xx 客户端错误 类别。服务器通过返回 400 明确告知客户端：“你的请求格式或内容有问题，请检查后重试”
***** 404 Not Found
******_ 请求的对象不存在
***** 505 HTTP Version Not Supported
******_ 服务器不支持请求报文使用的HTTP版本

** 首部行
*** Connection: close
****_ 表示服务器发送完报文后将关闭该TCP连接
*** Date: Tue, 20 May 2025 15:33:45 GMT
****_ 表示服务器**__发送__**响应报文的时间
*** Server: Apache
****_ 表示服务器类型。类似于请求报文中的User-agent请求头
*** Last-Modified: Sat, 15 Apr 2023 11:06:49 GMT
****_ 表示该对象最后修改或创建的日期和时间
****_ 此响应头会影响对象的缓存。可能在本机缓存也可能在网络缓存服务器(代理服务器)中缓存。
*** Content-Length: 332
****_ 表示发送的对象的字节数。可用于区分报文的边界。
*** Content-Type: application/json
****_ 表示发送的对象的类型

** 实体主体
***_ 包含了所请求的对象本身
@endmindmap
```

### 实践

使用telnet软件

telnet cis.poly.edu 80  
GET /~ross/ HTTP/1.1  
Host: cis.poly.edu  

## cookie

HTTP设计为无状态协议。  

HTTP可使用cookie来标识用户。  

cookie在用户端(浏览器)可存在一个cookie文件中。在Web服务端可存于数据库中。  

### cookie的产生过程： {id="cookie_generate"}
1. 当客户端（浏览器）首次向服务器发送HTTP请求时，服务器生成一个cookie信息并保存于数据库，通过响应头Set-Cookie字段返回。  
2. 客户端从响应头**Set-cookie**中取出cookie信息，存于本地的cookie文件中。  
3. 客户端下次向服务器发送HTTP请求时，通过请求头**Cookie**字段带上cookie信息，服务端可通过它来识别用户。  

## Web缓存

**Web缓存器(Web cache)** 也叫 **代理服务器(proxy server)**，能够代表原始Web服务器来响应HTTP请求。  
Web缓存器会保存最近请求过的对象副本。可以通过配置浏览器来使得所有HTTP请求都指向Web缓存器。  

### 带Web缓存的请求过程： {id="Web_cache_process"}
1. 浏览器与Web缓存器之间建立TCP连接，并发送HTTP请求
2. Web缓存器检查是否有对象副本。若有，则直接返回该对象；若无，则Web缓存器与原始服务器之间建立TCP连接，并发送HTTP请求。原始服务器收到请求后，返回该对象。
3. Web缓存器收到该对象后，在本地存储一份，并向浏览器发送携带该对象的响应报文(通过原来已建立的TCP连接)。

### 缓存的过期问题-条件GET(conditional GET)

当代理服务器中的缓存过期时，代理服务器需要向原始服务器重新请求新的对象。  
请求报文中若带有If-modified-since请求头，则称这个GET请求为条件GET。  

* 代理服务器在每次收到原始服务器返回的对象后，存储对象副本的同时也会存储对象的“最后修改日期“(响应头Last-Modified中的日期)  
* 代理服务器收到浏览器请求后，向原始服务器发送条件GET，即在请求报文中带上If-modified-since请求头，此请求头的值为上次收到原始服务器响应报文中响应头Last-Modified中的值。  
* 原始服务器收到条件GET请求后，检查对象，若对象有变化则返回新的对象，若没有变化，则返回304状态码表示对象无变化。  

### 因特网上部署了大量的Web代理服务器，有两个好处： {id="proxy_advantage"}
1. 减少客户端的请求响应时间
2. 代理服务器大大减少了因特网上的Web流量，减少了网络负载
3. 减少了Web服务器的负担

举例：  
假设Web缓存服务器的缓存命中率为40%，命中时的总响应时间为10ms，没命中的总响应时间为2.01s，则平均响应时间为0.4 * (0.01s) + 0.6 * 2.01s  

### CDN
因特网中可以设置很多分散的Web缓存服务器，这些服务器共同组成了内容分发网(Content Distribution Network, CDN)。  
CDN分为共享的CDN(如Akamai和Limelight)和专用的CDN(如google和Netflix)。  

## HTTP/2

### HTTP/1.0的问题
* **HTTP/1.0默认使用短连接**  
 
每个对象的请求都需要建立一个新的TCP连接，每个对象都需要两个RTT+对象的传输时间，即n个对象的总时间：n * (2RTT + L/R)。  
解决此**耗时**问题：采用多个并发TCP连接。多个并发的TCP连接导致服务器负担重，一般服务器会限制并发数。

### HTTP/1.1的问题
* **HTTP/1.1默认使用长连接(非流水线)**  
 
一个Web页面可以只使用一个TCP连接。  
HTTP/1.1非流水线情况下，n个对象共需花费：RTT + n * (RTT + L/R)  

* **HTTP/1.1支持长连接+流水线方式(现实未被大规模使用)**  

非流水线情况下，每个对象的请求都必须等待收到上一个对象的响应之后。使用流水线可缩短时间。

* **服务器只能按顺序响应请求(FCFS)**  
* **一个Web页面只使用单一的TCP连接，这种方式存在队头阻塞[Header Of Line (HOL) blocking]问题**
 
```plantuml
@startmindmap
* HOL阻塞问题
** 后面的小对象要等待前面的大对象传输完毕才能够开始传输，用户体验差
** 丢失恢复(丢失重传)时实际上暂停了对象的传输：增加了延时
@endmindmap
```

HTTP/1.1解决HOL阻塞问题的典型方法：**使用多个并行的TCP连接。**  

```plantuml
@startmindmap
* 使用多个并行TCP连接导致的问题
** 多个并行TCP连接增加了服务器的负担
**:\
TCP的拥塞控制针对同一条瓶颈链路上的所有TCP连接，给出一个平等共享该链路的带宽。
假如有n个TCP连接共享同一条瓶颈链路，则每个连接大约得到1/n的带宽。  

许多HTTP/1.1打开多个TCP连接来传送一个Web页面，并不是为了避免HOL阻塞问题，而是为了霸占带宽。;
@endmindmap
```

### HTTP/2的改进：HTTP/2的设计目标是减少总延时
```plantuml
@startmindmap
* HTTP/2
** 分帧：对单一的TCP连接上的请求和响应进行多路复用
***_:\
对对象进行**分帧**，把每个对象拆分成多个帧(frame)。
同优先级的对象的帧，可以交错的来进行请求响应，这样可以缓解HOL阻塞问题

将HTTP报文分帧、交错的发送并在接收端组装起来，这一能力由HTTP/2协议的**__成帧子层__**提供
成帧子层还提供对帧进行二进制编码的功能，二进制协议解析更加高效;

** 客户端可以指定请求对象的优先级
***:\
为每个报文分配1到256之间的权重，数字越大表示优先级越高。

还可以指明不同报文段之间的相关性。;
** 服务器可以主动向客户端推送客户端可能需要的对象(客户端没有主动请求)
***_ 服务器可以分析请求的HTML页面，识别出需要请求的对象，提前将对象主动推给浏览器
** 支持二进制编码，效率高
** 支持头部压缩：HPACK算法
***_:\
HTTP/2 的头部压缩通过 **__静态表预定义高频字段__**、**__动态表按需更新__** 以及 **__哈夫曼编码优化字符串__** 三重机制，实现了头部数据量的指数级减少。
这一技术不仅解决了 HTTP/1.x 的冗余传输问题，还为后续协议（如 HTTP/3 的 QPACK）奠定了基础;
@endmindmap
```

* **HTTP/2没有根本解决HOL阻塞问题**  
 
HTTP/2的分帧使得不同的对象的请求响应可以交错进行，大大缓解了HOL阻塞问题。  
但是由于HTTP/2对所有对象的请求仍然是在同一个TCP连接上进行，所以并没有从根本上解决HOL问题。  

HTTP/2可以和HTTP/1.1一样使用多个并行的TCP连接来缓解HOL阻塞，但是也会带来和HTTP/1.1一样的问题。  

* **HTTP/2不保证安全性**  
 
HTTP/2 不保证安全性，需通过TLS来保证。  
但回带来握手次数多的问题，握手次数：3RTT(1个RTT TCP建立连接，2个RTT TLS握手)

## HTTP/3

HTTP/3 使用新的**QUIC**协议，而不使用TCP。  
QUIC协议在应用层实现，它基于UDP协议。  

* **基于UDP，可以实现连接迁移(TCP无法做到)**

* **使用TLS 1.3(QUIC和TLS是协作关系，而不是包含关系)**

* **组成：HTTP/3 = HTTP/2 + QUIC**

将应用层HTTP/2一些好的功能下移到QUIC上， 如多路复用（同时传多个对象）、对象分帧传输（交错帧）、自定义优先级、二进制传输、头部压缩等  

将部分TCP特性上移到QUIC上， 如TCP的可靠性：流量控制、拥塞控制。  
基于UDP重新高效实现、避免TCP序号二义性  

![HTTP3组成.png](../images/HTTP3组成.png) {width="350" style="inline"}

![HTTP3组成_形成.png](../images/HTTP3组成_形成.png) {width="350" style="inline"}

* **HTTP/3协议栈**

QUIC在应用层实现

![HTTP3协议栈.png](../images/HTTP3协议栈.png) {width="400"}

![QUIC协议.png](../images/QUIC协议.png) {width="400"}

* **HTTP演化**

![HTTP演化.png](../images/HTTP演化.png) {width="700"}

```plantuml
@startmindmap
* QUIC协议
@endmindmap
```
