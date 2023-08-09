---
title: 设计模式-类图（第0篇）
date: 2020-05-03 13:19:31
updated: 2020-05-03
toc: true
tags:
- DesignPattern 
- 类图
weight: 10

---

## 类图六大关系

类与类之间的关系分为六种：

* 依赖（Dependence）
* 泛化/继承（Generalization）
* 实现（Implementation）
* 关联（Association）
* 聚合（Aggregation）
* 组合（Composition）

### 依赖关系（Dependence）

只要类中用到了对方即构成依赖关系。

* 类的成员属性
* 方法的返回类型
* 方法接收的参数类型
* 方法内部使用到

### 泛化关系（Generalization）

泛化关系其实就继承关系，它是依赖关系的特例

### 实现关系（Implementation）

实际上就是A类实现B类，实现关系是依赖关系的特例

### 关联关系（Association）

关联关系实际是 ***类与类之间的联系，他是依赖关系的特例***

特点： 具有导航性：即单向关系与双向关系
具有多重性： 0 ， 1 ， *

### 聚合关系（Aggregation）

聚合关系表示整体与部分的关系，***部分可以与整体分开***。
聚合关系是关联关系的特例,因此也具有导航性与多重性

eg: person 与 IDCard 的关系

### 组合关系（Composition）

也是整体与部分的关系，只是***整体与部分不可分开***

eg: person 与 head 的关系
