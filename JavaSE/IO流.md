# IO流

[TOC]

### 什么是比特(Bit),什么是字节(Byte),什么是字符(Char),它们长度是多少,各有什么区别

1.  Bit最小的二进制单位 ，是计算机的操作部分 取值0或者1
2.  Byte是计算机操作数据的最小单位由8位bit组成 取值（-128-127）
3.  Char是用户的可读写的最小单位，在java里面由16位bit组成 取值（0-65535）

### 说下常用的io流  

输入输出流是相对内存而言的
字节流：InputStream、OutputStream  
字符流：Reader、Writer(这四个都是抽象类)  
IO类设计时使用了装饰者设计模式

读取文件的时候会用到fileinputstream，当我们希望输入六读取更快的时候会吧fileinputsream传入和bufferinputsteam的构造函数做一个装饰也就是功能的拓展，让文件缓存到内存读取更快。装饰者模式主要实现的是功能的动态扩展。通常的扩展方法是通过继承，但是导致动态扩展类创建过多的类。

## NIO

传统io是阻塞的，当一个线程读取文件，这个读的过程是阻塞的，而异步nio在读的时候既可以读又可以写。nio里面有一个channel这个对象既可以读也可以写，此外有一个selecter多路复用管理器，用来管理多个chanel，这样就不用堆

### 什么是java序列化，如何实现java序列化？

例如，在web开发中，如果对象被保存在了Session中，tomcat在重启时要把Session对象序列化到硬盘，这个对象就必须实现Serializable接口。如果对象要经过分布式系统进行网络传输或通过rmi等远程调用，这就需要在网络上传输对象，被传输的对象就必须实现Serializable接口。


- 序列化和反序列化的概念

　　把对象转换为字节序列的过程称为对象的序列化。 
　　把字节序列恢复为对象的过程称为对象的反序列化。 
　　对象的序列化主要有两种用途： 
　　1） 把对象的字节序列永久地保存到硬盘上，通常存放在一个文件中； 
　　2） 在网络上传送对象的字节序列。

　　在很多应用中，需要对某些对象进行序列化，让它们离开内存空间，入住物理硬盘，以便长期保存。比如最常见的是Web服务器中的Session对象，当有 10万用户并发访问，就有可能出现10万个Session对象，内存可能吃不消，于是Web容器就会把一些seesion先序列化到硬盘中，等要用了，再把保存在硬盘中的对象还原到内存中。
　　
- JDK类库中的序列化API

　　java.io.ObjectOutputStream代表对象输出流，它的writeObject(Object obj)方法可对参数指定的obj对象进行序列化，把得到的字节序列写到一个目标输出流中。 
　　java.io.ObjectInputStream代表对象输入流，它的readObject()方法从一个源输入流中读取字节序列，再把它们反序列化为一个对象，并将其返回。 只有实现了Serializable和Externalizable接口的类的对象才能被序列化。Externalizable接口继承自 Serializable接口，实现Externalizable接口的类完全由自身来控制序列化的行为，而仅实现Serializable接口的类可以 采用默认的序列化方式 。
　　
　　对象序列化包括如下步骤： 
　　
- 1 创建一个对象输出流，它可以包装一个其他类型的目标输出流，如文件输出流； 
　　
　　2 通过对象输出流的writeObject()方法写对象。

　　对象反序列化的步骤如下： 
　　
　　1 创建一个对象输入流，它可以包装一个其他类型的源输入流，如文件输入流； 
　　
　　2 通过对象输入流的readObject()方法读取对象。
　　
　　测试证明, flush操作是直接写入磁盘, 而且是每个索引链或者说每个DWPT写入一个segment, 但是只flush不commit, 在closeIndexWriter时, 所有写入磁盘的数据会回滚, 即被删除

flush动作不是将内存中缓存的索引写入磁盘, 而是写入操作系统缓冲区???
只有Index Writer上的commit操作才会导致ram directory上的数据完全同步到文件。Index Writer提供了实时获得reader的API，这个调用将导致flush操作，生成新的segment，但不会commit（fsync），从而减少 了IO。新的segment被加入到新生成的
reader里。从返回的reader里，可以看到更新。所以，只要每次新的搜索都从IndexWriter获得一个新的reader，就可以搜索到最新的内容。这一操作的开销仅仅是flush，相对commit来说，开销很小。

Lucene的index组织方式为一个index目录下的多个segment。新的doc会加入新的segment里，这些新的小segment每隔一段时间就合并起来。因为合并，总的segment数

量保持的较小，总体search速度仍然很快。为了防止读写冲突，lucene只创建新的segment，并在任何active的reader不在使用后删除掉老的segment。

flush是把数据写入到操作系统的缓冲区，只要缓冲区不满，就不会有硬盘操作。

commit是把所有内存缓冲区的数据写入到硬盘，是完全的硬盘操作。

重量级操作。这是因为，Lucene索引中最主要的结构posting通过VINT和delta的格式存

储并紧密排列。合并时要对同一个term的posting进行归并排序，是一个读出，合并再生

成的过程。





在IndexWriter获得reader的方法中，主要调用了两个方法doflush()和maybeMerge()。doflush() 将调用DocumentsWriter的flush方法，生成新的segment，返回的reader将能访问到新的segment。 DocumentsWriter接收多个document添加，并写入到同一个segment里。每一个加入的doc会经过多个DocConsumer组 成的流水线，他们包括StoredFieldsWriter（内部调用 FieldsWriter），TermVectorsTermsWriter，FreqProxTermsWriter，NormsWriter等。在外 界没有主动调用flush的情况下，RAM buffer全用完了或者加入的doc数足够大后，才会创建新的segment并flush到目录中。