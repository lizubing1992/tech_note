###1.数据程序的分层，管理类，数据DAO
###2.IOC ,DI 都是依赖容器注入的，控制权都不是在自己上面
###3.注入方式有三种，set方式（基本是用这种） 构造方法注入，接口注入
###4.<bean name = "....",value = "对应的类全路劲">
	<property
	<ref value = ""></bean>
	</property>
注入的配置
###5.autowire 匹配机制 一般使用byName 和byType
###6 bean的生命周期方法最好不要和scope = proprType一起用，容易出问题
	init-method  destory-method