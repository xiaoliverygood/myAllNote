# ssm

## 什么是JavaBean

JavaBean就是有一定规范的Java实体类，跟普通类差不多，不同的是类内部提供了一些公共的方法以便外界对该对象内部属性进行操作，比如set、get操作，实际上，就是我们之前一直在用的：

```java
public class User{
	private String name;
	private int age;
	public String getName(){
		return name;
	}
	public String getAge(){
		return age;
	}
	public void setName(String name){
		this.name = name;
	}
	public void setAge(int age){
		this.age = age;
	}
}
```

它的所有属性都是private，所有的属性都可以通过get/set方法进行访问，同时还需要有一个无参构造（默认就有）

因此我们之前编写的很多类，其实都可以是一个JavaBean。

## IoC理论基础

在我们之前的图书管理系统Web应用程序中，我们发现，整个程序其实是依靠各个部分相互协作，共同完成一个操作，比如要展示借阅信息列表，那么首先需要使用Servlet进行请求和响应的数据处理，然后请求的数据全部交给对应的Service（业务层）来处理，当Service发现要从数据库中获取数据时，再向对应的Mapper发起请求。

它们之间就像连接在一起的齿轮，谁也离不开谁：

![image-20230404204312151](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304042043226.png)

就像一个团队，每个人的分工都很明确，流水线上的一套操作必须环环相扣，这是一种高度耦合的体系。

虽然这样的体系逻辑非常清晰，整个流程也能够让人快速了解，但是这样存在一个很严重的问题，我们现在的时代实际上是一个软件项目高速迭代的时代，我们发现很多App三天两头隔三差五地就更新，而且是什么功能当下最火，就马不停蹄地进行跟进开发，因此，就很容易出现，之前写好的代码，实现的功能，需要全部推翻，改成新的功能，那么我们就不得不去修改某些流水线上的模块，但是这样一修改，会直接导致整个流水线的引用关系大面积更新。

就像我不想用这个Service实现类了，我想使用其他的实现类用不同的逻辑做这些功能，那么这个时候，我们只能每个类都去挨个进行修改，当项目特别庞大时，光是改个类名就够你改一天。

因此，高耦合度带来的缺点是很明显的，也是现代软件开发中很致命的问题。如果要改善这种情况，我们只能将各个模块进行解耦，让各个模块之间的依赖性不再那么地强。也就是说，Service的实现类，不再由我们决定，而是让程序自己决定，所有的实现类对象，全部交给程序来管理，所有对象之间的关系，也由程序来动态决定，这样就引入了IoC理论。

IOC是Inversion of Control的缩写，翻译为：“控制反转”，把复杂系统分解成相互合作的对象，这些对象类通过封装以后，内部实现对外部是透明的，从而降低了解决问题的复杂度，而且可以灵活地被重用和扩展。

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304042042018.png)

我们可以将对象交给IoC容器进行管理，比如当我们需要一个接口的实现时，由它根据配置文件来决定到底给我们哪一个实现类，这样，我们就可以不用再关心我们要去使用哪一个实现类了，我们只需要关心，给到我的一定是一个可以正常使用的实现类，能用就完事了，反正接口定义了啥，我只管调，这样，我们就可以放心地让一个人去写视图层的代码，一个人去写业务层的代码，开发效率那是高的一匹啊。

高内聚，低耦合，是现代软件的开发的设计目标，而Spring框架就给我们提供了这样的一个IoC容器进行对象的管理。

## 使用IoC容器

首先一定要明确，使用Spring首要目的是为了使得软件项目进行解耦，而不是为了去简化代码！

Spring并不是一个独立的框架，它实际上包含了很多的模块：

![image-20230404204332426](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304042043505.png)

而我们首先要去学习的就是Core Container，也就是核心容器模块。

Spring是一个非入侵式的框架，就像一个工具库一样，因此，我们只需要直接导入其依赖就可以使用了。

---

---



使用ioc容器可以方便后面修改，可以大面积修改

beam：由spring ioc实例化，组装，管理的对象，说白了类似于以前的getter satter方法那样可以访问类，但是那是方法，这是一个类。类去管理； 

JavaBean就是有一定规范的Java实体类，跟普通类差不多，不同的是类内部提供了一些公共的方法以便外界对该对象内部属性进行操作，比如set、get操作



### 第一个Spring项目

我们创建一个新的Maven项目，并导入Spring框架的依赖，Spring框架的坐标：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.3.13</version>
</dependency>
```

接着在resource中创建一个Spring配置文件，命名为test.xml，直接右键点击即可创建：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

最后，在主方法中编写：

```java
public static void main(String[] args) {
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("text");
    
}
```

这样，一个最基本的Spring项目就创建完成了，接着我们来看看如何向IoC容器中注册JavaBean，首先创建一个Student类：

```java
//注意，这里还用不到值注入，只需要包含成员属性即可，不用Getter/Setter。
public class Student {
    String name;
    int age;
}
```

然后在配置文件中添加这个bean：

```xml
<bean name="student" class="com.test.bean.Student"/>
```

现在，这个对象不需要我们再去生成了，而是由IoC容器来提供：

```java
public static void main(String[] args) {
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("test.xml");
    Student student = (Student) context.getBean("student");
    System.out.println(student);
}
```

实际上，这里得到的Student对象是由Spring通过反射机制帮助我们创建的！

---

#### me：

- 添加依赖，
- 写springBeam的xml配置文件
- 最好配置应用程序上下文，配置完xml那里会提示的，最好点击配置

```java
package org.example;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context=new ClassPathXmlApplicationContext("Application.xml");
        Student student=(Student)context.getBean("StudentBean");//不用我们去new对象了，context来管理该对象，context来给我们提供对象，默认的，同一个beam给我们对象始终是同一个对象
         Student s1=context.getBean(Student.class);//这样也是可以的，这样的好处是还不要类型转换
        System.out.println(student.hello());
    }
}
//如果是一个接口，有多个实现类实现了接口，或者只有一个实验类实现了接口，如果我使用new的方法实现了接口，后期我想改的时候，需要大费周章的去修改，new第二个对象，但是如果我是使用beam去管理的，我直接给beam另一个对象，这样beam给出去给接口实现的就是另一个类，这样的话，修改起来是非常方便的
//或者我使用 Student s1=context.getBean(Student.class);这种，直接去到接口就停下来，不要点class，（将Student.class改成接口名称）这样beam还会自动搜索实现了接口的实现类，前提是在beam的配置文件添加了实现接口的实现类
//同一个beam默认给我们的是同一个对象，也就是说s1和student是同一个对象，注意是默认情况下 
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean name="StudentBean" class="org.example.Student">//后面是要管理的类型，提供的类型

    </bean>
</beans>
```

### 注册使用beam：

- 1·import可以导入其他的配置文件，可以是本地的，也可以是网络的

- 2·ioc容器并不是全自动的，注意不要冲突，如有两个都是实现了抽象类，beam又是两个，又不写清楚，这会导致报错，不知道选择哪一个

- 3·抽象类并不能添加到beam容器中，也就是不能注册为beam，本来就不能直接new抽象类，所以根部不可能添加到bean，使用bean去搞一个抽象类对象出来；

- 4·bean里面的属性，name属性和id属性功能都是一样的，但是id属性的命名需要注意，比较严格

- 5·bean类型一样，属性有可能不一样。类型一样，是指class是一样的，但是使用bean出来的对象是不一样的

- 6·<alias 标签是给bean起别名的，就是嫌弃bean的命名太长了，想要使用的时候，名字更加短，则是使用起别名的方法，类似于c语言的typedef起别名

  - ==7·==同一个bean，默认情况下给我们的是同一个对象：

  - ```java
          Student student=(Student)context.getBean("StudentBean");//不用我们去new对象了，context来管理该对象，context来给我们提供对象，默认的，同一个beam给我们对象始终是同一个对象
             Student s1=context.getBean(Student.class);//这样也是可以的，这样的好处是还不要类型转换
    ```

    像上面的代码，s1与student对象是同一个，因为同一个bean默认是给同一个对象，并且这个对象是bean加载的时候就创建的了。就是新建context对象的时候。

    - ==8==·如果不希望是得到同一个对象，就要去bean的xml文件里面修改bean的scope属性，这个是选择单一模式还是原型模式，默认是单一模式，也就是一个bean对应只有一个对象，如上面的s1与student；但是如果将scope改成prototype模式（原型模式），则会创建一个新的对象，则s1与student对象是不同的，不是同一个对象原型模式是要用到对象的时候才会加载，和单一模式不一样。
    - 9·依赖属性：只有依赖属性的bean被加载，当前的这个bean才会加载；就是别人不加载，自己也不会加载

### 注入依赖（就是对象的属性，如何注入，如果对象是有参构造，如何解决）：

- 案例实现：

- - Application.xml文件：

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
        <bean name="PcTeacher" class="org.example.MusicTeacher"></bean>
        <bean name="StudentBean" class="org.example.Student" >
            <  name="MyTeacher" ref="PcTeacher"></property>
    
    <!--        这个name非常关键，因为他要找到Student对象的MyTeacher属性，所以要与类的属性名字相同，ref是找到符合bean的，叫它给对象来填我需要的属性，相当于我属性new了一个对象-->
    <!--注意类还需要set属性的方法，不然报错-->
    <!--        也可以注入其他的值，反正就是实现对应的属性-->
        </bean>
    </beans>
    ```

    

对应接口的实现类：   

```java
package org.example;

public class MusicTeacher implements Teacher{
    @Override
    public void teach() {
        System.out.println("我是音乐老师，我教你唱歌");
    }
}

```

student类：

```java
package org.example;

public class Student {
    String name;
    String sex;
    public String  hello(){
      return "hello xiaoli";
    }
   public Teacher MyTeacher;//使用bean进行给与对象，要记得配一个set的方法给它

    public void setMyTeacher(Teacher myTeacher) {
        this.MyTeacher = myTeacher;
    }
}

```

main：方法

```java

package org.example;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context=new ClassPathXmlApplicationContext("Application.xml");
       Student s1=context.getBean(Student.class);
        Student student=(Student)context.getBean("StudentBean");//不用我们去new对象了，context来管理，context来给我们提供对象
      s1.MyTeacher.teach();
        System.out.println(s1.MyTeacher);
        System.out.println(student.MyTeacher);//是同一个老师，因为同一个beam，都是name为"PcTeacher的bean，在studentbean中，添加property标签作用是将student对象的Myteacher属性，bean给了一个对象去实现，而不用自己new了
        //如果要自己new的话;public Teacher MyTeacher=new PcTeacher;因为PcTeacher的bean是单一模式，所以s1与student的teacher对象是同一个
        System.out.println(s1);
        System.out.println(student);
    }
}
```

- #### 个人理解：

