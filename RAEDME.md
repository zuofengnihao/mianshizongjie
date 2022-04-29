# 八股文

### JDK JRE JVM 区别
* JDK 开发环境 包含JRE JRE包含JVM
* JRE 运行环境 包含JVM
* JVM 虚拟机 运行`.class`字节码文件的一个程序
  * 类加载子系统
  * 运行时数据区
  * 执行引擎

### `==`与`equals`区别
* `==`如果对比的是基本类型那就是对比的是值，如果对比的是对象，那么就是对比堆中的地址是否一致。
* `equals`是`object`类的方法，如果不重写，那么就是使用`==`来对比，即对比的也是堆中的地址。

### `final`的使用
* 修饰类，类不可以被继承
* 修饰方法，方法不可被修改
* 修饰属性，属性不可以被修改，如果是对象不可以修改对象的本身，但是可以修改对象的属性
> 匿名内部类只能使用final修饰的变量，因为方法中的定义的变量在栈中，会随方法结束而被回收，但是内部类不会被回收，它需要一份这个变量的copy，而为了保证这个值的安全性，必须使用final修饰

### String StringBuilder StringBuffer 区别
* String final修饰的，修改String的值其实是产生新的对象，而StringBuffer和StringBuilder不会。
* StringBuffer线程安全，StringBuilder是非线程安全的。
* 如果需要经常改变操作的对象，建议使用StringBuffer和StringBuilder，不考虑线程安全用StringBuilder，考虑线程安全用StringBuffer

### 重载重写的区别
* 重载：一般在同一个类中使用，方法的名称相同，参数列表不同，在JVM层面这重载的方法本就不是同一个方法
* 重写：一般在父子类中使用，方法的名称相同，参数列表相同，返回值与异常小于等于父类方法，在jvm层面为invokevirtual，会生成动态链接与方法表

### 接口和抽象类
* 抽象类abstract修饰的类，接口是implement
* 抽象类可以有实现方法，可以有类属性，接口不能有实现方法和类属性，但是可以有default属性
* 接口是用来规范约束实现类的，抽象类则是为了代码复用，抽象类不可以被实例化

### List和Set
* List 有序，Set无序。
* List可重复，Set不可重复。
* List可以存在多个Null，Set只能存在一个Null
* List可根据下标直接获取元素，Set必须通过iterator遍历获取

### hashcode和equals
* hashcode是计算hash值的方法。equals如果不重写，在object类中只是做了==的计算，即比较栈中的值或地址引用。
> hashset判断是否重复，先判断hashcode的值，如果相等再equals方法判断，如果没重写equals则直接对比的是内存地址

### ArrayList与LinkList
* ArrayList使用的动态数组，是一块连续的内存空间，适合下标访问，即访问速度快。关于插入速度，其实主要原因是扩容，因为数组长度固定，超出长度插入时，会触发扩容，而扩容需要重新申请一块连续的地址，再将原有的数据copy过去，比较浪费性能。
* LinkList使用的是

# Spring

### Spring用到哪些设计模式
1. 工厂模式：BeanFactory就是简单工厂模式的体现，用来创建对象的实例
2. 单例模式：Bean默认的为单例模式
3. 代理模式：Spring的Aop功能用到了JDK的动态代理和CGLIB字节码技术
4. 模板方法：用来解决代码重复问题，比如 RestTemplate JmsTemplate，JpaTemplate
5. 观察者模式：定义对象一种一多关系的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都会得到通知被自动更新，如Spring中Listener的实现-ApplicationListener

### Spring框架支持的几种bean的作用阈
1. 第一种singleton（单例）：bean在每个spring ioc容器中有一个实例对象
2. 第二种prototype（原型）：一个bean的定义可以有多个实例
3. 第三种request（请求）：每次http请求都会创建一个bean，该作用域仅在基于web的spring ApplicationContext情形下有效
4. 第四种session：在一个http session中，一个bean定义对已一个实例，该作用域仅在基于web的spring ApplicationContext情形下有效
5. 第五种global-session：在一个全局的Http Session中，一个bean定义对应一个实例，该作用域仅在基于web的spring ApplicationContext情形下有效
> 注意：缺省的Spring Bean的作用域是Singleton。使用prototype作用域需要慎重思考，因为频繁创建和销毁对象会带来很大的性能开销（gc回收 stop the world）

