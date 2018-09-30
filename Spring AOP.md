## 1.Pointcut expression 切面表达式
主要用于注解：@Aspect   @Point @Advice
## Wildcards（通配符）
###  1.1  * 匹配任意数量的字符
###  1.2 + 匹配制定雷及其子类
###  1.3 .. 一般用于匹配任意数的子包或参数 
### 还有 || && ！

## 2 designators

 重点是execution 匹配方法 

匹配包/类型 within
 @Point("com.*.*")

匹配对象
@Point("this(包相关的东西)")

this 对像 （实现接口可以 ），target 方法 bean 队形的Service等

运行时织入的Spring AOP
动态代理 接口 + 实现
--静态代理
代理类和目标类都实现同一个接口
代理委托目标类实现
客户类通过接口引用让代理类来完成功能实现
---动态代理技术{接口代理 ， 继承代理}
基于Proxy来开发的，接口使用InvocationHandler ,只能基于接口进行动态代理
Proxy,newProoxyInstance  v 

责任链调用 使用Chain +游标方式调用 