- 使用bean标签的property标签，可以将bean的对象的属性进行实现，也就是给一个值或者给一个对象

​	如果对应的类是有参构造，还要使用construtor-ary标签，进行形参的指定

这两个标签中的name是指类的对应属性的名称，要和类的属性要一致的名字。

ref是指对应的bean的名称；value是对应的值

如果属性是集合类，在property里面还要写对应集合内的标签，如list标签与list集合类

---

---

- ### 自动装配：

- 在标签中，加入auto属性，就能实现自动寻找合适的类型的注入，有两种自动装配：Bytype和ByName两种方式，如果同一个类型的bean有两个，推荐使用ByName，或者在对应的bean里面，加入主要的优先的字眼，

- - 如果使用ByName的话，记住set方法那个名称和对应的Bean的名称要保持一致，ByName，就是通过名字去寻找相对应的bean，

  - 构造方法也是可以自动的，也是同样的道理，记住auto的字样就是自动的，autoConstruct就是自动的构造器，根据构造方法自动识别

  - ---

  - 

    ### 生命周期与继承 

    

生命周期：在类中添加init和destry方法，在对应的bean标签中，添加属性，init—method指定对应的类中的方法名称，则会联系上对应的方法；

关于对象什么时候被销毁，在context.close后，对象会被销毁，因为容器关闭了

#### 继承：

在bean的属性中，加入parent的属性，表示继承xxxbean，xxxbean里面的东西是会被继承的，如bean的property的标签。

如果继承有冲突，如自己定义了类的属性property，又继承了父类的，默认优先使用自己父类的

---

---

 

使用注解开发：

方法一：半注解，无需xml，但是需要配置类：

配置类：

```java
package org.example.config;

import org.example.Student;
import org.springframework.beans.factory.annotation.Configurable;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.ImportResource;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Controller;

import java.beans.BeanProperty;

@Configuration//等价于xml文件配置
public class MainConfig {
      
    @Bean(initMethod = "init")//原来在bean标签中的属性，可以在括号中写,之前构造方法，现在可以直接传参，还有之前的属性，也是可以直接传参，如果是之前接口类型，需要实例化如体育老师，信息老师等，直接在方法体里面写
    public Student student(){
        return new Student();//使用bean注解，等价于一个bean的标签,bean标签name属性就是student，也就是方法名,也可以在注解那里配name，加上括号就行：@Bean（”student“）
   //自动装配，还可以在需要类里面添加注解AutoWired，如果出现多个能满足自动分配的，还要添加一个注解进行指定@Qualifier//明确那个类型
    }

}

```



```java
package org.example;

import org.example.config.MainConfig;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
 //       ApplicationContext context=new ClassPathXmlApplicationContext("Application.xml");//现在使用注解形式开发，要用其他实现
        ApplicationContext context=new AnnotationConfigApplicationContext(MainConfig.class);//使用注解，使用config类进行配置
        System.out.println(context.getBean("student"));

    }
}
```

方法二：全部注解：无需配置类，但是要有配置类，里面是空的;

1`在需要加入bean容器的对象所属于的类，在前面加上@Component，表示一个对象为bean对象

2·加上@componenscan表示主键扫描包里面有加入bean对象的类，并识别成功后加上标志，注意括号填写的是包名：

---

我们可以在需要注册为Bean的类上添加`@Component`注解来将一个类进行注册**（现在最常用的方式）**，不过要实现这样的方式，我们需要添加一个自动扫描来告诉Spring，它需要在哪些包中查找我们提供的`@Component`声明的Bean。

```java
@Component("lbwnb")   //同样可以自己起名字
public class Student {

}
```

要注册这个类的Bean，只需要添加@Component即可，然后配置一下包扫描：

```java
@Configuration
@ComponentScan("com.test.bean")   //包扫描，这样Spring就会去扫描对应包下所有的类
public class MainConfiguration {

}
```

不过，无论是通过@Bean还是@Component形式注册的Bean，Spring都会为其添加一个默认的name属性，比如：

```java
@Component
public class Student {
}
```

它的默认名称生产规则依然是类名并按照首字母小写的驼峰命名法来的，所以说对应的就是student：

```java
Student student = (Student) context.getBean("student");   //这样同样可以获取到
System.out.println(student);
```

同样的，如果是通过@Bean注册的，默认名称是对应的方法名称：

```java
@Bean
public Student artStudent(){
    return new Student();
}

Student student = (Student) context.getBean("artStudent");
System.out.println(student);

```

总结;使用全注解开发：bean的名称是类名全小写，使用bean注解注册，要么是方法名字，要么是bean后面括号的

---

---

## SpringEL表达式

SpEL 是一种强大，简洁的装配 Bean 的方式，它可以通过运行期间执行的表达式将值装配到我们的属性或构造函数当中，更可以调用 JDK 中提供的静态常量，获取外部 Properties 文件中的的配置。

### 外部属性注入

有些时候，我们甚至可以将一些外部配置文件中的配置进行读取，并完成注入。

我们需要创建以.properties结尾的配置文件，这种配置文件格式很简单，类似于Map，需要一个Key和一个Value，中间使用等号进行连接，这里我们在resource目录下创建一个test.properties文件：

```properties
test.name=Text//左边是键，右边是值
```

这样，Key就是`test.name`，Value就是`Text，我们可以通过一个注解直接读取到外部配置文件中对应的属性值，首先我们需要引入这个配置文件，我们可以在配置类上添加`@PropertySource`注解：

```java
@Configuration
@ComponentScan("com.test.bean")
@PropertySource("classpath:test.properties")   //注意，类路径下的文件名称需要在前面加上classpath://要指定这个文本晚间在哪里
public class MainConfiguration{
    
}
```

接着，我们就可以开始快乐的使用了，我们可以使用 @Value 注解将外部配置文件中的值注入到任何我们想要的位置

```java
@Component
public class Student {
    @Value("${test.name}")   //这里需要在外层套上 ${ }//这里可以理解为输入键，需要加这个${键的名称}，如果是常量，直接字符串那样输入就行
    private String name;   //String会被自动赋值为配置文件中对应属性的值

    public void hello(){
        System.out.println("我的名字是："+name);
    }
}
```

### 使用spel表达式

```java
   String na="My name is czz!";
        Expression exp=expression.parseExpression("toString()");//表达式如果是对象的方法，getvalue括号写对象，如果是一整个java表达式，使用单引号进行区分
        Expression exp2=expression.parseExpression("'Hello'.toString()");//这个是不适用对象
        System.out.println(exp.getValue(na));//getValue是要传输对象的，对象是要在getvalue括号里的，方法实在表达式那里，并用双引号括起来
        System.out.println(exp2.getValue());
```

<img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202304061303637.png" alt="image-20230406130323596" style="zoom:150%;" />运行结果



---

除了获取，我们也可以调用表达式的setValue方法来设定属性的值：

```java
Expression exp = parser.parseExpression("name");
exp.setValue(student, "刻师傅");   //同样的，这个属性得有访问权限且能set才可以，否则会报错
```

除了属性调用，我们也可以使用运算符进行各种高级运算：

```java
Expression exp = parser.parseExpression("66 > 77");   //比较运算
System.out.println(exp.getValue());
```

```java
Expression exp = parser.parseExpression("99 + 99 * 3");   //算数运算
System.out.println(exp.getValue());
```

对于那些需要导入才能使用的类，我们需要使用一个特殊的语法：

```java
Expression exp = parser.parseExpression("T(java.lang.Math).random()");   //由T()囊括，包含完整包名+类名
//Expression exp = parser.parseExpression("T(System).nanoTime()");   //默认导入的类可以不加包名
System.out.println(exp.getValue());
```

### 集合操作相关语法

现在我们的类中存在一些集合类：

```java
@Component
public class Student {
    public Map<String, String> map = Map.of("test", "你干嘛");
    public List<String> list = List.of("AAA", "BBB", "CCC");
}
```

我们可以使用SpEL快速取出集合中的元素：

```java
Expression exp = parser.parseExpression("map['test']");  //对于Map这里映射型，可以直接使用map[key]来取出value
System.out.println(exp.getValue(student));
```

```java
Expression exp = parser.parseExpression("list[2]");   //对于List、数组这类，可以直接使用[index]
System.out.println(exp.getValue(student));
```

我们也可以快速创建集合：

```java
Expression exp = parser.parseExpression("{5, 2, 1, 4, 6, 7, 0, 3, 9, 8}"); //使用{}来快速创建List集合
List value = (List) exp.getValue();
value.forEach(System.out::println);
```

```java
Expression exp = parser.parseExpression("{{1, 2}, {3, 4}}");   //它是支持嵌套使用的
```

```java
//创建Map也很简单，只需要key:value就可以了
Expression exp = parser.parseExpression("{name: '小明', info: {address: '北京市朝阳区', tel: 10086}}");
System.out.println(exp.getValue());
```

你以为就这么简单吗，我们还可以直接根据条件获取集合中的元素：

```java
@Component
public class Student {
    public List<Clazz> list = List.of(new Clazz("高等数学", 4));

    public record Clazz(String name, int score){ }
}
```

```java
//现在我们希望从list中获取那些满足我们条件的元素，并组成一个新的集合，我们可以使用.?运算符
Expression exp = parser.parseExpression("list.?[name == '高等数学']");
System.out.println(exp.getValue(student));
```

```java
Expression exp = parser.parseExpression("list.?[score > 3]");   //选择学分大于3分的科目
System.out.println(exp.getValue(student));
```

我们还可以针对某个属性创建对应的投影集合：

```java
Expression exp = parser.parseExpression("list.![name]");   //使用.!创建投影集合，这里创建的时课程名称组成的新集合
System.out.println(exp.getValue(student));
```

![image-20230406162753097](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304061627136.png)

我们接着来介绍安全导航运算符，安全导航运算符用于避免NullPointerException，它来自Groovy语言。通常，当您有对对象的引用时，您可能需要在访问对象的方法或属性之前验证它是否为空。为了避免这种情况，安全导航运算符返回null而不是抛出异常。以下示例显示了如何使用安全导航运算符：

```java
Expression exp = parser.parseExpression("name.toUpperCase()");   //如果Student对象中的name属性为null
System.out.println(exp.getValue(student));
```

![image-20230406162739331](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304061627390.png)

当遇到null时很不方便，我们还得写判断：

```java
if(student.name != null)
    System.out.println(student.name.toUpperCase());
```

Java 8之后能这样写：

```java
Optional.ofNullable(student.name).ifPresent(System.out::println);
```

但是你如果写过Kotlin：

```kotlin
println(student.name?.toUpperCase());
```

类似于这种判空问题，我们就可以直接使用安全导航运算符，SpEL也支持这种写法：

```java
Expression exp = parser.parseExpression("name?.toUpperCase()");
System.out.println(exp.getValue(student));
```