### 单例Bean是线程安全的吗?
原文：不是，Spring框架中的单例bean不是线程安全的。  

spring中的bean默认是单例模式，spring框架并没有对单例bean进行多线程的封装处理。  

实际上大部分时候spring bean 无状态的，所有某种程度上来说bean也是线程安全的，但如果bean有状态的话，那就要开发者自己去保证线程安全了，最简单的就是改变bean的作用域，把singleton变为prototype，这样请求bean相当于new Bean()了，所以就可以保证线程安全了。

* 有状态就是有数据存储
* 无状态就是不会保存数据

个人理解：很多人并没有理解什么是线程安全，总是问这个是不是线程安全的啊，那个是不是线程安全的啊，一般只有问某种数据结构的类才这样问，我不是很喜欢这个bean是不是线程安全的，你没有保存多线程会访问的和修改的属性那就是线程安全的，保存了会被多线程访问并修改的属性就是线程不安全的，如果都不带修改，多线程访问也只是查看，那还是线程安全的，线程安全的定义就是这样的，一个数据只会被一个线程访问那怎么都是线程安全的，类似ThreadLocal。

### 既然单例Bean不是线程安全的那Spring是如何处理的?
就是我上面提到的ThreadLocal

# Redis

### 单线程的Redis为什么这么快

1. Redis基于内存，当然也可以持久化，但是持久化操作都是基于fork子进程和利用Linux系统的页缓存技术来完成，并不影响redis的性能。
2. 单线程操作：单线可以避免上下文的切换。
3. 合理高效的数据结构
4. 采用了非阻塞IO（BIO），多路复用模型，利用Select、poll、epoll可以同时监察多个流的IO事件，

# Nginx

### 什么是Nginx？
Nginx是一个 轻量级/高性能的反向代理Web服务器，用于 HTTP、HTTPS、SMTP、POP3 和 IMAP 协议。他实现非常高效的反向代理、负载平衡，他可以处理2-3万并发连接数，官方监测能支持5万并发，现在中国使用nginx网站用户有很多，例如：新浪、网易、 腾讯等。

### Nginx 有哪些优点？
* 跨平台、配置简单。
* 非阻塞、高并发连接：处理 2-3 万并发连接数，官方监测能支持 5 万并发。
* 内存消耗小：开启 10 个 Nginx 才占 150M 内存。
* 成本低廉，且开源。
* 稳定性高，宕机的概率非常小。
* 内置的健康检查功能：如果有一个服务器宕机，会做一个健康检查，再发送的请求就不会发送到宕机的服务器了。重新将请求提交到其他的节点上

### Nginx应用场景？
* http服务器。Nginx是一个http服务可以独立提供http服务。可以做网页静态服务器。
* 虚拟主机。可以实现在一台服务器虚拟出多个网站，例如个人网站使用的虚拟机。
* 反向代理，负载均衡。当网站的访问量达到一定程度后，单台服务器不能满足用户的请求时，需要用多台服务器集群可以使用nginx做反向代理。并且多台服务器可以平均分担负载，不会应为某台服务器负载高宕机而某台服务器闲置的情况。
* nginz 中也可以配置安全管理、比如可以使用Nginx搭建API接口网关,对每个接口服务进行拦截。

