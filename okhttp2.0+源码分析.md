### 2.0的包名：com.squareuo.okhttp
包结构组成：

最先分析http包下的类文件

###1. C AuthenticatorAdapter;

实现自Authenticator(身份认证)接口(远程服务的代理相关)：就只有两个方法 Request authenticator(Proxy proxy,Response response),Request authenticateProxy(Proxy proxy,Response reponse)返回一个包含安全认证凭证的请求，代理服务器提供可能会返回码为401情况下。
 遍历所有的Challenge 每个都会进行密码相关的凭证认证，返回符合要求的请求Request(代理凭证，普通的凭证)
###  2. I CacheRequest 获得缓存相关的Sink body(),去除缓存的abort()
###  3. C CacheStrategy 缓存策略 仅使用网络，仅使用缓存cache，根据缓存的时间expire判断是否使用缓存数据
###  4. C HeaderParse http的header解析器，跳过空格，指定的位置，后面的value值
###  5. C HttpConnection http连接器 ，底层使用socket连接，存在连接池ConnectionPool,后台数据使用sink接收，里面维护了固定长度的FixedLengthSink,FixedLengthSource，可以变化长度的ChunkedSource,ChunkedSink,未知长度的UnKnownLengthSink,UnknownLengthSource三种类型的数据流一种是写入Request，一种是获得Response
### 6. C HttpDate相当于DataUtils格式化各种时间
###  7. C HttpEngine 处理http请求的关键类,对请求Request进行包装：Transpot,Address，Route,HttpClient，RouteSelector等进行组装,其中sendReuqest()方法用于发送请求组装请求头，打开socket连接获取数据，networkRequest()默认的Request设置，cookie管理。请求Header的设置，路由选择器会多次选择适合的路由，所有的网络请求都是经由Transport的socket完成的，而相应的数据获取却是被单独提取为okio包用于做相应的io操作，验证是否使用缓存(自带的Last-Modifyed的请求Header)还是网络
### 8. C HttpMethod 验证是否需要缓存的，是否需要请求主体的，以及许可请求主体
### 9. C HttpTransport 实现Transport接口，实例化需要HttpEngine以及HttpConnection创建连接通道通道相关全部在这里
### 10.C OkHeaders 头部Header处理cookie，获取Header信息，以及其他的Header操作
### 11.C RealResponseBody 继承ResponseBody,主要是用于对Response中的source数据进行处理，转byte[]数组，获得contentType,length长度等
### 12.C RequestLine 获取请求的状态行，如GET /HTTP/1.1这种信息
### 13 C RetryableSink 实现了Sink接口，在内存中的缓存，写数据到socket，flush等操作
### 14.C RouteSelector 路由选择器，选择路由线路连接到原始服务器，每个连接需要一个可以选择的代理服务器，ip地址，TLS模式，连接可以被复用
### 15.C spdyTransport 基于http协议的增强协议用于提高访问速率，会根据特定的内核师傅支持spdy协议进行选择相应的协议；
### 16. C StatusLine 状态行，主要是用于解析状态码信息
### 17. I Transport 传输通道 

        
       