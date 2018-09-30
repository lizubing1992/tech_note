### 1.boolean:在虚拟机中用int数据类型来代替，相对于boolean数组将会编码成byte数组，每个元素占用8位，单独使用时是4个字节，数组中又是1个字节

###2.‘==’判断两个对象的内存地址是否一致，适用于原始数据类型和枚举类型，equals是Object类的方法，Object对他实现是比较地址，可以重写；

###3.Object中定义的方法：clone(),equals(),hashCode(),toString(),notify(),notifyAll(),wait(),finalize(),getClass(),

###4.ArrayList:内部采用数组存储元素，高效随机访问，动态调整大小
 LinkedList：链表存储元素，快速插入，删除元素，不支持高效访问
 Vector：线程安全ArrayList

###5.String:不可改变的字符序列，添加新字符需要重新创建String
 StringBuilder:可变字符序列，支持添加新字符(无需创建新对象)
 StringBuffer:可以看作线程安全版的StringBuilder


###6.HashMap:线程不安全，允许存在null键和null值，基于拉链法的散列表
 HashTable:线程安全，不允许存在null键和null值

###7.ConcurrentHashMap:支持并发读写的HashMap,读写时无需加锁，内部采用“分段存储”，只需对要进行写操作的数据所在“段”，进行加锁；

###8.HashMap:底层散列表，内部元素无序；
 TreeMap:底层红黑树，内部元素有序，自然排序或者是根据比较器生成；
LinkedHashMap:能够记住插入元素顺序的HashMap;

###9.Collection<E>:java集合框架中的基本的接口；
 Collections:集合框架的一个工具类，大量用于操作或者返回集合的静态方法；

###10：try-catch-finally中只有两种情况finally不会执行：调用System.exit(),JVM崩溃了；

###11.Throwable:异常层级中的基类；
  Error:内部错错误，这类错误使我们无法控制；
  Exception:表示异常，RuntimeException及其子类属于未检查异常，ArrayIndextOutOfBounds,NullPoint,我们通过判断语句避免检查异常发生，IOException及其子类属于已检查的异常。编译器会检查我们是否为所有可能抛出的已检查异常提供异常处理器；

###18.Override:重写，子类对父类中同一方法的重写定义；
  Overload:重载，定义一个与已定于的方法名称相同签名不同的新方法；

###19.接口：一种约定，实现类要遵循这个约定，，只能包含常量和方法声明，缺省或者public，可以有多个继承实现多个接口，继承时可见性保持一致
  抽象类：代价大，包含属性和方法，成员变量可定义为可见不可见，子类只能继承一个父类，继承时的可见性可大于父类；




  
