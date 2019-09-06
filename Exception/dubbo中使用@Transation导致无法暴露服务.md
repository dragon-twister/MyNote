# dubbo中使用@Transation导致无法暴露服务


#### 问题代码:

    @Transactional
    @Service(version = "1.0")
    public class UserInterfacesImpl implements UserInterfaces{
        ....
    }
    
#### 场景

在加上@Transactional后出现无法暴露服务的问题,代码回滚后问题消失

#### 原因分析 

使用了@Transactional后对象被代理,dubbo获取到的是spring的代理接口,不是服务.
    
#### 解决办法 
    
dubbo版本升级到2.5.9

https://github.com/mercyblitz/blogs/blob/master/java/dubbo/Dubbo-Annotation-Driven.md