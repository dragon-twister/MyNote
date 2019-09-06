# Tomcat 


## Servlet生命周期

1. **加载Servlet**。当Tomcat第一次访问Servlet的时候，**Tomcat会负责创建Servlet的实例**
2. **初始化**。当Servlet被实例化后，Tomcat会**调用init()方法初始化这个对象**
3. **处理服务**。当浏览器**访问Servlet**的时候，Servlet **会调用service()方法处理请求**
4. **销毁**。当Tomcat关闭时或者检测到Servlet要从Tomcat删除的时候会自动调用destroy()方法，**让该实例释放掉所占的资源**。一个Servlet如果长时间不被使用的话，也会被Tomcat自动销毁
5. **卸载**。当Servlet调用完destroy()方法后，等待垃圾回收。如果**有需要再次使用这个Servlet，会重新调用init()方法进行初始化操作**。

- 简单总结：**只要访问Servlet，service()就会被调用。init()只有第一次访问Servlet的时候才会被调用。destroy()只有在Tomcat关闭的时候才会被调用。**

Servlet是单例的

## Tomcat流程

- 请求->tomcat容器->filter->servlet->inteceptor->controller

### tomcat优化
    
    一、Tomcat内存优化
    - 内存
    - 动静分离
        
### 多个tomcat同时运行需要修改的端口？
    

    <Connector port="8080" maxThreads="150" minSpareThreads="25" maxSpareThreads="75" enableLookups="false" redirectPort="8443" acceptCount="100" debug="0" connectionTimeout="20000" disableUploadTimeout="true" />

    <Server port="8005" shutdown="SHUTDOWN" debug="0">

    <Connector port="8009" enableLookups="false" redirectPort="8443" debug="0" protocol="AJP/1.3" />
    
    linux查看启动的端口： netstat -ntlp


### 使用8009端口的AJP协议是什么用？

AJP协议负责和其他的HTTP服务器(如Apache)建立连接；
    
在把Tomcat与其他HTTP服务器集成时，就需要用到这个连接器。
    
之所以使用Tomcat和其他服务器集成，是因为Tomcat可以用作Servlet/JSP容器，但是对静态资源的处理速度较慢，不如Apache和IIS等HTTP服务器；因此常常将Tomcat与Apache等集成，前者作Servlet容器，后者处理静态资源。
    
而AJP协议便负责Tomcat和Apache的连接。Tomcat与Apache等集成的原理如下图(图片来源)：


### <Context>元素的属性: 

    <Context path="" docBase="E:/tomcatApp/JeasyCMS" reloadable="true" />

path:指定访问该Web应用的URL入口。 

docBase:指定Web应用的文件路径，可以给定绝对路径，也可以给定相对于<Host>的appBase属性的相对路径，如果Web应用采用开放目录结构，则指定Web应用的根目录，如果Web应用是个war文件，则指定war文件的路径。(指定项目所在地址) 

reloadable:如果这个属性设为true，tomcat服务器在运行状态下会监视在WEB-INF/classes和WEB-INF/lib目录下class文件的改动，如果监测到有class文件被更新的，服务器会自动重新加载Web应用。
    
### 连接器

    <Connector port="8080" 
    maxThreads="150" 
    minSpareThreads="25" 
    maxSpareThreads="75" 
    acceptCount="100" 
    />   

maxThreads="150" 表示最多同时处理150个连接 

minSpareThreads="25" 表示即使没有人使用也开这么多空线程等待 

maxSpareThreads="75" 表示如果最多可以空75个线程，例如某时刻有80人访问，之后没有人访问了，则tomcat不会保留80个空线程，而是关闭5个空的。 

acceptCount="100" 当同时连接的人数达到maxThreads时，还可以接收排队的连接，超过这个连接的则直接返回拒绝连接。 

mysql的默认连接数是100，可以通过修改etc/my.cnf配置文件中的max_connection来配置

服务器允许的最大连接数16384


### maxThreads如何配置

一般的服务器操作都包括量方面：1计算（主要消耗cpu），2等待（io、数据库等）

第一种极端情况，如果我们的操作是纯粹的计算，那么系统响应时间的主要限制就是cpu的运算能力，此时maxThreads应该尽量设的小，降低同一时间内争抢cpu的线程个数，可以提高计算效率，提高系统的整体处理能力。

第二种极端情况，如果我们的操作纯粹是IO或者数据库，那么响应时间的主要限制就变为等待外部资源，此时maxThreads应该尽量设的大，这样 才能提高同时处理请求的个数，从而提高系统整体的处理能力。此情况下因为tomcat同时处理的请求量会比较大，所以需要关注一下tomcat的虚拟机内 存设置和linux的open file限制。

我在测试时遇到一个问题，maxThreads我设置的比较大比如3000，当服务的线程数大到一定程度时，一般是2000出头，单次请求的响应时间就会急剧的增加，

百思不得其解这是为什么，四处寻求答案无果，最后我总结的原因可能是cpu在线程切换时消耗的时间随着线程数量的增加越来越大，

cpu把大多数时间都用来在这2000多个线程直接切换上了，当然cpu就没有时间来处理我们的程序了。

以前一直简单的认为多线程=高效率。。其实多线程本身并不能提高cpu效率，线程过多反而会降低cpu效率。

当cpu核心数<线程数时，cpu就需要在多个线程直接来回切换，以保证每个线程都会获得cpu时间，即通常我们说的并发执行。

所以maxThreads的配置绝对不是越大越好。

现实应用中，我们的操作都会包含以上两种类型（计算、等待），所以maxThreads的配置并没有一个最优值，一定要根据具体情况来配置。

最好的做法是：在不断测试的基础上，不断调整、优化，才能得到最合理的配置。

### acceptCount的配置，

我一般是设置的跟maxThreads一样大，这个值应该是主要根据应用的访问峰值与平均值来权衡配置的。

如果设的较小，可以保证接受的请求较快相应，但是超出的请求可能就直接被拒绝

如果设的较大，可能就会出现大量的请求超时的情况，因为我们系统的处理能力是一定的。


## 优化

Tomcat IO优化


