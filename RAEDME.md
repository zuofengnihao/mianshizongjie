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