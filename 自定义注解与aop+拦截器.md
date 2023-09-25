# 自定义注解与aop+拦截器

```java
@Retention(RetentionPolicy.RUNTIME)//这个注解表示我自定义的这个注解是运行时生效，（有的自定义注解可以设置，编译时有效，运行时有效...)
@Target(ElementType.METHOD)//这个注解表示这个自定义注解是用于方法的就是方法的注解，有的是字段的的注解，例如@parm这个注解就是，字段赋值
public @interface NoNeedLogin {
}
```

java.lang.annotation 提供了四种元注解，专门注解其他的注解（在自定义注解的时候，需要使用到元注解）：

 1、**@Documented**：指定被标注的注解会包含在javadoc中。

 ***\*2、@Retention\****： 指定注解的生命周期（源码、class文件、运行时），其参考值见类的定义：java.lang.annotation.RetentionPolicy

>  ●  RetentionPolicy**.SOURCE** : 在编译阶段丢弃。这些注解在编译结束之后就不再有任何意义，所以它们不会写入字节码。@Override, @SuppressWarnings都属于这类注解。
>  ●  RetentionPolicy**.CLASS** : 在类加载的时候丢弃。在字节码文件的处理中有用。注解默认使用这种方式。
>  ●  RetentionPolicy**.RUNTIME** : 始终不会丢弃，运行期也保留该注解，因此可以使用反射机制读取该注解的信息。**我们自定义的注解通常使用这种方式**。

 ***\*3、@Target\****：指定注解使用的目标范围（类、方法、字段等），其参考值见类的定义：java.lang.annotation.ElementType

>  ● ElementType.**CONSTRUCTOR ：**用于描述构造器。
>  ● ElementType.**FIELD ：**成员变量、对象、属性（包括enum实例）。
>  ● ElementType.**LOCAL_VARIABLE**: 用于描述局部变量。
>  ● ElementType.**METHOD ：** 用于描述方法。
>  ● ElementType.**PACKAGE ：**用于描述包。
>  ● ElementType.**PARAMETER** ：用于描述参数。
>  ● ElementType.**ANNOTATION_TYPE**：用于描述参数
>  ● ElementType.**TYPE ：**用于描述类、接口(包括注解类型) 或enum声明。

 **4、@Inherited**：指定子类可以继承父类的注解，只能是类上的注解，方法和字段的注解不能继承。即如果父类上的注解是@Inherited修饰的就能被子类继承。

