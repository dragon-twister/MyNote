[TOC]

# Https

## SSL流程
1. 服务器给客户端发送证书，证书包含（X.509标准）

    （1）证书的发布机构CA
    
    （2）证书的有效期
    
    （3）公钥（重要）
    
    （4）证书所有者的信息
    
    （5）CA数字签名（重要）
    
2. 客户端从操作系统拿到CA机构的根证书公钥校验证书签名，成功后生成对称密钥，用服务器的公钥加密后发送给服务器

3. 服务器收到报文后，用私钥读取对称密钥，

4. 后续的传输都使用这个对称密钥加密

## 证书链

- 证书链：证书包含的签名需要上层证书的公钥校验，上层证书的签名需要再上一层的证书公钥来校验，一直追溯到根证书


## 证书的签名、校验

- 服务器先提取证书信息的摘要（有效期、公钥、办法机构信息），再通过ca机构的私钥对摘要签名

- 客户端通过ca机构的公钥解开签名提取摘要，和证书内容的摘要做对比，一致则表明未被修改过。


## truststore和keystore的区别

性质都是一样的，都是存放key的一个仓库，区别在于，truststore里存放的是只包含公钥的数字证书，代表了可以信任的证书，而keystore是包含私钥的。


## Linux证书命令

- 证书生成
	
        keytool 
        -genkey 
        -alias tomcat(别名) 
        -keypass 123456(别名密码) 
        -keyalg RSA(生证书的算法名称，RSA是一种非对称加密算法) 
        -keysize 1024(密钥长度,证书大小) 
        -validity 365(证书有效期，天单位) 
        -keystore W:/tomcat.keystore(指定生成证书的位置和证书名称) 
        -storepass 123456(获取keystore信息的密码)
        -storetype (指定密钥仓库类型) 
        
        keytool -genkey -alias tomcat -keypass 123456 -keyalg RSA -keysize 1024 -validity 365 -keystore W:/tomcat.keystore -storepass 123456


- 查看证书信息：

        keytool -list -v -keystore tomcat.keystore