---
title: "注解"
catalog: true
date: 2021-04-20 12:22:10
subtitle:
header-img:
tags:
- JavaSE
---

### 定义注解

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface UseCase {
    int id();
    String description() default "no description";
}
```

**特性**

- 注解的定义使用@interface来标注
- 注解也会被编译成class文件
- 定义注解时可以通过元注解来对当前注解进行注解
- 注解中可以定义元素，且可以为元素指定默认值；没有元素的注解称为标记注解
- 注解不支持继承

### 使用注解

在使用注解时，可以在注解后的括号内以名-值对的形式使用注解的元素，其中值的类型需要与定义时指定的类型一致；可以对一个目标同时使用多个注解，使用多个注解的时候同一个注解不能重复使用。

```java
public class E02_PasswordUtils {
    @UseCase(id = 47, description = "Password must contain at least one numeric")
    public boolean validatePassword(String password) {
        return password.matches("\\w*\\d\\w*");
    }
    @UseCase(id = 48)
    public String encryptPassword(String password) {
        return new StringBuilder(password).reverse().toString();
    }
    @UseCase(id = 49, description = "New passwords can't equal previously used ones")
    public boolean checkForNewPassword(List<String> prevPasswords, String password) {
        return !prevPasswords.contains(password);
    }
}
```

如果在注解中定义了名为value的元素，并且在应用该注解的时候，名为value的元素是唯一需要赋值的一个元素，那么此时无需使用名-值对的这种语法，而只需在括号内给出value元素所需的值即可。

```java
// 定义注解
import java.lang.annotation.*;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {
    int value() default 0;
}
// 使用注解
public class Testable {
    public void execute() {
        System.out.println("Executing...");
    }
    @Test(11) // 快捷方式
    void testExecute() {
        execute();
    }
}
```

### 元注解

> Java 7中有四种元注解，元注解专门用来注解其他的注解

**@Target**

> 表示该注解可以用于什么地方

- ElementType.CONSTRUCTOR：表示该注解用于构造器上
- ElementType.FIELD：表示该注解用于域上，也可用于enum实例上
- ElementType.LOCAL_VARIABLE：可用于局部变量上
- ElementType.METHOD：可用于方法上
- ElementType.PACKAGE：可用于包声明上
- ElementType.PARAMETER：可用于参数声明上
- ElementType.TYPE：可用于类、接口、注解、enum类上

可以为@Target指定一个或多个值，多个值用逗号进行分隔。如果想要将注解应用于所有的ElementType，那么可以省去@Target元注解。

**@Retention**

> 表示需要在什么级别保存该注解信息

- RetentionPolicy.SOURCE：注解将被编译器丢弃
- RetentionPolicy.CLASS：注解在class文件中可用，但会被VM丢弃
- RetentionPolicy.RUNTIME：VM将在运行期也保留注解，因此可以通过反射机制读取注解的信息

**@Documented**

> 将此注解包含在Javadoc中

**@Inherited**

> 允许子类继承父类中的注解

### 注解处理器

> 当定义了注解且使用了注解后，需要创建注解处理器来读取注解并使其产生作用。

```java
public class E03_UseCaseTracker {
    public static void trackUseCases(List<Integer> useCases, Class<?> cl) {
        for (Method method : cl.getDeclaredMethods()) {
            UseCase useCase = method.getAnnotation(UseCase.class);
            if (useCase != null) {
                System.out.println("Found Use Case:" + useCase.id() + " " + useCase.description());
                useCases.remove(new Integer(useCase.id()));
            }
        }
        for (int i : useCases) {
            System.out.println("Warning: Missing use case-" + i);
        }
    }

    public static void main(String[] args) {
        List<Integer> useCases = new ArrayList<>();
        Collections.addAll(useCases, 47, 48, 49, 50);
        trackUseCases(useCases, E02_PasswordUtils.class);
    }
}
```

### 注解中的元素

注解元素可用的类型有：

- 八种基本类型（char、byte、short、int、long、float、double、boolean）
- String、Class、enum、Annotation
- 以上类型的数组

不可以使用除以上类型以外的其他类型；不可以使用任何包装类型。

### 对注解元素值的限制

- 如果为注解定义了注解元素，要么为其指定默认值，要么在使用注解的时候为注解元素赋值。
- 对于非基本类型的注解元素，无论是在声明为其指定默认值，还是在使用注解时为注解元素赋值，都不能以null作为其值。
- 既然每个注解元素都不能赋值为null，要想表示某个注解元素为空就只能自己定义一些特殊的值，比如空字符串或负数，以此约定某个注解元素为空。

