---
title: "单件模式（Singleton）"
catalog: true
date: 2021-03-08 17:01:55
subtitle:
header-img:
tags:
- Design Patterns
---

### 抛出问题

有一些对象我们只需要一个，这类对象只能有一个实例，如果制造出多个实例，就会导致许多问题产生。

### 使用全局静态变量实现单实例

**缺点**

如果将对象赋值给一个全局变量，那么必须在程序一开始就创建好对象，如果这个对象非常耗费资源，而程序在这次的执行过程中又一直没有用到它，那就会造成资源的浪费。

### 经典的单件模式实现

```java
public class Singleton {
    private static Singleton uniqueInstance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (uniqueInstance == null) {
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}
```

- 利用一个静态变量来记录Singleton类的唯一实例
- 将构造器声明为私有的，只有Singleton自身可以调用构造器
- 使用静态方法返回实例
- 使用延迟实例化（lazy instance），可以在我们使用对象时才进行创建

### 定义单件模式

确保一个类只有一个实例，并提供一个全局访问点。

### 经典单件模式实现存在的问题

在多线程环境下，经典的单件模式实现有可能会产生多个实例。

### 处理多线程问题

**同步方法**

```java
public class Singleton {
    private static Singleton uniqueInstance;
    private Singleton() {}
    public static synchronized Singleton getInstance() {
        if (uniqueInstance == null) {
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}
```

在getInstance()方法上加上synchronized关键字，可以保证在多线程环境下也不会产生多个实例。

但是在方法上使用synchronized会降低性能，其实只有在第一次执行此方法时才真正需要同步，一旦设置好uniqueInstance变量，就不再需要同步这个方法了。之后每次调用这个方法，同步都是一种累赘。

**使用急切方式创建实例，而不是使用延迟实例化**

如果应用程序总是创建并使用单件实例，或者在创建和运行时方面的负担不太繁重，可以使用急切（eagerly）创建方式创建实例

```java
public class Singleton {
    private static Singleton uniqueInstance = new Singleton();
    private Singleton() {}
    public static Singleton getInstance() {
        return uniqueInstance;
    }
}
```

**使用双重检测锁**

```java
public class Singleton {
    private volatile static Singleton uniqueInstance;
    private Singleton() {}
    public static Singleton getInstance() {
        if(uniqueInstance == null) {
            synchronized (Singleton.class) {
                if(uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

valatile关键词确保当uniqueInstance变量被初始化成Singleton实例时，多个线程正确地处理uniqueInstance变量。

