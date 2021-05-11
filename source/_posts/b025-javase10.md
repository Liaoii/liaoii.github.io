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

将内部类向上转型为其基类，尤其是转型为一个接口的时候，内部类就有了用武之地。因为内部类能够完全不可见，并且不可用，所得到的只是指向基类或接口的引用，所以能够很方便地隐藏实现细节。

将内部类指定为private，除了其外部类，任何其他类都不能访问它。客户端程序员想了解或访问这些成员，也会受到限制。甚至不能向下转型成private内部类，因为不能访问其名字。于是，private内部类给类的设计者提供了一种途径，通过这种方式可以完全阻止任何依赖于类型的编码，并且完全隐藏了实现的细节。

此外，从客户端程序员的角度来看，由于不能访问任何新增加的、原本不属于公共接口的方法，所以扩展接口是没有价值的。这也给Java编译器提供了生成更高效代码的机会。

```java
public interface Contents {
    int value();
}
public interface Destination {
    String readLabel();
}
```

```java
class Parcel {
    private class PContents implements Contents {
        private int i = 11;
        public int value() {
            return i;
        }
    }
    protected class PDestination implements Destination {
        private String label;
        private PDestination(String whereTo) {
            label = whereTo;
        }
        public String readLabel() {
            return label;
        }
    }
    public Destination destination(String s) {
        return new PDestination(s);
    }
    public Contents contents() {
        return new PContents();
    }
}
public class TestParcel {
    public static void main(String[] args) {
        Parcel p = new Parcel();
        Contents c = p.contents();
        Destination d = p.destination("Tasmania");
    }
}
```

### 在方法和作用域内的内部类

可以在一个方法里面或者在任意的作用域内定义内部类：

- 实现了某类型的接口，于是可以创建并返回其引用。
- 要解决一个复杂的问题，想创建一个类来辅助当前的解决方案，但是又不希望这个类是公共可用的。

```java
public class Parcel {
    public Destination destination(String s) {
        class PDestination implements Destination {
            private String label;
            private PDestination(String whereTo) {
                label = whereTo;
            }
            public String readLabel() {
                return label;
            }
        }
        return new PDestination(s);
    }
    public static void main(String[] args) {
        Parcel parcel = new Parcel();
        Destination d = parcel.destination("Tasmania");
    }
}
```

PDestination是destination()的一部分，而不是Parcel类的一部分，所以在destination()之外不能访问PDestination。但是并不是destination()方法执行完毕，PDestination就不可用了。

可以在同一个类中对多个内部类使用同一个类标识符，而不会产生命名冲突。

```java
public class Parcel {
    private void internalTracking(boolean b) {
        if (b) {
            class TrackingSlip {
                private String id;
                TrackingSlip(String s) {
                    id = s;
                }
                String getSlip() {
                    return id;
                }
            }
            TrackingSlip ts = new TrackingSlip("slip");
            String s = ts.getSlip();
        }
    }
    public void track() {
        internalTracking(true);
    }
    public static void main(String[] args) {
        Parcel parcel = new Parcel();
        parcel.track();
    }
}
```

虽然TrackingSlip类被嵌入在if语句的作用域内，但并不是说该类的创建是有条件的，它与其他类一起编译过了。

### 匿名内部类

```java
public class Parcel {
    public Contents contents() {
        return new Contents() {
            private int i = 11;
            public int value() {
                return i;
            }
        };
    }
    public static void main(String[] args) {
        Parcel parcel = new Parcel();
        Contents c = parcel.contents();
    }
}
```

匿名内部类将返回值的生成与表示这个返回值的类的定义结合在了一起。另外，这个类是匿名的，没有名字。

在匿名内部类末尾的分号，并不是用来标记此内部类结束的，实际上，它标记的是表达式的结束，只不过这个表达式正巧包含了匿名内部类。

如何使用有参构造器来创建匿名内部类：

```java
public class Wrapping {
    private int i;
    public Wrapping(int x) {
        i = x;
    }
    public int value() {
        return i;
    }
}
```

```java
public class Parcel {
    public Wrapping wrapping(int x) {
        return new Wrapping(x) {
            public int value() {
                return super.value() * 10;
            }
        };
    }
    public static void main(String[] args) {
        Parcel parcel = new Parcel();
        Wrapping w = parcel.wrapping(10);
    }
}
```

在匿名类中定义字段时，还能够对其执行初始化操作：

如果定义一个匿名内部类，并且希望它使用一个在其外部定义的对象，那么编译器会要求其参数引用是final的。

```java
public class Parcel {
    public Destination destination(final String dest) {
        return new Destination() {
            private String label = dest;
            public String readLabel() {
                return label;
            }
        };
    }
    public static void main(String[] args) {
        Parcel parcel = new Parcel();
        Destination d = parcel.destination("Tasmania");
    }
}
```

由于匿名内部类没有名字，所以在匿名内部类中不可能有构造器，要想实现类似构造器的行为，可以通过实例初始化来实现：

对于匿名类而言，实例初始化的实际效果就是构造器，但是因为不能重载实例初始化方法，所以匿名内部类仅有一个这样的构造器。

```java
abstract class Base {
    public Base(int i) {
        System.out.println("Base constructor, i = " + i);
    }
    public abstract void f();
}

public class AnonymousConstructor {
    public static Base getBase(int i) {
        return new Base(i) {
            {
                System.out.println("Inside instance initializer");
            }
            public void f() {
                System.out.println("In anonymous f()");
            }
        };
    }
    public static void main(String[] args) {
        Base base = getBase(49);
        base.f();
    }
}
```

此处的变量i不要求是final的，因为i被传递给匿名类的基类的构造器，它没有在匿名类内部被直接使用。

匿名内部类与正规的继承相比有些受限，因为匿名内部类既可以扩展类，也可以实现接口，但是不能两者兼备。而且如果是实现接口，也只能实现一个接口。