### Nginx怎么处理请求的？
```
server {        				 		# 第一个Server区块开始，表示一个独立的虚拟主机站点
   listen       80; 					# 提供服务的端口，默认80
   server_name  localhost; 				# 提供服务的域名主机名
   location / { 						# 第一个location区块开始
     root   html; 						# 站点的根目录，相当于Nginx的安装目录
     index  index.html index.html;  	# 默认的首页文件，多个用空格分开
} # 第一个location区块结果
```
* 首先，Nginx 在启动时，会解析配置文件，得到需要监听的端口与 IP 地址，然后在 Nginx 的 Master 进程里面先初始化好这个监控的Socket(创建 S ocket，设置 addr、reuse 等选项，绑定到指定的 ip 地址端口，再 listen 监听)。
* 然后，再 fork(一个现有进程可以调用 fork 函数创建一个新进程。由 fork 创建的新进程被称为子进程 )出多个子进程出来。
* 之后，子进程会竞争 accept 新的连接。此时，客户端就可以向 nginx 发起连接了。当客户端与nginx进行三次握手，与 nginx 建立好一个连接后。此时，某一个子进程会 accept 成功，得到这个建立好的连接的 Socket ，然后创建 nginx 对连接的封装，即 ngx_connection_t 结构体。
* 接着，设置读写事件处理函数，并添加读写事件来与客户端进行数据的交换。
* 最后，Nginx 或客户端来主动关掉连接，到此，一个连接就寿终正寝了。

### Nginx 是如何实现高并发的？
如果一个 server 采用一个进程(或者线程)负责一个request的方式，那么进程数就是并发数。那么显而易见的，就是会有很多进程在等待中。等什么？最多的应该是等待网络传输。  

而 Nginx 的异步非阻塞工作方式正是利用了这点等待的时间。在需要等待的时候，这些进程就空闲出来待命了。因此表现为少数几个进程就解决了大量的并发问题。  

Nginx是如何利用的呢，简单来说：同样的 4 个进程，如果采用一个进程负责一个 request 的方式，那么，同时进来 4 个 request 之后，每个进程就负责其中一个，直至会话关闭。期间，如果有第 5 个request进来了。就无法及时反应了，因为 4 个进程都没干完活呢，因此，一般有个调度进程，每当新进来了一个 request ，就新开个进程来处理。

##### 回想下，BIO 是不是存在酱紫的问题？
Nginx 不这样，每进来一个 request ，会有一个 worker 进程去处理。但不是全程的处理，处理到什么程度呢？处理到可能发生阻塞的地方，比如向上游（后端）服务器转发 request ，并等待请求返回。那么，这个处理的 worker 不会这么傻等着，他会在发送完请求后，注册一个事件：“如果 upstream 返回了，告诉我一声，我再接着干”。于是他就休息去了。此时，如果再有 request 进来，他就可以很快再按这种方式处理。而一旦上游服务器返回了，就会触发这个事件，worker 才会来接手，这个 request 才会接着往下走。  

这就是为什么说，Nginx 基于事件模型。  

由于 web server 的工作性质决定了每个 request 的大部份生命都是在网络传输中，实际上花费在 server 机器上的时间片不多。这是几个进程就解决高并发的秘密所在。即：  

webserver 刚好属于网络 IO 密集型应用，不算是计算密集型。  

异步，非阻塞，使用 epoll ，和大量细节处的优化。也正是 Nginx 之所以然的技术基石。

### 什么是正向代理？
一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。  

客户端才能使用正向代理。正向代理总结就一句话：代理端代理的是客户端。例如说：我们使用的OpenVPN 等等。  

### 什么是反向代理？
反向代理服务器可以隐藏源服务器的存在和特征。它充当互联网云和web服务器之间的中间层。这对于安全方面来说是很好的，特别是当您使用web托管服务时。

