---

title: 设计模式-创建型模式（第2篇）
date: 2020-05-04 14:11:04
updated: 2020-05-04 14:11:04
# images: static/img
toc: true
tags: 
  - DesignPattern 
weight: 30

---

## 创建型设计模式

> 通过"对象创建"模式绕开new, 来避免对象创建(new)过程中所导致的紧耦合(依赖具体类),从而支持对象创建的稳定.它是借口抽象后的第一步工作.

上篇说道创建型设计模式一共五种，分别为：

单例模式、抽象工厂模式、原型模式、建造者模式、工厂模式，下面分别详细的讲解这五种模式。

[各模式代码演示源码请访问](https://github.com/He-Song/designPattern)

### 单例模式

单例模式就是采取一定的方法保证在整个软件系统中，对某个类只存在一个对象实例，并且该类只提供一个取得其对象实例的方法（静态方法）

这里说道的采取***一定的方法***一般有以下几种：

1. 饿汉式（静态常量）

   * 构造器私有化（防止new）

   * 类的内部创建对象（申明成员变量的时候直接new）

   * 向外暴露一个静态的公共方法

 ```java
 class Singleton01{
   private final static Singleton01 instance = new Singleton01();
 
   private Singleton01() {
   }
 
   public static Singleton01 getInstance(){
	   return  instance;
   }
 }
 ```

2. 饿汉式（静态代码块）

   * 同上

   * 不同之处在于创建对象是在静态代码块中完成
     饿汉式优缺点：

   * 优点：简单，在类装载的时候就完成实例化，避免线程同步的问题

   * 缺点：没有达到lazy loading 的效果，造成内存浪费

 ```java
 class Singleton02{
   private final static Singleton02 instance;
 
   static {
	   instance = new Singleton02();
   }
   private Singleton02() {
   }
 
   public static Singleton02 getInstance(){
	   return  instance;
   }
 }
 ```
     
3. 懒汉式（线程不安全）不可用

   * 线程不安全 不可用

 ```java
 class Singleton01 {
   private static Singleton01 instance;
 
   private Singleton01() {
   }   
 
   public static Singleton01 getInstance() {
	   // 该方法不能保证线程安全
	   if (null == instance) {
		   instance = new Singleton01();
	   }
	   return instance;
   }
 }
 ```

4. 懒汉式（线程安全，同步方法）

   * 可用但是在方法上加同步锁，效率低

 ```java
 class Singleton02 {
   private static Singleton02 instance;
 
   private Singleton02() {
   }
 
   /*
   方法上加同步锁，效率低
	*/
   public static synchronized Singleton02 getInstance() {
	   if (null == instance) {
		   instance = new Singleton02();
	   }
	   return instance;
   }
 }
 ```

5. 懒汉式（线程不安全， 同步代码块）

   * **不可用**

 ```java
 class Singleton03 {
   private static Singleton03 instance;
 
   private Singleton03() {
   }
 
   public static Singleton03 getInstance() {
	   // 该方法不能保证线程安全
	   if (null == instance) {
		   //此处线程不安全
		   synchronized (Singleton03.class) {
			   instance = new Singleton03();
		   }
	   }
	   return instance;
   }
 }
 ```

 6. 双重检查

   * 提供一个静态的公共方法，加入双重检查的代码，解决线程安全的问题，同时解决懒加载的问题

   * 同时保证了效率，***推荐使用***

     ```java
     class Singleton {
       private static volatile Singleton instance;
     
       private Singleton() {
       }
     
       public static Singleton getInstance() {   
           if (null == instance) {
               synchronized (Singleton.class) {
                   if (instance == null) {
                       instance = new Singleton();
                   }
               }
           }
           return instance;
       }
     }
     ```

7. 静态内部类

   * 外部类装载的时候，静态内部类并不会装载

   * 当用到这个静态内部类的时候才回去装载，类在装载的时候没有线程同步的问题

   * 因此该问题即解决了线程同步的问题同时也达到了懒加载的效果，***建议使用***

 ```java
 /**
 * 静态内部类
 */
 class Singleton1 {
   private Singleton1() {
   }
 
   private static class SingletonInstance {
	   private static volatile Singleton1 INSTANCE = new Singleton1();
   }
 
   public static Singleton1 getInstance() {
	   return SingletonInstance.INSTANCE;
   }
 }
 ```

8. 枚举

   * 借助jdk1.5中添加的枚举来实现单例模式，不仅能避免多线程同步的问题，而且还能防止反序列化重新创建对象

   * 该方式是Effective Java 作者 Josh Bloch 提倡的方式

   * ***推荐使用***

 ```java
 /**
 * 枚举
 */   
 enum Singleton2 {
   INSTANCE;
   public void sayOK() {
	   System.out.println("hha");
   }
 }
 ```

### 抽象工厂模式

> 提供一个接口,让该接口负责创建一系列"相关或者相互依赖的对象", 无需指定它们具体的类

![image-20211014161212640](image-20211014161212640.png)

### 工厂模式

#### 工厂方法模式

> 定义一个用于创建对象的接口, 让子类决定实例化哪一个类.Factory Method 使得一个类的实例化延迟(目的:解耦)到子类
>
> 一个对象对应一个工厂

![image-20211014155437368](image-20211014155437368.png)

### 原型模式

> 使用原型实例指定创建对象的种类,然后通过拷贝这些原型来创建新的对象

![image-20211014161916032](image-20211014161916032.png)

### 建造者模式

> 动机: 一个复杂对象的创建工作

![image-20211014162343359](image-20211014162343359.png)

> 定义: 将一个复杂的对象的构建与其表示相分离,使得同样的构建过程(稳定)可以创建不同的表示(变化)

![image-20211014163459756](image-20211014163459756.png)
