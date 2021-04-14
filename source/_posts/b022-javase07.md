---
title: "复用类"
catalog: true
date: 2021-04-12 14:05:05
subtitle:
header-img:
tags:
- JavaSE
---

### 使用组合进行复用

> 在新的类中产生现有类的对象叫做组合。组合只是复用了现有程序代码的功能，而非它的形式。对于非基本类型的对象，必须将其引用置于新类中，而对于基本类型数据则可以直接定义。

```java
class WaterSource {
    private String s;
    WaterSource() {
        System.out.println("WaterSource");
        s = "Constructed";
    }
    public String toString() {
        return s;
    }
}

public class E01_SprinklerSystem {
    private String value1, value2, value3, value4;
    private WaterSource source = new WaterSource();
    private int i;
    private float f;
}
```

**toString()方法**

当将一个String与其他对象相加时，由于只能将一个String和另一个String相加，因此编译器会先将其他对象转换成String，这时就需要调用对象的toString()方法获得一个String，每一个非基本类型的对象都有一个toString()方法，当编译器需要一个String而你却只有一个对象时，该方法便会被调用。

**对象初始化的地方**

- 在定义对象的地方，这意味着它们总是能够在构造器被调用之前被初始化。
- 在类的构造器中。
- 在正要使用对象之前，这种方式称为惰性初始化，在生成对象不值得以及不必每次都生成对象的情况下，这种方式可以减少额外的负担。
- 使用实例初始化。

### 使用继承进行复用

> 当创建一个类时，总是在继承，因此，除非已明确指出要从其他类中继承，否则就是在隐式地从Java的标准根类Object进行继承。继承使用extends关键字实现，当继承一个基类时，会自动得到基类中所有的域和方法。
>
> 虽然子类（又叫导出类）会自动得到基类中所有的域和方法，但是可能会被访问权限所限制。所以，为了继承，一般的规则是将所有的数据成员都指定为private，将所有的方法指定为public。

```java
class Animal {
    public void say() {
        System.out.println("Animal");
    }
}
public class Dog extends Animal {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.say();
    }
}
```

**重写基类中的方法**

虽然导出类会自动获得基类中的方法，但自动获得的方法其行为没什么变化，要想其行为产生变化，可以对其进行重写。

除了使用基类中的方法，导出类也可以添加自己的方法。

可以通过super来调用基类中的方法。

```java
class Animal {
    public void say() {
        System.out.println("Animal");
    }
}
public class Dog extends Animal {
    public void say() {
        System.out.println("Dog");
        super.say();
    }
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.say();
    }
}
```

**为什么在子类中可以使用基类中的方法**

当创建一个导出类的对象时，该对象包含了一个基类的子对象，这个子对象与使用基类直接创建的对象是一样的。二者区别在于，后者来自于外部，而基类的子对象被包装在导出类对象内部。导出类之所以能自动获得基类中的域和方法，实际上是获得了基类的对象。导出类之所以能使用基类中的方法也是通过该对象去调用的。

**初始化基类**

如何才能保证导出类中一定包含基类的子对象，那就是在导出类的构造器中调用基类的构造器来执行初始化，Java会自动在导出类的构造器中插入对基类构造器的调用。

构建过程是从基类开始向外扩散的，基类在导出类构造器可以访问它之前就已经完成了初始化。即使不为一个类创建构造器，编译器也会提供一个默认的构造器，该构造器将调用基类的构造器。

**带参数的构造器**

首先，如果不为类提供任何构造器，编译器将会默认提供一个无参构造器。但是如果我们手动的为类提供了有参构造器，这时编译器将不再默认提供无参构造器，此时必须手动构建无参构造器。

其次，在导出类的构造器中首先要做的就是调用基类的构造器，即使没有在导出类的构造器中显式的用super()调用基类构造器，编译器也会隐式的调用基类的无参构造器。如果此时基类中没有提供无参构造器，编译器就会报错，我们要么为基类添加无参构造器，要么使用super()再配合参数调用基类的有参构造器。

### 代理

> 如果使用继承，导出类将暴露基类中的所有方法，有时候我们并不想暴露基类的所有方法，就可以使用代理。代理是以组合的形式来实现继承的效果，但这种继承的效果却是可控的，可以选择只提供成员对象中方法的一个子集。

