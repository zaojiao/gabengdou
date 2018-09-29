###
ReentrantLock、读写锁 原理与源代码翻阅、博客书写
###
Zk一致性原理及部分代码阅读、分布式锁zk的实现
###
Mysql索引原理、及家里的那本书籍阅读
###
Redis常用场景和数据结构学习及书籍阅读
###
分布式相关技术包括不限于：定时调度、zk、eruaka等

#### 2018-06-20
zk文章阅读：https://www.cnblogs.com/crazylqy/p/7132133.html

https://blog.csdn.net/gs80140/article/details/51496925

2018-06-20 进展

ReentrantLock -> LockSupport -> linux Posix mutex 继续中。。。
posix线程详解IBM文章三篇：
https://www.ibm.com/developerworks/cn/linux/thread/posix_thread1/index.html

https://www.ibm.com/developerworks/cn/linux/thread/posix_thread2/index.html

https://www.ibm.com/developerworks/cn/linux/thread/posix_thread3/index.html

这几年其实看了不少东西，但很多没有坚持，总是各种原因就停下来了，今天想想觉得最主要的问题就是不够贪心，总是觉得这样还好，这样就好，目标感不明确，为达目的誓不罢休才行。
The world is only you and the target. You need focus.

潜心学习：
ProductServiceImpl$$EnhancerBySpringCGLIB$$7604b631.queryProductList(<generated>)

#### Commons-Pool 原理和源码阅读解析
#### spring-web 模块源码解析

#### 2018-09-12
osgi技术学习、httpClients博客编写

#### 2018-09-21
SPI机制学习、TCCL学习 完成

#### 2018-09-22
深入虚拟机- 9、10章 （明天判断需要多久）

中秋假期温故知新，在重载、重写的动态特性上有新的学习

重载主要在接收者Receiver已经确定，然后根据参数类型确定调用方法，参数类型在编译器已知，因此编译器就确定了调用的方法

重写则不同，重写的接受者Receiver是在运行时，调用invokeVirtual时，按照以下三个步骤进行的：

1：查看当前栈顶的对象的实际类型，标记为C

2：查找C的方法中，是否有与需要执行的方法的方法名称、参数类型一样的方法，如果有则返回直接引用

3：如果C中没有，则依次查找C的父类型中，是否有2中要求的类，有则直接返回

4：以上步骤都操作了，仍然没有则报异常

9章
虚拟机早期优化，主要在javac阶段的优化，即 .java --> .class 过程中，包括 词法、语法分析、语法树生成、插入符号表、生成字节码等过程，这个过程中进行优化称为早期优化

第6章 class类结构 第7章 加载 第8章 虚拟机执行子系统

类结构篇待全书看完，在Exception和Code属性方面需要继续学习、

类加载上 中秋期间 温故了OSGI的特性、关于类加载的经典实现、后续有时间机会继续学习、已经学习了SPI机制和TCCL类加载器

虚拟机执行子系统中 帧栈的学习有了更清晰的了解、虚拟机的解释执行、编译执行的学习

解析、分派、静态分派（重载）、动态分派（重写）、动态语言特性invokeDynamic实现、MethodHandle的实现

MethodHandle编译时，做的事情，生成了一个代理类，代理类里所有的方法最终都是通过MethodHandle来调用的，所以所有的实现都会走MethodHandle方法
lookup().static.find() 动态调用的原理 书中做了解析

最后的帧栈执行

局部变量表
执行帧栈
等...

两种执行方法：寄存器执行、栈执行

各有优劣 寄存器快速，但可移植性差
栈执行 命令增多、效率不高、但可移植性好

#### 2018.09.28 
深入虚拟机 第9章：编译器早期优化 结束 今天开始编译器晚期优化 
后续 第10、11章 内存模型、线程锁优化等 这本书就结束了 计划十一期间争取结束掉JVM的学习

#### myBatis 原理学习 + 简单模仿实现

#### Netty学习
