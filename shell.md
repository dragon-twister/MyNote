# shell 命令

## vim

dd 剪切
yy 复制
p 粘贴
u 回退
/ 搜索
n 查看下一个关键字
N 查看上一个关键字

## linux：
nohup & ：退出的时候不退出程序
uname -a ：查看系统信息
cat /etc/issue：查看系统信息（容器内） 。 cat /proc/version 或 uname -a 查到的是宿主机的系统。
nohup java -jar refech-tax.jar --spring.datasource.url=jdbc:oracle:thin:@192.168.81.77:1521:NINVOICE --spring.datasource.username=jrwz2_zx  --spring.datasource.password=jrwz2_zx  --server.port=8081  --logging.file=logs/refech.log   > /dev/null 2>&1 &


## Docker:
docker ps：正在运行的容器
docker ps -a：所有容器
docker rm：删除容器
docker rmi：删除镜像
docker run --name refech-tax1 -p 8081:8080 -it 9d2740120aa7  /bin/bash : 运行镜像
docker exec -it cb5121f88849 /bin/bash ：进入容器
docker save -o C:\Users\Administrator\Desktop\111\zxxt-shandong.tar 9de2014f4654   导出镜像到本地
docker load -i C:\Users\Administrator\Desktop\111\zxxt-shandong.tar  导入镜像
docker cp C:\Users\Administrator\Desktop\centos7-jdk8\refech-tax.jar 0c0c304d8b4f:/usr/local
docker build -t centos7-jdk8:1.0.0 . :  -t name:tag dockerfile的目录
docker build -t centos7-jdk8:1.0.0 -f . C:  :  -t name:tag  -f dockerfile的目录 MV,COPY,ADD使用的相对位置  (解决Dockerfile文件与需要拷贝的文件不在同一个目录下的问)
Ctrl+P+Q ：退出容器操作界面
apt-get update、apt-get install vim : 无法使用vi时


## git

sudo apt-get install git

git config --global user.name "your name"        //设置用户名
git config --global user.email "your email"        //设置电子邮箱
git config user.name                                   //查看用户名
git config user.email                                  //查看电子邮箱
git config --list                                      //查看所有用户信息

注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

## CMD:
telnet 192.192.193.211 22 : 测试端口
dir:查看当前目录的文件
