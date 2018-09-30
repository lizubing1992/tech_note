增加接口的默认方法和静态方法
函数式接口FunctionInterface和lambda表达式
方法的引用
静态方法引用：ClassName::methodName;
实例上的实例方法引用： instanceName::methodName
超类上的实例方法引用：supper::methodName
类的实例方法引用：ClassName:methodName
构造方法引用Class:new
数组构造方法引用TypeName[]::new
4.stream的部分用法

### 5.Option的null安全操作‘
事实上，getUserName方法对输入参数并没有进行判断是否为null，因此，该方法是不安全的。如果在Java8之前，要避免可能存在的空指针异常的话就需要使用if-else进行逻辑处理，getUserName会改变如下：

	    private String getUserName(User user) {
    	if (user != null) {
    	return user.getUserName();
    	}
    	return null;
    	}

这是十分繁琐的一段代码。而如果使用Optional则会要精简很多：

	private String getUserName(User user) {
	    Optional<User> userOptional = Optional.ofNullable(user);
	    return userOptional.map(User::getUserName).orElse(null);
	}

常用的方法

### 6.Date/Time API的改进
