---
title: "多态"
catalog: true
date: 2021-04-13 17:07:22
subtitle:
header-img:
tags:
-JavaSE
---

### 为什么能够向上转型

对象既可以作为它自己本身的类型使用，也可以作为它的基类型使用，把对某个对象的引用视为对其基类型的引用的做法被称作向上转型。从导出类向上转型到基类可能会缩小接口，但不会比基类的全部接口更窄。

```java
class Instrument {
    public void play(Note n) {
        System.out.println("Instrument.play()");
    }
}
class Wind extends Instrument {
    public void play(Note n) {
        System.out.println("Wind.play " + n);
    }
}
public class Music {
    public static void tune(Instrument i) {
        i.play(Note.MIDDLE_C);
    }
    public static void main(String[] args) {
        Instrument flute = new Wind();
        tune(flute);
    }
}

```

但是使用基类的引用来替代导出类的引用也存在一个问题，那就是编译器要如何才能知道基类的引用具体指向的是哪一个导出类对象呢。编译器也无法得知，只有在运行时根据对象的类型才能知道具体指向的导出类对象。也就是动态绑定。

### 方法调用绑定

将一个方法调用同一个方法主体关联起来被称为绑定。

**前期绑定**

在程序执行前进行绑定叫做前期绑定，一般由编译器和连接程序实现。

**后期绑定**

在运行时根据对象的类型进行绑定叫做后期绑定，后期绑定也叫做动态绑定或者运行时绑定。要想实现后期绑定，就必须具有某种机制，以便在运行时能判断对象的类型，从而调用恰当的方法。编译器一直不知道对象的类型，但是方法调用机制能找到正确的方法体，并加以调用。

Java中除了static方法和final方法之外，其他所有的方法都是后期绑定。private方法也是属于final方法，所以private方法不会进行后期绑定。将某个方法声明为final一方面是为了防止其他人覆盖该方法，但更重要的一点是可以有效地关闭动态绑定，然后编译器可以为final方法调用生成更有效的代码。

### 为什么需要向上转型

如果不使用向上转型，就得将程序中原来针对基类所提供的方法修改为针对每一个导出类都提供一个方法，这无疑会增加很多耦合的代码。不仅如此，每当扩展出一个新的导出类时，都得为该导出类创建同其他导出类一样的处理方法。另外，如果要添加新的处理方法，又得需要花费大量精力来针对每一个导出类都提供实现。并且，如果我们忘记重载某个方法，编译器不会返回任何错误信息，这样关于类型的整个处理过程就变得难以操纵。

```java
enum Note {
    MIDDLE_C, C_SHARP, B_FLAT;
}
class Instrument {
    public void play(Note n) {
        System.out.println("Instrument.play()");
    }
}
class Wind extends Instrument {
    public void play(Note n) {
        System.out.println("Wind.play " + n);
    }
}
class Stringed extends Instrument {
    public void play(Note n) {
        System.out.println("Stringed.play() " + n);
    }
}
class Brass extends Instrument {
    public void play(Note n) {
        System.out.println("Brass.play() " + n);
    }
}
public class Music {
    public static void tune(Wind i) {
        i.play(Note.MIDDLE_C);
    }
    public static void tune(Stringed i) {
        i.play(Note.MIDDLE_C);
    }
    public static void tune(Brass i) {
        i.play(Note.MIDDLE_C);
    }
    public static void main(String[] args) {
        Wind wind = new Wind();
        Stringed stringed = new Stringed();
        Brass brass = new Brass();
        tune(wind);
        tune(stringed);
        tune(brass);
    }
}
```

### 使用向上转型的好处

既然在运行期间可以通过对象的类型进行动态的绑定，我们就可以编写只与基类打交道的程序代码了，这些代码对所有的导出类都可以正确运行。也就是说，发送消息给某个对象，让该对象去断定应该做什么事。

将程序设计成只与基类接口通信，这样的程序是可扩展的，可以从通用的基类继承出新的数据类型，从而添加一些新功能。而那些操纵基类接口的方法不需要任何改动就可以应用于新类。

### 无法覆盖私有方法

由于private方法被自动认为是final方法，而且private对导出类是屏蔽的。因此，在这种情况下，导出类中的同名方法就是一个全新的方法。既然基类中的private方法对导出类不可见，因此在导出类中也无法对基类中的方法进行重载。

只有非private方法才可以被覆盖。并且在导出类中，对于基类中的private方法，最好采用不同的名字。

```java
public class PrivateOverride {
    private void f() {
        System.out.println("private f()");
    }
    public static void main(String[] args) {
        E05_PrivateOverride po = new Derived();
        po.f();//结果为 private f(),属于前期绑定
    }
}
class Derived extends PrivateOverride {
    public void f() {
        System.out.println("public f()");
    }
}
```

### 域与静态方法不能多态

只有普通的方法调用可以是多态的，如果直接访问某个域，这个访问就将在编译期进行解析。

实际上，通常会将所有的域都设置成private，然后通过方法来访问它们。另外，不应该将基类中的域和导出类中的域赋予相同的名字，否则容易令人混淆。

```java
class Super {
    public int field = 0;
    public int getField() {
        return field;
    }
}
class Sub extends Super {
    public int field = 1;
    public int getField() {
        return field;
    }
    public int getSuperField() {
        return super.field;
    }
}
public class FieldAccess {
    public static void main(String[] args) {
        Super sup = new Sub();
        System.out.println("sup.field = " + sup.field + ", sup.getField() = " + sup.getField());
        Sub sub = new Sub();
        System.out.println("sub.field = " + sub.field + ", sub.getField() = " + sub.getField() +
                ", sub.getSuperField() = " + sub.getSuperField());
    }
}
```