```java
class SpaceShipControls {
    void up(int velocity){}
    void down(int velocity){}
    void left(int velocity){}
    void right(int velocity){}
    void forward(int velocity){}
    void back(int velocity){}
    void turboBoost() {}
}

public class SpaceShipDelegation {
    private String name;
    private SpaceShipControls controls = new SpaceShipControls();
    public SpaceShipDelegation(String name) {
        this.name = name;
    }
    public void up(int velocity) {
        controls.up(velocity);
    }
    public void down(int velocity) {
        controls.down(velocity);
    }
    public void left(int velocity) {
        controls.left(velocity);
    }
    public void right(int velocity) {
        controls.right(velocity);
    }
    public void forward(int velocity) {
        controls.forward(velocity);
    }
    public void back(int velocity) {
        controls.back(velocity);
    }
}
```

### 同时使用组合与继承

同时使用组合与继承来创建更加复杂的类

```java
class Plate {
    Plate(int i) {
        System.out.println("Plate constructor");
    }
}
class DinnerPlate extends Plate {
    DinnerPlate(int i) {
        super(i);
        System.out.println("DinnerPlate constructor");
    }
}
class Utensil {
    Utensil(int i) {
        System.out.println("Utensil constructor");
    }
}
class Spoon extends Utensil {
    Spoon(int i) {
        super(i);
        System.out.println("Spoon constructor");
    }
}
public class PlaceSetting {
    private Spoon spoon;
    private DinnerPlate dinnerPlate;

    public PlaceSetting(int i) {
        spoon = new Spoon(i);
        dinnerPlate = new DinnerPlate(i);
    }
}
```

### 正确清理

虽然Java中有自动垃圾回收机制，但是我们并不知道垃圾回收器何时会被调用，也不清楚它是否真的会被调用。因此，有时在类的生命周期中需要执行一些必须的清理活动时，就得显式地编写一个特殊方法来做这件事，并且为了确保清理动作一定会执行，必须将这一清理动作置于finally字句之中，以预防异常的出现。

在我们创建导出类对象时，会先逐层向上创建基类的子对象，所以对于导出来来说它是依赖基类的子对象的，如果在执行清理动作时先清理了基类子对象，可能会使导出类发生错误。所以清理的顺序正好与创建时相反。对于类中的成员对象同样如此，需要按照初始化的反顺序进行清理。

一旦涉及垃圾回收，能够信赖的事就不会很多了。垃圾回收器可能永远也不会被调用，即使被调用，它也可能以任何它想要的顺序来回收对象。最好的办法是除了内存以外，不能依赖垃圾回收器去做任何事。如果需要进行清理，最好自己编写清理方法，但不要使用finalize()方法。

```java
class Shape {
    Shape(int i) { System.out.println("Shape constructor"); }

    void dispose() { System.out.println("Shape dispose"); }
}
class Circle extends Shape {
    Circle(int i) {
        super(i);
        System.out.println("Drawing Circle");
    }
    void dispose() {
        System.out.println("Erasing Circle");
        super.dispose();
    }
}
class Triangle extends Shape {
    Triangle(int i) {
        super(i);
        System.out.println("Drawing Triangle");
    }
    void dispose() {
        System.out.println("Erasing Triangle");
        super.dispose();
    }
}
class Line extends Shape {
    private int start, end;
    Line(int start, int end) {
        super(start);
        this.start = start;
        this.end = end;
        System.out.println("Drawing Line: " + start + ", " + end);
    }
    void dispose() {
        System.out.println("Erasing Line: " + start + ", " + end);
        super.dispose();
    }
}
public class CADSystem extends Shape {
    private Circle circle;
    private Triangle triangle;
    private Line[] lines = new Line[3];
    public CADSystem(int i) {
        super(i + 1);
        for (int j = 0; j < lines.length; j++)
            lines[j] = new Line(j, j * j);
        circle = new Circle(1);
        triangle = new Triangle(1);
        System.out.println("Combined constructor");
    }
    public void dispose() {
        System.out.println("CADSystem.dispose()");
        triangle.dispose();
        circle.dispose();
        for (int i = lines.length - 1; i >= 0; i--) 
            lines[i].dispose();
        super.dispose();
    }
    public static void main(String[] args) {
        CADSystem system = new CADSystem(47);
        try {} finally {
            system.dispose();
        }
    }
}

```

### 名称屏蔽

如果Java的基类拥有某个已被多次重载的方法名称，那么在导出类中重新定义该方法名称并不会屏蔽其在基类中的任何版本。因此，无论是在该层或者它的基类中对方法进行定义，重载机制都可以正常工作。

