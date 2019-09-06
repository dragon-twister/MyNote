# java基础

[toc]

### error和exception有什么区别?
Error表示系统级的错误，是java运行环境中的内部错误或者硬件问题，例如OutOfMemoryError，StackOverflowError  
Exception表示程序本身引起的异常，常见的有空指针异常，数组越界，类型转换异常

### ==、equals、hashcode

- == 

数据类型分为基本数据类型和引用类型

基本数据类型比较的是值是否相等

引用类型比较的是对象在内存中的存放地址，对象的地址存放在栈中，所以也就是比较存放在栈中的引用。

- equals

object里面的equals方法也是用==实现的

String中的equals方法比较的是两个字符串是否相等

        
    即String中equals方法判断相等的步骤是：

    1.若A==B 即是同一个String对象 返回true
    
    2.用instanceof判断若对比对象是String类型，是则继续，否则返回false
    
    3.判断A、B长度是否一样，不一样的话返回false
    
    4.逐个字符比较，若有不相等字符，返回false

- hashcode

在jdk里面，hashmap和hashset添加元素底层都是调用的同一个方法，只是传参不一样。

        
       <!-- 如果说要添加的元素是A
        hashset调用的是put（A，Object）
        hashmap调用的是put（A.key，A.value）-->
        public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

Object类里面的hashcode在jdk里面是一个用其他语言编写的native方法

        

在集合中hashcode和equals用来判断两个元素是否重复。

之所以用了两个方法是因为如果集合中有1000个元素，那就要调用1000次的equals方法。

而先用hashcode定位到元素的存放位置，如果位置已经有元素了，那就再调用equals方法判断两个元素是否相等。这样就提高了效率。

> 
> 将对象放入到集合中时，首先判断要放入对象的hashcode值与集合中的任意一个元素的hashcode值是否相等，如果不相等直接将该对象放入集合中。如果hashcode值相等，然后再通过equals方法判断要放入对象与集合中的任意一个对象是否相等，如果equals判断不相等，直接将该元素放入到集合中，否则不放入。

###重写equals为什么要重写hashCode

这个，，，真是个意外，好久没看到过这个问题了，而且好久没重写过equals了，面试官还问“这不是面试常考题么”，额，我还是太菜。
主要有两点：

(1)不被重写（原生）的hashCode值是根据内存地址换算出来的一个值。
(2)不被重写（原生）的equals方法是严格判断一个对象是否相等的方法（object1 == object2）。

### String final

final指的是栈内存的引用不可变，但是堆里面存在的数据是可变的。String是一个数组，可以通过value[i]来操作值。

如果可变的话 当两个字符串一样的

### 字符串的创建

字面量创建：查找常量池有则返回引用，否则，堆里生成对象，同时在在常量池生成引用。

new:不管字符串常量池是否存在都在堆中创建

intern:如果字符串在字符串常量池已存在就返回该字符串对象，如果不存在就在常量池添加这个对象的引用

### StringBuffer

基于数组，StringBuffer的线程安全是通过synchronize关键字修饰方法实现的，拼接的时候会先判断容量是否足够，不够的话，会扩容为原来的两倍（原来默认为16个字符），如果再不够就直接扩容为拼接后的长度。

### http和https的主要区别：

1.https需要用到ca申请证书

2.http是明文传输，而https是ssl加密的传输

3.用的端口不一样80和443

### http状态码

200 请求成功

500 服务器错误

404 请求不到资源

### session

http状态是无状态的，如果要区分请求的是否是同一个浏览器，就需要sessionid，sessionid存储在cookie中，如果关闭浏览器之后需要重新登录是因为没有sessionid，而不是因为session过期。

session最大过期时间 30分钟 可以在tomcat/conf/web.xml中配置

session是tomcat生成的，存在内存


### Class对象的生成方式如下（不是实例化对象）：

1.Class.forName("类名字符串") （注意：类名字符串必须是全称，包名+类名）；

2.类名.class；

3.实例对象.getClass()；


### 等号和equals的区别

= 比较的是否引用指向的是同一个对象

equals是String里面重写的一个方法，他会一个一个字符比较是否相等

instanceof用于判断这个对象是否是某个类的实例

字符串在java8之前会存储在方法区，java8将方法区改为元数据了

### js刷新

location.reload()
    
### 拦截器和过滤器
    
过滤器：基于servlet提供的filter类实现，比如给所有请求设置字符编码

拦截器：

通过反射来实现

不一定能到达目标，可以拒绝，比如验证token

### tcp三次握手

- 客户端发送请求连接报文到服务器
- 服务器发送同意连接的报文到客户端
- 客户端发送收到同意连接的报文到服务器

三次握手之后不管是客户端还是服务器都确认对方能收到自己的信息

之所以是三次握手是因为通信的双方都要确认对方能收到自己的信息