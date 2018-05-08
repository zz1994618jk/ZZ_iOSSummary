## iOS开发之Socket&XMPP

# 什么是Socket?
- 1.HTTP 就是基于Socket实现;
- 2.网络模型(为了网络的可持续发展)网络模型有OSI参考模型和TCP/IP参考模型OSI模型:局域网;
- 3.局域网
    - 网线-水晶头-指针排序是规定
    - *IP地址可以对mac地址进行绑定
    - 192.168.0.23 - 192.168.0.140 - 最好确保它们之间行走的距离短
    - *交换机也可以实现路由的功能
    - *三层交换机 -带来路由的功能
    - *OSI参考模型，只是为实现网络交互建立一个参考标准
    - *TCP/IP参考模型是对OSI参考模型的简化
    - socket属于会话层
    - *http就是基于tcp数据传输
    - *UDP数据传输是不安全，对方收不收得都是不能保证
- 4.192.168.1.23 域名<br>
```objc
本地回环地址可以用来测试网卡有没有问题
   ping -c 4 127.0.0.1 如果ping不能，网卡坏了，或者网卡没插好
   ping -c 4 localhost的时候，返回的IP地址是127.0.0.1
   •本地有个/etc/hosts文件
   •更改/etc/hosts文件
    >sudo vi /etc/hosts
    >输入 i
    >输入127.0.0.1      www.baidu.com
    >按:输入wq
    >去除某一行 按 dd
   •访问域名的流程
  TCP:传输协议(用什么样的方式进行交互)
  HTTP:协议（数据格式）请求头"content-type" content-size 编码方式URL编码
  比如从广州到北京 坐飞机 高铁 火车（传输协议）
  到了北京后，进行交流用英语 国语，(HTTP)
  在开发过程中，经常发送HTTP请求，获取服务器返回的数据
  访问不了数据：问题可能是 "服务器没有开启"
  http://192.168.0.11
  ```
- 5.Socket(网络服务的一种机制)
   - IO 输入输出流
- 6.简单聊天室(socket)

```objc
*实现登录功能
http://192.168.1.1/login 实现登录的功能：username passoword传服务器
socket: 192.168.1.1:12345
登录指令: iam:zhangsan
*实现发送聊天功能
发送聊天数据指令: msg:xxxxx
•开启ChatSever服务
python 文件名

•http与socket的区别
 >http是基本socket的实现
 >http建立的连接称为短连接
 >socket建立的连接为长连接
 >http传输的数据格式是已经'规定好'
  请求头 content-type content-lengh
  响应头
 >socket实现数据传输是最原始，socket实现的数据传输格式可"自定义"
 登录:iam: 聊天消息msg:
 >http和socket都是基本"tcp"
•SocketServer（C语言）
```
## XMPP
```objc
CoreData Socket
QQ {iam:zhangsan} {msg:xxxxx}
 •每一个公司对即时通讯的需求不一样，所以每个公司都实现自己的即时通讯软件
*配置服务端
 1> 安装数据库mysql
    双击mysql-5.6.12-osx10.7-x86_64.dmg
  > 配置下mysql的用户名的密码
    默认mysql有一个root帐号，密码为空
  >mysql 登录
   mysql -u root -p
  >修改root的密码123456
   mysqladmin -u root password "123456"
  >查看数据库的命令
   mysql> show databases;
 2>安装xmpp服务端(openfire)
  -openfire它是基于java实现
  -如果要安装openfire 电脑必须安装java jdk
   "怎么判断你当前的电脑有没有安装jdk"
   在终端使用java -version 提示没有安装jdk,那就要手动安装
   安装jdk 双击jdk-7u45-macosx-x64.dmg文件
 3>配置数据库表
   访问 /usr/local/目录
   将openfire/resouces/databases的openfire_mysql.sql文件放置桌面
   安装mysqlworkbench
   建立连接
   创建一个数据库(openfire)为openfire服务
   往openfire数据库导入openfire_mysql.sql脚本文件
 4>配置openfire的管理后台
   teacher.local
   openfire管理控制台 用户名是admin 密码：123456
 5>使用"信息"登录的时候，输入用户名的时候
    一个完成的登录名称 ＝（用名 + @ + 服务器名称(teacher.local)）
  >使用spark如果运行错误，安装'JavaForOSX2014-001.dmg'文件
```



  