当遇到空时，只会得到一个null，而不是直接抛出一个异常：

![image-20230406162725556](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304061627592.png)

我们可以将SpEL配合 @Value 注解或是xml配置文件中的value属性使用，比如XML中可以这样写：

```xml
<bean id="numberGuess" class="org.spring.samples.NumberGuess">
    <property name="randomNumber" value="#{ T(java.lang.Math).random() * 100.0 }"/>
</bean>
```

或是使用注解开发：

```java
public class FieldValueTestBean {
    @Value("#{ systemProperties['user.region'] }")
    private String defaultLocale;
}
```

这样，我们有时候在使用配置文件中的值时，就能进行一些简单的处理了。

---

### 集合类个人总结：

- 用getvalue可以得到map，只需要key:value写在那个表达式上就行了。

- 用类似的方法可以得到list，就是平时怎么初始化就写在表达式上就行了

- 使用getvalue有两种方向，一种是不属于对象的，也就是没有对象的。第二种是有对象的。如果有对象，对象写在getvalue的括号里，表达式写想要得到的属性，翻译成xxx对象的kkk，如果没有对象的，表达式写什么，getvalue得到的值就是最终的运算结果，如写个集合类array下去，getvalue得到的是一个数组，要转化成一个数组进行接收。

- getvalue还可以接收数组下标的值，还可以接收map键，输出对应的值，getvalue就是得最终的结果;//对于Map这里映射型，可以直接使用map[key]来取出value 

- ---

- ---

- 

- ##  AOP（XML方式）

### aop意义：

- #### 在某些动作执行前后，加入额外的动作，不更改原有的代码：例如：本来打印两句话，hello和world，但是我想在中间插入一句话，又不想更改原本的代码，我可以使用aop，在打印world之前，添加一些动作，如打印日志，或者做其他的动作也是可以的

### 用xml的方式进行使用aop：

- 首先：在beans添加约束，接着在pom文件中添加依赖，（注意版本需要相同，和spring）

- #### 注意：

  - spring管理到的类才可以使用aop，也就是说类要进入loc容器才能使用aop这个spring框架的功能

  所以：第一步应该将需要添加额外动作对象loc容器中，也就是bean

  第二步：创建一个类，最好是类名+Aop的命令方式；（这个也是要加入loc容器的）

  第三步：在新建的aop类中写需要加入的动作的方法；

  #### 第四步：

- 在aop类的bean后面，注意是后面，写aop：config标签

- aop：config标签里面，注意是里面，加入==第一个标签：aop：pointcut==，这个是切点的意思，用于指定从哪个类的哪个方法进行切，属性是id和expression；id是用来辨认哪一个aop：pointcut的标签，第二个通常使用execution方法，括号里面写哪个类和哪个方法，但是有统一的规格进行输入，这个属性为了加入哪个是切点

- ```java
  <aop:pointcut id="test" expression=""/>
  ```

- `execution`填写格式如下：

  ```xml
  修饰符 包名.类名.方法名称(方法参数)
  ```

  - 修饰符：public、protected、private、包括返回值类型、static等等（使用*代表任意修饰符）*

  - 包名：如com.test（*代表全部，比如com.*代表com包下的全部包）*

  - 类名：使用*也可以代表包下的所有类

  - 方法名称：可以使用*代表全部方法

  - 方法参数：填写对应的参数即可，比如(String, String)，也可以使用*来代表任意一个参数，使用..代表所有参数。

    

- config里面的第二个标签：==Aop：aspect标签里面的属性==，ref是指aop类的地址，就是上面提高的xxxAop类

- ```java
   <aop:aspect ref="studentAOP">
  ```

  第三个标签：Aop：After/Before等，区别于执行顺序不一样，还能环绕，环绕是中间执行原方法，前后是自己的方法

  ```java
    <aop:after method="afterStudy" pointcut-ref="test"/>//method是指Aop类的方法，在aspect标签是找到你的aop类，这里定位于方法，pointcut是指切点的标签，ref是路径/id号之类的，反正定得到pointcut标签位置在那里是哪一个
  ```

  通过join point可以拿到切点方法得形参，属性等一些信息，是方法体里面的信息或者形参的信息；用它的getargs方法就可以，返回的是一个数组。

  环绕：它之所以能够实现在中间执行原方法，是因为使用了joinpoint的proceed方法，可以想象差不多是这个方法是复制了切点的方法，执行完aop的方法再接下去切点之后的方法；

  ### 接口实现：
  
  #### 直接aop类继承MethodBeforeAdievice 接口，实现里面的方法。如果想之后在插入方法，接口名大同小异的去更改就行

改完之后，标签也要修改，不用aspect标签了，使用advice-ref里面是写aop类，这样就行了，不用第三个标签什么after阿那些标签，不用我指定什么after还是befor，因为aop类接口匹配了

---

### 注解实现：（这个是重点）

接着我们来看看如何使用注解实现AOP操作，现在变回我们之前的注解开发，首先我们需要在主类添加`@EnableAspectJAutoProxy`注解，开启AOP注解支持：

```java
@EnableAspectJAutoProxy
@ComponentScan("org.example.entity")
@Configuration
public class MainConfiguration {
}
```

还是熟悉的玩法，类上直接添加`@Component`快速注册Bean：

```java
@Component
public class Student {
    public void study(){
        System.out.println("我是学习方法！");
    }
}
```

接着我们需要在定义AOP增强操作的类上添加`@Aspect`注解和`@Component`将其注册为Bean即可，就像我们之前在配置文件中也要将其注册为Bean那样：

```java
@Aspect
@Component
public class StudentAOP {

}
```

接着，我们可以在里面编写增强方法，并将此方法添加到一个切点中，比如我们希望在Student的study方法执行之前执行我们的`before`方法：

```java
public void before(){
    System.out.println("我是之前执行的内容！");
}
```

那么只需要添加@Before注解即可：

```java
@Before("execution(* org.example.entity.Student.study())")  //execution写法跟之前一样
public void before(){
    System.out.println("我是之前执行的内容！");
}
```

这样，这个方法就会在指定方法执行之前执行了，是不是感觉比XML配置方便多了。我们来测试一下：

```java
public static void main(String[] args) {
    ApplicationContext context = new AnnotationConfigApplicationContext(MainConfiguration.class);
    Student bean = context.getBean(Student.class);
    bean.study();
}
```

![image-20230406202642466](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304062026517.png)

同样的，我们可以为其添加`JoinPoint`参数来获取切入点信息，使用方法跟之前一样：

```java
@Before("execution(* org.example.entity.Student.study())")
public void before(JoinPoint point){
    System.out.println("参数列表："+ Arrays.toString(point.getArgs()));
    System.out.println("我是之前执行的内容！");
}
```

为了更方便，我们还可以直接将参数放入，比如：

```java
public void study(String str){
    System.out.println("我是学习方法！");
}
```

使用命名绑定模式，可以快速得到原方法的参数：

```java
@Before(value = "execution(* org.example.entity.Student.study(..)) && args(str)", argNames = "str")
//命名绑定模式就是根据下面的方法参数列表进行匹配
//这里args指明参数，注意需要跟原方法保持一致，然后在argNames中指明
public void before(String str){
    System.out.println(str);   //可以快速得到传入的参数
    System.out.println("我是之前执行的内容！");
}
```

除了@Before，还有很多可以直接使用的注解，比如@AfterReturning、@AfterThrowing等，比如@AfterReturning：

```java
public String study(){
    System.out.println("我是学习方法！");
    return "lbwnb";
}
```

```java
@AfterReturning(value = "execution(* org.example.entity.Student.study())", argNames = "returnVal", returning = "returnVal")   //使用returning指定接收方法返回值的参数returnVal
public void afterReturn(Object returnVal){
    System.out.println("返回值是："+returnVal);
}
```

同样的，环绕也可以直接通过注解声明：

```java
@Around("execution(* com.test.bean.Student.test(..))")
public Object around(ProceedingJoinPoint point) throws Throwable {
    System.out.println("方法执行之前！");
    Object val = point.proceed();
    System.out.println("方法执行之后！");
    return val;
}
```

---

### 个人总结注解aop：

- 首先要在配置类加入@EnableAspectJAutoProxy这个注解，开启aop

- 接着：`@Aspect`（类似于当时的第二个标签，表示增强）注解和`@Component`将其注册为Bean

- 第三步加入注解@before或者其他注解如@AfterReturning、@AfterThrowing 注意原本在pointcut标签中的execution指定原本的类需要在before这个注解括号里写；

- ```java
  @Before("execution(* org.example.entity.Student.study())")  //execution写法跟之前一样
  ```

  

----

---

## Mybatis与spring关联，实现注解开发

由web得注解开发，可以知道，创建一个Mapper接口，然后再xml文件中写入接口得地址，实现映射，然后接口里面得方法，加上注解，

接口得实现由：

```
 UserMapper userMapper = sqlSession.getMapper(UserMapper.class);//由sqlsession实现
```

### 注册为bean得方法大致分为两种：

- 如果是官方给出得类，因为不是自己定义的，所以经常都是使用了bean注解进行注册为bean，返回类型，方法名字，注解方法前面；方法和注解在config类；

- 第二种办法，就是在自己定义的类中，加上conponent的注解，在config类中加上conponentscan表示扫描

- 自动装配的问题：

- 使用Autowrite或者是Resource的注解，如果context容器中有同类型的对象，则会给注解下面的对象

- ```java
  context.gerbean(接口.class)//这里表示，在loc容器中寻找对象，是实现了接口类的对象
  ```

### 重点：mybatis与spring框架的整合：

1·添加依赖：加入mybatis-spring和spring-jdbc的jar包，在pom文件中添加

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.6</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.13</version>
</dependency>
```

2·多使用注解和接口开发，为了避免new对象，以后修改难以修改，所以，将sqlsession变成bean，因为sqlsession是一个接口，所以使用到一个类，它是封装了sqlsession的，也就是当它是sqlsession来用就行。那就是sqlsessionTemplete类，将这个类的一个对象加入到loc容器中

==使用起来当sqlsession来使用就行==

```java
@Configuration
@ComponentScan("com.test")
public class TestConfiguration {
    @Bean
    public SqlSessionTemplate sqlSessionTemplate() throws IOException {
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("mybatis-config.xml"));
        return new SqlSessionTemplate(factory);//括号里面是要sqlsession工厂
    }
}
```

它的介绍：`SqlSessionTemplate` 是 MyBatis-Spring 整合模块提供的一个类，是对 `SqlSession` 的封装。它实现了 `SqlSession` 接口，提供了和 `SqlSession` 一样的 API，但是在使用时无需手动关闭 `SqlSession`，而是交由 Spring 容器来管理。



---

如何减少xml文件，不用配置mysql，使用到DataSource这个类，这个类是设置连接数据库的信息的

还要一个类，就是sqlsessionfactoryBean这个类是用于数据库的连接，这样就可以不用xml文件了

注册为bean是使用方法，修改也方便，到时候直接用bean的对象就行了

```java
@Configuration
@ComponentScan("com.test")
@MapperScan("com.test.mapper")
public class TestConfiguration {
    @Bean
    public DataSource dataSource(){
        return new PooledDataSource("com.mysql.cj.jdbc.Driver",
                "jdbc:mysql://localhost:3306/study", "root", "123456");
    }

    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(@Autowired DataSource dataSource){
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        return bean;
    }
}
```

---

---

## 集成JUnit测试

既然使用了Spring，那么怎么集成到JUnit中进行测试呢，首先大家能够想到的肯定是：

```java
public class TestMain {