jdk1.8又提供了以下两个元注解

 **5、@Native：**指定字段是一个常量，其值引用native code。

 ***\*6、@Repeatable\**：**注解上可以使用重复注解，即可以在一个地方可以重复使用同一个注解，像spring中的包扫描注解就使用了这个。

 ***\*7、使用\**[\**@interface\**](https://github.com/interface)\**关键词来定义注解。\****

**注意:** 自定义注解需要使用到元注解。

- 注解方法不能有参数。
- 注解方法的返回类型局限于原始类型，字符串，枚举，注解，或以上类型构成的数组。
- 注解方法可以包含默认值。

**@Target**
是专门用来限定某个自定义注解能够被应用在哪些Java元素上面的,不定义说明可以放在任何元素上。

## 使用案例：

注解类型的实现部分：

根据我们在自定义类的经验，在类的实现部分无非就是书写构造、属性或方法。但是，在自定义注解中，其实现部分**只能定义**一个东西：**注解类型元素（annotation type element）**。咱们来看看其语法：

```scss
public @interface lockAnnotation {

	String business();

	String cache();

	String[] keys();

}
```

 也许你会认为这不就是接口中定义抽象方法的语法嘛？别着急，咱们看看下面这个：

定义注解类型元素时需要注意如下几点：

访问修饰符必须为public，不写默认为public；
该元素的类型只能是基本数据类型、String、Class、枚举类型、注解类型（体现了注解的嵌套效果）以及上述类型的一位数组；
该元素的名称一般定义为名词，如果注解中只有一个元素，请把名字起为value（后面使用会带来便利操作）；
()不是定义方法参数的地方，也不能在括号中定义任何参数，仅仅只是一个特殊的语法；
default代表默认值，值必须和第2点定义的类型一致；
如果没有默认值，代表后续使用注解时必须给该类型元素赋值。
可以看出，注解类型元素的语法非常奇怪，即又有属性的特征（可以赋值）,又有方法的特征（打上了一对括号）。但是这么设计是有道理的，我们在后面的章节中可以看到：注解在定义好了以后，使用的时候操作元素类型像在操作属性，解析的时候操作元素类型像在操作方法。

@Target注解，是专门用来限定某个自定义注解能够被应用在哪些Java元素上面的。它使用一个枚举类型定义如下：

```cobol
public enum ElementType {

    /** 类，接口（包括注解类型）或枚举的声明 */

    TYPE,

    /** 属性的声明 */
    FIELD,

    /** 方法的声明 */

    METHOD,

    /** 方法形式参数声明 */

    PARAMETER,

    /** 构造方法的声明 */

    CONSTRUCTOR,

    /** 局部变量声明 */

    LOCAL_VARIABLE,

    /** 注解类型声明 */

    ANNOTATION_TYPE,
    /** 包的声明 */
    PACKAGE
}

```

（这个就是写在自定义注解得顶上的）

### 2.2.2 @Retention

@Retention注解，翻译为持久力、保持力。即用来修饰自定义注解的生命力。
注解的生命周期有三个阶段：1、Java源文件阶段；2、编译到class文件阶段；3、运行期阶段。同样使用了RetentionPolicy枚举类型定义了三个阶段：

```java
public enum RetentionPolicy {
    /**
     * Annotations are to be discarded by the compiler.
     * （注解将被编译器忽略掉）
     */
    SOURCE,
    
    /**
     * Annotations are to be recorded in the class file by the compiler
     * but need not be retained by the VM at run time.  This is the default
     * behavior.
     * （注解将被编译器记录在class文件中，但在运行时不会被虚拟机保留，这是一个默认的行为）
     */
    CLASS,
     
    /**
     * Annotations are to be recorded in the class file by the compiler and
     * retained by the VM at run time, so they may be read reflectively.
     * （注解将被编译器记录在class文件中，而且在运行时会被虚拟机保留，因此它们能通过反射被读取到）
     * @see java.lang.reflect.AnnotatedElement
     */
    RUNTIME

}
```

### 3.2 特殊语法

特殊语法一：

**如果注解本身没有注解类型元素，那么在使用注解的时候可以省略()，直接写为：@注解名，它和标准语法@注解名()等效！**

```
@Retention(RetentionPolicy.RUNTIME)
@Target(value = {ElementType.TYPE})
@Documented
public @interface FirstAnnotation {
}
//等效于@FirstAnnotation()
@FirstAnnotation
public class JavaBean{
	//省略实现部分
}
```

特殊语法二：

**如果注解本本身只有一个注解类型元素，而且命名为value，那么在使用注解的时候可以直接使用：@注解名(注解值)，其等效于：@注解名(value = 注解值)**

```
@Retention(RetentionPolicy.RUNTIME)
@Target(value = {ElementType.TYPE})
@Documented
public @interface SecondAnnotation {
	String value();
}
 
//等效于@ SecondAnnotation(value = "this is second annotation")
@SecondAnnotation("this is annotation")
public class JavaBean{
	//省略实现部分
}
```

## 使用方法：

要获取自定义注解中的字段值，您可以使用 Java 反射机制来实现。以下是一个示例代码，演示如何获取自定义注解中字段的值：

```java
NoNeedLogin annotation = ((HandlerMethod) handler).getMethodAnnotation(NoNeedLogin.class);//返回Boolean类型
if (annotation != null) {
    String value = annotation.value(); // 假设注解中有一个名为 value 的字段
    // 使用字段值进行相应的逻辑操作
}
```

在上述代码中，`NoNeedLogin` 是您的自定义注解类型，而 `annotation` 是从方法上获取的注解实例。通过调用 `annotation.value()` 方法，您可以获取到自定义注解中名为 `value` 的字段的值。

如果自定义注解中有多个字段，您可以按照相同的方式调用对应的字段访问方法，以获取各个字段的值。

请确保自定义注解中的字段已经定义，并且已经提供了对应的获取方法（也称为注解的成员方法）。如果没有定义对应的获取方法，将无法获取到字段的值。



如果自定义注解中定义了字段（也称为注解的成员变量），您可以使用相同的方法来获取这些字段的值。

假设您的自定义注解 `@NoNeedLogin` 定义了一个字段 `value`，您可以按以下方式获取该字段的值：

```java
NoNeedLogin annotation = ((HandlerMethod) handler).getMethodAnnotation(NoNeedLogin.class);
if (annotation != null) {
    String value = annotation.value();
    // 在这里使用 value 的值进行相应的逻辑操作
}
```

上述代码将首先通过 `getMethodAnnotation(NoNeedLogin.class)` 方法获取到 `@NoNeedLogin` 注解的实例。然后，您可以通过访问注解实例的成员方法来获取字段的值。在这个例子中，假设 `value` 字段是一个字符串类型的字段，您可以使用 `annotation.value()` 来获取它的值。

请注意，注解的字段需要根据其类型和定义的方式来调用相应的方法。例如，如果字段是一个整数类型，您应该使用 `annotation.someIntField()`；如果字段是一个布尔类型，您应该使用 `annotation.isSomeFlag()`。根据字段的类型和名称，调用相应的方法来获取字段的值。

通过这种方式，您可以获取到自定义注解中定义的字段的值，并在代码中进行相应的处理。



这个方法可以与AOP和拦截器一起使用，以实现对带有特定注解的方法进行额外的处理或拦截。

在AOP中，您可以创建一个切面（Aspect）来拦截带有特定注解的方法，并在方法执行前后执行一些额外的逻辑。通过使用 `getMethodAnnotation(NoNeedLogin.class)` 方法，您可以检查方法上是否存在 `@NoNeedLogin` 注解，并根据需要执行相应的操作。

在拦截器中，您可以在请求到达处理方法之前或之后拦截请求，并在其中检查方法上的注解信息。通过使用 `getMethodAnnotation(NoNeedLogin.class)` 方法，您可以判断方法是否标记为不需要登录验证，从而决定是否继续进行请求处理或执行其他逻辑。

这种结合自定义注解和AOP、拦截器的方式，可以在不修改原始方法的情况下，对方法进行额外的处理、拦截或验证。通过提供自定义注解来指定特定的行为，能够更加灵活地控制代码的执行流程和逻辑。

## mybatisplus的分页

```java
@Test
    public void findPage() {
        //创建Page对象，传递两个参数：当前页    每页显示记录数
        Page<User> page = new Page<>(1,3);
        //调用mp方法实现分页
        userMapper.selectPage(page,null);
        //IPage<User> pageModel = userMapper.selectPage(page,null);
        List<User> list = page.getRecords();
        System.out.println(list);
        System.out.println(page.getCurrent());
        System.out.println(page.getPages());
        System.out.println(page.getSize());
        System.out.println(page.getTotal());
        System.out.println(page.hasNext());
        System.out.println(page.hasPrevious());
    }

```

```java
import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
@Configuration
public class MpConfig {

    //分页插件
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        return new PaginationInterceptor();
    }
}

```

```java
package com.example.config;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@EnableTransactionManagement
@MapperScan("com.example.mapper")
@Configuration //配置类
public class MyBatisPlusConfig {
    
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {

        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();

        //添加乐观锁插件
       /* interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());*/

        //添加分页插件
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));


        return interceptor;

    } 
}


```