```java
class Homer {
    char doh(char c) {
        System.out.println("doh(char)");
        return 'd';
    }
    float doh(float f) {
        System.out.println("doh(float)");
        return 1.0f;
    }
}
class Milhouse {}
class Bart extends Homer {
    void doh(Milhouse m) {
        System.out.println("doh(Milhouse)");
    }
}
public class Hide {
    public static void main(String[] args) {
        Bart bart = new Bart();
        bart.doh(1);
        bart.doh('x');
        bart.doh(1.0f);
        bart.doh(new Milhouse());
    }
}
```

### @Override注解的作用

@Override注解表示该方法需要重写父类中的方法，但在重写时@Override注解并不是必须的，只要当前方法的方法签名与返回值和基类中的方法保持一致，就表示导出类是在重写基类中的方法。

使用@Override注解的好处就是能明确指明当前方法是在进行重写，编译器会强制检查当前方法与基类总被重写的方法的方法签名与返回值是否一致，如果不一致，编译器将会报错。这样就可以防止我们在重写的时候意外地进行了重载。

### 在组合与继承之间如何选择

组合和继承都允许在新的类中放置子对象，组合是显式地这样做，而继承则是隐式地做。

组合技术通常用于想在新类中使用现有类的功能而非它的接口这种情形。即在新类中嵌入某个对象，让其实现所需要的功能，但新类的用户看到的只是为新类所定义的接口，而非所嵌入对象的接口。为取得此效果，需要将新类中的子对象指定为private。

继承是使用某个现有类，并开发一个它的特殊版本。其目的主要是为了将一个通用类为了某种特殊需要而将其特殊化。

组合体现的是 has-a 关系，而继承体现的是 is-a 关系。关于到底是该用组合还是用继承，一个最清晰的判断方法就是是否需要从导出类向基类进行向上转型。如果必须向上转型，则继承是必要的，否则就应该慎用继承。

### protected关键字

由于访问权限的限制，要想导出类自动获得基类中的所有方法，一般的规则是将所有的数据成员都指定为private，将所有的方法指定为public。但是将所有的方法都指定为public也存在一个问题，那就是这些方法都将被暴露给其他所有类。protected关键字可以解决这个问题，当将一个方法声明为protected时，导出类可以自动获得它，但对于其他类来说却是private的。

protected还提供了包访问权限，所以同一个包中的类也可以访问protected成员。

尽管可以创建protected域，但是最好的方式还是将域保持为private，这样可以保留更改的权利，然后通过protected方法来控制类的继承者的访问权限。

```java
class Animal {
    protected void say() {
        System.out.println("Animal");
    }
}
public class Dog extends Animal {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.say();
    }
}
```

### 向上转型

> 使用继承最重要的就是用来表现新类和基类之间的关系，这种关系就是：导出类是基类的一种类型。由于继承可以确保基类中所有的方法在导出类中也同样有效，所以能够向基类发送的所有信息同样也可以向导出类发送。
>
> 将导出类对象的引用转换成基类对象的引用的动作，叫做向上转型。

```java
class Instrument {
    public void play() {}

    static void tune(Instrument i) {
        i.play();
    }
}
public class Wind extends Instrument {
    public static void main(String[] args) {
        Wind flute = new Wind();
        Instrument.tune(flute);
    }
}
```

### 为什么叫做向上转型

在传统的类的继承图的绘制方法中，会将根置于页面的顶端，然后逐渐向下。所以由导出类转型成基类，在继承图上是向上移动的，因此一般称为向上转型。

由于向上转型是从一个较专用类型向较通用类型转换，所以转换总是安全的。也就是导出类是基类的一个超集，导出类可能比基类含有更多的方法，但导出类一定会包含基类中所拥有的方法。也正因为如此，向上转型的过程可能会发生丢失方法，也就是无法通过基类的引用来调用导出类中多出来的方法。

### final数据

> 当final关键字用于修饰类的成员对象时，表示该成员对象是一个常量。
>
> final可以用于修饰类中的成员对象，如果成员对象是基本类型，final会使其值恒定不变；如果成员对象是非基本类型，final会使其引用恒定不变，一旦引用被初始化指向一个对象，就无法再把它改为指向另一个对象，然而，对象本身是可以修改的。

**编译器常量**

编译器常量必须同时满足：类型为基本数据类型、以final关键字修饰、在定义时就对其赋值。

对于编译器常量，编译器可以将该常量值代入任何可能用到它的计算式中，然后在编译时执行计算式，这减轻了一些运行时的负担。