    @Test
    public void test(){
        ApplicationContext context = new AnnotationConfigApplicationContext(TestConfiguration.class);
        TestService service = context.getBean(TestService.class);
        service.test();
    }
}
```

直接编写一个测试用例即可，但是这样的话，如果我们有很多个测试用例，那么我们不可能每次测试都去创建ApplicationContext吧？我们可以使用`@Before`添加一个测试前动作来提前配置ApplicationContext，但是这样的话，还是不够简便，能不能有更快速高效的方法呢？

Spring为我们提供了一个Test模块，它会自动集成Junit进行测试，我们可以导入一下依赖：

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.8.1</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.3.12</version>
</dependency>
```

这里导入的是JUnit5和SpringTest模块依赖，然后直接在我们的测试类上添加两个注解就可以搞定：

```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = TestConfiguration.class)
public class TestMain {

    @Autowired
    TestService service;
    
    @Test
    public void test(){
        service.test();
    }
}
```

`@ExtendWith`是由JUnit提供的注解，等同于旧版本的`@RunWith`注解，然后使用SpringTest模块提供的`@ContextConfiguration`注解来表示要加载哪一个配置文件，可以是XML文件也可以是类，我们这里就直接使用类进行加载。

配置完成后，我们可以直接使用`@Autowired`来进行依赖注入，并且直接在测试方法中使用注入的Bean，现在就非常方便了。

### 也就是使用了注解，不用new context容器

---



## SpringMvc

- #### Mvc主要分为三层架构：表示层，业务层，数据访问层

- MVC：model（通俗点讲，就是封装数据的实体类），view（视图层，也就是前端），controller（控制器，相当于servlet的基本功能）

- 搭建环境：

- 注意的是注意springmvc版本是否支持tomcat11

- 1·添加依赖，添加spring-mvc依赖，这个依赖包括了之前的spring依赖

- ```
   <dependency>    <groupId>org.springframework</groupId>    <artifactId>spring-webmvc</artifactId>    <version>5.3.13</version></dependency>
  ```

  

- 2·将web的xml文件替换：DispatcherServlet（调度的servlet，它是比servlet多一个功能————调度）替换掉tomcat的servlet，url-pattern写为‘/’

- 接着我们需要配置一下web.xml，将DispatcherServlet替换掉Tomcat自带的Servlet，这里url-pattern需要写为`/`，即可完成替换：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
      <servlet>
          <servlet-name>mvc</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      </servlet>
      <servlet-mapping>
          <servlet-name>mvc</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  </web-app>
  ```

  接着需要为整个Web应用程序配置一个Spring上下文环境（也就是容器），因为SpringMVC是基于Spring开发的，它直接利用Spring提供的容器来实现各种功能，这里我们直接使用注解方式进行配置，不再使用XML配置文件：

  ```xml
  <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>com.example.config.MvcConfiguration</param-value>
          </init-param>
          <init-param>
              <param-name>contextClass</param-name>
              <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
          </init-param>
  ```

  如果还是想使用XML配置文件进行配置，那么可以直接这样写：

  ```xml
  <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>配置文件名称</param-value>
  </init-param>
  ```

  - ### 使用全注解，不用xml配置文件：

​		首先添加一个类，Tomcat会在类路径中查找实现ServletContainerInitializer 接口的类，如果发现的话，就用它来配置Servlet容器，Spring提供了这个接口的实现类 SpringServletContainerInitializer , 通过@HandlesTypes(WebApplicationInitializer.class)设置，这个类反过来会查找实现WebApplicationInitializer 的类，并将配置的任务交给他们来完成，因此直接实现接口即可：这个也就是相当于web.xml文件

```java
public class MainInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{MainConfiguration.class};   //基本的Spring配置类，一般用于业务层配置
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[0];  //配置DispatcherServlet的配置类、主要用于Controller等配置
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};    //匹配路径，与上面一致
    }
}
```

顺便编写一下最基本的配置类：

```java
@Configuration
public class MainConfiguration {

}
```

总结步骤（全注解的）：1创建一个类，继承ServletContainerInitializer 接口的类，2创建一个webconfige的类，3·创建一个Mainconfig的类，在实现ServletContainerInitializer接口中的方法，填入webconfig类和Mainconfig类

![image-20230407232057259](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304072320387.png)



![image-20230408133827297](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081338380.png)

分两个配置类是因为一个是根容器，一个是servlet容器

----

## 控制器：

![image-20230408134314906](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081343995.png)

![image-20230408155514071](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081555151.png)

首先dispatcherServlet会收到请求，然后先经过HandlerMapping，请求映射

---

我们的请求到达Tomcat服务器之后，会交给当前的Web应用程序进行处理，而SpringMVC使用`DispatcherServlet`来处理所有的请求，也就是说它被作为一个统一的访问点，所有的请求全部由它来进行调度。

当一个请求经过`DispatcherServlet`之后，会先走`HandlerMapping`，它会将请求映射为`HandlerExecutionChain`，依次经过`HandlerInterceptor`有点类似于之前我们所学的过滤器，不过在SpringMVC中我们使用的是拦截器，然后再交给`HandlerAdapter`，根据请求的路径选择合适的控制器进行处理，控制器处理完成之后，会返回一个`ModelAndView`对象，包括数据模型和视图，通俗的讲就是页面中数据和页面本身（只包含视图名称即可）。

返回`ModelAndView`之后，会交给`ViewResolver`（视图解析器）进行处理，视图解析器会对整个视图页面进行解析，SpringMVC自带了一些视图解析器，但是只适用于JSP页面，我们也可以像之前一样使用Thymeleaf作为视图解析器，这样我们就可以根据给定的视图名称，直接读取HTML编写的页面，解析为一个真正的View。

解析完成后，就需要将页面中的数据全部渲染到View中，最后返回给`DispatcherServlet`一个包含所有数据的成形页面，再响应给浏览器，完成整个过程。

因此，实际上整个过程我们只需要编写对应请求路径的的Controller以及配置好我们需要的ViewResolver即可，之后还可以继续补充添加拦截器，而其他的流程已经由SpringMVC帮助我们完成了。

## 配置视图解析器：

首先我们需要实现最基本的页面解析并返回，第一步就是配置视图解析器，这里我们使用Thymeleaf为我们提供的视图解析器，导入需要的依赖：

```xml
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
    <version>3.0.12.RELEASE</version>
</dependency>
```

配置视图解析器非常简单，我们只需要将对应的`ViewResolver`注册为Bean即可，这里我们直接在配置类中编写：

```java
@ComponentScan("com.example.controller")
@Configuration
@EnableWebMvc
public class WebConfiguration {

  //我们需要使用ThymeleafViewResolver作为视图解析器，并解析我们的HTML页面
    @Bean
    public ThymeleafViewResolver thymeleafViewResolver(@Autowired SpringTemplateEngine springTemplateEngine){
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setOrder(1);   //可以存在多个视图解析器，并且可以为他们设定解析顺序
        resolver.setCharacterEncoding("UTF-8");   //编码格式是重中之重
        resolver.setTemplateEngine(springTemplateEngine);   //和之前JavaWeb阶段一样，需要使用模板引擎进行解析，所以这里也需要设定一下模板引擎
        return resolver;
    }
  
  	//配置模板解析器
  	@Bean
    public SpringResourceTemplateResolver templateResolver(){
        SpringResourceTemplateResolver resolver = new SpringResourceTemplateResolver();
        resolver.setSuffix(".html");   //需要解析的后缀名称
        resolver.setPrefix("/");   //需要解析的HTML页面文件存放的位置从Webapp开始
        return resolver;
    }
  	
  	//配置模板引擎Bean
  	@Bean
    public SpringTemplateEngine springTemplateEngine(@Autowired ITemplateResolver resolver){
        SpringTemplateEngine engine = new SpringTemplateEngine();
        engine.setTemplateResolver(resolver);   //模板解析器，默认即可
        return engine;
    }
}
```

注解@controller表示这个类是一个控制器，@requestMapping(里面是访问的路径)返回一个ModelAndView类的对象，可以指return new ModelAndView（html文件），这样子通过映射器就能用resquest的路径访问到，类似于@webServlet注解后面的地址；

---

## 狂神ssm：

1·什么是MVC：视图（前端），模型（实体类数据），控制器（servlet）



![image-20230408153255476](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081532598.png)

这里的xml配置就是当时使用@webServlet注解，所以哪个注解就是减少了这些配置：同样的：

![image-20230408153408087](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081534175.png)

使用这个配置类，也是可以减少web.xml配置

- - ### 使用全注解，不用xml配置文件：

​		首先添加一个类，Tomcat会在类路径中查找实现ServletContainerInitializer 接口的类，如果发现的话，就用它来配置Servlet容器，Spring提供了这个接口的实现类 SpringServletContainerInitializer , 通过@HandlesTypes(WebApplicationInitializer.class)设置，这个类反过来会查找实现WebApplicationInitializer 的类，并将配置的任务交给他们来完成，因此直接实现接口即可：这个也就是相当于web.xml文件

```java
public class MainInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{MainConfiguration.class};   //基本的Spring配置类，一般用于业务层配置
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[0];  //配置DispatcherServlet的配置类、主要用于Controller等配置
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};    //匹配路径，与上面一致
    }
}
```

顺便编写一下最基本的配置类：

```java
@Configuration
public class MainConfiguration {

}
```

总结步骤（全注解的）：1创建一个类，继承ServletContainerInitializer 接口的类，2创建一个webconfige的类，3·创建一个Mainconfig的类，在实现ServletContainerInitializer接口中的方法，填入webconfig类和Mainconfig类

为什么是两个config类，因为一个是根的配置类，一个是servlet的配置类：![image-20230408153848338](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081538432.png)

ssm的原理：

通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器。

1. HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping,HandlerMapping根据请求url查找Handler。
2. HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器，如上url被查找控制器为：hello。
3. HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等。
4. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。
5. Handler让具体的Controller执行。
6. Controller将具体的执行信息返回给HandlerAdapter,如ModelAndView。
7. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet。
8. DispatcherServlet调用视图解析器(ViewResolver)来解析HandlerAdapter传递的逻辑视图名。
9. 视图解析器将解析的逻辑视图名传给DispatcherServlet。
10. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图。

## xml模式：

1. 新建一个Moudle ， springmvc-02-hello ， 添加web的支持！

2. 确定导入了SpringMVC 的依赖！

3. 配置web.xml ， 注册DispatcherServlet

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
       <!--1.注册DispatcherServlet-->
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <!--关联一个springmvc的配置文件:【servlet-name】-servlet.xml-->
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc-servlet.xml</param-value>
           </init-param>
           <!--启动级别-1-->
           <load-on-startup>1</load-on-startup>
       </servlet>
       <!--/ 匹配所有的请求；（不包括.jsp）-->
       <!--/* 匹配所有的请求；（包括.jsp）-->
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

4. 编写SpringMVC 的 配置文件！名称：springmvc-servlet.xml : [servletname]-servlet.xml

   说明，这里的名称要求是按照官方来的

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd">
   </beans>
   ```