### Nginx目录结构有哪些？
```
[root@localhost ~]# tree /usr/local/nginx
/usr/local/nginx
├── client_body_temp
├── conf                             # Nginx所有配置文件的目录
│   ├── fastcgi.conf                 # fastcgi相关参数的配置文件
│   ├── fastcgi.conf.default         # fastcgi.conf的原始备份文件
│   ├── fastcgi_params               # fastcgi的参数文件
│   ├── fastcgi_params.default       
│   ├── koi-utf
│   ├── koi-win
│   ├── mime.types                   # 媒体类型
│   ├── mime.types.default
│   ├── nginx.conf                   # Nginx主配置文件
│   ├── nginx.conf.default
│   ├── scgi_params                  # scgi相关参数文件
│   ├── scgi_params.default  
│   ├── uwsgi_params                 # uwsgi相关参数文件
│   ├── uwsgi_params.default
│   └── win-utf
├── fastcgi_temp                     # fastcgi临时数据目录
├── html                             # Nginx默认站点目录
│   ├── 50x.html                     # 错误页面优雅替代显示文件，例如当出现502错误时会调用此页面
│   └── index.html                   # 默认的首页文件
├── logs                             # Nginx日志目录
│   ├── access.log                   # 访问日志文件
│   ├── error.log                    # 错误日志文件
│   └── nginx.pid                    # pid文件，Nginx进程启动后，会把所有进程的ID号写到此文件
├── proxy_temp                       # 临时目录
├── sbin                             # Nginx命令目录
│   └── nginx                        # Nginx的启动命令
├── scgi_temp                        # 临时目录
└── uwsgi_temp                       # 临时目录
```

### Nginx配置文件nginx.conf有哪些属性模块?
```
worker_processes  1;                                     # worker进程的数量
events {                                                 # 事件区块开始
    worker_connections  1024;                            # 每个worker进程支持的最大连接数
}                                                        # 事件区块结束
http {                                                   # HTTP区块开始
    include       mime.types;                            # Nginx支持的媒体类型库文件
    default_type  application/octet-stream;				# 默认的媒体类型
    sendfile        on;                                 # 开启高效传输模式
    keepalive_timeout  65;                              # 连接超时
    server {                                            # 第一个Server区块开始，表示一个独立的虚拟主机站点
        listen       80;                                # 提供服务的端口，默认80
        server_name  localhost;                         # 提供服务的域名主机名
        location / {                                    # 第一个location区块开始
            root   html;                               	# 站点的根目录，相当于Nginx的安装目录
            index  index.html index.htm;                # 默认的首页文件，多个用空格分开
        }                                               # 第一个location区块结果
        error_page   500502503504  /50x.html;           # 出现对应的http状态码时，使用50x.html回应客户
        location = /50x.html {                          # location区块开始，访问50x.html
            root   html;                                # 指定对应的站点目录为html
        }
    }
```

### cookie和session区别？

##### 共同：
存放用户信息。存放的形式：key-value格式 变量和变量内容键值对。

##### 区别：

###### cookie
* 存放在客户端浏览器
* 每个域名对应一个cookie，不能跨跃域名访问其他cookie
* 用户可以查看或修改cookie
* http响应报文里面给你浏览器设置
* 钥匙（用于打开浏览器上锁头）

###### session:
* 存放在服务器（文件，数据库，redis）
* 存放敏感信息
* 锁头
  
### 为什么 Nginx 不使用多线程？
* Apache: 创建多个进程或线程，而每个进程或线程都会为其分配 cpu 和内存（线程要比进程小的多，所以 worker 支持比 perfork 高的并发），并发过大会榨干服务器资源。
* Nginx: 采用单线程来异步非阻塞处理请求（管理员可以配置 Nginx 主进程的工作进程的数量）(epoll)，不会为每个请求分配 cpu 和内存资源，节省了大量资源，同时也减少了大量的 CPU 的上下文切换。所以才使得 Nginx 支持更高的并发。

### nginx和apache的区别
轻量级，同样起web服务，比apache占用更少的内存和资源。

抗并发，nginx处理请求是异步非阻塞的，而apache则是阻塞性的，在高并发下nginx能保持低资源，低消耗高性能。

高度模块化的设计，编写模块相对简单。

最核心的区别在于apache是同步多进程模型，一个连接对应一个进程，nginx是异步的，多个连接可以对应一个进程。

| Nignx | Apache |
|---|---|
|||
