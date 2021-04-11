---
title: "枚举"
catalog: true
date: 2021-04-19 14:59:04
subtitle:
header-img:
tags:
- JavaSE
---

### 新建一个枚举到底发生了什么

新建一个枚举类

```java
enum Explore {
	HERE, THERE
}
```

对其进行反编译

```java
final class Explore extends java.lang.Enum {
	public static final Explore HERE;
	public static final Explore THERE;
	public static final Explore[] values();
	public static Explore valueOf(java.lang.String);
	static {};
}
```

- 创建enum类时，编译器会生成一个相关的类，这个类继承自java.lang.Enum
- enum类为final，无法从enum类继承子类
- 可以将enum当作一个普通类，在其中新增方法，或者覆盖父类中的方法
- 编译器还为enum类新增了静态的values()方法和valueOf()方法和一个静态代码块

### 基本使用

**values()**

调用enum类型的values()方法，可以遍历enum实例，返回enum实例的数组，该数组中的实例的顺序与实例在enum中声明时的顺讯保持一致。

**ordinal()**

ordinal()方法返回enum实例在声明时的次序，从0开始。

**compareTo()**

Enum类实现了Comparable接口，所以它具有compareTo()方法，该方法通过实例声明时的次序来进行比较，先声明的实例小于后声明的实例。

**getDeclaringClass()**

在enum实例上调用getDeclaringClass()方法，可以获取该实例所属的enum类。

**name()**

name()方法返回enum实例声明时的名字，与toString()方法的效果相同。

**valueOf()**

valueOf()是在Enum中定义的static方法，它根据给定的名字返回相应的enum实例，如果不存在给定名字的实例，将会抛出异常。

### 对enum静态导入

> 在定义enum的同一个文件中无法进行静态导入，定义在默认包中的enum也无法被静态导入

```java
package com.java.enums;
public enum Color {
    RED,
    BLUE,
    YELLOW
}

package com.java.example;
import static com.java.enums.Color.*;
public class StaticImportEnum {
    public static void main(String[] args) {
        // 直接使用enum实例，而不再通过enum类型进行引用
        System.out.println(RED);
        System.out.println(BLUE);
        System.out.println(YELLOW);
    }
}
```

### 为enum添加新方法

> 可以将enum当成一个普通类，然后为其添加新的方法

```java
public enum Color {
    RED("红色"),
    BLUE("蓝色"),
    YELLOW("黄色"); //必须在实例序列的最后添加分号
    
    private String description;
    Color(String description) {
        this.description = description;
    }
    public String getDescription() {
        return description;
    }
    public static void main(String[] args) {
        for(Color color : Color.values())
            System.out.println(color.getDescription());
    }
}
```

- 当要在enum中自定义方法时，必须在enum实例序列的最后添加分号
- 自定义的属性或方法必须放在enum实例序列之后
- enum的构造器默认是private的，只能在enum定义的内部使用其构造器创建enum实例

### 覆盖enum中的方法

```java
public enum Color {
    RED, BLUE, YELLOW;

    @Override
    public String toString() {
        String id = name();
        String lower = id.substring(1).toLowerCase();
        return id.charAt(0) + lower;
    }
}
```

### 在switch中使用enum

> 一般情况下必须使用enum类型来引用enum实例，但是在case语句中可以直接使用enum实例

```java
enum Animal {
    DOG,
    CAT,
    MOUSE
}

public class SwitchWithEnum {
    public static void main(String[] args) {
        Animal animal = Animal.CAT;
        switch (animal) {
            case DOG:
                System.out.println("汪汪");
                break;
            case CAT:
                System.out.println("喵");
                break;
        }
    }
}
```

### values()方法探究

在使用enum类时，可以通过values()方法来获取所有的enum实例，编译器所创建的enum类都继承自Enum类，但是在Enum类中并没有发现values()方法，这是为什么呢？

实际上，values()是由编译器为enum类添加的static方法，所以在Enum类中才没有发现values()的踪迹；此外，编译器还为enum类添加了静态方法valueOf()，该方法与Enum类中所声明的方法不一样，Enum类中的valueOf()方法需要两个参数，而enum类中valueOf()只需要一个参数。

由于values()方法是由编译器插入到enum定义中的static方法，所以如果将enum实例向上转型为Enum，那么将不能再使用values()方法；不过可以通过Class类中的getEnumConstants()方法来获取所有enum实例：