5. 添加 处理映射器

   ```
   <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
   ```

6. 添加 处理器适配器

   ```
   <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
   ```

7. 添加 视图解析器

   ```xml
   <!--视图解析器:DispatcherServlet给他的ModelAndView-->
   <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
       <!--前缀-->
       <property name="prefix" value="/WEB-INF/jsp/"/>
       <!--后缀-->
       <property name="suffix" value=".jsp"/>
   </bean>
   ```

8. 编写我们要操作业务Controller ，要么实现Controller接口，要么增加注解；需要返回一个ModelAndView，装数据，封视图；

   ```java
   package com.kuang.controller;
   import org.springframework.web.servlet.ModelAndView;
   import org.springframework.web.servlet.mvc.Controller;
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   //注意：这里我们先导入Controller接口
   public class HelloController implements Controller {
       public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
           //ModelAndView 模型和视图
           ModelAndView mv = new ModelAndView();
           //封装对象，放在ModelAndView中。Model
           mv.addObject("msg","HelloSpringMVC!");
           //封装要跳转的视图，放在ModelAndView中
           mv.setViewName("hello"); //: /WEB-INF/jsp/hello.jsp
           return mv;
       }
   }
   ```

9. 将自己的类交给SpringIOC容器，注册bean

   ```
   <!--Handler--><bean id="/hello" class="com.kuang.controller.HelloController"/>
   ```

10. 写要跳转的jsp页面，显示ModelandView存放的数据，以及我们的正常页面；

    ```
    <%@ page contentType="text/html;charset=UTF-8" language="java" %><html><head>    <title>Kuangshen</title></head><body>    ${msg}</body></html>
    ```

---

对应注解模式：

![image-20230408160736092](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081607182.png)

这个对应为根目录配置类



---

![image-20230408164628131](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081646179.png)

因为这里我们将某个控制器注册为bean，如果这个控制器合适，dispatchServlet就会那这个控制器，也就是类似于Webservlet那样

<img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081649669.png" alt="image-20230408164936621" style="zoom:150%;" />

<img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081651066.png" alt="image-20230408165119017" style="zoom:150%;" />

<img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081652736.png" alt="image-20230408165257691" style="zoom:150%;" />

<img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081653703.png" alt="image-20230408165355667" style="zoom:150%;" />

ModelAndView（模型和视图）：这个就是相当于前端和实体类数据结合在一起，

![image-20230408170840778](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081708862.png)

也就是之前的servlet前端和数据进行了封装，调用了。也就是实现了分成管理。最后返回结果，给调度servlet

![image-20230408171047279](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081710404.png)

![image-20230408171819180](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081718231.png)

![image-20230408172158001](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081721038.png)

![image-20230408172419859](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304081724891.png)

## 使用注解开发mvc

mvc框架开发：

1·导入依赖

2·配置webxml中的调度servlet

接着我们需要配置一下web.xml，将DispatcherServlet替换掉Tomcat自带的Servlet，这里url-pattern需要写为`/`，即可完成替换：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>mvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>mvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

---

等下就可以注解；不用这个xml文件

配置类：

```java
package com.example.demo3.Config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class MainInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{MainConfigration.class};   //基本的Spring配置类，一般用于业务层配置
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfigration.class};  //配置DispatcherServlet的配置类、主要用于Controller等配置
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};    //匹配路径，与上面一致
    }
}

```



```java
package com.example.demo3.Config;

import org.springframework.context.annotation.Configuration;

@Configuration
public class MainConfigration {
}

```



```java
package com.example.demo3.Config;

import org.springframework.context.annotation.Configuration;

@Configuration
public class WebConfigration {

}

```

1·配置视图解析器：使用thymeleaf解析器导入依赖：

```xml
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
    <version>3.0.12.RELEASE</version>
</dependency>
```

第一个控制器：

```java
package com.example.demo3.Controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class MainController {
    //这个text是路径的名称，有点类似我们之前的@webservlet
    @RequestMapping("/tex")//请求映射//和bean一样，注解是为下面的方法，bean是将方面的某个类注册成为bean，通常使用@bean都是官方类为主
    public ModelAndView index(){//ModelAndView类似于我们之前的serverlet，只是该类比servlet更加强大，可以带数据，也有前端。之前servlet带数据要setArrbute
        ModelAndView modelAndView= new ModelAndView("index");//类似于请求转向，也是可以带数据的//视图和模型
        
//        return "/index";
        modelAndView.getModel().put("name","88900");
        return modelAndView;
    }
}
//这个就是类似于servlet了。
```

![image-20230408212959233](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304082129303.png)



![image-20230408213119119](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304082131164.png)

```java
@RequestMapping({"/index", "/test"})
public ModelAndView index(){
    return new ModelAndView("index");
}//设置多个请求路径，如果@RequestMapping在类之前，还可以设置前缀，也就是index前面还要加上xxx
```

我们也可以直接将`@RequestMapping`添加到类名上，表示为此类中的所有请求映射添加一个路径前缀，比如：

```java
@Controller
@RequestMapping("/yyds")
public class MainController {

    @RequestMapping({"/index", "/test"})
    public ModelAndView index(){
        return new ModelAndView("index");
    }
}
```

那么现在我们需要访问`/yyds/index`或是`/yyds/test`才可以得到此页面。我们可以直接在IDEA下方的端点板块中查看当前Web应用程序定义的所有请求映射，并且可以通过IDEA为我们提供的内置Web客户端直接访问某个路径。

我们接着来看下一个method属性，顾名思义，它就是请求的方法类型，我们可以限定请求方式，比如：

```java
@RequestMapping(value = "/index", method = RequestMethod.POST)
public ModelAndView index(){
    return new ModelAndView("index");
}
```

现在我们如果直接使用浏览器访问此页面，会显示405方法不支持，因为浏览器默认是直接使用GET方法获取页面，而我们这里指定为POST方法访问此地址，所以访问失败，我们现在再去端点中用POST方式去访问，成功得到页面。

我们也可以使用衍生注解直接设定为指定类型的请求映射：

```java
@PostMapping(value = "/index")
public ModelAndView index(){
    return new ModelAndView("index");
}
```

这里使用了`@PostMapping`直接指定为POST请求类型的请求映射，同样的，还有`@GetMapping`可以直接指定为GET请求方式，这里就不一一列举了。

我们可以使用`params`属性来指定请求必须携带哪些请求参数，比如：

```java
@RequestMapping(value = "/index", params = {"username", "password"})
public ModelAndView index(){
    return new ModelAndView("index");
}
```

比如这里我们要求请求中必须携带`username`和`password`属性，否则无法访问。它还支持表达式，比如我们可以这样编写：

```java
@RequestMapping(value = "/index", params = {"!username", "password"})
public ModelAndView index(){
    return new ModelAndView("index");
}
```

在username之前添加一个感叹号表示请求的不允许携带此参数，否则无法访问，我们甚至可以直接设定一个固定值：

```java
@RequestMapping(value = "/index", params = {"username!=test", "password=123"})
public ModelAndView index(){
    return new ModelAndView("index");
}
```

这样，请求参数username不允许为test，并且password必须为123，否则无法访问。

`header`属性用法与`params`一致，但是它要求的是请求头中需要携带什么内容，比如：

```java
@RequestMapping(value = "/index", headers = "!Connection")
public ModelAndView index(){
    return new ModelAndView("index");
}
```

那么，如果请求头中携带了`Connection`属性，将无法访问。其他两个属性：

* consumes： 指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;
* produces:  指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回；

如何在请求中获取数据：

- 在形式参数前面添加`@RequestParam`注解即可

```java
@RequestMapping(value = "/index")
public ModelAndView index(@RequestParam("username") String username){//跟mybatis很类似，差不多，也是赋值，之不是它是赋值在sql语句，不是
    System.out.println("接受到请求参数："+username);
    return new ModelAndView("index");
}
```

```
 Human selectHumanByNameAndSex(@Param("name") String username, Integer sex);//这个是mybatis的赋值
```

#### mybatis![image-20230408234244469](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304082342624.png)

如果注解里面的对象名（请求头携带的属性名称）与形参名称一样，可以不使用注解，当时最好都使用注解

#### @param注解的某些属性：

- require：可以设置请求头必须带值也可以不带值

- defaultValue：这个是默认值，如果请求头不带值，默认给的值

- 也可以使用原方法，使用HttpServerRequest 类的对象，也就是请求头对象，使用它的getxxx方法；而且这个时候的require非常聪明，知道你需要什么值

- ---