**为final成员对象赋值的时机**

为final成员对象赋值可以是在编译期，也可以是在运行期

```java
public class InitFinal {
    private static Random rand = new Random();
    //编译期赋值
    private final int compile = 10;
    //运行期赋值
    private final int run = rand.nextInt();
}
```

**static与final搭配使用**

static表示某个成员对象对于该类的所有对象只有一份；final表示某个成员对象是一个常量。static与final同时使用时，被修饰的成员对象全部用大写字母命名，并且字与字之间用下划线隔开。

**空白final**

在定义final成员对象时可以先不为其赋初值，但是编译器要求final成员对象在使用前必须被初始化。要想在使用前完成初始化，要么在其定义时就进行初始化，要么在类的构造器中进行初始化，如果类有多个构造器，必须在每一个构造器中都要进行初始化。且final成员对象一旦初始化之后，就不能改变了。

### final参数

在参数列表中将参数声明为final，如果该参数是基本数据类型，则在方法中无法对该参数重新赋值，但是可以进行计算；如果该参数是非基本数据类型，则在方法中无法将该参数指向新的对象，但是可以修改对象中的值。

```java
class Gizmo {
    public int count;
    public void spin() {}
}
public class E15_FinalArguments {
    void with(final Gizmo g) {
        //可以修改对象中的值
        g.count = 10; 
        //无法将其指向新的对象
        //g = new Gizmo(); 
    }
    void without(Gizmo g) {
        g = new Gizmo();
        g.spin();
    }
    int g(final int i) {
        //不可以重新进行赋值
        //i = 10;
        //可以进行计算
        return i + 1;
    }
}
```

### final方法

> final关键字修饰方法时，可以将方法锁定，防止任何继承类修改它的含义。它可以确保在继承中使方法行为保持不变，并且不会被覆盖。

**final和private**

类中所有的private方法都隐式地指定为是final的。由于无法取用private方法，所以也就无法覆盖它。可以对private方法添加final修饰词，但这并不能给该方法增加任何额外的意义。

覆盖只有在某方法是基类的接口的一部分时才会出现。如果某方法为private，它就不是基类的接口的一部分。它仅是一些隐藏于类中的程序代码，只不过是具有相同的名称而已。当在导出类中以相同的名称生成一个public、protected或包访问权限方法时，此时只是生成了一个新的方法，而没有覆盖基类中的方法。

由于private方法无法触及而且能有效隐藏，所以除了把它看成是因为它所归属的类的组织结构的原因而存在外，其他任何事物都不需要考虑到它。

### final类

当将某个类的整体定义为final时，就表示不打算继承该类，而且也不允许其他人这样做。所以无法继承一个final类。

由于final类禁止继承，所以final类中所有的方法都隐式的指定为final，可以为final类中的方法添加final修饰词，但这不会增添任何意义。

### 继承与初始化

```java
class Parent {
    private static int x = printInit("static Parent.x initialized");
    private int k = printInit("Parent.k initialized");
    static int printInit(String s) {
        System.out.println(s);
        return 47;
    }
    Parent() {
        System.out.println("parent constructor");
    }
}
class Insect extends Parent {
    private int i = 9;
    protected int j;
    private int k = printInit("Insect.k initialized");
    Insect() {
        System.out.println("i = " + i + ", j = " + j);
        j = 39;
    }
    private static int x = printInit("static Insect.x initialized");
    static int printInit(String s) {
        System.out.println(s);
        return 47;
    }
}
public class E18_Beetle extends Insect {
    private int k = printInit("Beetle.k initialized");
    public E18_Beetle() {
        System.out.println("k = " + k);
        System.out.println("j = " + j);
    }
    private static int x = printInit("static Beetle.x initialized");
    public static void main(String[] args) {
        System.out.println("Start");
        E18_Beetle b = new E18_Beetle();
    }
}
```

- 当试图访问一个类时，类加载器会找到该类的 .class 文件进行加载，如果该类有基类，类加载器会继续加载该基类。以此类推，直到最顶级的基类加载完成。
- 从最顶级的基类开始初始化其static成员，直到最底层的导出类的static成员初始化完成
- 从最顶层的基类开始，对类中的非static成员对象按声明顺序进行初始化，然后调用构造器。接着初始化导出类中的非static成员对象和调用其构造器。依次类推，知道最底层的导出类的非static成员对象初始化完成和调用构造器。