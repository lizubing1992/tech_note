
## IO包里面只有一个FileSystem接口类，主要是因为io操作被抽取出来作为一个单独的库来使用okio,
spdy包，spdy协议相关类
###1. E ErrorCode枚举类，定义一些出错码
###2. I FrameReader 帧读取接口类，修改header,ping
###3. I FrameWriter 帧写入接口类
###4. C Header 请求Header，一些header数据 status,method,path,scheme等，相当于一个工具类
###5. E HeaderMode Header类型的枚举类针对于Spdy以及http2而言
###6. C Hpack 一系列Header的list数据，用reader以及writer方法
###7. C Http2 http2的协议定义类
###8. C Huffman 一个包含code码的类
###9. I IncomingStreamHandler监听对等流连接上时通知处理，receive用于处理获得一个stream流之后的处理
###10. C NameValueBlockReader 主要用于读取Spdy3中的name,value值
###11. C ping 本地化的ping
###12. I PushObserver 初始化okhttp，继承者必须快速处理callBack函数
###13. C Settings spdy设置相关类
###14. C Spdy3 spdy协议相关类
###15. SpdyConnection spdy连接类，内部封装了ExecutorService线程池,底层使用socket发动请求,Reader内部类中的会执行execute()方法连接请求
###16. C SpdyStream spdy连接流，逻辑双向流，source的读写操作
###17. I Variant socket版本和方言协议 获得协议，新建Reader和Writer


 
 