- @RequestParam和@RequestHeader详解

  我们接着来看，如何获取到请求中的参数。

  我们只需要为方法添加一个形式参数，并在形式参数前面添加`@RequestParam`注解即可：

  ```java
  @RequestMapping(value = "/index")
  public ModelAndView index(@RequestParam("username") String username){
      System.out.println("接受到请求参数："+username);
      return new ModelAndView("index");
  }
  ```

  我们需要在`@RequestParam`中填写参数名称，参数的值会自动传递给形式参数，我们可以直接在方法中使用，注意，如果参数名称与形式参数名称相同，即使不添加`@RequestParam`也能获取到参数值。

  一旦添加`@RequestParam`，那么此请求必须携带指定参数，我们也可以将require属性设定为false来将属性设定为非必须：

  ```java
  @RequestMapping(value = "/index")
  public ModelAndView index(@RequestParam(value = "username", required = false) String username){
      System.out.println("接受到请求参数："+username);
      return new ModelAndView("index");
  }
  ```

  我们还可以直接设定一个默认值，当请求参数缺失时，可以直接使用默认值：

  ```java
  @RequestMapping(value = "/index")
  public ModelAndView index(@RequestParam(value = "username", required = false, defaultValue = "伞兵一号") String username){
      System.out.println("接受到请求参数："+username);
      return new ModelAndView("index");
  }
  ```

  如果需要使用Servlet原本的一些类，比如：

  ```java
  @RequestMapping(value = "/index")
  public ModelAndView index(HttpServletRequest request){
      System.out.println("接受到请求参数："+request.getParameterMap().keySet());
      return new ModelAndView("index");
  }
  ```

  直接添加`HttpServletRequest`为形式参数即可，SpringMVC会自动传递该请求原本的`HttpServletRequest`对象，同理，我们也可以添加`HttpServletResponse`作为形式参数，甚至可以直接将HttpSession也作为参数传递：

  ```java
  @RequestMapping(value = "/index")
  public ModelAndView index(HttpSession session){
      System.out.println(session.getAttribute("test"));
      session.setAttribute("test", "鸡你太美");
      return new ModelAndView("index");
  }
  ```

  我们还可以直接将请求参数传递给一个实体类：

  ```java
  @Data
  public class User {
      String username;
      String password;
  }
  ```

  注意必须携带set方法或是构造方法中包含所有参数，请求参数会自动根据类中的字段名称进行匹配：

  ```java
  @RequestMapping(value = "/index")
  public ModelAndView index(User user){
      System.out.println("获取到cookie值为："+user);
      return new ModelAndView("index");
  }
  ```

  `@RequestHeader`与`@RequestParam`用法一致，不过它是用于获取请求头参数的，这里就不再演示了。

### 请求转向与重定向：

在web学习的时候，是使用请求头进行请求转向和重定向，现在直接在视图前面加上redirrect：（重定向）或者forward：（请求转向）就能实现跳转

这个前缀和视图名称加在ModelAndView对象属性中，或者如果返回类型是string的话（如果不是ModelAndView类型，使用string类型，自动装配为ModelAndView类型），直接在return后面写就行：

```
@RequestMapping("/index")
public String index(){
    return "redirect:home";
}

@RequestMapping("/home")
public String home(){
    return "home";
}
```

通过添加`redirect:`前缀，就可以很方便地实现重定向，那么请求转发呢，其实也是一样的，使用`forward:`前缀表示转发给其他请求映射：

```java
@RequestMapping("/index")
public String index(){
    return "forward:home";//如果是MOdelAndView，那在新建对象的时候，使用构造方法，里面就是填写forward:home
}

@RequestMapping("/home")
public String home(){
    return "home";
}
```

- ### MVC原理过程（me）：

- 首先使用RequestMapping映射出来一个地址（GPT解释：@RequestMapping注解用于将HTTP请求映射到控制器方法上），类似于@webServlet那样，，用这地址向服务器发起请求访问，服务器收到后，首先经过调度中心的servlet，他分配控制器给我们，也就是ModelAndView对象，它从配置好的配置类读取MOdelAndView对象指定的文件,然后执行业务层的代码，执行后，返回该对象给调度中心，调度中心接收到以后，会分配给视图处理器，进行渲染和输出，响应到客户端渲染器也是在webConfig配置类中进行配置好的，调度中心的配置类是继承了 AbstractAnnotationConfigDispatcherServletInitializer类，这就是全注解开发。原理大概就是这样就是映射地址，请求发送给调度中心，调度中心收到modelAndView对象后，给视图渲染器进行渲染出来。

---

---

## RestFul风格表现层：

作用：让参数一起带在URL里面，并且使用某些注解进行读取：

#### http的请求方式：

- 第一种：Post，一般是是实现创建对象的功能：
- 第二种：Get的请求：一般是实现查询的功能
- 第三种方式：Put：一般使用更改数据
- 第四种方式：delete:一般使用是删除数据的操作

#### 使用方式：

```Java
在RequesMapping注解中添加：//http....//.../index/{参数的名称}//网页index后面跟的是参数的名称，用于后面取值
在注解后面的方法中的形参，可以使用注解获取值：@pathVariable（这里对应Url的参数名称）后面是类型和形参名称，也就是类似于Mybatis的@param注解那样

```



```java
javaCopy code@RestController
@RequestMapping("/api")
public class ApiController {
    
    // 实现GET请求，获取数据
    @GetMapping("/data/{id}")
    public Data getData(@PathVariable("id") Long id) {
        // 从数据库中获取数据
        Data data = dataService.getDataById(id);
        return data;
    }
    
    // 实现POST请求，提交数据
    @PostMapping("/data")
    public ResponseEntity<Void> postData(@RequestBody Data data) {
        // 将数据保存到数据库
        dataService.saveData(data);
        // 返回成功状态码
        return ResponseEntity.status(HttpStatus.CREATED).build();
    }
    
}
```



### 拦截器：

类似于之前web端遇到的过滤器：功能都是一样的：



![IMG_5613](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304091920649.JPEG)

### 拦截器的使用：

#### 第一步：创建一个类，实现HandLerInterceptor接口

第二实现接口中的三个方法

第一步先走preHandle的方法，只有这个方法返回了ture，程序才会接着往下走，因为没有被拦截，所以往下走去到控制器，也就是类似以前的servlet；

第二个方法：PostHandle：这是控制器处理后，没有抛出异常，进入的方法；

第三个方法：afterHandle，这个是完成后，进入的方法，也就是经过视图cup之后的方法，经过视图cpu后，输出给客户端

#### 第二步：要配置webconfig，让它识别到我们刚才接入接口的类：

在webconfig配置类中，添加Addinterceptors方法：正确来说是实现方法

类似于这样子：

```java
 @Override
  public void addInterceptors(InterceptorRegistry registry) {
    // 定义排除swagger访问的路径配置
    String[] swaggerExcludes =
        new String[] {"/swagger-ui.html", "/swagger-resources/**", "/webjars/**"};
    registry
        .addInterceptor(userTokenInterceptor)
        .addPathPatterns("/**")
        .excludePathPatterns(
            "/user/login", "/static/**", "/*.html", "/*.ico", "/*.json", "/*.png", "/heartbeat/**")
        .excludePathPatterns(swaggerExcludes);
    registry
        .addInterceptor(securityInterceptor)
        .addPathPatterns("/maintain/**", "/user/**")
        .excludePathPatterns("/user/login");
  }

```

或者简单点的：

```java
   public  void addInterceptors(InterceptorRegistry registry){//这个方法写在webCongige里面，注意不是根目录那个config
        registry.addInterceptor()//括号里面new一个我们刚才继承了接口类的对象出来
            //对象里面还有一些方法是设置拦截的：如设置拦截路径，addPath...设置不被拦截路径：excludepath
            
        
    }
```

#### 第三点：如果运行中，经过了第一个方法，当时控制器抛出异常：

首先：拦截器已经通过的第一个方法是没有问题的，因为进入控制器之前是需要经过第一个方法，第二个方法是进入不了的，因为第二个方法是需要控制器返回给调度中心才会进去的，第三个方法是需要进入的，因为出现异常，给到调度器，然后给视图cpu进行输出异常信息

### 多级拦截器：

它是按照添加顺序的走的；并非处理前和处理后的方法，是上一个拦截器输出的，因为像之前的过滤器那样，都是像栈的形式；

也是要在webConfig类进行配置的，而且一旦一个拦截器拦截下来，都是不会进行下去的

## 异常的处理：自定义异常界面

我们可以自定义一个异常处理控制器，一旦出现指定异常，就会转接到此控制器执行：

```java
@ControllerAdvice
public class ErrorController {

    @ExceptionHandler(Exception.class)//这个是匹配异常，当异常匹配成功，执行下面的方法
    public String error(Exception e, Model model){  //可以直接添加形参来获取异常
        e.printStackTrace();
        model.addAttribute("e", e);
        return "500";//相当于是替换了内嵌的500界面
    }
}
```

接着我们编写一个专门显示异常的页面：

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
  500 - 服务器出现了一个内部错误QAQ
  <div th:text="${e}"></div>
</body>
</html>
```

接着修改：

```java
@RequestMapping("/index")
public String index(){
    System.out.println("我是处理！");
    if(true) throw new RuntimeException("您的氪金力度不足，无法访问！");
    return "index";
}
```

访问后，我们发现控制台会输出异常信息，同时页面也是我们自定义的一个页面。

## JSON：

首先导入JSON框架包：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.78</version>
</dependency>
```

```java
public class MainText {
    @Test
    public void testJSON(){
        JSONObject jsonObject=new JSONObject();
        JSONObject jsonObject1=new JSONObject();
        jsonObject.put("text1", "hello");
        jsonObject.put("text2", "world");//键值对形式，也就是类似于Map
        jsonObject.put("jsonAginst", jsonObject1);//可以这样嵌套/并且js和json可以互相转换
        System.out.println(jsonObject.toJSONString());

    }
}
```

![image-20230409202147342](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304092021439.png)

![image-20230409202202384](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304092022448.png)

### 可以传入一个对象，Json自动帮我们变成键值对，键就是我们对象的属性：

![image-20230409202825318](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304092028394.png)

- 在PequesMapping注解下，再加个注解，@ResponseBoby，这样子，我们在方法中返回什么，客户端就能收到什么，而且MVC还非常聪明，可以传给它一个对象，它会自动装配成JSON对象进行输出，而且加入了这个注解，我们输出就不需要经过视图解析器

![image-20230409205635099](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304092056177.png)

```java
@RequestMapping("/yu")
@ResponseBody
public String yu(){
    return "helloworld";

}
```

![image-20230409205712724](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304092057780.png)

在ReqMapping注解中，还可以加上属性produces，可以让请求头知道我们返回的是JSON类型还是其他类型



### 文件上传和下载

利用SpringMVC，我们可以很轻松地实现文件上传和下载，同样的，我们只需要配置一个Resolver：

```java
@Bean("multipartResolver")   //注意这里Bean的名称是固定的，必须是multipartResolver
public CommonsMultipartResolver commonsMultipartResolver(){
    CommonsMultipartResolver resolver = new CommonsMultipartResolver();
    resolver.setMaxUploadSize(1024 * 1024 * 10);   //最大10MB大小
    resolver.setDefaultEncoding("UTF-8");   //默认编码格式
    return resolver;
}
```

接着我们直接编写Controller即可：

```java
@RequestMapping(value = "/upload", method = RequestMethod.POST)
@ResponseBody
public String upload(@RequestParam CommonsMultipartFile file) throws IOException {
    File fileObj = new File("test.html");//请求头将文件上传到了file里面，我们用文件流转存
    file.transferTo(fileObj);//文件上传到了file，我们用它对象的方法，进行转成文件流，文件流是我们自己的地址
    System.out.println("用户上传的文件已保存到："+fileObj.getAbsolutePath());
    return "文件上传成功！";
}
//记得导入包：import org.springframework.web.multipart.commons.CommonsMultipartResolver;
```

