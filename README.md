# Javaagent实践

> **引言**

 在使用Arthas的过程中,感觉有些功能很好用，但是有些不敢敲命令行的用起来会比较麻烦，,本着学习的态度,自己设计了一套类似的程序提供了WEB页面进行操作,并且会不断集成一些常用的功能  。

 在日常的工作中，经常会遇到一些让人头疼的问题，比如在上线前测试量不够，导致生产环境出现了一个小bug，影响了功能的正常使用（**对于小公司来讲，应该属于基操吧**），这个时候可能就是因为一小行代码所导致，但是为了修复bug不得不重启程序（没办法），这时候客户和老板可能就不满意了（用户量小还好），所以针对这种情况，自己设计了一套可以在线补偿的工具。

> **简介**

在决定好要设计这个程序的时候，调研了一些关键的技术，如下
|技术名称| 作用 |
|--|--|
| Javaagent | 在运行前加载，或在运行中对字节码进行操作 |
| VirtualMachine | 在运行中附着到目标进程上,并调用agent |


---

整个工程分为三个部分，前端页面，与页面交互的服务端，操作目标Java进程的agent
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201030140025315.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RheV9EYXlfTm9fQnVn,size_16,color_FFFFFF,t_70#pic_center)
| 工程 | 技术 |
|--|--|
| web | Vue + Ant design |
| server |Springboot+Websocket|
| agent |Javassist + cfr + JavaCompiler|

---
#### 目前已经实现的功能1.0
1. 热修复，输入目标工程的完整包名，比如有一个类叫com.abc,将该类反编译到目录(**同时会把旧的字节码也导出，为了防止出现问题快速恢复**)，对反编译出的java文件进行修改,然后重新编译，最后重新load字节码(**不是所有的类都能成功编译，因为反编译出的字节码可能被HotSpot优化，或其他未知原因，会将失败日志导出到当前目录**)
2. 补加执行时间，在指定的类中，比如com.abc中，对其中的所有方法或某一个方法(**需要方法名**)，进行追加打印执行时间
3. 打印高cpu线程堆栈，快速定位cpu高居不下线程执行的代码位置，以便解决问题

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201030150348804.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RheV9EYXlfTm9fQnVn,size_16,color_FFFFFF,t_70#pic_center)

#### 1.1版本
1. 新加aop功能 打印入参和返回值信息
---

server端源码（vue已经打包在了static下，启动直接访问lp:9675即可),授权这里其实没做，默认是同户名是**admin**，密码是**admin123**，如果需要打包记得关闭单元测试
 [github地址](https://github.com/NoBugBoy/dont-stop-agent)
 
代码在IDEA可以直接运行，如果通过jar包运行需要额外指定参数：
```shell
java -Xbootclasspath/a:$JAVA_HOME/lib/tools.jar -jar dont-stop-agent-1.0.jar 
```

---
由于业余时间独立制作，有些使用还是不是很方便，比如页面使用前需要先设置对应server端的ip，而且目前端口号（**9675**）还没办法修改，如果有时间后续会继续完善推出新功能，如果有想加的功能，或者有想一起做的可以私信或者评论

