##Binder是基于OpenBinder来实现的

在Android系统的Binder机制中，由一系列系统组件组成，分别是Client,Service,Service Manager和Binder驱动程序，其中Client,Service和Service Manager运行在用户空间，Binder驱动程序运行在内核空间，核心是Binder驱动程序，Service Manager提供辅助管理功能，

       1. Client、Server和Service Manager实现在用户空间中，Binder驱动程序实现在内核空间中

        2. Binder驱动程序和Service Manager在Android平台中已经实现，开发者只需要在用户空间实现自己的Client和Server

        3. Binder驱动程序提供设备文件/dev/binder与用户空间交互，Client、Server和Service Manager通过open和ioctl文件操作函数与Binder驱动程序进行通信

        4. Client和Server之间的进程间通信通过Binder驱动程序间接实现

        5. Service Manager是一个守护进程，用来管理Server，并向Client提供查询Server接口的能力