使用CommonsMultipartFile对象来接收用户上传的文件。它是基于Apache的Commons-fileupload框架实现的，我们还需要导入一个依赖：

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.4</version>
</dependency>
```

最后在前端添加一个文件的上传点：

```html
<div>
    <form action="upload" method="post" enctype="multipart/form-data">
        <input type="file" name="file">
        <input type="submit">
    </form>
</div>
```

​    这样，点击提交之后，文件就会上传到服务器了。

#### 下载文件：

下载其实和我们之前的写法大致一样，直接使用HttpServletResponse，并向输出流中传输数据即可。

```java
@RequestMapping(value = "/download", method = RequestMethod.GET)
@ResponseBody
public void download(HttpServletResponse response){
    response.setContentType("multipart/form-data");
    try(OutputStream stream = response.getOutputStream();
        InputStream inputStream = new FileInputStream("test.html")){
        IOUtils.copy(inputStream, stream);
    }catch (IOException e){
        e.printStackTrace();
    }
}
```

在前端页面中添加一个下载点：

```html
<a href="download" download="test.html">下载最新资源</a>
```

## security

功能 介绍：1·认证；2·授权3·防护

1·导入依赖：

2·创建一个类，是securityConfigration，继承WebSecurityConfiguraerAdampter类，也就是类似于调度中心dispaServlet类似，

```java
@EnableWebSecurity
public class SecurityInit extends AbstractSecurityWebApplicationInitializer {
//这个不需要填写内容的，只是告诉
}
```

还要新建一个类：SecurityConfig类，阶层WebSecurityAdapter类

```java
xxxxxxxxxx @Configuration
    @EnableWebSecurity
    public class SecurityConfiguration extends WebSecurityConfigurerAdapter {        //继承WebSecurityConfigurerAdapter，之后会进行配置}
```



并且要在调度中心类中加入Security配置类。跟RootConfig是同一个等级的；

```java
package bookmanage.bookmanage.Config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class MianInit extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{MainConfig_Root.class,  SecurityConfiguration .class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```



配置类：然后有调度中心，还有security初始化类

### 登录认证：

1`实现登录，要重写configure方法，，2。方法里 面使用密码加密器加密密码（数据库存的是加密后的密文），因为比较的是密文

加密器使用BCrytPasswordEncoder这个类，这个类是由securty这个框架提供的

添加用户，操作的对象是形参那个类的对象

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();  //这里使用SpringSecurity提供的BCryptPasswordEncoder
    auth//springboot框架封装了，所以直接拿来使用，springboot是注册为bean了
            .inMemoryAuthentication() //直接验证方式，之后会讲解使用数据库验证
            .passwordEncoder(encoder) //密码加密器
            .withUser("test")   //用户名
            .password(encoder.encode("123456"))   //这里需要填写加密后的密码
            .roles("user");   //用户的角色
}
```



无论是springboot还是springMVC都需要实现一个@service的实现类，还必须继承UserDetailsService接口//从数据库取用户作为登录用户，要加这个@service这个实现类（是实现了那个接口的实现类） 

### MVC：

```java
//需要创建一个Service实现，实现的是UserDetailsService，它支持我们自己返回一个UserDetails对象，我们只需直接返回一个包含数据库中的用户名、密码等信息的UserDetails即可，SpringSecurity会自动进行比对。
@Service//@Service注解用于类上，标记当前类是一个service类，加上该注解会将当前类自动注入到spring容器中，不需要再在applicationContext.xml文件定义bean了。
public class UserAuthService implements UserDetailsService {

    @Resource
    UserMapper mapper;

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        String password = mapper.getPasswordByUsername(s);  //从数据库根据用户名获取密码
        if(password == null)
            throw new UsernameNotFoundException("登录失败，用户名或密码错误！");
        return User   //这里需要返回UserDetails，SpringSecurity会根据给定的信息进行比对//这个User是securety的User
                .withUsername(s)
                .password(password)   //直接从数据库取的密码
                .roles("user")   //用户角色
                .build();
    }
}
```

### springBOOT模式：

```java
package com.example.service;

import com.example.Mapper.UserMapper;
import com.example.entity.User;
import jakarta.annotation.Resource;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class UserService implements UserDetailsService {
    //疑问点1，为什么要接这个接口，第二个这个接口干什么的
    //为什么有User类还要创建这个


    @Resource
    UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        if(username==null){
            throw new UsernameNotFoundException("用户名为空");

        }
        User user= userMapper.FindUserByName(username);
        if(user==null){
            throw new UsernameNotFoundException("用户名或密码错误");

        }

        return org.springframework.security.core.userdetails.User
                .withUsername(user.getName())
                .password(user.getPassword())
                .roles("user")
                .build();
    }
}

```



### 自定义登录界面

前面我们已经了解了如何实现数据库权限验证，那么现在我们接着来看看，如何将登陆页面修改为我们自定义的样式。

首先我们要了解一下SpringSecurity是如何进行登陆验证的，我们可以观察一下默认的登陆界面中，表单内有哪些内容：

```html
<div class="container">
      <form class="form-signin" method="post" action="/book_manager/login">
        <h2 class="form-signin-heading">Please sign in</h2>
        <p>
          <label for="username" class="sr-only">Username</label>
          <input type="text" id="username" name="username" class="form-control" placeholder="Username" required="" autofocus="">
        </p>
        <p>
          <label for="password" class="sr-only">Password</label>
          <input type="password" id="password" name="password" class="form-control" placeholder="Password" required="">
        </p>
<input name="_csrf" type="hidden" value="83421936-b84b-44e3-be47-58bb2c14571a">
        <button class="btn btn-lg btn-primary btn-block" type="submit">Sign in</button>
      </form>
</div>
```

我们发现，首先有一个用户名的输入框和一个密码的输入框，我们需要在其中填写用户名和密码，但是我们发现，除了这两个输入框以外，还有一个input标签，它是隐藏的，并且它存储了一串类似于Hash值的东西，名称为"_csrf"，其实看名字就知道，这玩意八成都是为了防止CSRF攻击而存在的。

从Spring Security 4.0开始，默认情况下会启用CSRF保护，以防止CSRF攻击应用程序，Spring Security CSRF会针对PATCH，POST，PUT和DELETE方法的请求（不仅仅只是登陆请求，这里指的是任何请求路径）进行防护，而这里的登陆表单正好是一个POST类型的请求。在默认配置下，无论是否登陆，页面中只要发起了PATCH，POST，PUT和DELETE请求一定会被拒绝，并返回**403**错误**（注意，这里是个究极大坑）**，需要在请求的时候加入csrfToken才行，也就是"83421936-b84b-44e3-be47-58bb2c14571a"，正是csrfToken，如果提交的是表单类型的数据，那么表单中必须包含此Token字符串，键名称为"_csrf"；如果是JSON数据格式发送的，那么就需要在请求头中包含此Token字符串。

综上所述，我们最后提交的登陆表单，除了必须的用户名和密码，还包含了一个csrfToken字符串用于验证，防止攻击。

因此，我们在编写自己的登陆页面时，需要添加这样一个输入框：

```html
<input type="text" th:name="${_csrf.getParameterName()}" th:value="${_csrf.token}" hidden>
```

隐藏即可，但是必须要有，而Token的键名称和Token字符串可以通过Thymeleaf从Model中获取，SpringSecurity会自动将Token信息添加到Model中。

接着我们就可以将我们自己的页面替换掉默认的页面了，我们需要重写另一个方法来实现：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
            .authorizeRequests()   //首先需要配置哪些请求会被拦截，哪些请求必须具有什么角色才能访问
            .antMatchers("/static/**").permitAll()    //静态资源，使用permitAll来运行任何人访问（注意一定要放在前面）
            .antMatchers("/**").hasRole("user")     //所有请求必须登陆并且是user角色才可以访问（不包含上面的静态资源）
}
```

首先我们需要配置拦截规则，也就是当用户未登录时，哪些路径可以访问，哪些路径不可以访问，如果不可以访问，那么会被自动重定向到登陆页面。

接着我们需要配置表单登陆和登录页面：

```java
.formLogin()       //配置Form表单登陆
.loginPage("/login")       //登陆页面地址（GET）
.loginProcessingUrl("/doLogin")    //form表单提交地址（POST）
.defaultSuccessUrl("/index")    //登陆成功后跳转的页面，也可以通过Handler实现高度自定义
.permitAll()    //登陆页面也需要允许所有人访问
```

需要配置登陆页面的地址和登陆请求发送的地址，这里登陆页面填写为`/login`，登陆请求地址为`/doLogin`，登陆页面需要我们自己去编写Controller来实现，登陆请求提交处理由SpringSecurity提供，只需要写路径就可以了。

```java
@RequestMapping("/login")
public String login(){
    return "login";
}
```

配置好后，我们还需要配置一下退出登陆操作：

```java
.and()
.logout()
.logoutUrl("/logout")    //退出登陆的请求地址
.logoutSuccessUrl("/login");    //退出后重定向的地址
```

注意这里的退出登陆请求也必须是POST请求方式（因为开启了CSFR防护，需要添加Token），否则无法访问，这里主页面就这样写：

```html
<body>
    <form action="logout" method="post">
        <input type="text" th:name="${_csrf.getParameterName()}" th:value="${_csrf.token}" hidden>
        <button>退出登陆</button>
    </form>
</body>
</html>
```

登陆成功后，点击退出登陆按钮，就可以成功退出并回到登陆界面了。

由于我们在学习的过程中暂时用不到CSFR防护，因此可以将其关闭，这样直接使用get请求也可以退出登陆，并且登陆请求中无需再携带Token了，推荐关闭，因为不关闭后面可能会因为没考虑CSRF防护而遇到一连串的问题：

```java
.and()
.csrf().disable();
```

这样就可以直接关闭此功能了，但是注意，这样将会导致您的Web网站存在安全漏洞。（这里为了之后省事，就关闭保护了，但是一定要记得在不关闭的情况下需要携带Token访问）

##   疑问GPT解答：

### 如何实现登录的：

