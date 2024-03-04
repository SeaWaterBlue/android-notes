# Binder

## 进程空间划分

- 一个进程空间分为内核空间和用户空间，内核空间
- 进程间，用户空间的数据不可共享，内核空间的数据可共享

## 不同的IPC通信方式区别

- IPC通信即进程间通信方式，有管道，消息队列，Socket，Binder这几种
- 其中管道，消息队列，Socket都需要两次数据拷贝，即发送方用户空间->内核空间，内核空间->接收方内核空间；而Binder只需要一次数据拷贝，即发送方用户空间->内核空间（至于内核空间到接收方用户空间则通过映射来实现。即数据到了内核空间也会同步到用户空间）

## Binder相关知识

- Binder通信采用C/S架构，从组件来看，包含Client，Server，ServiceManager，以及Binder驱动，架构图如下：

  ![img](https://upload-images.jianshu.io/upload_images/3985563-5ff2c4816543c433.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/921/format/webp)

- 大概运行流程：Client通过Binder名字获取Server中对Binder实体的引用，从而调用到Server的方法

- 实际应用层使用分为bindService方式与addService方式，前者是Client发送请求时再注册Service进而调用，后者则是Server进程要先注册Service到ServiceManager。一般非系统应用无法使用addService，而系统级别服务一般都使用addService与getService

