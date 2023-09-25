<img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202303190010877.png" alt="image-20230319001002202" style="zoom: 50%;" />   

# *** Note of  java***

## 类与对象之前：

Java内存：

- 堆内存-
- 栈内存-
- 方法区-

```
1.首先一定要注意大小写，因为大写和小写是有区别的，如System.out.printf();前面的大写S而不是小写的，这个是输出
```

```
2. Scanner scanner=new Scanner(System .in);//输入前必须要写的，写一次就够啦！
import java.util.Scanner;//一写scanner会自动召唤该模块！在顶部的
然后输入就是scanner.nextInt();这个是输入int的

scanner.next();这个是输入字符串的，但是遇到空格会终止

所以应该用scanner.nextLine();这样是按enter才是终止的

```

```
3. import java.util.*;（写在最开头，相当于c语言中的头文件）

然后在你要输入的地方输入以下代码

Scanner input=new Scanner(System.in);（这里的input 自己可以改，只要与下面的变量名保持一致就好）

如果接收整形，用nextInt

int i=input.nextInt();

如果要输入字符，用nextLine

String i =input.nextLine();

如果是浮点型中的float

float i=input.nextFloat(); 

如果是浮点型中的double

double i=input.nextDouble();
```

```
 4.在c语言的函数，在Java中叫方法

5.字符串+字符串=新的字符串，字符串可以直接相连接，用加法。

6.位运算左移<<一位，相当乘2，左移2位，相当于再左移一位的基础上x2.所以右移>>一位是除2

7.换行字符也是\n

8.System.out.println()自带换行功能
```

```
9.一共有8大数据类型

整型：bye（保存数字是最小的，只有127）

   Short

   Int

   Long（要在数字后面加个L）这是与c不一样的地方。还有，可以向下兼容数据，如:short b=200;int a=b;可以成立，因为int的数据空间大于short；

但如果int b=200;short a=b;错误，因为b的数据类型是int型，可包容的数据更大，所以不可以小的向上兼容，只能向下兼容，也就是向第一种那样！

浮点型：

Float:如果要想使用float型，要在数字后面加上F；否则变成double型数数据

Double

 整数类型可以向下兼容，比如int类型如果没有超过byte范围，可以自动转化为byte类型，但是超过了，强制转换是可以转换的，但是会缺失精度的问题。double可以变成float型，没有超过的情况下。也就是说，没有超过范围，自动转型，超出范围就要手动强制类型转换
 需要注意的是在使用long和float类型的时候，需要在值的后面加上L和F来做标记，否则会被识别为int和double类型

字符型：

Char

字符串型，（不是基本的数据类型，是对象类型）

String a=“hello”；这是c语言不能那么简单的，c语言字符串要么数组，要么指针限定。

10.设置变量是，在数据类型前面加上final，该变量一旦赋值后，就不能改变。类似于c语言的const关键字

11.二进制计算和转换

转换：如：11011一共有五位数字，所以应该这样算1*2^5-1+1*2^3+0*2^2+1*2^1+1*2^0

十进制转换成2进制：短除法一直下去直到0

注意事项：在java中无论是小数还是整数都要带符号位，首位就是符号位，如11001，其符号
位就是1，后面才是二进制。0代表正数，1代表负数。C语言是是没有符号位的》
```



## 类与对象：

- 如果一个源文件里面，有且只有一个public类，就是说，比如我设置了a类，在下面又class b，b不能加public。
- 如果一个类是public修饰，那么这个源文件一定和public类名相同
- ==使用多个类，最好就是新建不同的源文件，也就是新建多个类==
- 补充对象快捷键，ctrl+alt+v
- <img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202303190015076.png" alt="image-20230314092729483" style="zoom: 67%;" />
- <img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202303190015350.png" alt="image-20230314092900212" style="zoom: 50%;" />
- 12.类就是抽象的一种分类，如人类，鸟类等，具有相同的特点，有点像c语言的结构体，但又不是完全结构体，等下解释

对象是类的具体，如人类具体到某一个人。

```
创建一个学生类：public class student {

String name;

Int age;}
```

```
这是一个学生类，里面有2中属性

Student p1=new student（）;

Student p2=new student（）;这个2个对象，也相当于是两个人，也叫实例

P1.name=”李金钊“;

P1.age=20;

这是修改对象的属性
```

```
如果这是student p3=p1;

P1.name=ckc;

P3也是跟着会变，因为p1，p2，p3都是类似于指针，指向某一个对象或者新的对象，而c语言p3是不会跟着变的，因为它相当于是拷贝了一份，这里是指同一个对象。类似于指针

类名 变量名=new 类名（）；

 
```

- 13.在类里面自定义函数（跟c语言一样）,在主函数可以调用对象，调用函数。函数在java中叫方法，

```
void swap(int a,int b)
{
    System.out.println("答案是"+(a+b)+"");

}
在student中有个swap方法，可以使用

P1.swap（3，5）；这样就是p1对象会调用swap方法，所以，这就是操作对象；
```

- 14.关键字this，如：this.name表示当前对象的name，所以this.的意思是表示当前对象的…

- 15.同一个类里面可以存在重名的方法，但形参要不一样，如形参是2个与多个，或者类型不一样。如在student类中，有2个重名的方法：

```
Void setname(String name){this.name=name;}//俗称方法重载

Void setname(char name){this.name=name;}像这样是可以的存在两个同名称函数（方法）根据形参不同，所以来决定使用哪一个
```

- 16.构造方法来初始化对象：

```
 其实我们在类中，创建新对象是这样的；如student类

Student p1=new student（）;

其实里面的student(){}是这样的，里面不写任何东西，在类student自带student方法，跟方法相比就是缺了返回类型，我们叫构造方法。我们也可以改一改，让开始初始化如：student(String name,int age){this.name=name;this.age=age;}这些在main方法调用时加形参进去就会自动匹配形参；

Student p1=new student(“xiaomi”,23);

这样就创建了一个新的对象，名字叫xiaomi，年龄23；也可以两个构造方法一起放入类里面，形参不一样，用的也不一样。
```

- ###### 17.代码块：如果类里面有代码块，代码块比构造方法先执行；

- 18.执行顺序：静态的先执行，再到成员。无论是变量 。代码块，构造方法

```
静态变量，是属于类的，无论什么时候一旦修改，无论什么对象访问都是跟着修改的，类型前面加了static的就是静态变量，说白了该变量是共享的，无论是谁修改，谁访问都可以，因为它属于类的不是对象的，也就是不是成员变量。类似于c，c在类型前面加了static，会让局部变量变成全局变量。

使用事项：平时我们都是操控对象，如p1.什么什么，现在也可以继续用这样的方法，如我student类有个全局变量a，我可以使用student 的对象之一p1，进行访问和修改，但不标准，p1.a=6; p2.a=9；最终a的值是变成了9，无论哪一个对象都能访问修改，但是我们通常用类名来控制，而不是对象控制，如student.a=10;student.a=100;student是类名。静态方法也是如此，一般用类名去控制，而不是对象，方法类型前面多个static，变成静态方法

静态方法里面的变量只能是静态变量，而不是用对象的成员变量，也不能用成员方法。所以大致分两种，一种控制对象，一种控制类，静态方法也不能用this关键字，因为他不是对象，不能指代哪一个对象。

```

- 19.包的作用

-  包;为了避免类太多，不好找也不好管理，所以可以新建软件包把类分类，每个包里面的文件都要加上包名，package 包名，用不同一级的目录的类，也就是不同包的类，要进行导入

> 导入：impor 包名.类名 如果要导入包里面的全部类，应该是：impor 包名.*
>
> 如果包1，有sum这个类，包2也有sum这个类，使用sum是要说明使用哪一个包的sum，就是在前面类前面加上包名.

- 20.访问权限：不同包之前的类是有访问权限的：

Java有四种访问权限， 其中三种有访问权限修饰符，分别为private，public和protected，还有一种不带任何修饰符。

```
1. private:     Java语言中对访问权限限制的最窄的修饰符，一般称之为“私有的”。被其修饰的类、属性以及方法只能被该类的对象访问，其子类不能访问，更不能允许跨包访问。
2. default：即不加任何访问修饰符，通常称为“默认访问模式“。该模式下，只允许在同一个包中进行访问。
3. protect:     介于public     和     private 之间的一种访问修饰符，一般称之为“保护形”。被其修饰的类、属性以及方法只能被类本身的方法及子类访问，即使子类在不同的包中也可以访问。
4. public： Java语言中访问限制最宽的修饰符，一般称之为“公共的”。被其修饰的类、属性以及方法不仅可以跨类访问，而且允许跨包（package）访问。
```



下面用表格的形式来展示四种访问权限之间的异同点，这样会更加形象。表格如下所示：

|                       | **同一个类** | **同一个包** | **不同包的子类** | **不同包的非子类** |
| --------------------- | ------------ | ------------ | ---------------- | ------------------ |
| Private               | √            |              |                  |                    |
| Default（默认，不写） | √            | √            |                  |                    |
| Protected             | √            | √            | √                |                    |
| Public（公用）        | √            | √            | √                | √                  |



##  Java三大特性：继承，封装，多态

- ### 封装

  封装：将对象的属性和方法结合成一个整体，只有通过该整体，否则无法改变和访问对象的某些方法或者属性，该整体其实也是自定义方法，调用其他方法和使用private权限（除了同类的方法可以访问或者修改，其他包或者main类或者其他类没有权限）

  Private私有属性，除了类自己，其他的无法访问和修改

   自己自定义方法进行访问和修改;\

  

  ```java
  private String brand;
      private String model;
      private double size;
      private double price;
      private String config;
      public  int inventory;//库存
      public  double total_price;//总价
  
  public String getBrand() {
          return brand;
      }
  ```

- ### 继承

继承：像人类，人类又分为学生，教师，工人等不同种类。像这样的，人类我们通常叫做父类，学生，教师，工人我们叫做子类；

- 定义子类之前我们要先定义父类，定义子类，在子类名后面加上extents+父类名；

- 类是可以一直往下走的，除非某个类加上了final这个关键字；

- 对于父类是private的属性继承，子类是继承的，但是拿不到。So没办法

- 如果父类有构造方法，子类也必须要有构造方法，并且构造方法里面首先要调用super关键字，super.在这里的意思是父类的，this.在这里的意思是这个对象的，或者说这个类的。

- ```java
  student类（子类）
  package textstudy;
  public class student extends people{
      String master;
     student(String master, int age, String name, int id){
          super(id,name,age);
          this.master = master;
      }
  }
  people类（父类）
  package textstudy;
  
  public class people {
      private int id;
      private String name;
      private int age;
      people(int id, String name, int age){
          this.id = id;
          this.name = name;
          this.age = age;
      }
      int getId(){
          return id;
      }
      String getName(){
          return name;
      }
      int getAge(){
          return age;
      }
  }
  ```

  

