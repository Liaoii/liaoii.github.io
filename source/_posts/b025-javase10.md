---
title: "内部类"
catalog: true
date: 2021-05-10 16:07:43
subtitle:
header-img:
tags:
- JavaSE
---

什么时候需要内部类。

如果内部类是static的，其创建方式有何不同。

### 概述

内部类就是将一个类的定义放在另一个类的定义内部。

内部类是一种非常有用的特性，它允许你把一些逻辑相关的类组织在一起，并控制位于内部的类的可视性。内部类与组合是完全不同的概念。

### 使用内部类的好处

- 利用内部类来隐藏实现细节
- 内部类可以直接与外部类进行通信
- 使代码结构清晰

### 创建内部类

```java
public class Parcel {
    class Contents {
        private int i = 11;
        public int value() {
            return i;
        }
    }
    class Destination {
        private String label;
        Destination(String whereTo) {
            label = whereTo;
        }
        String readLabel() {
            return label;
        }
    }
    public Destination to(String s) {
        return new Destination(s);
    }
    public Contents contents() {
        return new Contents();
    }
    public void ship(String dest) {
        Contents c = contents();
        Destination d = to(dest);
        System.out.println(d.readLabel());
    }
}
```

### 链接到外部类

生成一个内部类的对象时，此对象与制造它的外围对象之间就有了一种联系，它能访问其外围对象的所有成员，而不需要任何特殊条件。

内部类为什么能自动拥有外围类所有成员的访问权？当某个外围类的对象创建了一个内部类对象时，此内部类对象必定会秘密地捕获一个指向那个外围类对象的引用，然后，在你访问此外围类的成员时，就是用那个引用来选择外围类的成员的。

如果内部类是非static的，那么内部类的对象只能在与其外围类的对象相关联的情况下才能被创建，因为构建内部类对象时，必须要有一个指向其外围类对象的引用，如果编译器访问不到这个引用就会报错。

```java
interface Selector {
    boolean end();
    Object current();
    void next();
}
public class Sequence {
    private Object[] items;
    private int next = 0;
    public Sequence(int size) {
        items = new Object[size];
    }
    public void add(Object x) {
        if(next < items.length) {
            items[next++] = x;
        }
    }
    private class SequenceSelector implements Selector {
        private int i = 0;
        public boolean end() {
            return i == items.length;
        }
        public Object current() {
            return items[i];
        }
        public void next() {
            if(i < items.length)
                i++;
        }
    }
    public Selector selector() {
        return new SequenceSelector();
    }
}
```

### .this与.new

如果需要在内部类中生成对外部类对象的引用，可以使用外部类的名字后面紧跟圆点和this。这样产生的引用自动地具有正确的类型，这一点在编译器就被知晓并受到检查，因此没有任何运行时开销。

```java
public class DotThis {
    void f() {
        System.out.println("DotThis.f()");
    }
    public class Inner {
        public DotThis outer() {
            return DotThis.this;
        }
    }
    public Inner inner() {
        return new Inner();
    }
}
```

如果想要创建某个内部类的对象，必须在new表达式中提供对其外部类对象的引用，就需要使用.new语法。

要想创建内部类的对象，不能直接引用外部类的名字来进行创建，而是必须使用外部类的对象来创建该内部类对象。在拥有外部类对象之前是不可能创建内部类对象的，因为内部类对象会暗暗地连接到创建它的外部类对象上。

如果创建的是静态内部类，那么就不需要对外部类对象的引用。

```java
public class DotNew {
    public class Inner {}
    public static void main(String[] args) {
        DotNew dotNew = new DotNew();
        DotNew.Inner dni = dotNew.new Inner();
    }
}
```

### 内部类与向上转型