```
UserDetailsService 接口用于从后端数据源（例如数据库）中加载用户信息，并返回一个 User 对象，包含了用户的详细信息（如用户名、密码、权限等）。AuthenticationManager 是 Spring Security 提供的一个接口，用于进行认证操作，它通常会调用 UserDetailsService 的实现类来加载用户信息，并进行密码验证。

当用户提交登录表单时，Spring Security 的认证流程会调用 UserDetailsService 的实现类来加载用户信息，并将用户输入的密码与从数据源中获取的密码进行比对。如果密码验证通过，认证流程会创建一个 Authentication 对象表示用户的认证信息，并将其放入 SecurityContextHolder 中，标记用户登录成功。

然后，Spring Security 会根据认证流程的配置，例如配置的登录成功的处理器（SuccessHandler），来执行相应的操作，例如重定向到指定页面、返回认证成功的信息等。

所以，登录是否成功是由 AuthenticationManager 来判断的，而不是 UserDetailsService 接口的实现类中的 Controller 方法的返回值。返回 UserDetails 对象只是加载用户信息的一部分，但最终的认证结果会由 AuthenticationManager 来决定。
```

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();  //这里使用SpringSecurity提供的BCryptPasswordEncoder
    auth//springboot框架封装了，所以直接拿来使用，springboot是注册为bean了//主要的还是这个
            .inMemoryAuthentication() //直接验证方式，之后会讲解使用数据库验证
            .passwordEncoder(encoder) //密码加密器
            .withUser("test")   //用户名
            .password(encoder.encode("123456"))   //这里需要填写加密后的密码
            .roles("user");   //用户的角色
}
```

```
在使用 Spring Security 进行用户认证时，一般情况下不会在 UserDetails 对象中进行具体的认证逻辑，而是通过自定义的 AuthenticationProvider 进行认证。

AuthenticationProvider 是 Spring Security 提供的一个接口，用于进行认证操作。您可以通过实现该接口，自定义认证逻辑。在认证流程中，AuthenticationProvider 的 authenticate 方法会被调用，传入一个 Authentication 对象，其中包含了用户输入的认证信息（如用户名和密码）。

在 authenticate 方法中，您可以根据传入的认证信息，例如 UserDetails 对象中的用户名和密码，与数据库中的用户信息进行比对，执行具体的认证逻辑。如果认证成功，可以返回一个经过认证的 Authentication 对象，表示认证成功的信息。

然后，AuthenticationManagerBuilder 会使用配置的 AuthenticationProvider 进行认证操作，其中包括了您自定义的认证逻辑。如果认证成功，认证流程会创建一个 Authentication 对象表示用户的认证信息，并将其放入 SecurityContextHolder 中，标记用户登录成功。
```

----

---

为什么没有这个dologin页面，表格却能给够提交数据，

- 可以认为security临时创建了这样的网页用来提取表格信息，进行校验

```java
.formLogin()       //配置Form表单登陆
.loginPage("/login")       //登陆页面地址（GET）
.loginProcessingUrl("/doLogin")    //form表单提交地址（POST）
.defaultSuccessUrl("/index")    //登陆成功后跳转的页面，也可以通过Handler实现高度自定义
.permitAll()    //登陆页面也需要允许所有人访问
```

需要配置登陆页面的地址和登陆请求发送的地址，这里登陆页面填写为`/login`，登陆请求地址为`/doLogin`，登陆页面需要我们自己去编写Controller来实现，登陆请求提交处理由SpringSecurity提供，只需要写路径就可以了。

答案：

```
.loginProcessingUrl() 方法指定表单数据将通过 POST 请求提交进行身份验证的 URL。

在这种情况下，"/doLogin" URL 被配置为 loginProcessingUrl。这个 URL 不一定要对应一个已经存在的网页。Spring Security 会处理传入的 POST 请求，提取表单数据，根据配置的身份验证提供者执行身份验证，并根据身份验证结果采取适当的操作（例如，重定向到成功或失败页面）。
```

个人加上理解：

```java
在这种情况下，"/doLogin" URL 被配置为 loginProcessingUrl。这个 URL 不一定要对应一个已经存在的网页。Spring Security 会处理传入的 POST 请求，提取表单数据，根据配置的身份验证提供者执行身份验证，并根据身份验证结果采取适当的操作（例如，重定向到成功或失败页面）。那我可以理解为security创建了这个临时网页URL用来数据处理，处理完后自动关闭该URL

是的，您可以理解为 Spring Security 在配置的 loginProcessingUrl（例如 "/doLogin"）上创建了一个临时的 URL 用于处理提交的表单数据。这个 URL 并不一定要对应一个已经存在的网页，而是由 Spring Security 自动处理传入的 POST 请求，并提取表单数据进行身份验证。验证结果后，Spring Security 会采取适当的操作，例如重定向到成功或失败页面。处理完成后，这个临时的 URL 会被关闭，即不再有效。这种方式允许 Spring Security 在处理表单提交时，临时创建一个 URL 来处理数据，并在处理完成后自动关闭，以确保安全性和数据保护。
```

### 使用数据库连接认证，要加userDetailsService(service)吗？  

 //使用自定义的Service实现类进行验证      .passwordEncoder(new BCryptPasswordEncoder());

```java
@Autowired
private UserDetailsService userService; // 自定义的 UserDetailsService 实现类

@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.userDetailsService(userService) // 指定自定义的 UserDetailsService 实现类
        .passwordEncoder(new BCryptPasswordEncoder()); // 配置密码加密方式，例如 BCryptPasswordEncoder
}

```

一般在提交表格后添加的：

```
.formLogin()
.loginPage("/login") // 登陆页面地址（GET）
.loginProcessingUrl("/doLogin") // form表单提交地址（POST）
.defaultSuccessUrl("/index") // 登陆成功后跳转的页面，也可以通过Handler实现高度自定义
.permitAll() // 登陆页面也需要允许所有人访问
.userDetailsService(userService) // 使用自定义的 UserDetailsService 实现类进行验证
.passwordEncoder(new BCryptPasswordEncoder()); // 配置密码加密方式，例如 BCryptPasswordEncoder

```

问题：

```
在springboot开发中我@Configuration//怎么知道这个是securety配置类//疑问点一
@EnableWebSecurity//表示这个类是security的配置类
public class SecurityConfigration {
    @Resource
    UserService userService;这样来配置security配置类可以吗？为什么？它是怎么知道这个是我的security配置类的/又不像MVC那样继承  WebSecurityConfigurerAdapter这个类？还是说我有注解@configer他会自动扫描，但是名称要正规才行？
```

1. 使用 `@Configuration` 注解，将类标记为配置类。
2. 使用 `@EnableWebSecurity` 注解，表示这个类是 Spring Security 的配置类。

```
在 Spring Security 中，您可以通过多种方式来配置安全性，包括实现 WebSecurityConfigurer 接口、继承 WebSecurityConfigurerAdapter 类或者使用 @EnableWebSecurity 注解。其中，使用 @EnableWebSecurity 注解的方式是一种简化配置的方式，可以让 Spring Security 自动扫描并识别该类为安全配置类。

在您的代码中，虽然没有继承 WebSecurityConfigurerAdapter 类，但通过使用 @EnableWebSecurity 注解和在类上标注 @Configuration 注解，以及在方法上使用 @Bean 注解，使得 Spring Security 能够识别到这是一个安全配置类，并将其中的配置应用于应用程序的安全性。

此外，您在配置类中使用了 @Bean 注解，定义了多个 Spring Bean，例如 BCryptPasswordEncoder、SecurityFilterChain 和 AuthenticationManager，这些 Bean 会被 Spring Security 自动加载并应用于应用程序的安全配置。

需要注意的是，使用 @EnableWebSecurity 注解的方式，Spring Security 会使用默认的配置，并根据配置类中的方法来进行相应的安全性配置。如果您需要更为详细和定制化的配置，可以继承 WebSecurityConfigurerAdapter 类并重写其中的方法，以实现更精细的安全性设置。但对于简单的安全性配置，使用 @EnableWebSecurity 注解也是一种有效的方式。
```

疑问2：

```
那我写注解@EnableWebSecurity@Configuration不继承WebSecurityConfigurerAdapter，不实现WebSecurityConfigurerAdapter的方法可以吗？
```

```
是的，您可以使用 @EnableWebSecurity 注解和 @Configuration 注解来创建一个安全配置类，而无需继承 WebSecurityConfigurerAdapter 类或者实现其方法。

@EnableWebSecurity 注解表明该类是一个安全配置类，并启用了 Spring Security 的安全性功能。@Configuration 注解表明该类是一个 Spring 配置类，用于配置应用程序的一些功能。

通过这种方式，您可以在配置类中使用 @Bean 注解来定义 Spring Bean，例如配置密码加密方式、定义认证和授权的规则、设置登录和注销的处理等。这些配置将会被 Spring Security 自动加载并应用于应用程序的安全性。

需要注意的是，虽然您不需要继承 WebSecurityConfigurerAdapter 类或者实现其方法，但这意味着您将使用 Spring Security 的默认配置。如果您需要更为详细和定制化的安全性配置，可以继承 WebSecurityConfigurerAdapter 类并重写其中的方法，以实现更精细的安全性设置。但对于简单的安全性配置，使用 @EnableWebSecurity 注解和 @Configuration 注解也是一种有效的方式。

所以不继承，不实现也是可以的，只要我将SecurityConfigration 这个类注册为bean就可以，

是的，您可以通过在 SecurityConfigration 类上添加 @Configuration 注解，并将其注册为 Spring Bean，以实现安全配置。

Spring Security 在启动时会自动扫描并加载带有 @Configuration 注解的类，并将其应用于应用程序的安全性设置。因此，只要您的 SecurityConfigration 类被正确注册为 Spring Bean，Spring Security 将会自动应用其中的安全性配置。

需要注意的是，您需要确保在应用程序的其他地方引用了 SecurityConfigration 类，以触发其初始化并将其作为 Bean 注册到 Spring 容器中。这可以通过在主类上使用 @ComponentScan 注解，或者在配置类中使用 @Import 注解来实现。
```

也可以方法重载：

![image-20230412222027154](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304122220285.png)

## 授权：(使用注解)

#### 在SecurityConfig配置类上添加开启用户授权的注解@EnableGlobalMethodSecurity(securedEnabled = true)

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304122231486.png)

```java
/**
 * Spring Security配置类
 */
@EnableWebSecurity
@EnableGlobalMethodSecurity(securedEnabled = true) //开启security注解
public class SecurityConfig extends WebSecurityConfigurerAdapter {
	...
}//这个开始角色不同，有没有权限进入网站的前提，不然所有角色都是同一个权限，所以用注解开始不同权限的划分
```

#### 添加授权的注解

在需要被授权的Controller上添加授权的注解

@PreAuthorize(“hasAuthority(‘update’)”)：具有update权限的用户，才可以访问该路径
@Secured(“ROLE_admin”)：具有admin角色的用户，才可以访问该路径

```java
	@RequestMapping("/test/delete")
    @Secured("ROLE_admin")//具有admin角色的用户，才可以访问该路径//这里是控制器的地方
    public String tt3(){
        return "测试删除";
    }
```



    @RequestMapping("/test/update")
    @PreAuthorize("hasAuthority('update')")//具有update权限的用户，才可以访问该路径
    public String tt4(){
        return "测试修改";
    }