- 关键字：instanceof：使用方法，对象名+instanceof+类名，通常在if括号里面使用，如果对象属于该类，会返回ture，否则会返回false。该类名可以是父类，就是大范围判断也行，具体到某一类也行：

 

[父类+对象名=new 父类（）；这对象是是属于父类的：]()

[父类+对象名=new 子类（）；这对象是属于子类的，因为new XXX（）；是啥就是属于啥类；也就是说，引用对象是子类，即使前面是写父类他也是属于子类：]()

[最顶层的类是object，是所有类的祖宗，main类也是它的子类，所以java本身了就继承了一些方法，如：object类下面是people类，在下面是student类和teacher类;所以有一些关键方法：]()

如equals，：对象1equals对象2，如果两个对象属性相同，返回ture，否则false；

==继承，也就是说子承父业，父类所拥有的方法，子类能拥有，子类还能加一些特殊的方法进去，父类是用不了的。==

==子类和父类均有同名的变量的时候，优先会考虑使用同类的变量，如果子类想使用父类的同名变量，在使用变量前加上super.这个关键字。==

==如果是不同名字的变量，直接用就行。==

- ### 方法重载

#### 方法重载：同类下，定义同名的方法，但是形参数目或者是类型不一样（必要条件），并且返回类型可以是一样或者不一样也行。要用哪个方法，关键看对应的形参的对应方法。

- ### 方法重写

#### 方法重写：不同类下，使用方法名字，形参一模一样的方法，但是方法体不一样。在该类下，使用该名称下的方法体，如果要使用父类的，请加上super.。

## 注意事项：

- 1.子类的访问权限不得低于父类，起码要等于父类。

- 2.方法返回类型前面加上final，表示最终的方法，不支持子类重写，但是可以子类重载。

- 3.final在类名前，表示不可以继承了，也就是没有子类了。

- 4.如果父类权限是private，也就是子类连访问的资格都没有，所以子类出现了同名的方法，这是自定义方法，不是方法重写；

- 5.在main类写方法，要用到静态方法，因为它是属于Main类的，运用时，因为在本类，所以不用Main.XXX而是直接用XXX，但如果在其他的类使用，要类名.XXX（）；

  

  ## 抽象类：

  - 抽象类：在类的class前面加上[abstract]()，表示该类是抽象类；

  抽象类无法实列，也就是说，假设people类是抽象类，没有办法people p1=new people（）；只能是子类实例，也就是说可以创建对象是抽象类的子类，如student p1=new student（）；但是后面可以是匿名内部类，相当于是无名字的子类

   

  - 抽象方法：在返回类型加上abstract，表示该方法是抽象方法;[抽象类可以不具有抽象方法；]()

  - 抽象方法与方法重写的关系：

    抽象方法说白了就是父类写方法声明，没有方法体，子类写方法体，属于之类的方法体，也就是方法重写，跟不是抽象方法重写的区别就是，抽象方法，子类必须要重写，而父类非抽象方法，子类可以用父类的方法，可以选择是否方法重写，重写就是属于自己的子类的方法，不重写就是父类的方法，但由于继承，父类的方法可以使用；抽象方法父类访问权限跟方法重写一样，不能是private，因为子类连访问权限都没有，如何方法重写；

  - ## 接口：

  - 接口：interface代替class，某一功能或者行为的抽象（只能包含方法的定义，在c语言叫做函数的声明），说白了类似于抽象方法的定义，但是不像抽象类那样，范围广。只是某些方法的定义写在一起，注意里面只有方法的定义，没有方法体，除非不是打算抽象方法，在方法定义前加上default，变成普通的方法，当然也可以方法重写，方法重载。

    再通俗点说，将多个方法定义写成一个文件（抽象方法可以省去public和abstract）类似于c语言的自定义头文件，头文件将函数声明都写在了里面。

  - implement表示接口的引入。一个类可以引用多个接口，每个接口名称要用逗号隔开。这和类不同，可以有多个接口，但是一个类只可以有一个父类。
  
  - 抽象方法，在子类中要方法重写，在接口里面的方法通常是抽象方法，除非在方法定义前面加上default或者变成静态变量或者静态方法。所以引用的接口里面的方法通常要在子类中方法重写。[接口就相当于抽象方法定义的集合]()。
  
  - 接口是可以继承的，并且接口可以多继承，可以有多个父接口。子接口的继承，子接口要方法重写，并且方法重写要调用父接口的方法，像类的继承那样，要使用super关键字，但是super前面还是写上父类接口名称。（这个不像类的继承）
  
  - 接口里面可以含有非抽象方法：在方法定义前面加上default关键字，然后写上方法体在接口里面，这就使得该方法不是抽象方法，也就是像普通方法一样，在类中可以选择方法是否重写，不重写会使用默认的方法。
  
  - 接口不能存在成员变量和成员方法，但是可以存在静态变量和方法。
  
  - 在接口中，因为只存在常量，而不存在变量，所以定义的常量只能一次赋值多次使用。也正是如此，所以定义常量的时候的关键字[（public static final)是可以省略的自动转换成final型]()。比如：在接口里面，int a=10；实际上是public static int a=10;自动是静态变量，还可以在int前面加上final，不过一旦加上final就意味着，a一旦有了值，就不能再赋值或者改变其值。
  
    > 常量的值是不可以修改的，只能进行一次赋值。
    >
    > <img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202303182351456.png" alt="image-20230313203529528" style="zoom: 67%;" />
  
  
  
  - 静态方法，在接口中不想用抽象方法之一就是静态方法，和普通类一样，在返回类型前面加上public static，[不能省略]()，不像刚才那变量，会自动转换成静态变量，[如果省略会变成抽象方法，不能带方法体，静态方法要带方法体，]()
  
    同样，无论是静态变量或者静态方法，都是属于接口。所以使用时，像类的静态方法或者静态变量那样来使用就行。接口名称.方法/变量
  
  
  - 接口也是可以继承的，接口的继承类似于类的继承，但又有点区别，某个接口可以有多个父接口，但是某个类只可以有一个父类，子接口可以拥有父接口的全部方法，除非父接口的方法是private。
  
  - 父类和接口都是有相同的方法（方法名称相同，形参相同，定义一模一样），若**子类没有重写**该方法的话，则**默认会优先调用父类**中的方法，也就是说子类会优先使用父类的方法，如果子类重写了方法，会使用子类重写的，如果重写想使用父类的，方法名前加上super.，如果想使用接口的，方法名前面加上接口名.super.
  - 接口存在非抽象方法有两个：1·静态方法，2·default         ![image-20230313204313268](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303182351400.png)
  
  - ## 枚举类 
  
  枚举类：新建-》枚举；public enum 枚举名{直接写内容，逗号隔开}；
  
  使用，直接枚举名.内容，注意数据类型是要用枚举名的，枚举名 a=枚举名.内容，该内容是枚举里面的。
  
  基本类型包装类，将基本类型包装成类，用起来像普通的类一样，而且自带的方法，jdk封装了某些方法。最顶层的是object类，下面有number子类，character子类还有xxx类，number类下面又有interger类…。
  
  基本类型包装类的作用，1·可以计算非常大的数据，保存非常大的数据，如big interger类，也有big decimal类可以计算非常高精度的数。
  
  2·运用某些封装的方法，可以将字符串转换成int型，或者16进制或者8进制进行解码，其基本的思想是jdk的object类中的number类里面封装了好多方法。
  
  疑问：自动装箱和自动拆箱
  
  - ## 数组：
  
  数组：存相同数据类型的集合；
  
  定义：数据类型【】数组名称；（方括号表示数组）数组定义，中括号不带大小，new的时候才带大小；
  
  数组类型，数组它本身也是类，但是不可见，底层是c++。因为也是要new关键字，也就是说数组是以对象类型形式存在的。
  
  ==Int [【】]()array=new int【10】==；访问和c一样，都是数组名【下标】；
  
  因为数组其实是一个类，所以里面封装了好多方法，所以我们可以使用这些方法，如array.length可以查看数组的长度…
  
  数组for循环有简易版，[for（int a：数组名）]()（这是int类型的数组）a的值是一直变化的，从数组[0]到数组的最后一个数的值，，基本类型数组和引用类型数组不一样，基本类型数组不会自动装箱，引用类型的数组会自动装箱。如引用类型String
  
  多维数组：加多一个方括号就是代表多维数组，和c语言一样，先行后列。
  
  注意事项，由于c语言的数组名时地址，而Java的数组名是指针类似的，指代某个对象，所以Java的数组是可以整行整列换掉，改变对象就行。C语言是要一个一个换掉的。
  
  - ## 可变长参数：
  
    可变长参数：形参可以是0-n个，形参形式如：void sum（String[…]()arc）;具有[三个省略号的，这样的叫做可变长参数]()。访问某个参数[，用到数组，其实这些参数都保存在arc这个数组上，arc【0】代表第一个参数，以此类推。]()
  
  - [一个方法，只能存在一个可变长参数]()，[并且可变长参数要放在最后的位置如：（String a，int…b）]()
  
  - main方法里面的string 【】args，这个数组默认情况下是一个空数组，是命令行参数。
  
  - ## 字符串
  
  String引用类型[，有两种，一种是双引号，一种是new一个新的对象]()
  
  区别：双引号的，如果两个对象内容相同，会判定为同一个对象，而new不会判断为同一个对象。所以为了区别内容是否相同，用equal方法去判断。
  
  - ### 字符串引用的方法
  
    - 1·contains（）判断字符串是否包含xxx字符
  
    - 2·split（）以…分割，将字符串分割
    - 3·substring（）将字符串裁剪，保留哪一段
    - 4·replace（）替换，将xxx与字符串的xxx替换
    - 5·reverse（）将字符串反转，最后一个字母变成第一个字母
    - 6·isempty（）判断是否为空字符串
    - 字符串和字符数组可以互相转化可以用tostring（）
  
  - ### Stringbuilder
  
    - 字符串可以相加，但是相加会创建一个新的对象，如果多个相加就非常损耗内存，所以又有了新的string类，stringbuilder类型，一样是一个类，用于字符串的处理，处理完在转回string类，字符串的拼接为主，方法名是append（添加进去的字符串）可以多个相加，并且仅仅组合一次，减少时间复杂度

 

- ## 正则表达式

- ##### 正则表达式：规定格式匹配字符串(来判断真假)。Matches方法来匹配

 

- ## 四大内部类（最重要匿名内部类）