如果某个方法是静态的，它的行为就不具有多态性：

```java
class StaticSuper {
    public static String staticGet() {
        return "Base staticGet()";
    }
    public String dynamicGet() {
        return "Base dynamicGet()";
    }
}
class StaticSub extends StaticSuper {
    public static String staticGet() {
        return "Derived staticGet()";
    }
    public String dynamicGet() {
        return "Derived dynamicGet()";
    }
}
public class StaticPolymorphism {
    public static void main(String[] args) {
        StaticSuper sup = new StaticSub();
        System.out.println(sup.staticGet());
        System.out.println(sup.dynamicGet());
    }
}
```

### 	构造器内部的多态方法的行为

如果在一个构造器的内部调用正在构造的对象的某个动态绑定方法，会发生什么情况？

在一般的方法内部，动态绑定的调用是在运行时才决定的，因为对象无法知道它是属于方法所在的那个类，还是属于那个类的导出类。如果要调用构造器内部的一个动态绑定方法，就要用到那个方法的被覆盖后的定义，但是被覆盖的方法在对象被完全构造之前就会被调用。

在任何构造器内部，整个对象可能只是部分形成，如果构造器只是在构建过程中的一个步骤，并且该对象所属的类是从这个构造器所属的类导出的，那么导出部分在当前构造器正在被调用的时刻仍旧是没有被初始化的。然而，一个动态绑定的方法调用却会向外深入到继承层次结构内部，它可以调用导出类里的方法。如果在构造器内部这样做，那么就可能会调用某个方法，而这个方法所操纵的成员可能还未进行初始化。

因此，编写构造器时有一条有效的规则：用尽可能简单的方法使对象进入正常状态，如果可以的话，避免调用其他方法。在构造器内唯一能够安全调用的那些方法是基类中的final方法，其中private方法也属于final方法，这些方法不能被覆盖。

```java
class Glyph {
    void draw() {
        System.out.println("Glyph.draw()");
    }
    Glyph() {
        System.out.println("Glyph() before draw()");
        draw();
        System.out.println("Glyph() after draw()");
    }
}
class RoundGlyph extends Glyph {
    private int radius = 1;
    RoundGlyph(int r) {
        radius = r;
        System.out.println("RoundGlyph.RoundGlyph(), radius = " + radius);
    }
    void draw() {
        System.out.println("RoundGlyph.draw(), radius = " + radius);
    }
}
public class PolyConstructors {
    public static void main(String[] args) {
        new RoundGlyph(5);
    }
}
```

### 更详细的初始化过程

- 当第一次访问一个类时，类加载器开始工作，逐层加载其基类，直到最后一个基类加载完成
- 从最顶层的基类开始，逐层对static成员进行初始化，直到最底层的导出类。
- 当使用new准备创建最底层的导出类对象时，先在堆中为该对象分配存储空间，并将存储空间初始化为二进制的零
- 在创建导出类对象之前需要创建基类的子对象，
- 在堆中为基类分配存储空间，并将存储空间初始化为二进制的零
- (直到最顶层的基类的内存空间初始化完成...)
- 按照声明顺序对基类的成员进行初始化
- 调用基类的构造器，基类的子对象初始化完成，回到导出类
- 按照声明顺序初始化导出类的成员
- 调用导出类的构造器，导出类初始化完成

### 协变返回类型

在导出类中的覆盖方法可以返回基类中被覆盖方法的返回类型的导出类型

```java
class Grain {
    public String toString() {
        return "Grain";
    }
}
class Wheat extends Grain {
    public String toString() {
        return "Wheat";
    }
}
class Mill {
    Grain process() {
        return new Grain();
    }
}
class WheatMill extends Mill {
    Wheat process() {
        return new Wheat();
    }
}
public class CovariantReturn {
    public static void main(String[] args) {
        Mill mill = new Mill();
        Grain grain = mill.process();
        System.out.println(grain);
        mill = new WheatMill();
        grain = mill.process();
        System.out.println(grain);
    }
}
```

### 用继承进行设计

多态是一种如此巧妙的工具，看起来似乎所有的东西都可以使用继承。但是事实上，当我们使用现成的类来建立新类时，应该优先选择组合。因为使用继承技术反而会使设计变得复杂。组合不会强制我们的程序设计进入继承的层次结构中，而且，组合更加灵活，它可以动态选择类型；相反，继承在编译时就需要知道确切类型。

引用在运行时可以与另一个不同的对象重新绑定起来，可以在运行期间获得动态灵活性；但是，不能在运行期间决定继承不同的对象，它必须要求在编译期间完全确定下来。

一条通用的准则是：用继承表达行为间的差异，并用字段表达状态上的变化。

**纯继承（is-a）**

纯继承表示只有在基类中已经建立的方法才可以在导出类中被覆盖。此时二者有着完全相同的接口，导出类可以接收所有发送给基类的消息，基类也可以接收所有发送给导出类的消息。使用纯继承就可以不用去知道子类的任何额外信息，避免向下转型。

**向下转型**

但是只要使用继承就不可避免的需要一些额外的扩展接口，且一旦向上转型，基类就无法访问导出类中额外的扩展部分。如果需要访问导出类中额外的部分，就需要进行向下转型。

要如何保证向下转型的安全性，在Java语言中，所有转型都会得到检查，即使是进行一次普通的加括弧形式的类型转换，在进入运行期时仍然会对其进行检查，以便保证它的确是我们希望的那种类型。如果不是，就会返回一个ClassCastException。这种在运行期间对类型进行检查的行为称作运行时类型识别（RTTI）。