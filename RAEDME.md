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