- #### 成员内部类

  成员内部类，是在类里面，包含一个类，类似于套娃。使用时，要[类.类.方法名。.是代表的的意思]().

  注意，[成员内部类使用前，要先创建外部类的对象]()。

  [内部类可以访问外部类，但是外部类不能访问内部类]()

  内部和外部都有同名变量或方法时，使用this关键字或者super关键字，

  如；this.name 当前类的名字

    Text.this.name表示外部类text的名字（.翻译成的）

    Super.name内部类父类的名字

    Text.super.name外部类父类的名字

  - ###  静态内部类：

    - 静态内部类：不用依赖外部类的对象，因为属于类的，所以[外部类名.内部类名.xxx]()，也就是说，[不用先创建外类对象，直接用类名]()

    -  内部类是静态时，使用内部类方法，但没有使用外部类方法，外部类是不会创建对象的，因为内部类是属于类的，而不是属于对象，没有用到外部类的一点一滴，也就是没有用到外部类的对象和方法。明确使用外部类，才会初始化。

  - ### 局部内部类

    #####    -局部内部类：在某个方法里面，生存周期懂得都懂。跟其他内部类类似。

  - ## 匿名内部类：                            

    

    匿名内部类：可以说是[临时的子类]()，作用：创建抽象类或者接口时，不能新建实例对象也就是说，[==在接口中或者抽象类中，直接new一个对象不行，需要用到子类，可以采取匿名内部类的方法，去实现。==]()

    <img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202303182351195.png" alt="image-20230313211418048" style="zoom: 67%;" />这个是错误的，因为people是抽象类，不能直接new一个对象

    匿名内部类实现抽象类，抽象类一定要有抽象方法：

    - New 抽象类/接口/普通类（几乎不用）{方法体}；其实本质上也是子类，所以可以用父类的办法，也就是说，可以在继承上添加方法，变量等等，[只是匿名内部类就是一种没有名字的类，]()

    - 运用时，类名.方法名（）；这里的类名是对象名

    ```java
    package textstudy;
    
    public class Main {
        public static void main(String[] args) {
            People p1 = new People() {
                @Override
                public void sayHello() {
                    System.out.println("Hello from anonymous class!");
                }
            };//创建了一个匿名内部类
            
            p1.sayHello();//运用时是对象名.方法名，就像普通类对象就行了，因为匿名内部类是相当于没有名字的子类
        }
    }
    
    interface People {
        void sayHello();
    }
    
    ```

    - 如果是接口的话，继承接口，[可以实现接口和方法重写]()。就像子类那样

    - ```Java
      interface Animal {
          void makeSound();
      }
      
      class Main {
          public static void main(String[] args) {
              Animal dog = new Animal() {//匿名内部类
                  @Override
                  public void makeSound() {
                      System.out.println("Woof!");
                  }
              };
      
              dog.makeSound(); // 输出 "Woof!"
          }
      }
      ```

  - ## Lambda表达式：

-  Lambda表达式：如果匿名内部类是接口，并且接口里面只有一种待实现的抽象方法，可以简写成lanbda表达式；

- 格式：接口 名称=（参数（在方法定义的参数，如果无参，直接空着））->{方法体}；

- #### 三大省略：

  - ##### 1.如果参数是一个，可以省略数据类型，如int a直接写成a，小括号都可以省略。

  - ##### 2.如果方法体内容只有一句，可以省略花括号；

  - ##### 3.如果方法体只有一句返回语句，也就是一句并且是返回语句，可以连return都不要。

- 注意事项：如果一个变量是外部变量，在匿名内部类中想使用，必须是final的变量，也就是说不能再次赋值的变量。

- 方法引用：lanbda表达式引用别的方法作为自己的方法。等号右边原本是方法重写，其实还可以[对象：：方法]()，==来引用某个对象的方法，或者类名：：方法来引用别的类的静态方法（因为属于类的方法）==。

- 说白了就是lanbda表达式右边还可以再简化，运用时还是像之前那样，[接口名.方法，因为lanbda表达式仅支持某个接口，并且该接口只能有一个抽象方法。]()

  - ###   异常：

    -  异常：异常类型有[两种]()，运行时异常（运行的时候才发现的）和编译时异常（编译不通过）。

    - 抛出异常：通过写if语句判断是否符合异常条件，如果符合异常条件，则进行输出异常，输出内容自定义，现在仅支持运行异常，如被除数等于0时输出异常。

    - #### 如何处理异常：

    - 运行时异常处理：

      - 如何实现：通过throw 关键字，throw 对象;该对象是属于rumtime exception(“xxx”);

      - 如一般直接throw rumtime exception(“被除数为0”).试例如下：

      - ```java
        //在Java中，可以通过创建一个继承自RuntimeException的子类来定义自己的运行时异常。以下是一个示例，该异常表示被除数不能等于零：
        public class DivideByZeroException extends RuntimeException {
            public DivideByZeroException() {
                super("被除数不能等于零");
            }
        }
        //在使用时，可以在进行除法计算时抛出这个异常。例如：
        
        public class Calculator {
            public static int divide(int dividend, int divisor) {
                if (divisor == 0) {
                    throw new DivideByZeroException();
                }
                return dividend / divisor;
            }
        }
        //如果尝试将除数设置为零，则会抛出DivideByZeroException异常。
        ```

        

      - 编译时异常处理：
        - 第一个处理办法：添加方法签名（在方法定义后面，形参括号前面。idea右键会有的）
        - 第二个处理办法;使用 try{ }catch{ }进行捕捉异常，父类捕捉了子类就不用捕捉了，要是父类使用方法签名，子类要么继续方法签名，要么捕捉。
        - try{ }catch{ }finally{ }这句语句不仅会捕捉异常，当有异常的时候，finally代码块里面的语句依然会运行，不受干扰

      

  - ### 泛型

  -  泛型类：在类型选择中，不知道要哪一种，类型定义时，不知道要哪一种类型，如String和int来存成绩，可以切换，后期来改类型，那就用泛型，因为泛型可以使用时才明确要哪一种类型。

  - 泛型使用，在类中使用泛型，要在类名后加上钻石运算符，“<>”符号里面要填充一个字母（任意的），也可以用?（问号通配符来代替）。

  - 在方法中使用泛型，多用于返回的类型不一样，在返回类型前面加上钻石运算符“<>”，同类一样，字母随意。形参也可以设置泛型，传参数时才明确。

  - 使用时，需要啥类型，使用对象要的类型写在钻石运算符里面，[也可以选择不写，因为有自动装箱功能]()，[但是钻石运算符不可省略。]()

  - 注意事项：
    - 1·只能在自己的类中使用，自己类是有钻石运算符，自己类才能用。
    - 2·不能用于静态变量或者静态方法。3·泛型不支持基本数据类型，用于引用类型，如int要写成interger类，数组是引用类型。所以数组是可以使用钻石运算符

   

  - 泛型设定边界：目的是设定形参的类型的范围：通过extends关键字和super关键字来设定上下界限；extends设定上界，super设定下界。
    - object类下面有number类和string类还有…number类下面有interge类，double类…



### -工具类：

-  Random类是随机数类，里面的某些方法是要对象的，所以要先创建一个对象再使用。

  - #### 数组工具类

  - Array类，里面好多方法是属于类的。主要的有：

  - ​	1·转化为字符串来打印，toString 多维数组用deeptostring.

  - ​    2·sort方法可以数组快速排序

  - ​	3·fill可以快速填充数组，让整个数组都是xxx

  - ​	4·copyof可以复制一个数组

  - ​	5·binarysearch是二分查找，可以快速查找下标，前提该数组是有序的。

  - ​	6·equal可以判断数组内容是否相等



- ### 函数式接口（通常是用lambda表达式）

  ### **一. Functional**

  ```java
  public class FunctionDemo {
  
   
  
    public static void main(String[] args) {
  
  ​    String result = testFuntion("kobe", name -> name + " bryant");
  
  ​    System.out.println(result);
  
    }
  
   
  
    /**
  
     \* 函数型接口，有输入，有输出
  
     *
  
     \* @param name
  
     \* @param function
  
     \* @return
  
     */
  
    public static String testFuntion(String name, Function<String, String> function) {
  
  ​    return function.apply(name);
  
    }
  
  }
  ```

  ### **二. Consumer**

  ```java
  public class ConsumerDemo {
    public static void main(String[] args) {
  ​    testConsumer(2, x -> System.out.println("传入的数字为:" + x));
    }
    /**
  
     \* 消费型接口，有输入，但是没返回值
  
     \* 
  
     \* @param num
  
     \* @param consumer
  
     \* @return
  
     */
    private static void testConsumer(int num, Consumer<Integer> consumer) {
  ​    consumer.accept(num);
    }
  }
  ```
  
  ### **三. Supplier**
  
  ```java
  public class SupplierDemo {
    public static void main(String[] args) {
  ​    String result = testSupplier(() -> "hello man!");
  ​    System.out.println(result);
    }
    /**
  
     \* 供给型接口，无输入，有输出
  
     \* 
  
     \* @param supplier
  
     \* @return
  
     */
  
    public static String testSupplier(Supplier<String> supplier) {
  ​    return supplier.get();
    }
  }
  ```
  
  ### **四. Predicate**
  
  ```java
  public class PredicateDemo {
    public static void main(String[] args) {
  ​    Boolean flag = testPredicate("b", str -> str.equals("a"));
  ​    System.out.println(flag);
    }
    /**
  
     \* 断言型接口，有输出，输出为Boolean
  
     *
  
     \* @param str
  
     \* @param predicate
  
     \* @return
  
     */
  
    public static Boolean testPredicate(String str, Predicate<String> predicate) {
  ​    return predicate.test(str);
    }
  }
  ```

  [作用意义：方法体是自己写的，类似于封装某些方法更方便]()



