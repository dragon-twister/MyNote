# 


- 目前比较流行的架构是微服务（1 你见过几种架构），但是微服务有一个缺点，服务间的调用有很大的开销，不像单体架构效率那么高。
- 

- 传统的socket连接是阻塞，一个连接就会有一个线程，大部分线程处于等待数据的状态，造成资源浪费，后来出现nio，他可以用一个线程处理所有连接，一个线程处理d读写操作


- http协议快还是 dubboo？

- 入站事件一般由I/O线程触发，以下事件为入站事件：


    ChannelRegistered() // Channel注册到EventLoop
    ChannelActive()     // Channel激活
    ChannelRead(Object) // Channel读取到数据
    ChannelReadComplete()   // Channel读取数据完毕
    ExceptionCaught(Throwable)  // 捕获到异常
    UserEventTriggered(Object)  // 用户自定义事件
    ChannelWritabilityChanged() // Channnel可写性改变，由写高低水位控制
    ChannelInactive()   // Channel不再激活
    ChannelUnregistered()   // Channel从EventLoop中注销
    

- 出站事件一般由用户触发，以下事件为出站事件：


    bind(SocketAddress, ChannelPromise) // 绑定到本地地址
    connect(SocketAddress, SocketAddress, ChannelPromise)   // 连接一个远端机器
    write(Object, ChannelPromise)   // 写数据，实际只加到Netty出站缓冲区
    flush() // flush数据，实际执行底层写
    read()  // 读数据，实际设置关心OP_READ事件，当数据到来时触发ChannelRead入站事件
    disconnect(ChannelPromise)  // 断开连接，NIO Server和Client不支持，实际调用close
    close(ChannelPromise)   // 关闭Channel
    deregister(ChannelPromise)  // 从EventLoop注销Channel





- 实现同步


    1、业务系统调用统一api发送消息；
    
    2、消息发送服务将消息缓存到消息管理器，缓存键值为id；
    
    3、消息发送服务将消息发送到服务器，同时线程进入休眠等待，还可以设置timeout；
    
    4、服务器处理消息后，将响应消息写到tcp流；
    
    5、消息接收服务接收到消息后，判断消息correspondId是否有值，如果有值，则说明有一个线程在等待该消息返回，根据correspondId找到该线程唤醒运行，此刻服务调用即得到返回值；
    
    6、correspondId为空的话，则将消息给业务系统处理。