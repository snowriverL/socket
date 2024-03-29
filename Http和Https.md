## http和https的区别
>  1. https协议需要到ca申请证书
>  2. http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议。
>  3. http和https使用的是完全不同的连接方式用的端口也不一样，前者是80，后者是443。
>  4. http的连接很简单，是无状态的。HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，要比http协议安全。
## 简单说说https是如何保证安全传输的
>  通过非对称加密+对称加密+第三方验证完成数据的安全传输
## https是不是绝对安全的？有没有办法被破解？
> 客户端本地添加了不受信任得证书，可能会导致不安全传输
## http无状态协议，怎么理解无状态协议。如何实现有状态的请求
> HTTP 协议本身不会对请求和响应之间的通信状态做保存。

>  1. 客户端支持的 cookie
>  2. 客户端支持的 cookie
## 说说http协议中的302状态码的作用
> 1. 302 redirect: 302 代表暂时性转移(Temporarily Moved )
> 2. 302表示旧地址A的资源还在（仍然可以访问），这个重定向只是临时地从旧地址A跳转到地址B，搜索引擎会抓取新的内容而保存旧的网址。

## 304缓存原理
> 协商缓存 : 客户端第二次请求此URL时，根据 HTTP 协议的规定，浏览器会向服务器传送 If-Modified-Since和If-None-Match(可选报头，值Etag的值) 报头，询问该时间之后文件是否有被修改过,如果服务器端的资源没有变化，则自动返回 HTTP 304 （Not Changed.）状态码，内容为空，否则重新发起请求，请求下载资源这样就节省了传输数据量。当服务器端代码发生改变或者重启服务器时，则重新发出资源，返回和第一次请求时类似。从而保证不向客户端重复发出资源，也保证当服务器有变化时，客户端能够得到最新的资源。

## http协议1.0和http协议1.1的区别
> 1. 长连接  
HTTP 1.0需要使用keep-alive参数来告知服务器端要建立一个长连接，而HTTP1.1默认支持长连接。  
HTTP是基于TCP/IP协议的，创建一个TCP连接是需要经过三次握手的,有一定的开销，如果每次通讯都要重新建立连接的话，对性能有影响。因此最好能维持一个长连接，可以用个长连接来发多个请求。

> 2. 节约带宽  
HTTP 1.1支持只发送header信息(不带任何body信息)，如果服务器认为客户端有权限请求服务器，则返回100，否则返回401。客户端如果接受到100，才开始把请求body发送到服务器。  
这样当服务器返回401的时候，客户端就可以不用发送请求body了，节约了带宽。  
另外HTTP还支持传送内容的一部分。这样当客户端已经有一部分的资源后，只需要跟服务器请求另外的部分资源即可。这是支持文件断点续传的基础。

> 3. HOST域  
  现在的web server例如tomat，设置虚拟站点是非常常见的，也即是说，web server上的多个虚拟站点可以共享同一个ip和端口。  
  HTTP1.0是没有host域的，HTTP1.1才支持这个参数

## http协议1.1和http协议2.0的区别
> 1. 多路复用  
  HTTP2.0使用了多路复用的技术，做到同一个连接并发处理多个请求，而且并发请求的数量比HTTP1.1大了好几个数量级。  
  当然HTTP1.1也可以多建立几个TCP连接，来支持处理更多并发的请求，但是创建TCP连接本身也是有开销的。  
  TCP连接有一个预热和保护的过程，先检查数据是否传送成功，一旦成功过，则慢慢加大传输速度。因此对应瞬时并发的连接，服务器的响应就会变慢。所以最好能使用一个建立好的连接，并且这个连接可以支持瞬时并发的请求。

> 2. 数据压缩  
    HTTP1.1不支持header数据的压缩，HTTP2.0使用HPACK算法对header的数据进行压缩，这样数据体积小了，在网络上传输就会更快。

> 3. 服务器推送  
  当我们对支持HTTP2.0的web server请求数据的时候，服务器会顺便把一些客户端需要的资源一起推送到客户端，免得客户端再次创建连接发送请求到服务器端获取。这种方式非常适合加载静态资源。  
  服务器端推送的这些资源其实存在客户端的某处地方，客户端直接从本地加载这些资源就可以了，不用走网络，速度自然是快很多的。