- ## 集合类

  它是继承collextion类的，这个类里面封装了好多方法，与数组的区别，1-共同点：都是容器，大小固定；[不同点：数组可以是基本数据类型，而集合类只能是引用类型]()，因为这些集合类接口都是[使用了泛型]()

  - ### 集合类的通用方法：

  - 1·toArray();转换成数组，返回一个数组，这个数组是副本数组，并不是传入的数组，是复制了一个数组，返回复制的数组

  - 2·add() 加入元素

  - contains() 判断是否数组是否包含形参的元素，

  - isEnty()判断数组是否为空

  - remove()移除指定的元素，如果指定的元素在列表中或者链表中是多个的，则会移除第一个指定元素

  - addall()将一个集合类中所有的元素，添加到当前这个集合中

  - removeall()移除所有的元素

  - clear ()清空当前集合类的所有元素

  - equals()判断对象是否相等，判断的是地址

  - 

  - ### ArrayList类 ： 这是一个线性表的顺序表的类，继承于集合类

    - 1·是一个有序的集合，插入元素默认是从尾部插入的，当然也可以选择是在位置插入
    - 2·每一个元素都有属于自己的下标位置
    - 3·列表中允许有重复元素，如：“AAA”与“AAA”是同一个元素，但是可以全纪录下来，就是跟数组一样

    相比集合类加入的方法（与链表相同）：

    - sort()集合排序
    - set()改变指定元素的值
    - removeif(条件)，如果条件满足，则会移除满足条件的元素

  - ### Linkedlist类：这个是链表类，顺序表该有的方法他都有

  - 数据结构可知，这个链表底层的实现和线性表是由差别的

  - 在类里面新建了一个静态类，所以这个静态类是属于外部类的，有空看看源码吧！

  - ## 迭代器：—可以将多种不同的集合类遍历方式进行统一

  - 提出疑问：链表为什么也支持for循环的增强版；anwser：链表虽然不是数组，但是链表有迭代器
  
  - - 迭代器工作原理：底层实现：迭代器中有一个hasnext（）的方法，指向当前的下一个元素，如果下一个元素是NULL，则会退出，类似于链表的头指针。迭代器也是分开有数组和链式，但功能是一样的。所以链表也是支持for循环增强版
    - 
  
  - 注意：迭代器只能用一次，一次之后就会消失，要再次使用要重新申请，
  
    - 2	只要实现了迭代器接口就能用for循环的增强版：
  
    - ```
       LinkedList li=new LinkedList<String>();//创建列表
              li.add("***8888");//链表加入元素
              li.add("uuyuih");
                                               //设置一个对象，是li的迭代器
              for (Iterator it = li.iterator();; it.hasNext(); ) {
                  String i = it.next().toString();
                  System.out.println(i);
              }
      ```
  
      
  
  - ```java
    LinkedList<String> li = new LinkedList<String>();
    li.add("***8888");
    li.add("uuyuih");
    Iterator<String> iter = li.iterator();
    while (iter.hasNext()) {
        String s = iter.next();
        System.out.println(s);
    }
    
    ```
  
    - ## Set集合与map映射
  
    - ### set：
  
      - 与Link与ArrayList区别：1set集合不能出现重复的元素，不能像顺序表那样
  
        - ​								2·不支持随机访问，也不支持下标访问；因为他的底层是哈希表，不支持下标访问，哈希表是通过哈希函数计算存在那里的，我们也不知道
  
        - hashset与Treeset集合类是会自动排序的，一传入参数，就会排序的。但是Linkhashset不会
  
        - ```java
          
          public class Text {
              public static void main(String[] args) {
                  HashSet<String> list_set=new HashSet<String>();
                  list_set.add("AAA");
                  list_set.add("DDD");
                  list_set.add("BBB");
                  System.out.println(list_set.toString());
              }
          }
          输出AAABBBDDD
              
              public class Text {
              public static void main(String[] args) {
                  LinkedHashSet<String> list_set=new LinkedHashSet<>();
                  list_set.add("AAA");
                  list_set.add("DDD");
                  list_set.add("BBB");
                  System.out.println(list_set.toString());
              }
          }
          输出AAADDDBBB
              
                 public class Text {
              public static void main(String[] args) {
                    HashSet<String> list_set=new LinkedHashSet<>();//验证他真正的类是后面的为标准//个人想法，但是可以这样认为
                  list_set.add("AAA");
                  list_set.add("DDD");
                  list_set.add("BBB");
                  System.out.println(list_set.toString());
              }
          }
          输出AAADDDBBB
          
          ```
          
      



- ### Map映射;

- 映射很熟悉，就像函数那样，一一对应，在这里也是如此，键值对应，一个键只能一个值，但是一个值可以多个健，就像x只能有一个y，y可以有多个x。但是日常使用都是一一对应

- Map<k,v>:k是代表键，v是代表值

- 方法：get()通过键来得到对应的值

- containsvalue()查看当前这个集合类的对象，是否含有某个value

- containskey()查看当前这个集合类的对象，是否含有某个key

- put()存放键值

- remove()移除元素

- 

### Stream流：

这个是jdk8的方法，主要是通过一些设定条件，使得集合类的对象删减某些元素

方法：

- sorted()排序，默认从小到大，可以在括号里面写个lambda表达式变成从大到小
- distinct()：删除重复的元素
- filter(收集条件)
- collect()前三个相当于摄制一部机器，这里才会执行，启动机器。开始收集。如我设定了要删除重复元素，这里才会返回删除后的新集合

```java

public class Text {
    public static void main(String[] args) {
       ArrayList<Integer> list=new ArrayList<>();
       list.add(7);
       list.add(6);
       list.add(9);
       list.add(76);
       list.add(88);
       list.add(35);
       list.add(7);
        list=list.stream().distinct().collect(Collectors.toCollection(ArrayList::new));
        System.out.println(list.toString());
    }
}

输出7 6 9 76 88 35重复的7删除了
```

![image-20230315134952237](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303182353594.png)



- ## io流

​	JDK提供了一套用于IO操作的框架，为了方便我们开发者使用，就定义了一个像水流一样，根据流的传输方向和读取单位，分为字节流InputStream和OutputStream以及字符流Reader和Writer的IO框架，当然，这里的Stream并不是前面集合框架认识的Stream，这里的流指的是数据流，[通过流，我们就可以一直从流中读取数据，直到读取到尽头]()，（缓冲流一个道理）或是不断向其中写入数据，直到我们写入完成，而这类IO就是我们所说的BIO，

==字节流一次读取一个字节，也就是一个`byte`的大小，而字符流顾名思义，就是一次读取一个字符，也就是一个`char`的大小==

==（在读取纯文本文件的时候更加适合），有关这两种流，会在后面详细介绍，这个章节我们需要学习16个关键的流。==

- ### 文件流

- ```java
   public static void main(String[] args) {    try {   //注意，IO相关操作会有很多影响因素，有可能出现异常，所以需要明确进行处理        FileInputStream inputStream = new FileInputStream("路径");        //路径支持相对路径和绝对路径    } catch (FileNotFoundException e) {        e.printStackTrace();    }}
  ```

  

在使用完成一个流之后，必须关闭这个流来完成对资源的释放，否则资源会被一直占用：

