### mybatis 中的延迟加载通常用到CGLIB动态代理

### 简介
      Mapper.xml映射文件中定义了操作数据库的sql，每个sql是一个statement，映射文件是mybatis的核心。
	　　映射文件中有很多属性，常用的就是parameterType(输入类型)、resultType(输出类型)、resultMap（）、rparameterMap（）。
	  parameterType(输入类型)
### 1、#{}与${}
{}实现的是向prepareStatement中的预处理语句中设置参数值，sql语句中#{}表示一个占位符即?。就是一个占位符
使用占位符#{}可以有效防止sql注入，在使用时不需要关心参数值的类型，mybatis会自动进行java类型和jdbc类型的转换。#{}可以接收简单类型值或pojo属性值，如果parameterType传输单个简单类型值，#{}括号中可以是value或其它名称。
### ${}和#{}不同，
通过${}可以将parameterType 传入的内容拼接在sql中且不进行jdbc类型转换， ${}可以接收简单类型值或pojo属性值，如果parameterType传输单个简单类型值，${}括号中只能是value。使用${}不能防止sql注入，但是有时用${}会非常方便，

### foreach
　　向sql传递数组或List，mybatis使用foreach解析，如下：
　　如果我们需要传入多个ID来查询多个用户的信息，这也就可以使用foreach。我们先考虑下如果只写sql语句是如下：
 
	1 SELECT * FROM USERS WHERE username LIKE '%张%' AND (id =10 OR id =89 OR id=16)
	2 SELECT * FROM USERS WHERE username LIKE '%张%'  id IN (10,89,16)
 
### index：为数组的下标。
###　　item：为数组每个元素的名称，名称随意定义
###　　open：循环开始
###　　close：循环结束
###　　separator：中间分隔输出
###　　通过POJO传入List，映射文件如下：
	1 <if test="ids!=null and ids.size>0">
	2             <foreach collection="ids" open=" and id 		in(" close=")" item="id" separator="," >
	3                 #{id}
	4             </foreach>
	5 </if>


### trim标签有点类似于replace效果。

 trim 属性

                prefix：前缀覆盖并增加其内容

                suffix：后缀覆盖并增加其内容

                prefixOverrides：前缀判断的条件

                suffixOverrides：后缀判断的条件


### MyBatis Mapper.xml的choose标签的作用类似于java中的switch语句，使用choose标签时为了避免使用大量的if元素。choose元素包含了when元素（对应于java中的case语句），otherwise元素则对应java中的default语句。

### resultMap
constructor - 用于在实例化类时，注入结果到构造方法中
idArg - ID 参数;标记出作为 ID 的结果可以帮助提高整体性能
arg - 将被注入到构造方法的一个普通结果
id – 一个 ID 结果;标记出作为 ID 的结果可以帮助提高整体性能
result – 注入到字段或 JavaBean 属性的普通结果
association – 一个复杂类型的关联;许多结果将包装成这种类型
嵌套结果映射 – 关联可以指定为一个 resultMap 元素，或者引用一个
collection – 一个复杂类型的集合
嵌套结果映射 – 集合可以指定为一个 resultMap 元素，或者引用一个
discriminator – 使用结果值来决定使用哪个 resultMap
case – 基于某些值的结果映射
嵌套结果映射 – 一个 case 也是一个映射它本身的结果,因此可以包含很多相 同的元素，或者它可以参照一个外部的 resultMap
对于非自增id，需要使用uuid生成函数生成之后再insert插入

### sqlSessionFactory 会话工厂 工具类使用
 sqlSession 会话 
 connecter 连接

批量别名的定义，使用package 默认类名，大小写都可以

映射文件的加载，使用class时mapper.java,mapper.xml放在同一个packag里面

sql片段针对单表处理的
多表查询的时候外键使用外键所在的javabean注入即可


