# 1.类构建和销毁

## 1.1 静态工厂方法和构造方法对比

 1.静态工厂方法对比构造方法来讲有一个可以命名的方法名，构造方法名字都一样
 
 2.每次调用的时候不需要创建对象（静态的优势）
 
 3.静态工厂可以返回原类的及其子类对象（JDBC连接）
 
 4.创建参数化类型实例时，代码更加简洁
 
 5.类如果不含有公有或者受保护的构造器，就不能被子类化
 
 6.与其他的静态方法没啥区别
 
 ## 1.2 避免创建不必要的实例
 1.一些重量级别的类如 Date,Calender,TimeZone等，尽量在类初始化的时候使用static来初始化
 
 2.优先使用基本类型而不是装箱类型，担心无意识的自动拆装箱