```java
public static void main(String[] args) {
    FileInputStream inputStream = null;    //定义可以先放在try外部
    try {
        inputStream = new FileInputStream("路径");
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } finally {
        try {    //建议在finally中进行，因为关闭流是任何情况都必须要执行的！
            if(inputStream != null) inputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

虽然这样的写法才是最保险的，但是显得过于繁琐了，尤其是finally中再次嵌套了一个try-catch块，因此在JDK1.7新增了try-with-resource语法，用于简化这样的写法（本质上还是和这样的操作一致，只是换了个写法）

```java
public static void main(String[] args) {

    //注意，这种语法只支持实现了AutoCloseable接口的类！
    try(FileInputStream inputStream = new FileInputStream("路径")) {   //直接在try()中定义要在完成之后释放的资源

    } catch (IOException e) {   //这里变成IOException是因为调用close()可能会出现，而FileNotFoundException是继承自IOException的
        e.printStackTrace();
    }
    //无需再编写finally语句块，因为在最后自动帮我们调用了close()
}
```

反编译是一样的：通常使用上面这个，因为可以自动关闭文件，防止文件被占用导致其他对象读取写入失败：而且尽量使用绝对路径，也就是完全路径，用 \\ \或者/隔开每个目录，因为\表示转义字符

- ### 文件字节输入流：

- 现在我们拿到了文件的输入流，那么怎么才能读取文件里面的内容呢？我们可以使用`read`方法：

  ```java
  public static void main(String[] args) {
      //test.txt：a
      try(FileInputStream inputStream = new FileInputStream("test.txt")) {
          //使用read()方法进行字符读取
          System.out.println((char) inputStream.read());  //读取一个字节的数据（英文字母只占1字节，中文占2字节）
          System.out.println(inputStream.read());   //唯一一个字节的内容已经读完了，再次读取返回-1表示没有内容了
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  使用read可以直接读取一个字节的数据，注意，流的内容是有限的，读取一个少一个。我们如果想一次性全部读取的话，可以直接使用一个while循环来完成：

  ```java
  public static void main(String[] args) {
      //test.txt：abcd
      try(FileInputStream inputStream = new FileInputStream("test.txt")) {
          int tmp;
          while ((tmp = inputStream.read()) != -1){   //通过while循环来一次性读完内容
              System.out.println((char)tmp);
          }
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  使用`available`方法能查看当前可读的剩余字节数量（注意：并不一定真实的数据量就是这么多，尤其是在网络I/O操作时，这个方法只能进行一个预估也可以说是暂时能一次性可以读取的数量，当然在磁盘IO下，一般情况都是真实的数据量）

  ```java
  try(FileInputStream inputStream = new FileInputStream("test.txt")) {
      System.out.println(inputStream.available());  //查看剩余数量
  }catch (IOException e){
      e.printStackTrace();
  }
  ```

  当然，一个一个读取效率太低了，那能否一次性全部读取呢？我们可以预置一个合适容量的byte[]数组来存放：

  ```java
  public static void main(String[] args) {
      //test.txt：abcd
      try(FileInputStream inputStream = new FileInputStream("test.txt")) {
          byte[] bytes = new byte[inputStream.available()];   //我们可以提前准备好合适容量的byte数组来存放
          System.out.println(inputStream.read(bytes));   //一次性读取全部内容（返回值是读取的字节数）
          System.out.println(new String(bytes));   //通过String(byte[])构造方法得到字符串
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  也可以控制要读取数量：

  ```java
  System.out.println(inputStream.read(bytes, 1, 2));   //第二个参数是从给定数组的哪个位置开始放入内容，第三个参数是读取流中的字节数
  ```

  **注意**：一次性读取同单个读取一样，当没有任何数据可读时，依然会返回-1

  通过`skip()`方法可以跳过指定数量的字节：

  ```java
  public static void main(String[] args) {
      //test.txt：abcd
      try(FileInputStream inputStream = new FileInputStream("test.txt")) {
          System.out.println(inputStream.skip(1));
          System.out.println((char) inputStream.read());   //跳过了一个字节
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  注意：FileInputStream是不支持`reset()`的，虽然有这个方法，但是这里先不提及。

  既然有输入流，那么文件输出流也是必不可少的：

  ```java
  public static void main(String[] args) {
      //输出流也需要在最后调用close()方法，并且同样支持try-with-resource
      try(FileOutputStream outputStream = new FileOutputStream("output.txt")) {
          //注意：若此文件不存在，会直接创建这个文件！
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  输出流没有`read()`操作而是`write()`操作，使用方法同输入流一样，只不过现在的方向变为我们向文件里写入内容：

  ```java
  public static void main(String[] args) {
      try(FileOutputStream outputStream = new FileOutputStream("output.txt")) {
          outputStream.write('c');   //同read一样，可以直接写入内容
        	outputStream.write("lbwnb".getBytes());   //也可以直接写入byte[]
        	outputStream.write("lbwnb".getBytes(), 0, 1);  //同上输入流
        	outputStream.flush();  //建议在最后执行一次刷新操作（强制写入）来保证数据正确写入到硬盘文件中
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  那么如果是我只想在文件尾部进行追加写入数据呢？我们可以调用另一个构造方法来实现：

  ```java
  public static void main(String[] args) {
      try(FileOutputStream outputStream = new FileOutputStream("output.txt", true)) {  //true表示开启追加模式
          outputStream.write("lb".getBytes());   //现在只会进行追加写入，而不是直接替换原文件内容
          outputStream.flush();
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  利用输入流和输出流，就可以轻松实现文件的拷贝了：

  ```java
  public static void main(String[] args) {
      try(FileOutputStream outputStream = new FileOutputStream("output.txt");
          FileInputStream inputStream = new FileInputStream("test.txt")) {   //可以写入多个
          byte[] bytes = new byte[10];    //使用长度为10的byte[]做传输媒介
          int tmp;   //存储本地读取字节数
          while ((tmp = inputStream.read(bytes)) != -1){   //直到读取完成为止
              outputStream.write(bytes, 0, tmp);    //写入对应长度的数据到输出流
          }
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  

  

  例如视频复制：
  
  ```java
  package Textonlin;
  
  import java.io.FileInputStream;
  import java.io.FileNotFoundException;
  import java.io.FileOutputStream;
  import java.io.IOException;
  import java.util.*;
  import java.util.function.Consumer;
  import java.util.stream.Collectors;
  
  public class Text
  {
      public static void main(String[] args) throws IOException
      {
  
         try(FileInputStream f1=new FileInputStream("L:\\源文件\\java\\1.mov")){
             FileOutputStream f2=new FileOutputStream("L:\\源文件\\java\\3.mov");
             int temp;
             byte []b=new byte[10240];
             while((temp = f1.read(b)) != -1){
                 f2.write(b, 0, temp);
  
             }
         }catch (IOException e){//为什么e要在这里创建，下面即是答案：因为出现异常才会创建e
  
             e.printStackTrace();
         }
  
  
      }
  }
  
  Anwser：在第二个例子中，代码正确地使用了try-catch块来捕获IOException异常，并将该异常对象赋值给变量e。这使得程序能够正确地捕获并处理异常，以避免程序崩溃或产生未处理的异常。
  因此，第二个例子是更好的代码实现。
  在第二个式子中，如果没有异常，e会创建吗？
  在第二个示例代码中，如果没有异常抛出，那么 IOException e 变量实际上不会被创建。这是因为变量只会在 catch 块中初始化并赋值，只有当 try 块中的代码抛出了对应的异常才会执行 catch 块。
  如果 try 块中的代码没有抛出异常，那么程序将会顺序执行下去，直到 try-catch 结构之外。在这种情况下，变量 IOException e 不会被创建或初始化。
  ```

  
  
  ### 文件字符流
  
  字符流不同于字节，字符流是以一个具体的字符进行读取，因此它只适合读纯文本的文件，如果是其他类型的文件不适用：
  
  ```java
  public static void main(String[] args) {
      try(FileReader reader = new FileReader("test.txt")){
        	reader.skip(1);   //现在跳过的是一个字符
          System.out.println((char) reader.read());   //现在是按字符进行读取，而不是字节，因此可以直接读取到中文字符
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```
  
  同理，字符流只支持`char[]`类型作为存储：
  
  ```java
  public static void main(String[] args) {
      try(FileReader reader = new FileReader("test.txt")){
          char[] str = new char[10];
          reader.read(str);
          System.out.println(str);   //直接读取到char[]中
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```
  
  既然有了Reader肯定也有Writer：
  
  ```java
  public static void main(String[] args) {
      try(FileWriter writer = new FileWriter("output.txt")){
        	writer.getEncoding();   //支持获取编码（不同的文本文件可能会有不同的编码类型）
         writer.write('牛');
         writer.append('牛');   //其实功能和write一样
        	writer.flush();   //刷新
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```
  
  我们发现不仅有`write()`方法，还有一个`append()`方法，但是实际上他们效果是一样的，看源码：
  
  ```java
  public Writer append(char c) throws IOException {
      write(c);
      return this;
  }
  ```
  
  append支持像StringBuilder那样的链式调用，返回的是Writer对象本身。





### 文件流：

```java
File file =new File("路径");
```

这个类的方法：

- exists（）判断文件是否存在
- getasoluepath()获取文件的相对路径
- creatnewfile()创建新的文件
- file.mkdir()新建文件夹（和Linux系统一样）

### 缓冲流：

- 缓冲流就相当于比以前多了两个东西，就像水供应给用户，经过自来水厂净化。自来水厂就相当于一个缓冲流

- 缓冲流入区：提前将文件读取一部分进去缓冲区，要用的时候，可以快速读取，读取数据多于缓冲区的情况下，再从文件输入流中读取。缓冲（Buffered)

- 

  要创建一个缓冲字节流，只需要将原本的流作为构造参数传入BufferedInputStream即可：

  ```java
  public static void main(String[] args) {
      try (BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream("test.txt"))){   //传入FileInputStream
          System.out.println((char) bufferedInputStream.read());   //操作和原来的流是一样的
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  实际上进行I/O操作的并不是BufferedInputStream，而是我们传入的FileInputStream，而BufferedInputStream虽然有着同样的方法，但是进行了一些额外的处理然后再调用FileInputStream的同名方法，这样的写法称为`装饰者模式`，我们会在设计模式篇中详细介绍。我们可以来观察一下它的`close`方法源码：

  ```java
  public void close() throws IOException {
      byte[] buffer;
      while ( (buffer = buf) != null) {
          if (bufUpdater.compareAndSet(this, buffer, null)) {  //CAS无锁算法，并发会用到，暂时不需要了解
              InputStream input = in;
              in = null;
              if (input != null)
                  input.close();
              return;
          }
          // Else retry in case a new buf was CASed in fill()
      }
  }
  ```

  实际上这种模式是父类FilterInputStream提供的规范，后面我们还会讲到更多FilterInputStream的子类。

  我们可以发现在BufferedInputStream中还存在一个专门用于缓存的数组：

  ```java
  /**
   * The internal buffer array where the data is stored. When necessary,
   * it may be replaced by another array of
   * a different size.
   */
  protected volatile byte buf[];
  ```

  I/O操作一般不能重复读取内容（比如键盘发送的信号，主机接收了就没了），而缓冲流提供了缓冲机制，一部分内容可以被暂时保存，BufferedInputStream支持`reset()`和`mark()`操作，首先我们来看看`mark()`方法的介绍：

  ```java
  /**
   * Marks the current position in this input stream. A subsequent
   * call to the <code>reset</code> method repositions this stream at
   * the last marked position so that subsequent reads re-read the same bytes.
   * <p>
   * The <code>readlimit</code> argument tells this input stream to
   * allow that many bytes to be read before the mark position gets
   * invalidated.
   * <p>
   * This method simply performs <code>in.mark(readlimit)</code>.
   *
   * @param   readlimit   the maximum limit of bytes that can be read before
   *                      the mark position becomes invalid.
   * @see     java.io.FilterInputStream#in
   * @see     java.io.FilterInputStream#reset()
   */
  public synchronized void mark(int readlimit) {
      in.mark(readlimit);
  }
  ```

  当调用`mark()`之后，输入流会以某种方式保留之后读取的`readlimit`数量的内容，当读取的内容数量超过`readlimit`则之后的内容不会被保留，当调用`reset()`之后，会使得当前的读取位置回到`mark()`调用时的位置。

  ```java
  public static void main(String[] args) {
      try (BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream("test.txt"))){
          bufferedInputStream.mark(1);   //只保留之后的1个字符
          System.out.println((char) bufferedInputStream.read());
          System.out.println((char) bufferedInputStream.read());
          bufferedInputStream.reset();   //回到mark时的位置
          System.out.println((char) bufferedInputStream.read());
          System.out.println((char) bufferedInputStream.read());
      }catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```

  我们发现虽然后面的部分没有保存，但是依然能够正常读取，其实`mark()`后保存的读取内容是取`readlimit`和BufferedInputStream类的缓冲区大小两者中的最大值，而并非完全由`readlimit`确定。因此我们限制一下缓冲区大小，再来观察一下结果：

  ```java
  public static void main(String[] args) {
      try (BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream("test.txt"), 1)){  //将缓冲区大小设置为1
          bufferedInputStream.mark(1);   //只保留之后的1个字符
          System.out.println((char) bufferedInputStream.read());
          System.out.println((char) bufferedInputStream.read());   //已经超过了readlimit，继续读取会导致mark失效
          bufferedInputStream.reset();   //mark已经失效，无法reset()
          System.out.println((char) bufferedInputStream.read());
          System.out.println((char) bufferedInputStream.read());
      }catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```

  了解完了BufferedInputStream之后，我们再来看看BufferedOutputStream，其实和BufferedInputStream原理差不多，只是反向操作：

  ```java
  public static void main(String[] args) {
      try (BufferedOutputStream outputStream = new BufferedOutputStream(new FileOutputStream("output.txt"))){
          outputStream.write("lbwnb".getBytes());
          outputStream.flush();
      }catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```

  操作和FileOutputStream一致，这里就不多做介绍了。

  既然有缓冲字节流，那么肯定也有缓冲字符流，缓冲字符流和缓冲字节流一样，也有一个专门的缓冲区，BufferedReader构造时需要传入一个Reader对象：

  ```java
  public static void main(String[] args) {
      try (BufferedReader reader = new BufferedReader(new FileReader("test.txt"))){
          System.out.println((char) reader.read());
      }catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```

  使用和reader也是一样的，内部也包含一个缓存数组：

  ```java
  private char cb[];
  ```

  相比Reader更方便的是，它支持按行读取：

  ```java
  public static void main(String[] args) {
      try (BufferedReader reader = new BufferedReader(new FileReader("test.txt"))){
          System.out.println(reader.readLine());   //按行读取
      }catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```

  读取后直接得到一个字符串，当然，它还能把每一行内容依次转换为集合类提到的Stream流：

  ```java
  public static void main(String[] args) {
      try (BufferedReader reader = new BufferedReader(new FileReader("test.txt"))){
          reader
                  .lines()
                  .limit(2)
                  .distinct()
                  .sorted()
                  .forEach(System.out::println);
      }catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```

  它同样也支持`mark()`和`reset()`操作：

  ```java
  public static void main(String[] args) {
      try (BufferedReader reader = new BufferedReader(new FileReader("test.txt"))){
          reader.mark(1);
          System.out.println((char) reader.read());
          reader.reset();
          System.out.println((char) reader.read());
      }catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```

  BufferedReader处理纯文本文件时就更加方便了，BufferedWriter在处理时也同样方便：

  ```java
  public static void main(String[] args) {
      try (BufferedWriter reader = new BufferedWriter(new FileWriter("output.txt"))){
          reader.newLine();   //使用newLine进行换行
          reader.write("汉堡做滴彳亍不彳亍");   //可以直接写入一个字符串
        	reader.flush();   //清空缓冲区
      }catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```

  合理使用缓冲流，可以大大提高我们程序的运行效率.

  ### 转换流

  - 转换流：可以将文件字节流和文件字符流进行相互转换。也是用类似缓冲流那样包装定义，也就是装饰者模式
  - 

例如使用场景：

有时会遇到这样一个很麻烦的问题：我这里读取的是一个字符串或是一个个字符，但是我只能往一个OutputStream里输出，但是OutputStream又只支持byte类型，如果要往里面写入内容，进行数据转换就会很麻烦，那么能否有更加简便的方式来做这样的事情呢？

```java
public static void main(String[] args) {
    try(OutputStreamWriter writer = new OutputStreamWriter(new FileOutputStream("test.txt"))){  //虽然给定的是FileOutputStream，但是现在支持以Writer的方式进行写入
        writer.write("lbwnb");   //以操作Writer的样子写入OutputStream
    }catch (IOException e){
        e.printStackTrace();
    }
}
```

同样的，我们现在只拿到了一个InputStream，但是我们希望能够按字符的方式读取，我们就可以使用InputStreamReader来帮助我们实现：

```java
public static void main(String[] args) {
    try(InputStreamReader reader = new InputStreamReader(new FileInputStream("test.txt"))){  //虽然给定的是FileInputStream，但是现在支持以Reader的方式进行读取
        System.out.println((char) reader.read());
    }catch (IOException e){
        e.printStackTrace();
    }
}
```

InputStreamReader和OutputStreamWriter本质也是Reader和Writer，因此可以直接放入BufferedReader来实现更加方便的操作。

- ### 打印流

- 我们一直都在使用打印流，如Syste.out这就是打印流的一种，只是这个是打印在控制台，我们还可以打印在文本中，也可以用这种方式进行输出；PrintStream：

- 使用方法和缓冲流，都是一样的，将文本字节/字符输出/入流进行包装，也是装饰者模式

- 打印到文本中是out.put方法；

- 

- 打印流其实我们从一开始就在使用了，比如`System.out`就是一个PrintStream，PrintStream也继承自FilterOutputStream类因此依然是装饰我们传入的输出流，但是它存在自动刷新机制，例如当向PrintStream流中写入一个字节数组后自动调用`flush()`方法。PrintStream也永远不会抛出异常，而是使用内部检查机制`checkError()`方法进行错误检查。最方便的是，它能够格式化任意的类型，将它们以字符串的形式写入到输出流。

  ```java
  public final static PrintStream out = null;
  ```

  可以看到`System.out`也是PrintStream，不过默认是向控制台打印，我们也可以让它向文件中打印：

  ```java
  public static void main(String[] args) {
      try(PrintStream stream = new PrintStream(new FileOutputStream("test.txt"))){
          stream.println("lbwnb");   //其实System.out就是一个PrintStream
      }catch (IOException e){
          e.printStackTrace();
      }
  }
  ```

  我们平时使用的`println`方法就是PrintStream中的方法，它会直接打印基本数据类型或是调用对象的`toString()`方法得到一个字符串，并将字符串转换为字符，放入缓冲区再经过转换流输出到给定的输出流上。

  ![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303182353464.png)

  因此实际上内部还包含这两个内容：

  ```java
  /**
   * Track both the text- and character-output streams, so that their buffers
   * can be flushed without flushing the entire stream.
   */
  private BufferedWriter textOut;
  private OutputStreamWriter charOut;
  ```

  与此相同的还有一个PrintWriter，不过他们的功能基本一致，PrintWriter的构造方法可以接受一个Writer作为参数，这里就不再做过多阐述了。

  而我们之前使用的Scanner，使用的是系统提供的输入流：

  ```java
  public static void main(String[] args) {
      Scanner scanner = new Scanner(System.in);   //系统输入流，默认是接收控制台输入
  }
  ```

  我们也可以使用Scanner来扫描其他的输入流：

  ```java
  public static void main(String[] args) throws FileNotFoundException {
      Scanner scanner = new Scanner(new FileInputStream("秘制小汉堡.txt"));  //将文件内容作为输入流进行扫描
  }
  ```

  相当于直接扫描文件中编写的内容，同样可以读取。

- ### 对象流

- 1·也是包装，装饰者模式

- 2·想使用对象流，传入的对象必须是要实现了序列化接口才可以。

- 既然基本数据类型能够读取和写入基本数据类型，那么能否将对象也支持呢？ObjectOutputStream不仅支持基本数据类型，通过对对象的序列化操作，以某种格式保存对象，来支持对象类型的IO，注意：它不是继承自FilterInputStream的。

  ```java
  public static void main(String[] args) {
      try (ObjectOutputStream outputStream = new ObjectOutputStream(new FileOutputStream("output.txt"));
           ObjectInputStream inputStream = new ObjectInputStream(new FileInputStream("output.txt"))){
          People people = new People("lbw");
          outputStream.writeObject(people);
        	outputStream.flush();
          people = (People) inputStream.readObject();
          System.out.println(people.name);
      }catch (IOException | ClassNotFoundException e) {
          e.printStackTrace();
      }
  }
  
  static class People implements Serializable{   //必须实现Serializable接口才能被序列化
      String name;
  
      public People(String name){
          this.name = name;
      }
  }
  ```

  在我们后续的操作中，有可能会使得这个类的一些结构发生变化，而原来保存的数据只适用于之前版本的这个类，因此我们需要一种方法来区分类的不同版本：

  ```java
  static class People implements Serializable{
      private static final long serialVersionUID = 123456;   //在序列化时，会被自动添加这个属性，它代表当前类的版本，我们也可以手动指定版本。
  
      String name;
  
      public People(String name){
          this.name = name;
      }
  }
  ```

  当发生版本不匹配时，会无法反序列化为对象：

  ```java
  java.io.InvalidClassException: com.test.Main$People; local class incompatible: stream classdesc serialVersionUID = 123456, local class serialVersionUID = 1234567
  	at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
  	at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:2003)
  	at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1850)
  	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2160)
  	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1667)
  	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:503)
  	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:461)
  	at com.test.Main.main(Main.java:27)
  ```

  如果我们不希望某些属性参与到序列化中进行保存，我们可以添加`transient`关键字：

  ```java
  public static void main(String[] args) {
      try (ObjectOutputStream outputStream = new ObjectOutputStream(new FileOutputStream("output.txt"));
           ObjectInputStream inputStream = new ObjectInputStream(new FileInputStream("output.txt"))){
          People people = new People("lbw");
          outputStream.writeObject(people);
          outputStream.flush();
          people = (People) inputStream.readObject();
          System.out.println(people.name);  //虽然能得到对象，但是name属性并没有保存，因此为null
      }catch (IOException | ClassNotFoundException e) {
          e.printStackTrace();
      }
  }
  
  static class People implements Serializable{
      private static final long serialVersionUID = 1234567;
  
      transient String name;
  
      public People(String name){
          this.name = name;
      }
  }
  ```

  其实我们可以看到，在一些JDK内部的源码中，也存在大量的transient关键字，使得某些属性不参与序列化，取消这些不必要保存的属性，可以节省数据空间占用以及减少序列化时间。

  - 对象流作用，通俗点理解类似于加密，（以一种不知道啥格式进行数据的读取）写上去的文件类似于乱码那样，看不懂啥格式，自己去读取，输入输出才能得到正确的数据，对象流也是一共有两个，输入和输出

### 总而言之：除了文件字符/节输出/入流，其他流都是文件字符/节输入输出流的包装，在此功能上新加了某些方法，俗称装饰者模式

- ## 多线程：

- ```
  Runnable r1=new Runnable() {
              @Override
              public void run() {
                  System.out.println("我是小李");
              }
          };//要new一个接口并实例化。等会t1线程传入告诉它要干什么
  
          Thread t1=new Thread(r1);//要传入运行内容
          t1.start();//t1线程开始运行
  ```

  因为接口是只有一个方法带实现，所以可以用lambda表达式，

  ```
    Thread t1=new Thread(()->{
              System.out.println("我是小李");
          });
          t1.start();
  ```

  

因为thread方法形参默认传入Runnable接口了

```
 public Thread(Runnable task) {
        this(null, null, 0, task, 0, null);
    }所以可以用lambda表达式
```

- 主线程与子线程同时运行，没有谁是先结束再开始，也就是谁先做谁后做
- 类的方法：
  - sleep(时间，毫秒为单位);
  - interrupt();取消中断，就是当对象下达非强制中断命令后，这个类的命令可以让线程取消终止；
- 对象的方法：
  - start();开始执行线程，线程启动
  - run:当前面的线程跑完再启动本线程
  - interrupt();缓慢的终止线程，不是强制终止，像电脑在桌面按关机键关机，强制终止像电脑按住开机键关机
  - stop():强制终止

实际上，线程和进程差不多，也会等待获取CPU资源，一旦获取到，就开始按顺序执行我们给定的程序，当需要等待外部IO操作（比如Scanner获取输入的文本），就会暂时处于休眠状态，等待通知，或是调用`sleep()`方法来让当前线程休眠一段时间：

```java
public static void main(String[] args) throws InterruptedException {
    System.out.println("l");
    Thread.sleep(1000);    //休眠时间，以毫秒为单位，1000ms = 1s
    System.out.println("b");
    Thread.sleep(1000);
    System.out.println("w");
    Thread.sleep(1000);
    System.out.println("nb!");
}
```

我们也可以使用`stop()`方法来强行终止此线程：

```java
public static void main(String[] args) throws InterruptedException {
    Thread t = new Thread(() -> {
        Thread me = Thread.currentThread();   //获取当前线程对象
        for (int i = 0; i < 50; i++) {
            System.out.println("打印:"+i);
            if(i == 20) me.stop();  //此方法会直接终止此线程
        }
    });
    t.start();
}
```

### 线程的休眠和中断

我们前面提到，一个线程处于运行状态下，线程的下一个状态会出现以下情况：

- 当CPU给予的运行时间结束时，会从运行状态回到就绪（可运行）状态，等待下一次获得CPU资源。
- 当线程进入休眠 / 阻塞(如等待IO请求) / 手动调用`wait()`方法时，会使得线程处于等待状态，当等待状态结束后会回到就绪状态。
- 当线程出现异常或错误 / 被`stop()` 方法强行停止 / 所有代码执行结束时，会使得线程的运行终止。

我们发现，每一个Thread对象中，都有一个`interrupt()`方法，调用此方法后，会给指定线程添加一个中断标记以告知线程需要立即停止运行或是进行其他操作，由线程来响应此中断并进行相应的处理，我们前面提到的`stop()`方法是强制终止线程，这样的做法虽然简单粗暴，但是很有可能导致资源不能完全释放，而类似这样的发送通知来告知线程需要中断，让线程自行处理后续，会更加合理一些，也是更加推荐的做法。我们来看看interrupt的用法：

```java
public static void main(String[] args) {
    Thread t = new Thread(() -> {
        System.out.println("线程开始运行！");
        while (true){   //无限循环
            if(Thread.currentThread().isInterrupted()){   //判断是否存在中断标志
                break;   //响应中断
            }
        }
        System.out.println("线程被中断了！");
    });
    t.start();
    try {
        Thread.sleep(3000);   //休眠3秒，一定比线程t先醒来
        t.interrupt();   //调用t的interrupt方法
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

通过`isInterrupted()`可以判断线程是否存在中断标志，如果存在，说明外部希望当前线程立即停止，也有可能是给当前线程发送一个其他的信号，如果我们并不是希望收到中断信号就是结束程序，而是通知程序做其他事情，我们可以在收到中断信号后，复位中断标记，然后继续做我们的事情：

```java
public static void main(String[] args) {
    Thread t = new Thread(() -> {
        System.out.println("线程开始运行！");
        while (true){
            if(Thread.currentThread().isInterrupted()){   //判断是否存在中断标志
                System.out.println("发现中断信号，复位，继续运行...");
                Thread.interrupted();  //复位中断标记（返回值是当前是否有中断标记，这里不用管）
            }
        }
    });
    t.start();
    try {
        Thread.sleep(3000);   //休眠3秒，一定比线程t先醒来
        t.interrupt();   //调用t的interrupt方法
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

复位中断标记后，会立即清除中断标记。那么，如果现在我们想暂停线程呢？我们希望线程暂时停下，比如等待其他线程执行完成后，再继续运行，那这样的操作怎么实现呢？

```java
public static void main(String[] args) {
    Thread t = new Thread(() -> {
        System.out.println("线程开始运行！");
        Thread.currentThread().suspend();   //暂停此线程//不推荐使用
        System.out.println("线程继续运行！");
    });
    t.start();
    try {
        Thread.sleep(3000);   
        t.resume();   //恢复此线程//不推荐使用
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

虽然这样很方便地控制了线程的暂停状态，但是这两个方法我们发现实际上也是不推荐的做法，它很容易导致死锁！

- ### 线程的优先级

  实际上，Java程序中的每个线程并不是平均分配CPU时间的，为了使得线程资源分配更加合理，Java采用的是抢占式调度方式，优先级越高的线程，优先使用CPU资源！我们希望CPU花费更多的时间去处理更重要的任务，而不太重要的任务，则可以先让出一部分资源。线程的优先级一般分为以下三种：

```Java
MIN_PRIORITY 最低优先级
MAX_PRIORITY 最高优先级
NOM_PRIORITY 常规优先级
public static void main(String[] args) {
    Thread t = new Thread(() -> {
        System.out.println("线程开始运行！");
    });
    t.start();
    t.setPriority(Thread.MIN_PRIORITY);  //通过使用setPriority方法来设定优先级
}


```

- ### 进程让位与加入

  - 让位操作：（进程让位几十毫秒，而并不是让哪个进程先执行完，也只是让一下cpu资源，让某个进程快一点而已，但是两个或者多个进程都是执行中的） 方法是[对象.yield();]()

  - 加入操作：(进程让某个进程先执行，优先执行完先，再执行其他线程)：要被提前执行的对象.join()；提前执行的对象执行完先，再执行其他的进程

  - 加入：当我们希望一个线程等待另一个线程执行完成后再继续进行，我们可以使用`join()`方法来实现线程的加入：

    ```java
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            System.out.println("线程1开始运行！");
            for (int i = 0; i < 50; i++) {
                System.out.println("1打印："+i);
            }
            System.out.println("线程1结束！");
        });
        Thread t2 = new Thread(() -> {
            System.out.println("线程2开始运行！");
            for (int i = 0; i < 50; i++) {
                System.out.println("2打印："+i);
                if(i == 10){
                    try {
                        System.out.println("线程1加入到此线程！");
                        t1.join();    //在i==10时，让线程1加入，先完成线程1的内容，在继续当前内容
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        });
        t1.start();
        t2.start();
    }
    ```

    我们发现，线程1加入后，线程2等待线程1待执行的内容全部执行完成之后，再继续执行的线程2内容。注意，线程的加入只是等待另一个线程的完成，并不是将另一个线程和当前线程合并！我们来看看：

    ```java
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            System.out.println(Thread.currentThread().getName()+"开始运行！");
            for (int i = 0; i < 50; i++) {
                System.out.println(Thread.currentThread().getName()+"打印："+i);
            }
            System.out.println("线程1结束！");
        });
        Thread t2 = new Thread(() -> {
            System.out.println("线程2开始运行！");
            for (int i = 0; i < 50; i++) {
                System.out.println("2打印："+i);
                if(i == 10){
                    try {
                        System.out.println("线程1加入到此线程！");
                        t1.join();    //在i==10时，让线程1加入，先完成线程1的内容，在继续当前内容
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        });
        t1.start();
        t2.start();
    }
    ```

    实际上，t2线程只是暂时处于等待状态，当t1执行结束时，t2才开始继续执行，只是在效果上看起来好像是两个线程合并为一个线程在执行而已。

- ### 线程锁与线程同步：

- 在开始讲解线程同步之前，我们需要先了解一下多线程情况下Java的内存管理：

  ![image-20221004203914215](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303182354230.png)

  线程之间的共享变量（比如之前悬念中的value变量）存储在主内存（main memory）中，每个线程都有一个私有的工作内存（本地内存），工作内存中存储了该线程以读/写共享变量的副本。它类似于我们在`计算机组成原理`中学习的多核心处理器高速缓存机制：

  ![image-20221004204209038](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303182354421.png)

  高速缓存通过保存内存中数据的副本来提供更加快速的数据访问，但是如果多个处理器的运算任务都涉及同一块内存区域，就可能导致各自的高速缓存数据不一致，在写回主内存时就会发生冲突，这就是引入高速缓存引发的新问题，称之为：缓存一致性。

  实际上，Java的内存模型也是这样类似设计的，==当我们同时去操作一个共享变量时，如果仅仅是读取还好，但是如果同时写入内容，就会出现问题！==好比说一个银行，如果我和我的朋友同时在银行取我账户里面的钱，难道取1000还可能吐2000出来吗？我们需要一种更加安全的机制来维持秩序，保证数据的安全性！

- 解决办法：加一把锁，线程锁

通过[synchronized关键字]()来创造一个线程锁，首先我们来认识一下synchronized代码块，它需要在括号中填入一个内容，必须是一个对象或是一个类，我们在value自增操作外套上同步代码块：

```java
private static int value = 0;

public static void main(String[] args) throws InterruptedException {
    Thread t1 = new Thread(() -> {
        for (int i = 0; i < 10000; i++) {
            synchronized (Main.class){  //使用synchronized关键字创建同步代码块
                value++;
            }
        }
        System.out.println("线程1完成");
    });
    Thread t2 = new Thread(() -> {
        for (int i = 0; i < 10000; i++) {
            synchronized (Main.class){//线程锁中的代码块，属于静态方法，那就要用类，属于对象的方法，那就用对象锁，因为静态方法属于类。如这里的value是静态变量，属于Main类。
                value++;
            }
        }
        System.out.println("线程2完成");
    });
    t1.start();
    t2.start();
    Thread.sleep(1000);  //主线程停止1秒，保证两个线程执行完成
    System.out.println(value);
}
两个线程或者多个线程，内容同步要用同一把对象锁、；

```

- #### 锁的理解：

- 每一把对象锁相当于是一张vip卡，拿到了才能进入代码块里面执行代码！

- 同一张vip卡，锁的对象是同一个

### 死锁：死锁

其实死锁的概念在`操作系统`中也有提及，它是指两个线程相互持有对方需要的锁，但是又迟迟不释放，导致程序卡住：

![image-20221004205058223](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303182354735.png)

我们发现，线程A和线程B都需要对方的锁，但是又被对方牢牢把握，由于线程被无限期地阻塞，因此程序不可能正常终止。我们来看看以下这段代码会得到什么结果：

```java
public static void main(String[] args) throws InterruptedException {
    Object o1 = new Object();
    Object o2 = new Object();
    Thread t1 = new Thread(() -> {
        synchronized (o1){
            try {
                Thread.sleep(1000);
                synchronized (o2){
                    System.out.println("线程1");
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    Thread t2 = new Thread(() -> {
        synchronized (o2){
            try {
                Thread.sleep(1000);
                synchronized (o1){
                    System.out.println("线程2");
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    t1.start();
    t2.start();
}
```

所以，我们在编写程序时，一定要注意，不要出现这种死锁的情况。那么我们如何去检测死锁呢？我们可以利用jstack命令来检测死锁，首先利用jps找到我们的java进程：

```shell
nagocoler@NagodeMacBook-Pro ~ % jps
51592 Launcher
51690 Jps
14955 
51693 Main
nagocoler@NagodeMacBook-Pro ~ % jstack 51693
...
Java stack information for the threads listed above:
===================================================
"Thread-1":
	at com.test.Main.lambda$main$1(Main.java:46)
	- waiting to lock <0x000000076ad27fc0> (a java.lang.Object)
	- locked <0x000000076ad27fd0> (a java.lang.Object)
	at com.test.Main$$Lambda$2/1867750575.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)
"Thread-0":
	at com.test.Main.lambda$main$0(Main.java:34)
	- waiting to lock <0x000000076ad27fd0> (a java.lang.Object)
	- locked <0x000000076ad27fc0> (a java.lang.Object)
	at com.test.Main$$Lambda$1/396873410.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)

Found 1 deadlock.
```

jstack自动帮助我们找到了一个死锁，并打印出了相关线程的栈追踪信息，同样的，使用`jconsole`也可以进行监测。

因此，前面说不推荐使用 `suspend()`去挂起线程的原因，是因为`suspend()`在使线程暂停的同时，并不会去释放任何锁资源。其他线程都无法访问被它占用的锁。直到对应的线程执行`resume()`方法后，被挂起的线程才能继续，从而其它被阻塞在这个锁的线程才可以继续执行。但是，如果`resume()`操作出现在`suspend()`之前执行，那么线程将一直处于挂起状态，同时一直占用锁，这就产生了死锁。

---



- ### wait和notify方法

- wait：等待，wait的方法只能在锁的代码块中使用。也就是用于同步代码块里面：作用：释放当前的对象锁给另一个进程，自己等待另一个进程给它唤醒，（不唤醒，一直等下去，不会结束进程，哪怕其他进程结束了），而且唤醒后，要等唤醒它的进程执行完，自己才会恢复运行。

- notify是唤醒线程，notifyall是唤醒多个线程，哪个线程先被唤醒看运气。对象锁和对象的wait是要求同一个对象





其实我们之前可能就发现了，Object类还有三个方法我们从来没有使用过，分别是`wait()`、`notify()`以及`notifyAll()`，他们其实是需要配合synchronized来使用的（实际上锁就是依附于对象存在的，每个对象都应该有针对于锁的一些操作，所以说就这样设计了）当然，只有在同步代码块中才能使用这些方法，正常情况下会报错，我们来看看他们的作用是什么：

```java
public static void main(String[] args) throws InterruptedException {
    Object o1 = new Object();
    Thread t1 = new Thread(() -> {
        synchronized (o1){
            try {
                System.out.println("开始等待");
                o1.wait();     //进入等待状态并释放锁
                System.out.println("等待结束！");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    Thread t2 = new Thread(() -> {
        synchronized (o1){
            System.out.println("开始唤醒！");
            o1.notify();     //唤醒处于等待状态的线程
          	for (int i = 0; i < 50; i++) {
               	System.out.println(i);   
            }
          	//唤醒后依然需要等待这里的锁释放之前等待的线程才能继续
        }
    });
    t1.start();
    Thread.sleep(1000);
    t2.start();
}
```

我们可以发现，对象的`wait()`方法会暂时使得此线程进入等待状态，同时会释放当前代码块持有的锁，这时其他线程可以获取到此对象的锁，当其他线程调用对象的`notify()`方法后，会唤醒刚才变成等待状态的线程（这时并没有立即释放锁）。注意，必须是在持有锁（同步代码块内部）的情况下使用，否则会抛出异常！

notifyAll其实和notify一样，也是用于唤醒，但是前者是唤醒所有调用`wait()`后处于等待的线程，而后者是看运气随机选择一个。

### ThreadLocal的使用

既然每个线程都有一个自己的工作内存，那么能否只在自己的工作内存中创建变量仅供线程自己使用呢？

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303182354821.png)

[我们可以使用ThreadLocal类，来创建工作内存中的变量，它将我们的变量值存储在内部（只能存储一个变量]()），不同的线程访问到ThreadLocal对象时，都只能获取到当前线程所属的变量。

```java
public static void main(String[] args) throws InterruptedException {
    ThreadLocal<String> local = new ThreadLocal<>();  //注意这是一个泛型类，存储类型为我们要存放的变量类型
    Thread t1 = new Thread(() -> {
        local.set("lbwnb");   //将变量的值给予ThreadLocal
        System.out.println("变量值已设定！");
        System.out.println(local.get());   //尝试获取ThreadLocal中存放的变量
    });
    Thread t2 = new Thread(() -> {
        System.out.println(local.get());   //尝试获取ThreadLocal中存放的变量
    });
    t1.start();
    Thread.sleep(3000);    //间隔三秒
    t2.start();
}
```

上面的例子中，我们开启两个线程分别去访问ThreadLocal对象，我们发现，第一个线程存放的内容，第一个线程可以获取，但是第二个线程无法获取，我们再来看看第一个线程存入后，第二个线程也存放，是否会覆盖第一个线程存放的内容：

```java
public static void main(String[] args) throws InterruptedException {
    ThreadLocal<String> local = new ThreadLocal<>();  //注意这是一个泛型类，存储类型为我们要存放的变量类型
    Thread t1 = new Thread(() -> {
        local.set("lbwnb");   //将变量的值给予ThreadLocal
        System.out.println("线程1变量值已设定！");
        try {
            Thread.sleep(2000);    //间隔2秒
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("线程1读取变量值：");
        System.out.println(local.get());   //尝试获取ThreadLocal中存放的变量
    });
    Thread t2 = new Thread(() -> {
        local.set("yyds");   //将变量的值给予ThreadLocal
        System.out.println("线程2变量值已设定！");
    });
    t1.start();
    Thread.sleep(1000);    //间隔1秒
    t2.start();
}
```

[我们发现，即使线程2重新设定了值，也没有影响到线程1存放的值，所以说，不同线程向ThreadLocal存放数据，只会存放在线程自己的工作空间中，而不会直接存放到主内存中，因此各个线程直接存放的内容互不干扰。]()

我们发现在线程中创建的子线程，无法获得父线程工作内存中的变量：

```java
public static void main(String[] args) {
    ThreadLocal<String> local = new ThreadLocal<>();
    Thread t = new Thread(() -> {
       local.set("lbwnb");
        new Thread(() -> {
            System.out.println(local.get());
        }).start();
    });
    t.start();
}
```

我们可以使用InheritableThreadLocal来解决：

```java
public static void main(String[] args) {
    ThreadLocal<String> local = new InheritableThreadLocal<>();
    Thread t = new Thread(() -> {
       local.set("lbwnb");
        new Thread(() -> {
            System.out.println(local.get());
        }).start();
    });
    t.start();
}
```

在InheritableThreadLocal存放的内容，会自动向子线程传递。

个人总结：

- threadlocal是用于每个线程的数据存放，线程一使用了，设定了值，其他线程不能使用非自己线程设定的值，因为set方法和get方法是绑定线程的。
- 一个对象，多个线程用，还互不影响。

### 定时器

我们有时候会有这样的需求，我希望定时执行任务，比如3秒后执行，其实我们可以通过使用`Thread.sleep()`来实现：

```java
public static void main(String[] args) {
    new TimerTask(() -> System.out.println("我是定时任务！"), 3000).start();   //创建并启动此定时任务
}

static class TimerTask{
    Runnable task;
    long time;

    public TimerTask(Runnable runnable, long time){
        this.task = runnable;
        this.time = time;
    }

    public void start(){
        new Thread(() -> {
            try {
                Thread.sleep(time);
                task.run();   //休眠后再运行
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
```

我们通过自行封装一个TimerTask类，并在启动时，先休眠3秒钟，再执行我们传入的内容。那么现在我们希望，能否循环执行一个任务呢？比如我希望每隔1秒钟执行一次代码，这样该怎么做呢？

```java
public static void main(String[] args) {
    new TimerLoopTask(() -> System.out.println("我是定时任务！"), 3000).start();   //创建并启动此定时任务
}

static class TimerLoopTask{
    Runnable task;
    long loopTime;

    public TimerLoopTask(Runnable runnable, long loopTime){
        this.task = runnable;
        this.loopTime = loopTime;
    }

    public void start(){
        new Thread(() -> {
            try {
                while (true){   //无限循环执行
                    Thread.sleep(loopTime);
                    task.run();   //休眠后再运行
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
```

现在我们将单次执行放入到一个无限循环中，这样就能一直执行了，并且按照我们的间隔时间进行。

但是终究是我们自己实现，可能很多方面还没考虑到，Java也为我们提供了一套自己的框架用于处理定时任务：

```java
public static void main(String[] args) {
    Timer timer = new Timer();    //创建定时器对象
    timer.schedule(new TimerTask() {   //注意这个是一个抽象类，不是接口，无法使用lambda表达式简化，只能使用匿名内部类
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName());    //打印当前线程名称
        }
    }, 1000);    //执行一个延时任务
}
```

我们可以通过创建一个Timer类来让它进行定时任务调度，我们可以通过此对象来创建任意类型的定时任务，包延时任务、循环定时任务等。我们发现，虽然任务执行完成了，但是我们的程序并没有停止，这是因为Timer内存维护了一个任务队列和一个工作线程：

```java
public class Timer {
    /**
     * The timer task queue.  This data structure is shared with the timer
     * thread.  The timer produces tasks, via its various schedule calls,
     * and the timer thread consumes, executing timer tasks as appropriate,
     * and removing them from the queue when they're obsolete.
     */
    private final TaskQueue queue = new TaskQueue();

    /**
     * The timer thread.
     */
    private final TimerThread thread = new TimerThread(queue);
  
		...
}
```

TimerThread继承自Thread，是一个新创建的线程，在构造时自动启动：

```java
public Timer(String name) {
    thread.setName(name);
    thread.start();
}
```

而它的run方法会循环地读取队列中是否还有任务，如果有任务依次执行，没有的话就暂时处于休眠状态：

```java
public void run() {
    try {
        mainLoop();
    } finally {
        // Someone killed this Thread, behave as if Timer cancelled
        synchronized(queue) {
            newTasksMayBeScheduled = false;
            queue.clear();  // Eliminate obsolete references
        }
    }
}

/**
 * The main timer loop.  (See class comment.)
 */
private void mainLoop() {
  try {
       TimerTask task;
       boolean taskFired;
       synchronized(queue) {
         	// Wait for queue to become non-empty
          while (queue.isEmpty() && newTasksMayBeScheduled)   //当队列为空同时没有被关闭时，会调用wait()方法暂时处于等待状态，当有新的任务时，会被唤醒。
                queue.wait();
          if (queue.isEmpty())
             break;    //当被唤醒后都没有任务时，就会结束循环，也就是结束工作线程
                      ...
}
```

`newTasksMayBeScheduled`实际上就是标记当前定时器是否关闭，当它为false时，表示已经不会再有新的任务到来，也就是关闭，我们可以通过调用`cancel()`方法来关闭它的工作线程：

```java
public void cancel() {
    synchronized(queue) {
        thread.newTasksMayBeScheduled = false;
        queue.clear();
        queue.notify();  //唤醒wait使得工作线程结束
    }
}
```

因此，我们可以在使用完成后，调用Timer的`cancel()`方法以正常退出我们的程序：

```java
public static void main(String[] args) {
    Timer timer = new Timer();
    timer.schedule(new TimerTask() {
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName());
            timer.cancel();  //结束
        }
    }, 1000);
}
```

-  主要是两个方法：
  - 定时启动schedule()；形参是TinmerTask类，这是一个抽象类，要实现抽象方法run。一般推荐使用匿名内部类去使用，第二个形参是时间，设置时间，run方法执行后，隔多久再次执行run方法
  - 取消定时器，取消任务：cancel();(要写在run里面，就是加条件让它达到什么时候才运行cancel)；不然run一直出不去

### 守护线程

不要把操作系统重的守护进程和守护线程相提并论！

守护进程在后台运行运行，不需要和用户交互，本质和普通进程类似。而守护线程就不一样了，当其他所有的非守护线程结束之后，守护线程自动结束，也就是说，Java中所有的线程都执行完毕后，守护线程自动结束，因此守护线程不适合进行IO操作，只适合打打杂：

```java
public static void main(String[] args) throws InterruptedException{
    Thread t = new Thread(() -> {
        while (true){
            try {
                System.out.println("程序正常运行中...");
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    t.setDaemon(true);   //设置为守护线程（必须在开始之前，中途是不允许转换的）
    t.start();
    for (int i = 0; i < 5; i++) {
        Thread.sleep(1000);
    }
}
```

在守护线程中产生的新线程也是守护的：

```java
public static void main(String[] args) throws InterruptedException{
    Thread t = new Thread(() -> {
        Thread it = new Thread(() -> {
            while (true){
                try {
                    System.out.println("程序正常运行中...");
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        it.start();
    });
    t.setDaemon(true);   //设置为守护线程（必须在开始之前，中途是不允许转换的）
    t.start();
    for (int i = 0; i < 5; i++) {
        Thread.sleep(1000);
    }
}
```