```java
enum Search {
    HITHER, YON
}

public class UpcastEnum {
    public static void main(String[] args) {
        Enum e = Search.HITHER;
        for (Enum en : e.getClass().getEnumConstants())
            System.out.println(en);
    }
}
```

### 让enum实现接口

> 所有的enum都继承自java.lang.Enum类，在Java中只能单继承，所以enum不能再继承其他类，但是可以实现其他接口；要调用接口中的方法必须通过enum实例去调用。

```java
import java.util.Random;

interface Generator<T> {
    T next();
}

enum Color implements Generator<Color>{
    RED, BLUE, YELLOW;
    private Random rand = new Random();
    @Override
    public Color next() {
        return values()[rand.nextInt(values().length)];
    }
}

public class EnumImplementation {
    private static <T> void printNext(Generator<T> rg) {
        System.out.print(rg.next() + ". ");
    }
    public static void main(String[] args) {
        // 通过enum实例去调用
        Color color = Color.RED;
        for (int i = 0; i < 10; i++) {
            printNext(color);
        }
    }
}
```

### 从enum类中随机选取enum实例

> 在很多场景中都会用到从一个enum类中随机选取一个enum实例的功能

```java
import java.util.Random;

public class Enums {
    private static Random rand = new Random();
    public static <T extends Enum<T>> T random(Class<T> ec) {
        return random(ec.getEnumConstants());
    }
    public static <T> T random(T[] values) {
        return values[rand.nextInt(values.length)];
    }
}
```

测试：

```java
enum Activity {
    SITTING, LYING, STANDING, HOPPING, RUNNING, DODGING, JUMPING, FALLING, FLYING
}

public class RandomTest {
    public static void main(String[] args) {
        for (int i = 0; i < 20; i++)
            System.out.print(Enums.random(Activity.class) + " ");
    }
}
```

### 使用接口组织枚举

> 某些时候，需要对enum类中原有的元素进行扩展，或者想通过子类将enum中的元素进行分组。由于enum类是final的，所以无法为其创建子类，但可以通过接口来实现。

**使用接口来组织枚举**

> 在一个接口的内部，创建实现该接口的枚举，以此将元素进行分组，可以达到将枚举元素分类组织的目的。

```java
// 生物
public interface Biology {
    // 动物
    enum Animal implements Biology {
        DOG, CAT, PIG, COW
    }
    // 植物
    enum Botany implements Biology {
        TREE, GRASS
    }
}
```

**使用接口中的枚举**

```java
public class BiglogyUse {
    public static void main(String[] args) {
        // 通过接口引用枚举类再引用枚举实例
        Biology biglogy = Biology.Animal.CAT;
        biglogy = Biology.Botany.GRASS;
    }
}
```

**使用枚举再次封装**

```java
public enum Expo {
    ANIMAL(Biology.Animal.class),
    BOTANY(Biology.Botany.class);

    private Biology[] values;
    Expo(Class<? extends Biology> kind) {
        values = kind.getEnumConstants();
    }
    public Biology randomSelection() {
        return Enums.random(values);
    }
}
```

**使用枚举的枚举**

```java
public class ExpoUse {
    public static void main(String[] args) {
        for (int i = 0; i < 5; i++) {
            for (Expo expo : Expo.values()) {
                Biology biology = expo.randomSelection();
                System.out.println(biology);
            }
            System.out.println("-----");
        }
    }
}
```

**更简便的实现枚举的枚举的方式**

```java
public enum Category {
    ANIMAL(Biology.Animal.class),
    BOTANY(Biology.Botany.class);

    Biology[] values;
    Category(Class<? extends Biology> kind) {
        values = kind.getEnumConstants();
    }
    interface Biology {
        enum Animal implements Biology {
            DOG, CAT, PIG
        }
        enum Botany implements Biology {
            TREE, GRASS
        }
    }
    public Biology randomSelection() {
        return Enums.random(values);
    }
}
```

### EnumSet

> EnumSet是专门为Enum设计的一个集合类，EnumSet中的所有元素都必须是指定的enum类的enum实例。EnumSet可以说是Enum的一个补充，它通过其丰富的方法来完成Enum无法完成的工作。

**特性**

- 集合中的元素有序且不可重复，实际上EnumSet中元素的顺序是由enum类中实例的定义顺序来决定的
- EnumSet集合中不允许加入null元素，否则在使用时会报空指针异常
- EnumSet无法通过构造器进行实例化

**方法介绍**