## 如何保证基于http协议的接口的安全性
> 使用TOKEN+签名认证 保证请求安全性
  * 做一个认证服务，提供一个认证的webapi，用户先访问它获取对应的token
  * 用户拿着相应的token以及请求的参数和服务器端提供的签名算法计算出签名后再去访问指定的api
  * 服务器端每次接收到请求就获取对应用户的token和请求参数，服务器端再次计算签名和客户端签名做对比，如果验证通过则正常访问相应的api，验证失败则返回具体的失败信息
  
## http协议上传文件，数据如何传输？

## 说说https协议的优缺点
> 1. 优点
 * 使用HTTPS协议可认证用户和服务器，确保数据发送到正确的客户机和服务器
 * HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，要比http协议安全，可防止数据在传输过程中不被窃取、改变，确保数据的完整性
 * HTTPS是现行架构下最安全的解决方案，虽然不是绝对安全，但它大幅增加了中间人攻击的成本
 * 谷歌曾在2014年8月份调整搜索引擎算法，并称“比起同等HTTP网站，采用HTTPS加密的网站在搜索结果中的排名将会更高  
 
> 2.缺点
  * HTTPS协议握手阶段比较费时，会使页面的加载时间延长近50%，增加10%到20%的耗电；
  * HTTPS连接缓存不如HTTP高效，会增加数据开销和功耗，甚至已有的安全措施也会因此而受到影响；
  * SSL证书需要钱，功能越强大的证书费用越高，个人网站、小网站没有必要一般不会用。
  * SSL证书通常需要绑定IP，不能在同一IP上绑定多个域名，IPv4资源不可能支撑这个消耗。
  * HTTPS协议的加密范围也比较有限，在黑客攻击、拒绝服务攻击、服务器劫持等方面几乎起不到什么作用。最关键的，SSL证书的信用链体系并不安全，特别是在某些国家可以控制CA根证书的情况下，中间人攻击一样可行。

## 一次http请求的完整交互流程

> 1. 建立TCP连接  
在HTTP工作开始之前，Web浏览器首先要通过网络与Web服务器建立连接，该连接是通过TCP来完成的，该协议与IP协议共同构建Internet，即著名的TCP/IP协议族，因此Internet又被称作是TCP/IP网络。HTTP是比TCP更高层次的应用层协议，根据规则，只有低层协议建立之后才能进行更高层协议的连接，因此，首先要建立TCP连接，一般TCP连接的端口号是80。

> 2. Web浏览器向Web服务器发送请求命令   
一旦建立了TCP连接，Web浏览器就会向Web服务器发送请求命令。例如：GET/sample/hello.jsp HTTP/1.1。

> 3. Web浏览器发送请求头信息   
浏览器发送其请求命令之后，还要以头信息的形式向Web服务器发送一些别的信息，之后浏览器发送了一空白行来通知服务器，它已经结束了该头信息的发送。

> 4. Web服务器应答   
客户机向服务器发出请求后，服务器会客户机回送应答， HTTP/1.1 200 OK ，应答的第一部分是协议的版本号和应答状态码。

> 5. Web服务器发送应答头信息   
正如客户端会随同请求发送关于自身的信息一样，服务器也会随同应答向用户发送关于它自己的数据及被请求的文档。

> 6. Web服务器向浏览器发送数据   
Web服务器向浏览器发送头信息后，它会发送一个空白行来表示头信息的发送到此为结束，接着，它就以Content-Type应答头信息所描述的格式发送用户所请求的实际数据。

> 7. Web服务器关闭TCP连接   
一般情况下，一旦Web服务器向浏览器发送了请求数据，它就要关闭TCP连接，然后如果浏览器或者服务器在其头信息加入了这行代码：Connection:keep-alive,TCP连接在发送后将仍然保持打开状态，于是，浏览器可以继续通过相同的连接发送请求。保持连接节省了为每个请求建立新连接所需的时间，还节约了网络带宽。