### Inject，Component，Module，Provides是dagger2中的最基础最核心的知识点。奠定了dagger2的整个依赖注入框架。

###Inject主要是用来标注目标类的依赖和依赖的构造函数
### Component它是一个桥梁，一端是目标类，另一端是目标类所依赖类的实例，它也是注入器（Injector）负责把目标类所依赖类的实例注入到目标类中，同时它也管理Module。
### Module和Provides是为解决第三方类库而生的，Module是一个简单工厂模式，Module可以包含创建类实例的方法，这些方法用Provides来标注