```java
    // 以一个enum类中的所有enum实例来初始化EnumSet
    <E extends Enum<E>> EnumSet<E> allOf(Class<E> elementType)
    // 以enum类中某个范围的enum实例来初始化EnumSet
    <E extends Enum<E>> EnumSet<E> range(E from, E to)
    // 以目标集合与当前集合元素的差集来初始化EnumSet
    <E extends Enum<E>> EnumSet<E> complementOf(EnumSet<E> s)
    // 以一个或多个enum实例来初始化EnumSet
    <E extends Enum<E>> EnumSet<E> of(E e)
    <E extends Enum<E>> EnumSet<E> of(E e1, E e2)
    <E extends Enum<E>> EnumSet<E> of(E e1, E e2, E e3)
    <E extends Enum<E>> EnumSet<E> of(E e1, E e2, E e3, E e4)
    <E extends Enum<E>> EnumSet<E> of(E e1, E e2, E e3, E e4, E e5)
    <E extends Enum<E>> EnumSet<E> of(E first, E... rest)
    // 从一个集合中复制其所有元素
    <E extends Enum<E>> EnumSet<E> copyOf(EnumSet<E> s)
    <E extends Enum<E>> EnumSet<E> copyOf(Collection<E> c)
```

### EnumMap

> EnumMap是一种特殊的Map，它要求其中的键必须来自一个enum。由于enum本身的限制，所以EnumMap在内部可由数组实现，因此EnumMap的速度很快。

```java
enum Animals {
    DOG, CAT
}

interface Behavior {
    void call();
}

public class EnumMaps {
    public static void main(String[] args) {
        EnumMap<Animals, Behavior> enumMap = new EnumMap<Animals, Behavior>(Animals.class);
        enumMap.put(Animals.DOG, new Behavior() {
            @Override
            public void call() {
                System.out.println("汪汪");
            }
        });
        enumMap.put(Animals.CAT, new Behavior() {
            @Override
            public void call() {
                System.out.println("喵喵");
            }
        });
        for (Map.Entry<Animals, Behavior> entry : enumMap.entrySet()) {
            System.out.print(entry.getKey() + ": ");
            entry.getValue().call();
        }
    }
}
```

**特性**

- EnumMap的键必须来自某个enum类的enum实例
- enum类中enum实例的定义顺序决定了其在EnumMap中的顺序

### 为enum实例提供方法

> 可以为enum实例编写方法，从而为每个enum实例赋予各自不同的行为。要实现常量相关的方法，你需要为enum类定义一个或多个abstract方法，然后为每个enum实例实现该抽象方法。

```java
public enum E08_ConstantSpecificMethod {
    DATE_TIME {
        String getInfo() {
            return DateFormat.getDateInstance().format(new Date());
        }
    },
    CLASSPATH {
        String getInfo() {
            return System.getenv("CLASSPATH");
        }
    },
    VERSION {
        String getInfo() {
            return System.getProperty("java.version");
        }
    };
    abstract String getInfo();
    public static void main(String[] args) {
        for (E08_ConstantSpecificMethod csm : values()) {
            System.out.println(csm.getInfo());
        }
    }
}
```

每个enum实例具备了自己独特的行为，就好像enum实例就好像一个独特的类，且在调用getInfo()的时候体现了多态的行为。但是不能真的将enum实例作为一个类型来使用，因为每个enum元素都是enum类的一个static final实例。由于它们是static实例，所以无法访问外部类的非static元素或方法，所以对于enum实例来说，其行为与一般的内部类也不相同。

**enum实例覆盖enum类的方法**

```java
public enum OverrideConstantSpecific {
    NUT,BOLT,WASHER {
        void f() {
            System.out.println("Overridden method");
        }
    };
    void f() {
        System.out.println("default behavior");
    }
}
```

### 使用enum实现职责链设计模式

> 在职责链设计模式中，程序员以多种不同的方式来解决一个问题，然后将它们链接在一起。当一个请求到来时，它遍历这个链，直到链中的某个解决方案能够处理该请求。

```java
public class Handler {
    enum ProblemHandler {
        STEP_ONE {
            boolean handle(Object obj) {
                // do something there
                return false;
            }
        },
        STEP_TWO {
            boolean handle(Object obj) {
                // do something there
                return false;
            }
        },
        STEP_THREE {
            boolean handle(Object obj) {
                // do something there
                return true;
            }
        };
        abstract boolean handle(Object obj);
    }
    static void handle() {
        for (ProblemHandler handler : ProblemHandler.values()) {
            if (handler.handle(null))
                return;
        }
        System.out.println("处理失败");
    }
}
```
