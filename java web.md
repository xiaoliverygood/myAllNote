# java web

## java网络编程：

### 网络基础原理：

-  <img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202303192330444.png" alt="image-20230319225553804" style="zoom: 67%;" />

- - <img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202303192330058.png" alt="image-20230319231146892" style="zoom: 50%;" />i

  - #### ipv4与ipv6的区别：

    1·ipv4最大为255.255.255.255一个ip地址是有4组数字，每组数字是8bit，共32bit
    ipv6是5个字符串，每个字符串之间是用::隔开，共128bit 如：xxxx::xxxx::xxxx::xxxx

    每一台电脑在同一个网络上都有一个自己的IP地址，用于区别于其他的电脑，我们可以通过对方主机的IP地址对其进行访问。那么我手机连接的移动流量，能访问到连接家里路由器的电脑吗？（不能，因为他们不属于同一个网络）

    而我们的电脑上可能运行着大量的程序，每一个程序可能都需要通过网络来访问其他计算机，那这时该如何区分呢？我们可以通过端口号来区分：

    ![image-20230319231724098](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303192330572.png)

    因此，我们一般看到的是这样的：`192.168.0.11:8080`，通过`IP:端口`的形式来访问目标主机上的一个应用程序服务。注意端口号只能是0-65535之间的值！

    IP地址分为IPv4和IPv6，IPv4类似于`192.168.0.11`，我们上面提到的例子都是使用的IPv4，它一共有四组数字，每组数字占8个bit位，IPv4地址`0.0.0.0`表示为2进制就是：00000000.00000000.00000000.00000000，共32个bit，最大为`255.255.255.255`，实际上，IPv4能够表示的所有地址，早就已经被用完了。IPv6能够保存128个bit位，因此它也可以表示更多的IP地址，一个IPv6地址看起来像这样：`1030::C9B4:FF12:48AA:1A2B`，目前也正在向IPv6的阶段过度。

    - ### tcp与upp的传输协议：

    - TCP：当一台计算机想要与另一台计算机通讯时，两台计算机之间的通信需要畅通且可靠（会进行三次握手，断开也会进行四次挥手），这样才能保证正确收发数据，因此TCP更适合一些可靠的数据传输场景。

    - UDP：它是一种无连接协议，数据想发就发，而且不会建立可靠传输，也就是说传输过程中有可能会导致部分数据丢失，但是它比TCP传输更加简单高效，适合视频直播之类的。

    - ![image-20230319232002314](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303192331535.png)

    - ## 了解Socket技术

      通过Socket技术（它是计算机之间进行**通信**的**一种约定**或一种方式），我们就可以实现两台计算机之间的通信，Socket也被翻译为`套接字`，是操作系统底层提供的一项通信技术，它支持TCP和UDP。而Java就对socket底层支持进行了一套完整的封装，我们可以通过Java来实现Socket通信。

      要实现Socket通信，我们必须创建一个数据发送者和一个数据接收者，也就是客户端和服务端，我们需要提前启动服务端，来等待客户端的连接，而客户端只需要随时启动去连接服务端即可！

      ```java
      //服务端
      public static void main(String[] args) {
          try(ServerSocket server = new ServerSocket(8080)){    //将服务端创建在端口8080上
              System.out.println("正在等待客户端连接...");
              Socket socket = server.accept();  //当没有客户端连接时，线程会阻塞，线程进入等待，直到有客户端连接为止
              System.out.println("客户端已连接，IP地址为："+socket.getInetAddress().getHostAddress());
          }catch (IOException e){
              e.printStackTrace();
          }
      }
      
      ```

      

      ```java
      //客户端
      public static void main(String[] args) {
          try (Socket socket = new Socket("localhost", 8080)){
              System.out.println("已连接到服务端！");
          }catch (IOException e){
              System.out.println("服务端连接失败！");
              e.printStackTrace();
          }
      }
      ```

      实际上它就是一个TCP连接的建立过程：

      一旦TCP连接建立，服务端和客户端之间就可以相互发送数据，直到客户端主动关闭连接。当然，服务端不仅仅只可以让一个客户端进行连接，我们可以尝试让服务端一直运行来不断接受客户端的连接：

      
    
      ```java
      public static void main(String[] args) {
          try(ServerSocket server = new ServerSocket(8080)){    //将服务端创建在端口8080上
              System.out.println("正在等待客户端连接...");
              while (true){   //无限循环等待客户端连接
                  Socket socket = server.accept();
                  System.out.println("客户端已连接，IP地址为："+socket.getInetAddress().getHostAddress());
                   // //一连接到客户端，会返回一个Socket类的对象，我们称为socket然后执行下面那一句，然后因为while循环，下面那一句话执行后。然后又等待连接，所以可以同时连接多个客户端
              }
          }catch (IOException e){
              e.printStackTrace();
          }
      }
      ```

      现在我们就可以多次去连接此服务端了。

      ![image-20230319234439338](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303192344366.png)

      ![image-20230319234501586](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303192345609.png)

      ![image-20230319234527502](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303192345576.png)

      ![image-20230319234543395](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303192345461.png)
    
    - 

- 传输文件：

- 客户端：

- ```java
  package client;
  
  import java.io.*;
  import java.net.Socket;
  
  public class Main_client {
      public static void main(String[] args) {
          try(Socket socket=new Socket("localhost",8080)){
              File file = new File("client.txt");
              if(!file.exists()){//如果文件不存在，则会生成一个文件！
                  file.createNewFile();
              }
             InputStreamReader reader=new InputStreamReader(new FileInputStream(file));
             OutputStream out_server=socket.getOutputStream();
  
              int a ;
              while ((a = reader.read())!=-1){
                 out_server.write(a);
             }
          }catch ( IOException e){
              System.out.println("client error of connection");
  
              e.printStackTrace();
          }
  
      }
  }
  
  ```

- ```
  //服务端：
  package server;
  
  import java.io.*;
  import java.net.ServerSocket;
  import java.net.Socket;
  
  public class server_Main {
      public static void main(String[] args) {
          try(ServerSocket serverSocket = new ServerSocket(8080)){
              while(true){
                  Socket socket = serverSocket.accept();
                  System.out.println("client is connected successfully");
                  File file = new File("server.txt");
                  if(!file.exists()){
                      file.createNewFile();
                  }
                  InputStreamReader reader = new InputStreamReader(socket.getInputStream());
                  OutputStreamWriter output=new OutputStreamWriter(new FileOutputStream("server.txt"));
                  int a;
                  while((a=reader.read())!=-1){
                      output.write(a);
                  }
                  output.flush();
                  output.close();
              }
          }catch (IOException e){
              System.out.println("connect is error!");
              e.printStackTrace();
          }
      }
  }
  
  ```

  

# 理论部分：

### 数据模型

数据模型与现实世界中的模型一样，是对现实世界数据特征的一种抽象。实际上，我们之前学习的类就是对现实世界数据的一种抽象，比如一个学生的特征包括姓名，年龄，年级，学号，专业等，这些特征也称为实体的一种属性，属性具有以下特点：

* 属性不可再分

* 一个实体的属性可以有很多个

* 用于唯一区分不同实体的的属性，称为Key，比如每个同学的学号都是不一样的，再比如身份证为key（主键），因为有同名同姓的人，但是身份证是没有一样的。

* 属性取值可以有一定的约束，比如性别只能是男或是女

* 实体或是属性之间可以具有一定的联系，比如一个老师可以教很多个学生，而学生相对于老师就是被教授的关系；又比如每个同学都有一个学号与其唯一对应，因此学号和学生之间也有一种联系。而像一个老师教多个学生的联系就是一种一对多的联系（1:n），而学号唯一对应，就是一种一对一的联系（1:1）；每一个老师不仅可以教多个学生，每一个学生也可以有多个教师，这就是一种多对多的联系（n:m）

* 

  通过构建一个ER图，我们就能很好地理清不同数据模型之间的关系和特点。

  ![img](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Ffile1.renrendoc.com%2Ffileroot_temp2%2F2020-10%2F17%2F763fb9f3-871d-4f1c-abe7-0a5025cf52a5%2F763fb9f3-871d-4f1c-abe7-0a5025cf52a52.gif&refer=http%3A%2F%2Ffile1.renrendoc.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1637926750&t=31a308e5d8efd32bae55a40c963f459d)

  

  这个就是e-r图，，圆形表示表的属性，长方形表示模型的名称。菱形表示关系

  ## 数据库的规范化

  要去设计存放一个实体的表，我们就需要了解数据库的关系规范化，尽可能减少“不好”的关系存在，如何设计一个优良的关系模型是最关键的内容！简而言之，我们要学习一下每一个表该如何去设计。

  ### 第一范式（1NF）

  第一范式是指数据库的每一列都是不可分割的基本数据项，而下面这样的就存在可分割的情况：

  * 学生（姓名，电话号码）

  电话号码实际上包括了`家用座机电话`和`移动电话`，因此它可以被拆分为：

  * 学生（姓名，座机号码，手机号码）

  满足第一范式是关系型数据库最基本的要求！

  ### 第二范式（2NF）

  第二范式要求表中必须存在主键，且其他的属性必须完全依赖于主键，比如：

  * 学生（<u>学号</u>，姓名，性别）

  学号是每个学生的唯一标识，每个学生都有着不同的学号，因此此表中存在一个主键，并且每个学生的所有属性都依赖于学号，学号发生改变就代表学生发生改变，姓名和性别都会因此发生改变，所有此表满足第二范式。

  ### 第三范式（3NF）

  在满足第二范式的情况下，所有的属性都不传递依赖于主键，满足第三范式。

  * 学生借书情况（<u>借阅编号</u>，学生学号，书籍编号，书籍名称，书籍作者）

  实际上书籍编号依赖于借阅编号，而书籍名称和书籍作者依赖于书籍编号，因此存在传递依赖的情况，我们可以将书籍信息进行单独拆分为另一张表：

  * 学生借书情况（<u>借阅编号</u>，学生学号，书籍编号）
  * 书籍（<u>书籍编号</u>，书籍名称，书籍作者）

  这样就消除了传递依赖，从而满足第三范式。

  ### BCNF

  BCNF作为第三范式的补充，假设仓库管理关系表为StorehouseManage(仓库ID, 存储物品ID, 管理员ID, 数量)，且有一个管理员只在一个仓库工作；一个仓库可以存储多种物品。这个数据库表中存在如下决定关系：

  (仓库ID, 存储物品ID) →(管理员ID, 数量)

  (管理员ID, 存储物品ID) → (仓库ID, 数量)

  所以，(仓库ID, 存储物品ID)和(管理员ID, 存储物品ID)都是StorehouseManage的候选关键字，表中的唯一非关键字段为数量，它是符合第三范式的。但是，由于存在如下决定关系：

  (仓库ID) → (管理员ID)

  (管理员ID) → (仓库ID)

  即存在关键字段决定关键字段的情况，如果修改管理员ID，那么就必须逐一进行修改，所以其不符合BCNF范式。

  一般，满足到第三范式就好了，不需要BCNF，除非高级的

  # 正式入门

  首先sql语法了解：1	sql支持多行，几行语句，只需要一个；就行   2·几行语句或者一行语句，要用“；”结尾。3	大同小异，每个sql软件的语言都不完全一样，但是基本上一样，只有小小的区别

  Mysql大致分为四大部分，1·DDL（数据库定义） 第二部分DCL，第三部分：数据操作语句号，第四部分：数据查询语句

  # DDL：

  ### SQL数据类型

  以下的数据类型用于字符串存储：

  * char(n)可以存储任意字符串，但是是固定长度为n，如果插入的长度小于定义长度时，则用空格填充。
  * varchar(n)也可以存储任意数量字符串，长度不固定，但不能超过n，不会用空格填充。
  * char形与varchat形共同点，可以储存任意字符串，不同点：char的字符长度固定，一定要n，不够的空格来凑。varchar长度不固定，不超过n就行！

  以下数据类型用于存储数字：

  * smallint用于存储小的整数，范围在 (-32768，32767)
  * int用于存储一般的整数，范围在 (-2147483648，2147483647)
  * bigint用于存储大型整数，范围在 (-9,223,372,036,854,775,808，9,223,372,036,854,775,807)
  * float用于存储单精度小数
  * double用于存储双精度的小数

  以下数据类型用于存储时间：

  * date存储日期
  * time存储时间
  * year存储年份
  * datetime用于混合存储日期+时间

  ## 1.创建数据库：

  ]

  ```
  create database 数据库名称
  //为了显示中文，要设置utf8编码
  create database 数据库名称 default charset utf8 collate utf8_general_ci
  ```

  

  ![image-20230312230154479](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222052418.png)

  ![image-20230312230239212](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222052110.png)

  删除数据库：

  ```
  drop database 数据库名称
  ```

  ![image-20230312230319378](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222052056.png)

  ## 创建表：

  ### 列级约束条件

  列级约束有六种：主键Primary key、外键foreign key 、唯一 unique、检查 check （MySQL不支持）、默认default 、非空/空值 not null/ null

  ### 表级约束条件

  表级约束有四种：主键、外键、唯一、检查

  ```sql
  [CONSTRAINT <外键名>] FOREIGN KEY 字段名 [，字段名2，…] REFERENCES <主表名> 主键列1 [，主键列2，…]
  ```

  ```
   create table 表名(列名 数据类型[列级约束条件],  列名 数据类型[列级约束条件],   ...  [,表级约束条件]);
  ```

  

  ```
  //首先创建数据库：名称为teachar
  create database czz default charset utf8 collate utf8_general_ci;
  //进入库里面
  use czz;
  //创建一个表，id为主键
  create table teacher（ teacher_id int primary key,teacher_name varchar ,teacher_sex enmu('男',‘女’)default '男'）;
  //创建了一个名称为teacher的表，里面元素，也就是列分别为：老师id，老师姓名，老师性别（使用了枚举类型，默认为男，选择），记得要加括号，在cahr后面，因为要规定长度，还有，表的属性要用括号概起来
  
  
  写法二：
   `teacher_id` int NOT NULL,
    `teacher_name` varchar(255) NOT NULL,
    `teacher_sex` enum('man','woman') NOT NULL DEFAULT 'man',
    PRIMARY KEY (`teacher_id`)
  
  ```

  ### 修改表

  如果我们想修改表结构，我们可以通过`alter table`来进行修改：

  ```sql
  ALTER TABLE 表名[ADD 新列名 数据类型[列级约束条件]]
  							 [DROP COLUMN 列名[restrict|cascade]]
  							 [ALTER COLUMN 列名 新数据类型]
  ```

  我们可以通过ADD来添加一个新的列，通过DROP来删除一个列，不过我们可以添加restrict或cascade，默认是restrict，表示如果此列作为其他表的约束或视图引用到此列时，将无法删除，而cascade会强制连带引用此列的约束、视图一起删除。还可以通过ALTER来修改此列的属性。

  ### 删除表

  我们可以通过`drop table`来删除一个表：

  ```sql
  DROP TABLE 表名[restrict|cascade]
  ```

  其中restrict和cascade上面的效果一致。

---

### 删除表

我们可以通过`drop table`来删除一个表：

```sql
DROP TABLE 表名[restrict|cascade]
```

其中restrict和cascade上面的效果一致。

***

## 数据库操纵语言（DML）

前面我们已经学习了如何使用SQL语句来创建、修改、删除数据库以及表，而如何向数据库中插入、删除、更新数据，将是本版块讨论的重点。

### 插入数据

通过使用`insert into`语句来向数据库中插入一条数据（一条记录）：

```sql
INSERT INTO 表名 VALUES(值1, 值2, 值3)
```

如果插入的数据与列一一对应，那么可以省略列名，但是如果希望向指定列上插入数据，就需要给出列名：

```sql
INSERT INTO 表名(列名1, 列名2) VALUES(值1, 值2)
```

我们也可以一次性向数据库中插入多条数据：

```sql
INSERT INTO 表名(列名1, 列名2) VALUES(值1, 值2), (值1, 值2), (值1, 值2)
```

我们来试试看向我们刚刚创建的表中添加三条数据。

### 修改数据

我们可以通过`update`语句来更新表中的数据：

```sql
UPDATE 表名 SET 列名=值,... WHERE 条件
```

注意，SQL语句中的等于判断是`=`

**警告：**如果忘记添加`WHERE`字句来限定条件，将使得整个表中此列的所有数据都被修改！

### 删除数据

我们可以通过使用`delete`来删除表中的数据：

```sql
DELETE FROM 表名
```

通过这种方式，将删除表中全部数据，我们也可以使用`where`来添加条件，只删除指定的数据：

```sql
DELETE FROM 表名 WHERE 条件
```

***

## 数据库查询语言（DQL）

数据库的查询是我们整个数据库学习中的重点内容，面对数据库中庞大的数据，该如何去寻找我们想要的数据，就是我们主要讨论的问题。

### 单表查询(select关键字去查询)

单表查询是最简单的一种查询，我们只需要在一张表中去查找数据即可，通过使用`select`语句来进行单表查询：

```sql
-- 指定查询某一列数据
SELECT 列名[,列名] FROM 表名
-- 会以别名显示此列
SELECT 列名 别名 FROM 表名
-- 查询所有的列数据
SELECT * FROM 表名
-- 只查询不重复的值
SELECT DISTINCT 列名 FROM 表名
```

我们也可以添加`where`字句来限定查询目标：

```sql
SELECT * FROM 表名 WHERE 条件
```

### 常用查询条件

* 一般的比较运算符，包括=、>、<、>=、<=、!=等。
* 是否在集合中：in、not in
* 字符模糊匹配：like，not like
* 多重条件连接查询：and、or、not

我们来尝试使用一下上面这几种条件。

### 排序查询(order by)

我们可以通过`order by`来将查询结果进行排序：

///*代表所有的意思

```sql
SELECT * FROM 表名 WHERE 条件 ORDER BY 列名 ASC|DESC
```

使用ASC表示升序排序，使用DESC表示降序排序，默认为升序。

我们也可以可以同时添加多个排序：

```sql
SELECT * FROM 表名 WHERE 条件 ORDER BY 列名1 ASC|DESC, 列名2 ASC|DESC
```

这样会先按照列名1进行排序，每组列名1相同的数据再按照列名2排序。

### 聚集函数

聚集函数一般用作统计，包括：

* `count([distinct]*)`统计所有的行数（distinct表示去重再统计，下同）
* `count([distinct]列名)`统计某列的值总和
* `sum([distinct]列名)`求一列的和（注意必须是数字类型的）
* `avg([distinct]列名)`求一列的平均值（注意必须是数字类型）
* `max([distinct]列名)`求一列的最大值
* `min([distinct]列名)`求一列的最小值

一般聚集函数是这样使用的：

```sql
SELECT count(distinct 列名) FROM 表名 WHERE 条件 
```

### 分组和分页查询（limit）

通过使用`group by`来对查询结果进行分组，它需要结合聚合函数一起使用：

```sql
SELECT sum(*) FROM 表名 WHERE 条件 GROUP BY 列名
```

我们还可以添加`having`来限制分组条件：

```sql
SELECT sum(*) FROM 表名 WHERE 条件 GROUP BY 列名 HAVING 约束条件
```

我们可以通过`limit`来限制查询的数量，只取前n个结果：

```sql
SELECT * FROM 表名 LIMIT 数量
```

我们也可以进行分页：

```sql
SELECT * FROM 表名 LIMIT 起始位置,数量
```

- ### 总结：

- #### select是查找，where是限制条件，order by是排序，limit是限制数量，分页输出  group by是分组

![image-20230322151944638](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222052868.png)

```sql
SELECT * FROM 表名 WHERE 条件 ORDER BY 列名 ASC|DESC
```

使用ASC表示升序排序，使用DESC表示降序排序，默认为升序。

我们也可以可以同时添加多个排序：

```sql
SELECT * FROM 表名 WHERE 条件 ORDER BY 列名1 ASC|DESC, 列名2 ASC|DESC
```

这样会先按照列名1进行排序，每组列名1相同的数据再按照列名2排序。

### 多表查询

多表查询是同时查询的两个或两个以上的表，多表查询会提通过连接转换为单表查询。

```sql
SELECT * FROM 表1, 表2
```

直接这样查询会得到两张表的笛卡尔积，也就是每一项数据和另一张表的每一项数据都结合一次，会产生庞大的数据。

```sql
SELECT * FROM 表1, 表2 WHERE 条件
```

这样，只会从笛卡尔积的结果中得到满足条件的数据。

**注意：**如果两个表中都带有此属性吗，需要添加表名前缀来指明是哪一个表的数据。

##### 记得要限制条件，不然数据庞大！

### 自身连接查询

自身连接，就是将表本身和表进行笛卡尔积计算，得到结果，但是由于表名相同，因此要先起一个别名：

```sql
SELECT * FROM 表名 别名1, 表名 别名2
```

其实自身连接查询和前面的是一样的，只是连接对象变成自己和自己了。

### 外连接查询

外连接就是专门用于联合查询情景的，比如现在有一个存储所有用户的表，还有一张用户详细信息的表，我希望将这两张表结合到一起来查看完整的数据，我们就可以通过使用外连接来进行查询，外连接有三种方式：

* 通过使用`inner join`进行内连接，只会返回两个表满足条件的交集部分：

![在这里插入图片描述](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222052611.png)

* 通过使用`left join`进行左连接，不仅会返回两个表满足条件的交集部分，也会返回左边表中的全部数据，而在右表中缺失的数据会使用`null`来代替（右连接`right join`同理，只是反过来而已，这里就不再介绍了）：

![在这里插入图片描述](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222052364.png)



总结：（什么连接，什么表全输出，自己是左，从左到右；内连接取交集）

- #### inner join是取两个列表的交集（内连接）

- #### left join是取左边表+右边表和左边表条件符合的集合，左边全输出，右边取符合条件的！后面接表的表名和on来限制条件！

- #### right join 是取右边表+左边边表和右边表条件符合的集合，右边全输出，左边取符合条件的！后面接表的表名和on来限制条件！

### 嵌套查询

我们可以将查询的结果作为另一个查询的条件，比如：

```sql
SELECT * FROM 表名 WHERE 列名 = (SELECT 列名 FROM 表名 WHERE 条件)
```

## 实践（查询）：

- 总的预览：

- - 学生表：![image-20230322172043355](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222052886.png)

  - 教师表：
  - ![image-20230322172142271](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222052460.png)
  - 

- #### 使用where限制条件：

![image-20230322171934737](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222052899.png)



- #### 使用limit分开输出：

- ![image-20230322172317905](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222051118.png)

- 使用排序：

- ![image-20230322172832974](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222051468.png)

- 使用限制条件，加上排序

- ![image-20230322201852677](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222051028.png)

- 多表查询，查交集，

- ![image-20230322202721161](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303222051786.png)

- 

- 多表查询，查学生表左连接

- ![image-20230327203839081](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303272038195.png)

- ## 用户：

庞大的数据库不可能由一个人来管理，我们需要更多的用户来一起管理整个数据库。

### 创建用户

我们可以通过`create user`来创建用户：

```sql
CREATE USER 用户名 identified by 密码;
```

也可以不带密码：

```sql
CREATE USER 用户名;
```

我们可以通过@来限制用户登录的登录IP地址，`%`表示匹配所有的IP地址，默认使用的就是任意IP地址。

### 登陆用户

首先需要添加一个环境变量，然后我们通过cmd去登陆mysql：

```sql
login -u 用户名 -p
```

输入密码后即可登陆此用户，我们输入以下命令来看看能否访问所有数据库：

```sql
show databases;
```

我们发现，虽然此用户能够成功登录，但是并不能查看完整的数据库列表，这是因为此用户还没有权限！

### 用户授权

我们可以通过使用`grant`来为一个数据库用户进行授权：

```sql
grant all|权限1,权限2...(列1,...) on 数据库.表 to 用户 [with grant option]
```

其中all代表授予所有权限，当数据库和表为`*`，代表为所有的数据库和表都授权。如果在最后添加了`with grant option`，那么被授权的用户还能将已获得的授权继续授权给其他用户。

我们可以使用`revoke`来收回一个权限：

```sql
revoke all|权限1,权限2...(列1,...) on 数据库.表 from 用户
```

***

## 视图

==视图本质就是一个查询的结果，不过我们每次都可以通过打开视图来按照我们想要的样子查看数据==。既然视图本质就是一个查询的结果，那么它本身就是一个虚表，并不是真实存在的，数据实际上还是存放在原来的表中。说白了就是我们通过查询得到的表格保存下来，方便后期使用

我们可以通过`create view`来创建视图;

```sql
CREATE VIEW 视图名称(列名) as 子查询语句 [WITH CHECK OPTION];
```

WITH CHECK OPTION是指当创建后，如果更新视图中的数据，是否要满足子查询中的条件表达式，不满足将无法插入，创建后，我们就可以使用`select`语句来直接查询视图上的数据了，因此，还能在视图的基础上，导出其他的视图。

1. 若视图是由两个以上基本表导出的，则此视图不允许更新。
2. 若视图的字段来自字段表达式或常数，则不允许对此视图执行INSERT和UPDATE操作，但允许执行DELETE操作。
3. 若视图的字段来自集函数，则此视图不允许更新。
4. 若视图定义中含有GROUP BY子句，则此视图不允许更新。
5. 若视图定义中含有DISTINCT短语，则此视图不允许更新。
6. 若视图定义中有嵌套查询，并且内层查询的FROM子句中涉及的表也是导出该视图的基本表，则此视图不允许更新。例如将成绩在平均成绩之上的元组定义成一个视图GOOD_SC： CREATE VIEW GOOD_SC AS SELECT Sno, Cno, Grade FROM SC WHERE Grade > (SELECT AVG(Grade) FROM SC); 　　导出视图GOOD_SC的基本表是SC，内层查询中涉及的表也是SC，所以视图GOOD_SC是不允许更新的。
7. 一个不允许更新的视图上定义的视图也不允许更新

通过`drop`来删除一个视图：

```sql
drop view apptest
```

***

## 索引（index）

在数据量变得非常庞大时，通过创建索引，能够大大提高我们的查询效率==，就像Hash表一样==，它能够快速地定位元素存放的位置，我们可以通过下面的命令创建索引：

有两种格式，一种是哈希，一种是二叉树

字段添加索引，搜索是快很多，但是不能过度使用，加了进去，使用起来是没有任何区别的，但是搜索是快了很多，就像键值对那样

```sql
-- 创建索引
CREATE INDEX 索引名称 ON 表名 (列名)
-- 查看表中的索引
show INDEX FROM student
```

我们也可以通过下面的命令删除一个索引：

```sql
drop index 索引名称 on 表名
```

虽然添加索引后会使得查询效率更高，但是我们不能过度使用索引，索引为我们带来高速查询效率的同时，也会在数据更新时产生额外建立索引的开销，同时也会占用磁盘资源。

---



## 触发器

- #### 作用：（比如删除某个表中的数据，但是存在外键，可以用触发器，先删除外键，再删除表的数据），触发器，说白就是在执行xxx指令前，先让它执行另外的语句 

触发器就像其名字一样，在某种条件下会自动触发，在`select`/`update`/`delete`时，会自动执行我们预先设定的内容，触发器通常用于检查内容的安全性，相比直接添加约束，触发器显得更加灵活。

触发器所依附的表称为基本表，当触发器表上发生`select`/`update`/`delete`等操作时，会自动生成两个临时的表（new表和old表，只能由触发器使用）

比如在`insert`操作时，新的内容会被插入到new表中；在`delete`操作时，旧的内容会被移到old表中，我们仍可在old表中拿到被删除的数据；在`update`操作时，旧的内容会被移到old表中，新的内容会出现在new表中。

```sql
CREATE TRIGGER 触发器名称 [BEFORE|AFTER] [INSERT|UPDATE|DELETE] ON 表名/视图名 FOR EACH ROW DELETE FROM student WHERE student.sno = new.sno
```

 FOR EACH ROW表示针对每一行都会生效，无论哪行进行指定操作都会执行触发器！

通过下面的命令来查看触发器：

```sql
SHOW TRIGGERS
```

如果不需要，我们就可以删除此触发器：

```sql
DROP TRIGGER 触发器名称
```

***

## 事务

- 个人理解：类似于拷贝一份备用文件，开始事务后，就对备份的文件进行操作，但是不提交的话，数据库就不会改变，这也体现了隔离性。提交后，才会去修改数据库的正式文件，备份文件在数据库中不能显示，因为备份文件是简化理解，并不是真实存在。这样的好处是，可以在写语句时，操作错误，有机会走回头路，不要更新正式文件就行，很好的保证了数据的安全性，说白了，即使损坏了数据，我没有写入正式文件，我都可以撤销修改。但我在备份文件中，显示我想要的正确操作结果，我可以更新正式文件就行！

​		当我们要进行的操作非常多时，比如要依次删除很多个表的数据，我们就需要执行大量的SQL语句来完成，这些数据库操作语句就可以		构成一个事务！只有Innodb引擎支持事务，我们可以这样来查看支持的引擎：

​			commit; #提交事务
​		-- 一旦提交，就无法再进行回滚了！

```sql
SHOW ENGINES;
```

MySQL默认采用的是Innodb引擎，我们也可以去修改为其他的引擎。

事务具有以下特性：

- **原子性：**一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
- **一致性：**在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
- **隔离性：**数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
- **持久性：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

我们通过以下例子来探究以下事务：

```sql
begin;   #开始事务
...
rollback;  #回滚事务
savepoint 回滚点;  #添加回滚点
rollback to 回滚点; #回滚到指定回滚点
...
commit; #提交事务
-- 一旦提交，就无法再进行回滚了！
```

---



- ## java jdbc

- - - ### Statement接口（操作数据库增删改查）

  - Java中的`Statement`是一种用于执行SQL语句的接口。它是Java JDBC API的一部分，允许开发者在Java应用程序中连接到关系型数据库并执行查询、更新或删除数据等操作。

    `Statement`接口有三种类型：`Statement`，`PreparedStatement`和`CallableStatement`。其中，`Statement`对象用于执行静态SQL语句，而`PreparedStatement`对象用于执行动态SQL语句。`CallableStatement`对象用于调用存储过程。

    `Statement`对象通过`Connection`对象创建，例如：

    ```
    javaCopy codeConnection conn = DriverManager.getConnection(url, username, password);
    Statement stmt = conn.createStatement();
    ```

    然后，使用`executeQuery()`方法执行查询操作，使用`executeUpdate()`方法执行更新操作。例如：

    ```
    javaCopy codeResultSet rs = stmt.executeQuery("SELECT * FROM employees");
    int rowsUpdated = stmt.executeUpdate("UPDATE employees SET salary = 50000 WHERE department = 'Sales'");
    ```

    在执行完SQL语句之后，可以通过`ResultSet`对象获取查询结果。

    需要注意的是，使用`Statement`对象执行SQL语句存在SQL注入攻击的风险。因此，在执行动态SQL语句时，建议使用`PreparedStatement`对象来替代`Statement`对象，以避免SQL注入攻击。

    ---

    

    - ### Connection类（连接数据库，创建statement对象给statement接口使用）

    ---

    在Java中，使用`Connection`类建立数据库连接，并且可以使用该类创建`Statement`对象来执行SQL语句。

    具体来说，`Connection`类是Java JDBC API提供的一个接口，它表示与数据库的连接。在使用`Connection`类时，需要提供数据库连接的URL、用户名和密码等信息。例如：

    ```
    javaCopy codeString url = "jdbc:mysql://localhost:3306/mydatabase";
    String user = "root";
    String password = "mypassword";
    Connection conn = DriverManager.getConnection(url, user, password);//DriverManager驱动管理，连接成功会返回一个connection对象
    ```

    其中，`DriverManager.getConnection()`方法返回一个`Connection`对象，表示与指定URL、用户名和密码的数据库的连接。

    通过`Connection`对象，可以使用`createStatement()`方法创建`Statement`对象。例如：

    ```
    javaCopy code
    Statement stmt = conn.createStatement();
    ```

    然后，使用`executeQuery()`方法执行查询操作，使用`executeUpdate()`方法执行更新操作。例如：

    ```
    javaCopy codeResultSet rs = stmt.executeQuery("SELECT * FROM employees");
    int rowsUpdated = stmt.executeUpdate("UPDATE employees SET salary = 50000 WHERE department = 'Sales'");
    ```

    在执行完SQL语句之后，可以通过`ResultSet`对象获取查询结果。

    需要注意的是，为了保证代码的安全性，应该使用参数化的SQL语句来避免SQL注入攻击。这时候，应该使用`PreparedStatement`对象来替代`Statement`对象。`PreparedStatement`对象可以通过`Connection`对象的`prepareStatement()`方法创建。例如：

    ```
    javaCopy codePreparedStatement pstmt = conn.prepareStatement("SELECT * FROM employees WHERE name = ?");
    pstmt.setString(1, "John");
    ResultSet rs = pstmt.executeQuery();
    ```

    这里的`pstmt.setString(1, "John")`表示将参数1设置为字符串"John"。然后，通过`executeQuery()`方法执行查询操作，并使用`ResultSet`对象获取查询结果。

    总之，在Java中，`Connection`类是用于建立数据库连接，`Statement`类是用于执行SQL语句的，它们是Java JDBC API中的重要组件。

#### 疑问一：为什么statement接口和connection接口不用匿名内部类或者lambda表达式实现

- 关于为什么不使用lambda表达式或匿名内部类来实现接口的问题，是因为Connection和Statement接口是由JDBC驱动程序==提供的具体类实现的，这些类在运行时动态加载。这些类以自己的方式实现了接口中定义的方法==，而实现细节对API的用户是隐藏的。因此，您不需要担心这些接口在底层是如何实现的。通俗点说：因为DriverManager这个类实现了全部接口，封装好了，只要调用方法，如getConnection得到connection对象，而statement接口，是因为connection接口有个方法，creatStatement，返回了一个Statement对象，实现了statement接口的方法，有点套娃

### 实现登陆与SQL注入攻击

在使用之前，我们先来看看如果我们想模拟登陆一个用户，我们该怎么去写：

```java
try (Connection connection = DriverManager.getConnection("URL","用户名","密码");
     Statement statement = connection.createStatement();
     Scanner scanner = new Scanner(System.in)){
    ResultSet res = statement.executeQuery("select * from user where username='"+scanner.nextLine()+"'and pwd='"+scanner.nextLine()+"';");
    while (res.next()){
        String username = res.getString(1);
        System.out.println(username+" 登陆成功！");
    }
}catch (SQLException e){
    e.printStackTrace();
}
```

用户可以通过自己输入用户名和密码来登陆，乍一看好像没啥问题，那如果我输入的是以下内容呢：

```sql
Test
1111' or 1=1; -- 
# Test 登陆成功！
```

1=1一定是true，那么我们原本的SQL语句会变为：

```sql
select * from user where username='Test' and pwd='1111' or 1=1; -- '
```

我们发现，如果允许这样的数据插入，那么我们原有的SQL语句结构就遭到了破坏，使得用户能够随意登陆别人的账号。因此我们可能需要限制用户的输入来防止用户输入一些SQL语句关键字，但是关键字非常多，这并不是解决问题的最好办法。

### 使用PreparedStatement

我们发现，如果单纯地使用Statement来执行SQL命令，会存在严重的SQL注入攻击漏洞！而这种问题，我们可以使用PreparedStatement来解决：

```java
public static void main(String[] args) throws ClassNotFoundException {
    try (Connection connection = DriverManager.getConnection("URL","用户名","密码");
         PreparedStatement statement = connection.prepareStatement("select * from user where username= ? and pwd=?;");
         Scanner scanner = new Scanner(System.in)){

        statement.setString(1, scanner.nextLine());
        statement.setString(2, scanner.nextLine());
        System.out.println(statement);    //打印查看一下最终执行的
        ResultSet res = statement.executeQuery();
        while (res.next()){
            String username = res.getString(1);
            System.out.println(username+" 登陆成功！");
        }
    }catch (SQLException e){
        e.printStackTrace();
    }
}
```

我们发现，我们需要提前给到PreparedStatement一个SQL语句，并且使用`?`作为占位符，它会预编译一个SQL语句，通过直接将我们的内容进行替换的方式来填写数据。使用这种方式，我们之前的例子就失效了！我们来看看实际执行的SQL语句是什么：

```
com.mysql.cj.jdbc.ClientPreparedStatement: select * from user where username= 'Test' and pwd='123456'' or 1=1; -- ';
```

我们发现，我们输入的参数一旦出现`'`时，会被变为转义形式`\'`，而最外层有一个真正的`'`来将我们输入的内容进行包裹，因此它能够有效地防止SQL注入攻击！

### 管理事务

JDBC默认的事务处理行为是自动提交，所以前面我们执行一个SQL语句就会被直接提交（相当于没有启动事务），所以JDBC需要进行事务管理时，首先要通过Connection对象调用setAutoCommit(false) 方法, 将SQL语句的提交（commit）由驱动程序转交给应用程序负责。

```java
con.setAutoCommit();   //关闭自动提交后相当于开启事务。
// SQL语句
// SQL语句
// SQL语句
con.commit();或 con.rollback();
```

一旦关闭自动提交，那么现在执行所有的操作如果在最后不进行`commit()`来提交事务的话，那么所有的操作都会丢失，只有提交之后，所有的操作才会被保存！也可以使用`rollback()`来手动回滚之前的全部操作！

```java
public static void main(String[] args) throws ClassNotFoundException {
    try (Connection connection = DriverManager.getConnection("URL","用户名","密码");
         Statement statement = connection.createStatement()){

        connection.setAutoCommit(false);  //关闭自动提交，现在将变为我们手动提交
        statement.executeUpdate("insert into user values ('a', 1234)");
        statement.executeUpdate("insert into user values ('b', 1234)");
        statement.executeUpdate("insert into user values ('c', 1234)");

        connection.commit();   //如果前面任何操作出现异常，将不会执行commit()，之前的操作也就不会生效
    }catch (SQLException e){
        e.printStackTrace();
    }
}
```

我们来接着尝试一下使用回滚操作：

```java
public static void main(String[] args) throws ClassNotFoundException {
    try (Connection connection = DriverManager.getConnection("URL","用户名","密码");
         Statement statement = connection.createStatement()){

        connection.setAutoCommit(false);  //关闭自动提交，现在将变为我们手动提交
        statement.executeUpdate("insert into user values ('a', 1234)");
        statement.executeUpdate("insert into user values ('b', 1234)");

        connection.rollback();   //回滚，撤销前面全部操作

        statement.executeUpdate("insert into user values ('c', 1234)");

        connection.commit();   //提交事务（注意，回滚之前的内容都没了）

    }catch (SQLException e){
        e.printStackTrace();
    }
}
```

同样的，我们也可以去创建一个回滚点来实现定点回滚：

```java
public static void main(String[] args) throws ClassNotFoundException {
    try (Connection connection = DriverManager.getConnection("URL","用户名","密码");
         Statement statement = connection.createStatement()){

        connection.setAutoCommit(false);  //关闭自动提交，现在将变为我们手动提交
        statement.executeUpdate("insert into user values ('a', 1234)");
        
        Savepoint savepoint = connection.setSavepoint();   //创建回滚点
        statement.executeUpdate("insert into user values ('b', 1234)");

        connection.rollback(savepoint);   //回滚到回滚点，撤销前面全部操作

        statement.executeUpdate("insert into user values ('c', 1234)");

        connection.commit();   //提交事务（注意，回滚之前的内容都没了）

    }catch (SQLException e){
        e.printStackTrace();
    }
}
```

通过开启事务，我们就可以更加谨慎地进行一些操作了，如果我们想从事务模式切换为原有的自动提交模式，我们可以直接将其设置回去：

```java
public static void main(String[] args) throws ClassNotFoundException {
    try (Connection connection = DriverManager.getConnection("URL","用户名","密码");
         Statement statement = connection.createStatement()){

        connection.setAutoCommit(false);  //关闭自动提交，现在将变为我们手动提交
        statement.executeUpdate("insert into user values ('a', 1234)");
        connection.setAutoCommit(true);   //重新开启自动提交，开启时把之前的事务模式下的内容给提交了
        statement.executeUpdate("insert into user values ('d', 1234)");
        //没有commit也成功了！
    }catch (SQLException e){
        e.printStackTrace();
    }

```

通过学习JDBC，我们现在就可以通过Java来访问和操作我们的数据库了！为了更好地衔接，我们还会接着讲解主流持久层框架——Mybatis，加深JDBC的记忆。

- ## lombok（使用注解，帮助我们生成方法）也就是语法糖

- 个人理解：这个类似于插件，帮我们生成了方法，如构造方法，自己不用写方法，它是在反编译的时候，里面是使用和我们之前写代码一样的方法，我们只需要在类里面写个注解，就可以帮我们生成方法。



我们来看看，使用原生方式和小辣椒方式编写类的区别，首先是传统方式：

```java
public class Student {
    private Integer sid;
    private String name;
    private String sex;

    public Student(Integer sid, String name, String sex) {
        this.sid = sid;
        this.name = name;
        this.sex = sex;
    }

    public Integer getSid() {             //长！
        return sid;
    }

    public void setSid(Integer sid) {     //到！
        this.sid = sid;
    }

    public String getName() {             //爆！
        return name;
    }

    public void setName(String name) {    //炸！
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }
}
```

而使用Lombok之后：

```java
@Getter
@Setter
@AllArgsConstructor
public class Student {
    private Integer sid;
    private String name;
    private String sex;
}
```

我们发现，使用Lombok之后，只需要添加几个注解，就能够解决掉我们之前长长的一串代码！

---

### 使用Lombok

我们通过实战来演示一下Lombok的实用注解：

* 我们通过添加`@Getter`和`@Setter`来为当前类的所有字段生成get/set方法，他们可以添加到类或是字段上，注意静态字段不会生成，final字段无法生成set方法。

  * 我们还可以使用@Accessors来控制生成Getter和Setter的样式。 

* 我们通过添加`@ToString`来为当前类生成预设的toString方法。

* 我们可以通过添加`@EqualsAndHashCode`来快速生成比较和哈希值方法。

* 我们可以通过添加`@AllArgsConstructor`和`@NoArgsConstructor`来快速生成全参构造和无参构造。

* 我们可以添加`@RequiredArgsConstructor`来快速生成参数只包含`final`或被标记为`@NonNull`的成员字段。

* **使用`@Data`能代表`@Setter`、`@Getter`、`@RequiredArgsConstructor`、`@ToString`、`@EqualsAndHashCode`全部注解**。

  * 一旦使用`@Data`就不建议此类有继承关系，因为`equal`方法可能不符合预期结果（尤其是仅比较子类属性）。

* 使用`@Value`与`@Data`类似，但是并不会生成setter并且成员属性都是final的。

* 使用`@SneakyThrows`来自动生成try-catch代码块。

* 使用`@Cleanup`作用与局部变量，在最后自动调用其`close()`方法（可以自由更换）

* 使用`@Builder`来快速生成建造者模式。

  * 通过使用`@Builder.Default`来指定默认值。
  * 通过使用`@Builder.ObtainVia`来指定默认值的获取方式。

  主要用的：

  - @Setter

  - @Getter//这两个常见于封装

  - @RequiredArgsConstructor

  - @ToString//重写tostring方法

  - @EqualsAndHashCode

  - 上面几个可以用一个注解概括：@data，但是，一旦使用`@Data`就不建议此类有继承关系，因为`equal`方法可能不符合预期结果（尤其是仅比较子类属性）。

  - 使用`@SneakyThrows`来自动生成try-catch代码块。//常用扑捉异常

  - 使用`@Cleanup`作用与局部变量，在最后自动调用其`close()`方法（可以自由更换），因为trycatch语法糖没有使用，所以不能自动关闭流之类的，要手动关闭，所以使用这个注解可以快速关闭

  - 使用`@Builder`来快速生成建造者模式。常用于造一个对象，类似于四大函数接口的supplier接口，但是不需要自己写，如果使用接口，要用lambda表达式或者匿名内部类

  - ---

  - 

    # XML简单了解

    ## xml的介绍：

    - xml作用是数据的存储和传输，还有配置文件，如mybatis的配置等，与html有点类似，但是和html是不一样的，因为xml是数据的传输和存储，而html是数据的展示。

    - #### xml与html的区别：

      - 1：xml是用于数据的传输和存储，不用于数据的展示，html才是用于数据的展示
      - 2·xml的标签必须自定义，而html的标签不能自定义
      - 3·html的语法不严格，而xml的语法极其严格
      - 4·xml是需要根节点

      ### 语法规则：

      1·xml必须根节点

      2·头部声明可有可无，

      ```
      <? xml verson"1.0" encoding="utf-8"?>这个是头部声明，不是标签，所以可以可有可无，并且只有一边
      ```

      3·标签要成对出现，大小写是有区别的，标签不能交叉，但是可以嵌套

      4·使用数学符号，如<,>这样的符号会以为你是标签，所以遇到这样的符号，要使用等价的字符：

      ![image-20230326181342523](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303282227690.png)

      ### 属性

      一个标签可以多个属性，属性的值用引号进行括起来

      命令规则数字字母不划线，数字不开头

      xml的属性非常鸡肋

      ---

      - 不解析内容：

      - 如果遇到了大量的数学符号，那么要么使用实体替换，还有一中办法用CDATA

      - CDATA里面的内容不会解析

      - ```xml
        [!CDATA[不解析的内容]]
        ```

        xml解析后，类似于返回一个对象，对象类似于列表，也可以理解为数组吧，object类数组：

        ![image-20230326170455664](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303282227473.png)

    根节点不解析，其他是会解析的，标签就是数组/列表的变量名，如这里，是一个数组，数组的对象是man，man里面有name，age。也就是结构体类似

* - ## mybits（数据库的封装）

  - ### 配置文件：

  - - #### 了解xml语言：

    - xml语言是用于数据的存储和传输的有以下特点：

    - 在开始介绍Mybatis之前，XML语言发明最初是用于数据的存储和传输，它可以长这样：

      ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <outer>
        <name>阿伟</name>
        <desc>怎么又在玩电动啊</desc>
      	<inner type="1">
          <age>10</age>
          <sex>男</sex>
        </inner>
      </outer>
      ```

      如果你学习过前端知识，你会发现它和HTML几乎长得一模一样！但是请注意，虽然它们长得差不多，但是他们的意义却不同，HTML主要用于通过编排来展示数据，而XML主要是存放数据，它更像是一个配置文件！当然，浏览器也是可以直接打开XML文件的。

      一个XML文件存在以下的格式规范：

      * 必须存在一个根节点，将所有的子标签全部包含。
      * 可以但不必须包含一个头部声明（主要是可以设定编码格式）
      * 所有的标签必须成对出现，可以嵌套但不能交叉嵌套
      * 区分大小写。
      * 标签中可以存在属性，比如上面的`type="1"`就是`inner`标签的一个属性，属性的值由单引号或双引号包括。

      XML文件也可以使用注释：

      ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!-- 注释内容 -->
      ```

      通过IDEA我们可以使用`Ctrl`+`/`来快速添加注释文本（不仅仅适用于XML，还支持很多种类型的文件）

      那如果我们的内容中出现了`<`或是`>`字符，那该怎么办呢？我们就可以使用XML的转义字符来代替：

      ![img](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.jxdoc.com%2Fpic%2F28d1ff67caaedd3383c4d358%2F1-332-jpg_6_0_______-505-0-0-505.jpg&refer=http%3A%2F%2Fimg.jxdoc.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1639322216&t=88d1ea1adb9cbf1611eaf4c9fa16b8b0![image-20230325001113965](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303250014930.png)

    - 不需要太深入了解，会用，会配置文件就行；

  #### ==配置文件是为了等下连接数据库的，基本格式在网站下载，cp就行==

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${驱动类（含包名）}"/>
        <property name="url" value="${数据库连接URL}"/>
        <property name="username" value="${用户名}"/>
        <property name="password" value="${密码}"/>
      </dataSource>
    </environment>
  </environments>
</configuration>
```

这个就是配置文件，可以自动连接数据库，记得后缀名是xml，还有记得导入jar包

通过进行配置，我们就告诉了Mybatis我们链接数据库的一些信息，包括URL、用户名、密码等，这样Mybatis就知道该链接哪个数据库、使用哪个账号进行登陆了（也可以不使用配置文件，这里不做讲解，还请各位小伙伴自行阅读官方文档）

配置文件完成后，我们需要在Java程序启动时，让Mybatis对配置文件进行读取并得到一个`SqlSessionFactory`对象：

```java
public static void main(String[] args) throws FileNotFoundException {
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(new FileInputStream("mybatis-config.xml"));//先new一个工厂
    try (SqlSession sqlSession = sqlSessionFactory.openSession(true)){//工厂new一个对象出来
			//暂时还没有业务
    }
}
```



```java
 SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(new FileInputStream("mybatis-config.xml")//这是帮我们连接数据库的，其中xml文件里面已经写了数据库的地址，用户和密码，使用SqlSessionFactoryBuilder()这个类可以用buid方法读取文件进行连接，连接后，会返回一个 SqlSessionFactory类的对象，这对象可以理解为数据库吧，可以创建好多个SqlSession类的对象，这个SqlSession类可以看作是一个会话，比如我一个数据库，3个软件进行数据的读写，那就相当于是3个会话，3个会话可以同时进行，SqlSession类的一个对象也可以看做成statement类的一个对象，也是支持这样的功能：
3个会话都是相互独立的，互不干扰，除非对数据进行修改；比如我在第一个软件上，读取xxx数据，第二个软件上读取kkk的数据，我就可以同时读取。我也可以只用一个软件去读取，只是谁先谁后问题：
这相当于我用SqlSessionFactory（工厂）可以创建2个SqlSession对象，同时操作，也就是2个会话。当然我也可以只用一个对象操作，只是像读取xx或者kkk数据，再读取另一个数据。所以SqlSession类就是这样的关系
```

每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的，我们可以通过`SqlSessionFactory`来创建多个新的会话，`SqlSession`对象，每个会话就相当于我不同的地方登陆一个账号去访问数据库，你也可以认为这就是之前JDBC中的`Statement`对象，会话之间相互隔离，没有任何关联。

而通过`SqlSession`就可以完成几乎所有的数据库操作，我们发现这个接口中定义了大量数据库操作的方法，因此，现在我们只需要通过一个对象就能完成数据库交互了，极大简化了之前的流程。

---



```java
import lombok.Data;

@Data
public class Student {
    int sid;   //名称最好和数据库字段名称保持一致，不然可能会映射失败导致查询结果丢失
    String name;
    String sex;
}
```

在根目录下重新创建一个mapper文件夹，新建名为`TestMapper.xml`的文件作为我们的映射器，并填写以下内容：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="TestMapper">
    <select id="selectStudent" resultType="com.test.entity.Student">
        select * from student
    </select>
</mapper>
```

其中namespace就是命名空间，每个Mapper都是唯一的，因此需要用一个命名空间来区分，它还可以用来绑定一个接口。我们在里面写入了一个select标签，表示添加一个select操作，同时id作为操作的名称，resultType指定为我们刚刚定义的实体类，表示将数据库结果映射为`Student`类，然后就在标签中写入我们的查询语句即可。

编写好后，我们在配置文件中添加这个Mapper映射器：

```xml
<mappers>
    <mapper url="file:mappers/TestMapper.xml"/>
    <!--    这里用的是url，也可以使用其他类型，我们会在后面讲解    -->
</mappers>
```

最后在程序中使用我们定义好的Mapper即可：





动态sql：if条件满足才能进行合并sql主句，用于限制条件居多

### 接口封装：Mapper名称要与接口名称保持一致，select标签中的id要与接口方法中的方法名字保持一致；使用时接口实现是要依靠sqlsession.getMapper语句进行读取mapper的xml文件

==[注意select标签的id一定要与接口方法名字保持一致，Mapper的名称一定要与接口名称保持一样；]()==





## 注解使用mybatis：

与之前不同的是，我们使用注解加接口；再也不用写mapper.xml文件了，如何使用：

- 首先：在config.xml文件，mappers标签，新加一个映射，mapper，该标签里面的属性，是class，因为接口是class。class=接口的地址；
- 如果有多个接口可以使用包
- 接口前面写@xxx注解，比如我要select，那就写@select（“sql语句”），下面是接口的方法，注解的方法；加入和删除以此类推！
- 也可以是使用自定义的映像规则，使用results，里面是写一个数组，可以写多个result注解，result注解可以自定义映射规则，其中column表示数据库的属性，如我数据库有一个字段是sid，那我column=“sid”表示我数据库某个对象的sid，property表示我接收对象的属性，如我student类里面属性是id，那我property=“id”，这样一个result设置，就将数据库对象的sid映射到我需要对象的id里面
- @one可以一对一关系：
- @constructargs({})大括号里面，通过指定的映射类型，进行构造函数的指定，也就是如果有多个构造方法，可以使用此方法来决定需要哪个构造方法
- 如果是多个参数，在sql语句中需要输入多个参数，在形参中，需要@param进行指定哪个形参对应的sql语句参数，在形参的数据类型前面进行注解；@paeam（sid），sid是sql语句中的输入参数sid；
- 如果形参传入的是一个对象，sql语句需要的是对象的某个属性，sql语句参数应该改为（对象.属性），@param（对象），这样就能够对应得上了
- 案例：
- 

```java
public interface UserMapper {
    @Select("SELECT * FROM users WHERE id = #{userId}")
    User getUserById(int userId);

    @Insert("INSERT INTO users (username, password) VALUES (#{username}, #{password})")
    void insertUser(User user);

    @Update("UPDATE users SET password = #{password} WHERE id = #{id}")
    void updateUser(User user);

    @Delete("DELETE FROM users WHERE id = #{id}")
    void deleteUser(int userId);
}

```



```xml
<mappers>
    <mapper class="com.example.mapper.UserMapper"/>
</mappers>

```



```java
SqlSession sqlSession = sqlSessionFactory.openSession();
try {
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

    User user = userMapper.getUserById(1);
    System.out.println(user);

    User newUser = new User("test", "123456");
    userMapper.insertUser(newUser);
    System.out.println(newUser);

    newUser.setPassword("654321");
    userMapper.updateUser(newUser);

    userMapper.deleteUser(newUser.getId());

    sqlSession.commit();
} finally {
    sqlSession.close();
}

```



- ## junit单元测试（导包，注解，方法，run）

- ### 1·导包：单元测试要使用两个jar包

  - ### 2·注解，在方法名字前加上注解，主要的注解有三个@Text@Before@After，

    - @Text注解是测试，@text之后是测试方法
    - @before，注解是之前，表示下面的方法是在text注解的方法跑之前要先执行的
    - @after 之后，表示注解后的方法是在text注解方法跑完后再运行的

  - ### 方法

  - 测试方法满足要求：

    - 1 方法的权限必须是public
    - 2 方法不是静态方法
    - 3返回类型必须是void
    - 4没有形参
    - 最好方法名称就是method（如果多个方法可以后面加序号）

如果想看返回值对不对，使用断言工具，因为断言工具类里面有方法，是判断期盼值与真实值是否一样，不一样是抛出异常。Assert类（断言工具类）

---

使用实操：

我们可以通过断言工具类来进行判定：

```java
public class TestMain {
    @Test
    public void method(){
        System.out.println("我是测试案例！");
        Assert.assertEquals(1, 2);    //参数1是期盼值，参数2是实际测试结果值
    }
}
```

通过运行代码后，我们发现测试过程中抛出了一个错误，并且IDEA给我们显示了期盼结果和测试结果，那么现在我们来测试一个案例，比如我们想查看冒泡排序的编写是否正确：

```java
@Test
public void method(){
    int[] arr = {0, 4, 5, 2, 6, 9, 3, 1, 7, 8};

    //错误的冒泡排序
    for (int i = 0; i < arr.length - 1; i++) {
        for (int j = 0; j < arr.length - 1 - i; j++) {
            if(arr[j] > arr[j + 1]){
                int tmp = arr[j];
                arr[j] = arr[j+1];
                // arr[j+1] = tmp;
            }
        }
    }

    Assert.assertArrayEquals(new int[]{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}, arr);
}
```

通过测试，我们发现得到的结果并不是我们想要的结果，因此现在我们需要去修改为正确的冒泡排序，修改后，测试就能正确通过了。我们还可以再通过一个案例来更加深入地了解测试，现在我们想测试从数据库中取数据是否为我们预期的数据：

```java
@Test
public void method(){
    try (SqlSession sqlSession = MybatisUtil.getSession(true)){
        TestMapper mapper = sqlSession.getMapper(TestMapper.class);
        Student student = mapper.getStudentBySidAndSex(1, "男");

        Assert.assertEquals(new Student().setName("小明").setSex("男").setSid(1), student);
    }
}
```

那么如果我们在进行所有的测试之前需要做一些前置操作该怎么办呢，一种办法是在所有的测试用例前面都加上前置操作，但是这样显然是很冗余的，因为一旦发生修改就需要挨个进行修改，因此我们需要更加智能的方法，我们可以通过`@Before`注解来添加测试用例开始之前的前置操作：

```java
public class TestMain {

    private SqlSessionFactory sqlSessionFactory;
    @Before
    public void before(){
        System.out.println("测试前置正在初始化...");
        try {
            sqlSessionFactory = new SqlSessionFactoryBuilder()
                    .build(new FileInputStream("mybatis-config.xml"));
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        System.out.println("测试初始化完成，正在开始测试案例...");
    }

    @Test
    public void method1(){
        try (SqlSession sqlSession = sqlSessionFactory.openSession(true)){
            TestMapper mapper = sqlSession.getMapper(TestMapper.class);
            Student student = mapper.getStudentBySidAndSex(1, "男");

            Assert.assertEquals(new Student().setName("小明").setSex("男").setSid(1), student);
            System.out.println("测试用例1通过！");
        }
    }

    @Test
    public void method2(){
        try (SqlSession sqlSession = sqlSessionFactory.openSession(true)){
            TestMapper mapper = sqlSession.getMapper(TestMapper.class);
            Student student = mapper.getStudentBySidAndSex(2, "女");

            Assert.assertEquals(new Student().setName("小红").setSex("女").setSid(2), student);
            System.out.println("测试用例2通过！");
        }
    }
}
```

同理，在所有的测试完成之后，我们还想添加一个收尾的动作，那么只需要使用`@After`注解即可添加结束动作：

```java
@After
public void after(){
    System.out.println("测试结束，收尾工作正在进行...");
}
```

---

- ## maven（使jar，代码，xml文件管理更加方便）

- 使用maven：在新建项目的时候选择maven
- ![image-20230326113141067](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303261131146.png)

![image-20230326114141183](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303261141312.png)

main里面的Java是装我们平时写的源代码，text里面的Java（绿色的）是装测试用的Java，pom.xml是写导入jar的，resources是写一些文件，如mybatis的xml文件的。这样子项目结构就会非常清晰

- maven有自己的中心仓库，我们大部分jar包可以在中心仓库下载，它下载之前看我们的本地仓库有没有，有的话直接用

- ### 导入前的知识

了解导入的知识，四要素

那么首先，我们需要了解一下POM文件，它相当于是我们整个Maven项目的配置文件，它也是使用XML编写的：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>MavenTest</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

</project>
```

我们可以看到，Maven的配置文件是以`project`为根节点，而`modelVersion`定义了当前模型的版本，一般是4.0.0，我们不用去修改。

`groupId`、`artifactId`、`version`这三个元素合在一起，用于唯一区别每个项目，别人如果需要将我们编写的代码作为依赖，那么就必须通过这三个元素来定位我们的项目，我们称为一个项目的基本坐标，所有的项目一般都有自己的Maven坐标，因此我们通过Maven导入其他的依赖只需要填写这三个基本元素就可以了，无需再下载Jar文件，而是Maven自动帮助我们下载依赖并导入。

* `groupId` 一般用于指定组名称，命名规则一般和包名一致，比如我们这里使用的是`org.example`，一个组下面可以有很多个项目。
* `artifactId` 一般用于指定项目在当前组中的唯一名称，也就是说在组中用于区分于其他项目的标记。
* `version` 代表项目版本，随着我们项目的开发和改进，版本号也会不断更新，就像LOL一样，每次赛季更新都会有一个大版本更新，我们的Maven项目也是这样，我们可以手动指定当前项目的版本号，其他人使用我们的项目作为依赖时，也可以根本版本号进行选择（这里的SNAPSHOT代表快照，一般表示这是一个处于开发中的项目，正式发布项目一般只带版本号）

`properties`中一般都是一些变量和选项的配置，我们这里指定了JDK的源代码和编译版本为1.8，无需进行修改。

### Maven依赖导入

现在我们尝试使用Maven来帮助我们快速导入依赖，我们需要导入之前的JDBC驱动依赖、JUnit依赖、Mybatis依赖、Lombok依赖，那么如何使用Maven来管理依赖呢？

我们可以创建一个`dependencies`节点：

```xml
<dependencies>
    //里面填写的就是所有的依赖
</dependencies>
```

那么现在就可以向节点中填写依赖了，那么我们如何知道每个依赖的坐标呢？我们可以在：https://mvnrepository.com/ 进行查询（可能打不开，建议用流量，或是直接百度某个项目的Maven依赖），我们直接搜索lombok即可，打开后可以看到已经给我们写出了依赖的坐标：

### ==这样就是导入四要素(重点)==

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.22</version>
    <scope>provided</scope>
</dependency>
```

我们直接将其添加到`dependencies`节点中即可，现在我们来编写一个测试用例看看依赖导入成功了没有：

```java
public class Main {
    public static void main(String[] args) {
        Student student = new Student("小明", 18);
        System.out.println(student);
    }
}
```

```java
@Data
@AllArgsConstructor
public class Student {
    String name;
    int age;
}
```

![image-20230326115159465](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303261151551.png)

- ### 更改下载源

通过流程图我们得知，一个项目依赖一般是存储在中央仓库中，也有可能存储在一些其他的远程仓库（私服），几乎所有的依赖都被放到了中央仓库中，因此，Maven可以直接从中央仓库中下载大部分的依赖（Maven第一次导入依赖是需要联网的），远程仓库中下载之后 ，会暂时存储在本地仓库，我们会发现我们本地存在一个`.m2`文件夹，这就是Maven本地仓库文件夹，默认建立在C盘，如果你C盘空间不足，会出现问题！

在下次导入依赖时，如果Maven发现本地仓库中就已经存在某个依赖，那么就不会再去远程仓库下载了。

可能在导入依赖时，小小伙伴们会出现卡顿的问题，我们建议配置一下IDEA自带的Maven插件远程仓库地址，我们打开IDEA的安装目录，找到`安装根目录/plugins/maven/lib/maven3/conf`文件夹，找到`settings.xml`文件，打开编辑：

找到mirros标签，添加以下内容：

```xml
<mirror>
      <id>nexus-aliyun</id>
      <mirrorOf>*</mirrorOf>
      <name>Nexus aliyun</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror> 
```

这样，我们就将默认的远程仓库地址（国外），配置为国内的阿里云仓库地址了（依赖的下载速度就会快起来了）

---

### Maven依赖作用域

除了三个基本的属性用于定位坐标外，依赖还可以添加以下属性：

- **type**：依赖的类型，对于项目坐标定义的packaging。大部分情况下，该元素不必声明，其默认值为jar
- ==**scope==**：依赖的范围（作用域，着重讲解）
- **optional**：标记依赖是否可选
- **exclusions**：用来排除传递性依赖（一个项目有可能依赖于其他项目，就像我们的项目，如果别人要用我们的项目作为依赖，那么就需要一起下载我们项目的依赖，如Lombok）

我们着重来讲解一下`scope`属性，它决定了依赖的作用域范围：

* **compile** ：为默认的依赖有效范围。如果在定义依赖关系的时候，没有明确指定依赖有效范围的话，则默认采用该依赖有效范围。此种依赖，在编译、运行、测试时均有效。
* **provided** ：在编译、测试时有效，但是在运行时无效，也就是说，项目在运行时，不需要此依赖，比如我们上面的Lombok，我们只需要在编译阶段使用它，编译完成后，实际上已经转换为对应的代码了，因此Lombok不需要在项目运行时也存在。
* **runtime** ：在运行、测试时有效，但是在编译代码时无效。比如我们如果需要自己写一个JDBC实现，那么肯定要用到JDK为我们指定的接口，但是实际上在运行时是不用自带JDK的依赖，因此只保留我们自己写的内容即可。
* **test** ：只在测试时有效，例如：JUnit，我们一般只会在测试阶段使用JUnit，而实际项目运行时，我们就用不到测试了，那么我们来看看，导入JUnit的依赖：

同样的，我们可以在网站上搜索Junit的依赖，我们这里导入最新的JUnit5作为依赖：

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.8.1</version>
    <scope>test</scope>
</dependency>
```

我们所有的测试用例全部编写到Maven项目给我们划分的test目录下，位于此目录下的内容不会在最后被打包到项目中，只用作开发阶段测试使用：

```java
public class MainTest {

    @Test
    public void test(){
        System.out.println("测试");
      	//Assert在JUnit5时名称发生了变化Assertions
        Assertions.assertArrayEquals(new int[]{1, 2, 3}, new int[]{1, 2});
    }
}
```

因此，一般仅用作测试的依赖如JUnit只保留在测试中即可，那么现在我们再来添加JDBC和Mybatis的依赖：

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.27</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
</dependency>
```

我们发现，Maven还给我们提供了一个`resource`文件夹，我们可以将一些静态资源，比如配置文件，放入到这个文件夹中，项目在打包时会将资源文件夹中文件一起打包的Jar中，比如我们在这里编写一个Mybatis的配置文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <setting name="cacheEnabled" value="true"/>
        <setting name="logImpl" value="JDK_LOGGING" />
    </settings>
    <!-- 需要在environments的上方 -->
    <typeAliases>
        <package name="com.test.entity"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/study"/>
                <property name="username" value="test"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper class="com.test.mapper.TestMapper"/>
    </mappers>
</configuration>
```

现在我们创建一下测试用例，顺便带大家了解一下Junit5的一些比较方便的地方：

```java
public class MainTest {

    //因为配置文件位于内部，我们需要使用Resources类的getResourceAsStream来获取内部的资源文件
    private static SqlSessionFactory factory;

    //在JUnit5中@Before被废弃，它被细分了：
    @BeforeAll // 一次性开启所有测试案例只会执行一次 (方法必须是static)
    // @BeforeEach 一次性开启所有测试案例每个案例开始之前都会执行一次
    @SneakyThrows
    public static void before(){
        factory = new SqlSessionFactoryBuilder()
                .build(Resources.getResourceAsStream("mybatis.xml"));
    }


    @DisplayName("Mybatis数据库测试")  //自定义测试名称
    @RepeatedTest(3)  //自动执行多次测试
    public void test(){
        try (SqlSession sqlSession = factory.openSession(true)){
            TestMapper testMapper = sqlSession.getMapper(TestMapper.class);
            System.out.println(testMapper.getStudentBySid(1));
        }
    }
}
```

那么就有人提问了，如果我需要的依赖没有上传的远程仓库，而是只有一个Jar怎么办呢？我们可以使用第四种作用域：

* **system**：作用域和provided是一样的，但是它不是从远程仓库获取，而是直接导入本地Jar包：

```xml
<dependency>
     <groupId>javax.jntm</groupId>
     <artifactId>lbwnb</artifactId>
     <version>2.0</version>
     <scope>system</scope>
     <systemPath>C://学习资料/4K高清无码/test.jar</systemPath>
</dependency>
```

比如上面的例子，如果scope为system，那么我们需要添加一个systemPath来指定jar文件的位置，这里就不再演示了。

----



- ### 排除依赖（主要用于降低升级jar包的版本）

- Maven可选依赖

  当项目中的某些依赖不希望被使用此项目作为依赖的项目使用时，我们可以给依赖添加`optional`标签表示此依赖是可选的，默认在导入依赖时，不会导入可选的依赖：

  ```xml
  <optional>true</optional>
  ```

  比如Mybatis的POM文件中，就存在大量的可选依赖：

  ```xml
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.30</version>
    <optional>true</optional>
  </dependency>
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.30</version>
    <optional>true</optional>
  </dependency>
  <dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
    <optional>true</optional>
  </dependency>
   ...
  ```

  由于Mybatis要支持多种类型的日志，需要用到很多种不同的日志框架，因此需要导入这些依赖来做兼容，但是我们项目中并不一定会使用这些日志框架作为Mybatis的日志打印器，因此这些日志框架仅Mybatis内部做兼容需要导入使用，而我们可以选择不使用这些框架或是选择其中一个即可，也就是说我们导入Mybatis之后想用什么日志框架再自己加就可以了。

  ### Maven排除依赖

  我们了解了可选依赖，现在我们可以让使用此项目作为依赖的项目默认不使用可选依赖，但是如果存在那种不是可选依赖，但是我们导入此项目有不希望使用此依赖该怎么办呢，这个时候我们就可以通过排除依赖来防止添加不必要的依赖：

  ```xml
  <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.8.1</version>
      <scope>test</scope>
      <exclusions>
          <exclusion>
              <groupId>org.junit.jupiter</groupId>
              <artifactId>junit-jupiter-engine</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  ```

  我们这里演示了排除JUnit的一些依赖，我们可以在外部库中观察排除依赖之后和之前的效果。

  ### Maven继承关系

  一个Maven项目可以继承自另一个Maven项目，比如多个子项目都需要父项目的依赖，我们就可以使用继承关系来快速配置。

  我们右键左侧栏，新建一个模块，来创建一个子项目：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <parent>
          <artifactId>MavenTest</artifactId>
          <groupId>org.example</groupId>
          <version>1.0-SNAPSHOT</version>
      </parent>
      <modelVersion>4.0.0</modelVersion>
  
      <artifactId>ChildModel</artifactId>
  
      <properties>
          <maven.compiler.source>8</maven.compiler.source>
          <maven.compiler.target>8</maven.compiler.target>
      </properties>
  
  </project>
  ```

  我们可以看到，IDEA默认给我们添加了一个parent节点，表示此Maven项目是父Maven项目的子项目，子项目直接继承父项目的`groupId`，子项目会直接继承父项目的所有依赖，除非依赖添加了optional标签，我们来编写一个测试用例尝试一下:

  ```java
  import lombok.extern.java.Log;
  
  @Log
  public class Main {
      public static void main(String[] args) {
          log.info("我是日志信息");
      }
  }
  ```

  可以看到，子项目也成功继承了Lombok依赖。

  我们还可以让父Maven项目统一管理所有的依赖，包括版本号等，子项目可以选取需要的作为依赖，而版本全由父项目管理，我们可以将`dependencies`全部放入`dependencyManagement`节点，这样父项目就完全作为依赖统一管理。

  ```xml
  <dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
              <version>1.18.22</version>
              <scope>provided</scope>
          </dependency>
          <dependency>
              <groupId>org.junit.jupiter</groupId>
              <artifactId>junit-jupiter</artifactId>
              <version>5.8.1</version>
              <scope>test</scope>
          </dependency>
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>8.0.27</version>
          </dependency>
          <dependency>
              <groupId>org.mybatis</groupId>
              <artifactId>mybatis</artifactId>
              <version>3.5.7</version>
          </dependency>
      </dependencies>
  </dependencyManagement>
  ```

  我们发现，子项目的依赖失效了，因为现在父项目没有依赖，而是将所有的依赖进行集中管理，子项目需要什么再拿什么即可，同时子项目无需指定版本，所有的版本全部由父项目决定，子项目只需要使用即可：

  ```xml
  <dependencies>
      <dependency>
          <groupId>org.projectlombok</groupId>
          <artifactId>lombok</artifactId>
          <scope>provided</scope>
      </dependency>
  </dependencies>
  ```

  当然，父项目如果还存在dependencies节点的话，里面的内依赖依然是直接继承：

  ```xml
  <dependencies>
      <dependency>
          <groupId>org.junit.jupiter</groupId>
          <artifactId>junit-jupiter</artifactId>
          <version>5.8.1</version>
          <scope>test</scope>
      </dependency>
  </dependencies>
  
  <dependencyManagement>
      <dependencies>
        ...
  ```

  

  ### Maven常用命令

我们可以看到在IDEA右上角Maven板块中，每个Maven项目都有一个生命周期，实际上这些是Maven的一些插件，每个插件都有各自的功能，比如：

* `clean`命令，执行后会清理整个`target`文件夹，在之后编写Springboot项目时可以解决一些缓存没更新的问题。
* `validate`命令可以验证项目的可用性。
* `compile`命令可以将项目编译为.class文件。
* `install`命令可以将当前项目安装到本地仓库，以供其他项目导入作为依赖使用
* `verify`命令可以按顺序执行每个默认生命周期阶段（`validate`，`compile`，`package`等）

### Maven测试项目

通过使用`test`命令，可以一键测试所有位于test目录下的测试案例，请注意有以下要求：

* 测试类的名称必须是以`Test`结尾，比如`MainTest`
* 测试方法上必须标注`@Test`注解，实测`@RepeatedTest`无效

这是由于JUnit5比较新，我们需要重新配置插件升级到高版本，才能完美的兼容Junit5：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <!-- JUnit 5 requires Surefire version 2.22.0 or higher -->
            <version>2.22.0</version>
        </plugin>
    </plugins>
</build>
```

现在`@RepeatedTest`、`@BeforeAll`也能使用了。

### Maven打包项目

我们的项目在编写完成之后，要么作为Jar依赖，供其他模型使用，要么就作为一个可以执行的程序，在控制台运行，我们只需要直接执行`package`命令就可以直接对项目的代码进行打包，生成jar文件。

当然，以上方式仅适用于作为Jar依赖的情况，如果我们需要打包一个可执行文件，那么我不仅需要将自己编写的类打包到Jar中，同时还需要将依赖也一并打包到Jar中，因为我们使用了别人为我们通过的框架，自然也需要运行别人的代码，我们需要使用另一个插件来实现一起打包：

```xml
<plugin>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.1.0</version>
    <configuration>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
        <archive>
            <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.test.Main</mainClass>
            </manifest>
        </archive>
    </configuration>
    <executions>
        <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

在打包之前也会执行一次test命令，来保证项目能够正常运行，当测试出现问题时，打包将无法完成，我们也可以手动跳过，选择`执行Maven目标`来手动执行Maven命令，输入`mvn package -Dmaven.test.skip=true `来以跳过测试的方式进行打包。

最后得到我们的Jar文件，在同级目录下输入`java -jar xxxx.jar`来运行我们打包好的Jar可执行程序（xxx代表文件名称）

* `deploy`命令用于发布项目到本地仓库和远程仓库，一般情况下用不到，这里就不做讲解了。
* `site`命令用于生成当前项目的发布站点，暂时不需要了解。

我们之前还讲解了多模块项目，那么多模块下父项目存在一个`packing`打包类型标签，所有的父级项目的packing都为pom，packing默认是jar类型，如果不作配置，maven会将该项目打成jar包。作为父级项目，还有一个重要的属性，那就是modules，通过modules标签将项目的所有子项目引用进来，在build父级项目时，会根据子模块的相互依赖关系整理一个build顺序，然后依次build。

***

## maven生命周期：

- 生命周期，双击命令，执行命令

- clean：清理target文件夹，清理缓存，解决缓存没有刷新的问题

- compile：编译为.class文件（一般用不上，因为idea是集成开发环境）

- install：将当前项目作为jar包进行安装到本地仓库，供给别人的使用，像junit就是jar包，但是也要依赖别人的jar包

- text：帮助text测试，其实就是省去自己点击运行

  ---

  - 计算机网络基础：

  互联网所有的资源都会有一个url/域名，都会被解析成ip地址

  

  

  ---

  

  ## html前端知识

  - ### xml与html的区别：

    - 1：xml是用于数据的传输和存储，不用于数据的展示，html才是用于数据的展示

    - 2·xml的标签必须自定义，而html的标签不能自定义

    - 3·html的语法不严格，而xml的语法极其严格

    - 4·xml是需要根节点

    - ### 新建一个html项目
  
    - - 新建项目->web模块(java空项目也行)->新建html5文件，文件名字通常为index。最好在idea设置->工具->web浏览器预览重新加载行为更改为变更时
  
        
        
        ## html的语言规范
        
        #### 一·html通常会分为两个部分，头部和主体，（head和body）
        
        头部主要是包含页面的标题，也就是浏览器标签页的标题在这里还可以存储一些设置，导入css，js的内容
        
        #### 2·我们发现现在什么东西都没有，但是在浏览器的标签位置显示了网页的名称为`Title`，并且显示了一个IDEA的图标作为网页图标。
        
        现在我们稍微进行一些修改：
        
        ```html
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">//这里设置编码格式
            <title>lbw的直播间</title>//这里是浏览器标签页显示
        </head>
        <body>
            现在全体起立
        </body>
        </html>
        ```
        
        再次打开浏览器，我们发现页面中出现了我们输入的文本内容，并且标题也改为了我们自定义的标题。
        
        我们可以在设置->工具->Web浏览器和预览中将重新加载页面规则改为`变更时`，这样我们使用内置浏览器或是外部浏览器，可以自动更新我们编写的内容。
        
        我们还可以在页面中添加一个图片，随便将一张图片放到html文件的同级目录下，命名为`image.xxx`，其中xxx是后缀名称，不要修改，我们在body节点中添加以下内容：
        
        ```html
        <img width="300" src="image.xxx" alt="剑光如我，斩尽牛杂">//第一个属性是设置大小，300像素。第二个属性是设置文件来源，第三个是当文件加载不了的时候，显示的文字
        <!--  注意xxx替换成对应的后缀名称  -->
        ```
        
        我们发现，我们的页面中居然能够显示我们添加的图片内容。因此，我们只需要编写对应的标签，浏览器就能够自动识别为对应的组件，并将其展示到我们的浏览器窗口中。
        
        我们再来看看插入一个B站的视频，很简单，只需要到对应的视频下方，找到分享，我们看到有一个嵌入代码：
        
        ```html
        <iframe src="//player.bilibili.com/player.html?aid=333231998&bvid=BV1rA411g7q8&cid=346917516&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="800" height="500"> </iframe>
        ```
        
        每一个页面都是通过这些标签来编写的，几乎所有的网站都是使用HTML编写页面。
        
      - 3·
      
      - 我们首先来看头部，我们之前使用的HTML文件中头部包含了这些内容：
      
        ```html
        <meta charset="UTF-8">
        <title>lbw的直播间</title>
        ```
      
        首先`meta`标签用于定义页面的一些元信息，这里使用它来定义了一个字符集（编码格式），一般是UTF-8，下面的`title`标签就是页面的标题，会显示在浏览器的上方。我们现在来给页面设置一个图标，图标一般可以在字节跳动的IconPark网站找到：https://iconpark.oceanengine.com/home，选择一个自己喜欢的图标下载即可。
      
        将图标放入到项目目录中，并命名为icon.png，在HTML头部添加以下内容：
      
        ```html
        <link rel="icon" href="icon.png" type="image/x-icon" />
        ```
      
        `link`标签用于关联当前HTML页面与其他资源的关系，关系通过`rel`属性指定，这里使用的是icon表示这个文件是当前页面图标。
      
        现在访问此页面，我们发现页面的图标已经变成我们指定的图标样式了。
      
        现在我们再来看主体，我们可以在主体内部编写该页面要展示的所有内容，比如我们之前就用到了img标签来展示一个图片，其中每一个标签都称为一个元素：
      
        ```html
        <img width="300" src="image.xxx" alt="当图片加载失败时，显示的文本">
        ```
      
        我们发现，这个标签只存在一个，并没有成对出现，HTML中有些标签是单标签，也就是说只有这一个，还有一些标签是双标签，必须成对出现，HTML中，也不允许交叉嵌套，但是出现交叉嵌套时，浏览器并不会提示错误，而是仍旧尝试去解析这些内容，甚至会帮助我们进行一定程度的修复，比如：
      
        ```html
        <body>
        
            <iframe src="//player.bilibili.com/player.html?aid=333231998&bvid=BV1rA411g7q8&cid=346917516&page=1" width="800" height="500"
                    scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true">
        </body>
        </iframe>
        ```
      
        很明显上面的代码已经出现交叉嵌套的情况了，但是依然能够在浏览器中正确地显示。
      
        在主体中，我们一般使用div标签来分割页面：
      
        ```html
        <body>
            <div>我是第一块</div>
            <div>我是第二块</div>
        </body>
        ```
      
        通过使用`div`标签，我们将整个页面按行划分，而高度就是内部元素的高度，那么如果只希望按元素划分，也就是说元素占多大就划分多大的空间，那么我们就可以使用`span`标签来划分：
      
        ```html
        <body>
            <div>
                <span>我是第一块第一个部分</span>
                <span>我是第一块第二个部分</span>//这个是用于分区域的，就是div分一整行后，可以再进行划分
            </div>
            <div>我是第二块</div>//这个是用于表示板块的，以行进行划分，除非指定多么的大，不然整行都是它的
        </body>
        ```
      
        我们也可以使用`p`段落标签，它一般用于文章分段：
      
        ```html
        <body>
            <p>
                你看这个彬彬啊，才喝几罐就醉了，真的太逊了。 这个彬彬就是逊呀！
                听你这么说，你很勇哦？ 开玩笑，我超勇的，超会喝的啦。
                超会喝，很勇嘛。身材不错哦，蛮结实的嘛。
            </p>
            <p>
                哎，杰哥，你干嘛啊。都几岁了，还那么害羞！我看你，完全是不懂哦！
                懂，懂什么啊？ 你想懂？我房里有一些好康的。
                好康，是新游戏哦！ 什么新游戏，比游戏还刺激！
            </p>
            <p>
                杰哥，这是什么啊？ 哎呦，你脸红啦！来，让我看看。
                不要啦！！ 让我看看嘛。 不要啦，杰哥，你干嘛啊！//p是用于文章的分段
                让我看看你法语正不正常啊！
            </p>
        </body>
        ```
      
        那么如果遇到特殊字符该怎么办呢？和XML一样，我们可以使用转义字符
      
        ![image-20230329123753326](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303291255654.png)
      
        **注意：**多个连续的空格字符只能被识别为一个，如果需要连续多个必须使用转义字符，同时也不会识别换行，换行只会变成一个空格，需要换行必须使用`br`标签。
      
        通过了解了HTML的一些基础语法，我们现在就知道一个页面大致是如何编写了。
      
        ### HTML常用标签
      
        前面我们已经了解了HTML的基本语法规范，那么现在我们就来看看，有哪些常用的标签吧，首先是换行和分割线：
      
        * br 换行
        * hr 分割线
      
        ```html
        <body>
            <div>
                我是一段文字<br>我是第二段文字
            </div>
            <hr>
            <div>我是底部文字</div>
        </body>
        ```
      
        标题一般用h1到h6表示，我们来看看效果：
      
        ```html
        <body>
        <h1>一级标题</h1>
        <h2>二级标题</h2>
        <h3>三级标题</h3>
        <h4>四级标题</h4>
        <h5>五级标题</h5>
        <h6>六级标题</h6>
        <p>我是正文内容，真不错。</p>
        </body>
        ```
      
        现在我们来看看超链接，我们可以添加一个链接用于指向其他网站：
      
        ```html
        <a href="https://www.bilibili.com">点击访问小破站</a>
        ```
      
        我们也可以指定页面上的一个锚点进行滚动：
      
        ```html
        <body>
        <a href="#test">跳转锚点</a>
        <img src="image.jpeg" width="500">
        <img src="image.jpeg" width="500">
        <img src="image.jpeg" width="500">
        <img src="image.jpeg" width="500">
        <div id="test">我是锚点</div>
        <img src="image.jpeg" width="500">
        <img src="image.jpeg" width="500">
        <img src="image.jpeg" width="500">
        </body>
        ```
      
        每个元素都可以有一个id属性，我们只需要给元素添加一个id属性，就使用a标签可以跳转到一个指定锚点。
      
        我们接着来看看列表元素，这是一个无需列表，其中每一个`li`表示一个列表项：
      
        ```html
        <ul>
            <li>一号选项</li>
            <li>二号选项</li>
            <li>三号选项</li>
            <li>四号选项</li>
            <li>五号选项</li>
        </ul>
        ```
      
        我们也可以使用`ol`来显示一个有序列表：
      
        ```html
        <ol>
            <li>一号选项</li>
            <li>二号选项</li>
            <li>三号选项</li>
            <li>四号选项</li>
            <li>五号选项</li>
        </ol>
        ```
      
        表格也是很重要的一种元素，但是它编写起来相对有一点麻烦：
      
        ```html
        <table>
            <thead>
            <tr>
                <th>学号</th>
                <th>姓名</th>
                <th>性别</th>
                <th>年级</th>
            </tr>
            </thead>
            <tbody>
            <tr>
                <td>0001</td>
                <td>小明</td>
                <td>男</td>
                <td>2019</td>
            </tr>
            <tr>
                <td>0002</td>
                <td>小红</td>
                <td>女</td>
                <td>2020</td>
            </tr>
            </tbody>
        </table>
        ```
      
        虽然这样生成了一个表格，但是这个表格并没有分割线，并且格式也不符合我们想要的样式，那么如何才能修改这些基础属性的样式呢，我们就需要聊聊CSS了。
      
        ### 总结常用标签：
      
        - div划分板块，通常以行作为单位
      
        - span划分区域
      
        - p是常用于文章的段落
      
        - br是换行的标签
      
        - nbsp是空格的标签，因为换行或者多个空格都是认为一个空格，所以常用换行或者空格标签进行换行或者多空格
      
        - img是插入图片的标签
      
        - hr 分割线
      
        - <h1>  这样子表示一级标题，h1-h6</h1>
      
        ![image-20230329124844941](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303291255600.png)
      
        a标签常用于超链接，或者跳转瞄点，如果使用跳转瞄点，经常与div一起用，div可以设置属性id，跳转指定的idiv
      
        ```html
        <a href="https://www.bilibili.com">点击访问小破站</a>这是跳转网站
        ```
  
  
  
  
  
  ```html
  <a href="#test">跳转锚点</a>
  
  <div id="test">我是锚点</div>
  
  </body>
  ```
  
  这个是跳转瞄点
  
  
  
  - ol和ul都是列表的标签，但是区别是ol是列表前有数字，ul是没有数字是使用- 的
  
  - #### 表格：
  
  - thead表示表头
  
  - tr表示一行（在表头中）
  
  - th表示一列（在表头中）
  
  - tbody表示表的主体
  
  - td表示一列（在表的主体里）
  
  - ----
  
  - ---
  
  
  
  ### 表单：主要是输入框，提交内容到后台
  
  - ==1input标签表示输入==，大多数的input都是在label标签里面，label标签主要用于提示，输出文字，类似于span标签，和span效果一样，但是label标签可以使点击文字将光标自动移动到输入框里面，；input可以不包含在label标签里面，可以为input添加一个属性，id号，将label加个for+input的id；
  
  - ==输入框:input== ,input的属性有一个是type，这里是可以选择输入的类型：主要的类型有：button按钮，checkbox勾选项，color颜色，password，date….最常用的是text，因为是写入，当然是文本框大多数
  
  - ==创建按钮：==
  
  - 第一种方法：使用botton标签
  
  - 第二种方法：使用input标签，但是type是botton或者submit，建议使用第二种方法
  
  - 以上内容使用form标签包起来。也就是表单
  
  - ----
  
  - 表单就像其名字一样，用户在页面中填写了对应的内容，点击按钮就可以提交到后台，比如登陆界面，就可以使用表单来实现：
  
    一个网页中最重要的当属输入框和按钮了，那么我们来看看如何创建一个输入框和按钮：
  
    ```html
    <label>
        我是输入框
        <input type="text">
    </label>
    ```
  
    对于一个输入框，我们一般会将其包括在一个`lable`标签中，它和span效果一样，但是我们点击前面文字也能快速获取输入框焦点。
  
    ```html
    <body>
    <div>登陆我们的网站</div>
    <hr>
    <div>
        <label>
            账号：
            <input type="text">
        </label>
    </div>
    <div>
        <label>
            密码：
            <input type="password">
        </label>
    </div>
    </body>
    ```
  
    输入框可以有很多类型，我们来试试看password，现在输入内容就不会直接展示原文了。
  
    创建一个按钮有以下几种方式，在学习JavaWeb时，我们更推荐第二种方式，我们后面进行登陆操作需要配合表单使用：
  
    ```html
    <button>登陆</button>
    <input type="submit" value="登陆">
    <input type="button" value="登陆">
    ```
  
    现在我们就可以写一个大致的登陆页面了：
  
    ```html
    <body>
        <h1>登陆我们的网站</h1>
        <form>
            <div>
                <label>
                    账号：
                    <input type="text" placeholder="Username...">
                </label>
            </div>
            <div>
                <label>
                    密码：
                    <input type="password" placeholder="Password...">
                </label>
            </div>
            <br>
            <a href="https://www.baidu.com">忘记密码</a>
            <br>
            <br>
            <div>
                <input type="submit" value="登陆">
            </div>
        </form>
    </body>
    ```
  
    表单一般使用`form`标签将其囊括，但是现在我们还用不到表单提交，因此之后我们再来讲解表单的提交。
  
    `input`只能实现单行文本，那么如何实现多行文本呢？
  
    ```html
    <label>
        这是我们的文本框<br>
        <textarea placeholder="文本内容..." cols="10" rows="10"></textarea>
    </label>
    ```
  
    我们还可以指定默认的行数和列数，拖动左下角可以自定义文本框的大小。
  
    我们还可以在页面中添加勾选框：
  
    ```html
    <label>
        <input type="checkbox">
        我同意本网站的隐私政策
    </label>
    ```
  
    上面演示的是一个多选框，那么我们来看看单选框：
  
    ```html
    <label>
        <input type="radio" name="role">
        学生
    </label>
    <label>
        <input type="radio" name="role">
        教师
    </label>
    ```
  
    这里需要使用name属性进行分组，同一个组内的选项只能选择一个。
  
    我们也可以添加列表让用户进行选择，创建一个下拉列表：
  
    ```html
    <label>
        登陆身份：
        <select>
            <option>学生</option>
            <option>教师</option>
        </select>
    </label>
    ```
  
    默认选取的是第一个选项，我们可以通过`selected`属性来决定默认使用的是哪个选项。
  
    当然，HTML的元素远不止我们所提到的这些，有关更多HTML元素的内容，可以自行了解。
  
    
  
    ---
    
    css样式（控制器）：作用（高度自定义）
    
    - 创建：使用新建项目，名称为style的.css文件，在html头部加入
    
    - ```html
      <link href="style.css" rel=" stylesheet">
      ```
    
    - css中的boby是标签选择器，、
    
    - 在css花括号里面书写的东西是某个标签的规则
    
    - 如果设定单个标签或者多个，可以使用某个标签的id，也就是某个标签的属性，可以多个id，或者使用第二个办法，在标签中添加style属性（可以不用在css里面写），第一个办法具体到某个id，#id表示选则id的标签
    
    - 还需注意组合与优先级
    
    ## CSS样式
    
    之前我们编写的页面非常基础，我们只能通过一些很基本的属性来排列我们的页面元素，那么如何实现更高度的自定义呢，我们就需要用到CSS来自定义样式，首先我们创建一个名为`style.css`的文件。
    
    首先在我们HTML文件的头部添加：
    
    ```html
    <link href="style.css" rel="stylesheet">
    ```
    
    我们在CSS文件中添加以下内容：
    
    ```css
    body {
        text-align: center;
    }
    ```
    
    我们发现，网页的内容全部变为居中显示了，这正是css在生效，相当于我们现在给页面添加了自定义的样式规则。
    
    当然，我们也可以选择不使用CSS，而是直接对某个元素添加样式：
    
    ```html
    <body style="text-align: center;">
      ...
    ```
    
    这样的效果其实是等同于上面的css文件的，相当于我们直接把样式定义在指定元素上。
    
    也可以在头部直接定义样式，而不是使用外部文件：
    
    ```html
    <style>
        body {
            text-align: center;
        }
    </style>
    ```
    
    使用以上三种方式都可以自定义页面的样式，我们推荐使用还是第一种，不然我们的代码会很繁杂。
    
    样式的属性是非常多的，我们不可能一个一个全部讲完，视频中用到什么再来讲解什么，如果同学们感兴趣，可以自行下去了解。
    
    ### CSS选择器
    
    我们首先来了解一下选择器，那么什么是选择器呢？我们想要自定义一个元素的样式，那么我们肯定要去选择某个元素，只有先找到要自定义的元素，我们才能开始编写样式。
    
    我们上面的例子中使用的就是标签名选择器，它可以快速选择页面中所有指定的的标签，比如我们之前使用的就是`body`标签，那么就相当于页面中所有的body元素全都使用此样式，那么我们现在来试试看选择页面中所有的`input`标签：
    
    ```css
    input {
        width: 200px;
    }
    ```
    
    我们发现，页面中所有的`input`元素宽度全部被设定为了200个像素（`px`是单位大小，代表像素，除了`px`还有`em`和`rem`，他们是根据当前元素字体大小决定的相对大小，一般用于适配各种大小的浏览器窗口，这里暂时不用）
    
    样式编写完成后，如果只有一个属性，可以不带`;`若多个属性则每个属性后面都需要添加一个`;`
    
    因此，一个标签选择器的格式为：
    
    ```css
    标签名称 {
      属性名称: 属性值
    }
    ```
    
    我们还可以设定输入框的字体大小、行高等：
    
    ```css
    input {
        width: 200px;
        font-size: 20px;
        line-height: 40px;
    }
    ```
    
    我们现在可以通过选择器快速地去设置某个元素样式了，那么如何实现只设置某个元素的样式呢，现在我们来看看，id选择器，我们之前已经讲解过了，每个元素都可以有一个id属性，我们可以将其当做一个跳转的锚点使用，而现在，我们可以使用css来进行定位：
    
    我们先为元素添加id属性：
    
    ```html
    <h1 id="title">登陆我们的网站</h1>
    ```
    
    现在使用CSS选择我们的元素，并设定一个属性，选择某个id需要在前面加上一个`#`：
    
    ```css
    #title {
        color: red;
    }
    ```
    
    虽然id选择器已经可以很方便的指定某个元素，但是如果我们希望n个但不是元素都被选择，id选择器就无法实现了，因为每个元素的id是唯一的，不允许出现重复id的元素，因此接着我们来讲解一下类选择器。
    
    每个元素都可以有一个`class`属性，表示当前元素属于某个类（注意这里的类和我们Java中的类概念完全不同）一个元素可以属于很多个类，一个类也可以被很多个元素使用：
    
    ```html
    <form>
        <div >
            <label class="test">
                账号：
                <input type="text" placeholder="Username...">
            </label>
        </div>
        <div>
            <label class="test">
                密码：
                <input type="password" placeholder="Password...">
            </label>
        </div>
    </form>
    ```
    
    上面的例子中，两个`label`元素都使用了`test`类（类名称是我们自定义的），现在我们在css文件中编写以下内容来以类进行选择：
    
    ``` css
    .test{
        color: blue;
    }
    ```
    
    我们发现，两个标签的文本内容都变为了蓝色，因此使用类选择器，能够对所有为此类的元素添加样式。注意在进行类选择时，我们需要在类名前面加上`.`来表示。
    
    ### 组合选择器和优先级问题
    
    我们也可以让多个选择器，共用一个css样式：
    
    ```css
    .test, #title {
        color: red;
    }
    ```
    
    只需要并排写即可，注意中间需要添加一个英文的逗号用于分割，我们也可以使用`*`来一次性选择所有的元素：
    
    ```css
    * {
        color: red;
    }
    ```
    
    我们还可以选择位于某个元素内的某个元素：
    
    ```css
    div label {
        color: red;
    }
    ```
    
    这样的话，就会选择所有位于div元素中的label元素。
    
    当然，我们这里只介绍了一些常用的选择器，有关详细的CSS选择器可以查阅：https://www.runoob.com/cssref/css-selectors.html
    
    我们接着来看一下选择器的优先级：
    
    ![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303292114953.png)
    
    我们根据上面的信息，来测试一下，首先编写一下HTML文件：
    
    ```html
    <body>
        <div class="test" id="simple" style="color: blue">我是测试文本内容</div>
    </body>
    ```
    
    现在我们来编写一下css文件：
    
    ```css
    .test {
        color: yellow;
    }
    
    #simple {
        color: red;
    }
    
    * {
        color: palegreen;
    }
    ```
    
    那么现在我们可以看到，实际上生效的是我们直接编写在标签内部的内联属性，那么现在我们依次进行移除，来看看它们的优先级。
    
    那么如果我们希望某个属性无视任何的优先级，我们可以在属性后面添加`!important`标记，表示此属性是一个重要属性，它的优先级会被置为最高。
    
    **思考：**那要是我每个选择器的这个属性后面都加一个`!important`会怎么样？
    
    ### 自定义边距
    
    我们来看看，如何使用css控制一个div板块的样式，首先编写以下代码，相当于一个div嵌套了一个div元素：
    
    ```html
    <div id="outer">
        <div id="inner">
            
        </div>
    </div>
    ```
    
    现在编写一下自定义的css样式，我们将div设定为固定大小，并且背景颜色添加为绿色：
    
    ```css
    #outer {
        background: palegreen;
        width: 300px;
        height: 300px;
    }
    ```
    
    我们发现左侧快速预览页面存在空隙，这是因为浏览器给我们添加了一个边距属性，我们只需要覆盖此属性并将其设定为0即可：
    
    ```css
    body {
        margin: 0;
    }
    ```
    
    现在我们给内部嵌套的div也设定一个大小，并将颜色设定为橙色：
    
    ```css
    #inner {
        background: darkorange;
        width: 100px;
        height: 100px;
    }
    ```
    
    现在我们发现内部的div元素位于右上角，我们还可以以百分比的形式来指定大小：
    
    ```css
    #inner {
        background: darkorange;
        width: 100%;
        height: 100%;
    }
    ```
    
    百分比会依照当前可用大小来进行分配，比如当前位于一个div内部，并且外部div元素是固定大小300px，因此100%就相当于使用了外部的全部大小，也是300px，现在内部元素完全将外部元素覆盖了，整个元素现在呈现为橙色。
    
    我们可以为一个元素设定边距，边距分为外边距和内边距，外部元素内边距决定了内部元素与外部元素之间的间隔，我们来修改一下css样式：
    
    ```css
    #outer {
        background: palegreen;
        width: 300px;
        height: 300px;
        padding: 10px;
    }
    ```
    
    我们发现，内部的div元素小了一圈，这是因为外部div元素设定了内边距，上下左右都被设定为10px大小。
    
    而我们发现，实际上我们在一开始也是将body的外边距设定为了0，整个页面跟浏览器窗口直接间隔0px的宽度。
    
    
    
    
    
    
    
    ---
    
    
    
    
    
    
    
    ## JavaScript语言
    
    也称为js，是我们整个前端基础的重点内容，只有了解了JavaScript语言，我们才能了解前端如何与后端交互。
    
    JavaScript与Java没有毛关系，仅仅只是名字中包含了Java而已，跟Java比起来，它更像Python，它是一门解释型语言，不需要进行编译，它甚至可以直接在浏览器的命令窗口中运行。
    
    它相当于是前端静态页面的一个补充，它可以让一个普通的页面在后台执行一些程序，比如我们点击一个按钮，我们可能希望执行某些操作，比如下载文件、页面跳转、页面弹窗、进行登陆等，都可以使用JavaScript来帮助我们实现。
    
    我们来看看一个简单的JavaScript程序：
    
    ```js
    const arr = [0, 2, 1, 5, 9, 3, 4, 6, 7, 8]
    
    for (let i = 0; i < arr.length; i++) {
        for (let j = 0; j < arr.length - 1; j++) {
            if(arr[j] > arr[j+1]){
                const tmp = arr[j]
                arr[j] = arr[j+1]
                arr[j+1] = tmp
            }
        }
    }
    
    window.alert(arr)
    ```
    
    这段代码实际上就是实现了一个冒泡排序算法，我们可以直接在页面的头部中引用此js文件，浏览器会在加载时自动执行js文件中编写的内容：
    
    ```html
    <script src="test.js"></script>
    ```
    
    我们发现JS的语法和Java非常相似，但是它还是和Java存在一些不同之处，而且存在很多阴间语法，那么我们来看看JS的语法。
    
    ### JavaScript基本语法
    
    在js中，定义变量和Java中有一些不同，定义一个变量可以使用`let`关键字或是`var`关键字，IDEA推荐我们使用`let`关键字，因为`var`存在一定的设计缺陷（这里就不做讲解了，之后一律使用let关键字进行变量声明）：
    
    ```js
    let a = 10;
    a++;
    window.alert(a)
    ```
    
    上面的结果中，我们得到了a的结果是11，也就是说自增和自减运算在JS中也是支持的，并且JS每一句结尾可以不用加分号。
    
    js并不是Java那样的强类型语言（任意变量的类型一定是明确的），它是一门弱类型语言，变量的类型并不会在一开始确定，因此我们在定义变量时无需指定变量的确切类型，而是在运行时动态解析类型：
    
    ```js
    let a = 10;
    a = "HelloWorld！"
    console.info(a)
    ```
    
    我们发现，变量a已经被赋值为数字类型，但是我们依然在后续能将其赋值一个字符串，它的类型是随时可变的。
    
    很多人说，这种变态的类型机制是JS的一大缺陷。
    
    世界上只有两种语言：一种是很多人骂的，一种是没人用的。
    
    我们接着来看看，JS中存在的基本数据类型：
    
    * Number：数字类型（包括小数和整数）
    * String：字符串类型（可以使用单引号或是双引号）
    * Boolean：布尔类型（与Java一致）
    
    还包括一些特殊值：
    
    * undefined：未定义 - 变量声明但不赋值默认为undefined
    
    * null：空值 - 等同于Java中的null
    
    * NaN：非数字 - 值不是合法数字，比如：
    
      ```js
      window.alert(100/'xx')
      ```
    
    我们可以使用`typeof`关键字来查看当前变量值的类型：
    
    ```js
    let a = 10;
    console.info(typeof a)
    a = 'Hello World'
    console.info(typeof a)
    ```
    
    ### JavaScript逻辑运算和流程控制
    
    我们接着来看看js中的关系运算符，包括如下8个关系运算符：大于（>）,小于（<）,小于等于（<=）,大于等于（>=）,相等（==），不等（!=），全等（===），不全等（!==）
    
    其实关系运算符大致和Java中的使用方法一致，不过它还可以进行字符串比较，有点像C++的语法：
    
    ```js
    console.info(666 > 777)
    console.info('aa' > 'ab')
    ```
    
    那么，相等和全等有什么区别呢？
    
    ```java
    console.info('10' == 10)
    console.info('10' === 10)
    ```
    
    我们发现，在Java中，若运算符两边是不同的基本数据类型，会直接得到false，而JS中却不像这样，我们发现字符串的10居然等于数字10，而使用全等判断才是我们希望的结果。
    
    `==`的比较规则是：当操作数类型一样时，比较的规则和恒等运算符一样，都相等才相等，如果两个操作数是字符串，则进行字符串的比较，如果里面有一个操作数不是字符串，那两个操作数通过Number()方法进行转换，转成数字进行比较。
    
    因此，我们上面进行的判断实际上是运算符两边都进行了数字转换的结果进行比较，自然也就得到了true，而全等判断才是我们在Java中认识的相等判断。
    
    我们接着来看逻辑运算，JS中包括&&、||、&、|、?:等，我们先来看看位运算符：
    
    ```js
    console.info(4 & 7)
    console.info(4 | 7)
    ```
    
    实际上和Java中是一样的，那么我再来看看逻辑运算：
    
    ```js
    console.info(true || false)
    ```
    
    对于boolean变量的判断，是与Java一致的，但是JS也可以使用非Boolen类型变量进行判断：
    
    ```js
    console.info(!0)
    console.info(!1)
    ```
    
    和C/C++语言一样，0代表false，非0代表true，那么字符串呢？
    
    ```js
    console.info(!"a")
    console.info(!"")
    ```
    
    我们发现，空串为false，非空串为true，我们再来看看：
    
    ```js
    console.info(true || 7)
    console.info(7 || true)
    ```
    
    我们发现，前者得到的结果为true，而后者得到的结果却是是7，真是滑天下之大稽，什么鬼玩意，实际上是因为，默认非0都是true，而后者又是先判断的7，因此会直接得到7而不是被转换为true
    
    那么我们再来看看几个特殊值默认代表什么：
    
    ```js
    console.info(!undefined)
    console.info(!null)
    console.info(!NaN)
    ```
    
    最后来使用一下三元运算符，实际上和Java中是一样的：
    
    ```js
    let a = true ? "xx" : 20
    console.info(a)
    ```
    
    得益于JS的动态类型，emmm，三元运算符不一定需要固定的返回值类型。
    
    JS的分支结构，实际上和Java是一样的，也是使用if-else语句来进行：
    
    ```js
    if("lbwnb"){   //非空串为true
        console.info("!!!")
    } else {
        console.info("???")
    }
    ```
    
    同理，多分支语句也能实现：
    
    ```js
    if(""){
        console.info("!!!")
    } else if(-666){
        console.info("???")
    } else {
        console.info("O.O")
    }
    ```
    
    当然，多分支语句也可以使用switch来完成：
    
    ```js
    let a = "a"
    switch (a){
        case "a":
            console.info("1")
            break
        case "b":
            console.info("2")
            break
        case "c":
            console.info("3")
            break
        default:
            console.info("4")
    }
    ```
    
    接着我们来看看循环结构，其实循环结构也和Java相差不大：
    
    ```js
    let i = 10
    while(i--){
        console.info("100")
    }
    ```
    
    ```js
    for (let i = 0; i < 10; i++) {
        console.info("??")
    }
    ```
    
    ### JavaScript函数定义
    
    JS中的方法和Java中的方法定义不太一样，JS中一般称其为函数，我们来看看定义一个函数的格式是什么：
    
    ```js
    function f() {
        console.info("有一个人前来买瓜")
    }
    ```
    
    定义一个函数，需要在前面加上`function`关键字表示这是一个函数，后面跟上函数名称和`()`，其中可以包含参数，在`{}`中编写函数代码。我们只需要直接使用函数名+`()`就能调用函数：
    
    ```js
    f();
    ```
    
    我们接着来看一下，如何给函数添加形式参数以及返回值：
    
    ```js
    function f(a) {
        console.info("得到的实参为："+a)
        return 666
    }
    
    f("aa");
    ```
    
    由于JS是动态类型，因此我们不必指明参数a的类型，同时也不必指明返回值的类型，一个函数可能返回不同类型的结果，因此直接编写return语句即可。同理，我们可以在调用函数时，不传参，那么默认会使用undefined：
    
    ```js
    function f(a) {
        console.info("得到的实参为："+a)
        return 666
    }
    
    f();
    ```
    
    那么如果我们希望不传参的时候使用我们自定义的默认值呢？
    
    ```js
    function f(a = "6666") {
        console.info("得到的实参为："+a)
        return 666
    }
    
    f();
    ```
    
    我们可以直接在形参后面指定默认值。
    
    函数本身也是一种类型，他可以被变量接收，所有函数类型的变量，也可以直接被调用：
    
    ```js
    function f(a = "6666") {
        console.info("得到的实参为："+a)
        return 666
    }
    
    let k = f;
    k();
    ```
    
    我们也可以直接将匿名函数赋值给变量：
    
    ```js
    let f = function (str) {
        console.info("实参为："+str)
    }
    ```
    
    既然函数是一种类型，那么函数也能作为一个参数进行传递：
    
    ```js
    function f(test) {
        test();
    }
    
    f(function () {
        console.info("这是一个匿名函数")
    })
    ```
    
    对于所有的匿名函数，可以像Java的匿名接口实现一样编写lambda表达式：
    
    ```js
    function f(test) {
        test();
    }
    
    f(() => {
        console.info("可以，不跟你多bb")
    })
    ```
    
    ```js
    function f(test) {
        test("这个是回调参数");
    }
    
    f(param => {
        console.info("接受到回调参数："+param)
    })
    ```
    
    ### JavaScript数组和对象
    
    - 数组特点：
    
    - 1·可以是不同的类型：类似于python那样
    
    - ```
      let array =[1,"hello",ture,false]//这样子不同类型都是可以的 
      ```
    
      - JavaScript的数组是支持动态扩容的，通过下标直接使用，如果访问超出容量，会出现，undefined
    
      - 容量自动扩容到我们要访问的元素位置
    
      - 支持出栈和入栈操作，有对应的方法
    
        JavaScript对象：
    
        像Java那样，使用new关键字，花括号里面是属性，也是可以使用this关键字来添加属性，但是如果是使用lambda的话，使用this关键字会出错
    
        ---
    
        ## JavaScript事件（主要是触发动作和使用动作）
    
        - 使用动作在html的标签中添加属性，onxxx就是动作，里面是要触发动作后执行的内容，还可以是js文件里面的函数调用，如oninput多数是用于文本框输入，oncheck使用于点击，值得注意的是，注意双引号问题，注意不要冲突，如果有冲突，某对双引号要改为单引号，因为双引号都是就近原则，就近匹配。
    
        - 事件相当于一个通知，我们可以提前设定好事件发生时需要执行的内容，当事件发生时，就会执行我们预先设定好的JS代码。
    
          事件有很多种类型，其中常用的有：
    
          * onclick：点击事件
    
          * oninput：内容输入事件
    
          * onsubmit：内容提交事件
    
          * ```
            <input type="password" oninput="console.info('正在输入文本')">
            ```
    
            
    
        - ### 如果js与html想要打通隔阂，进行文件的数据传输与交流，就要使用dom对象：
    
        - dom对象里面有好多方法，可以通过html的标签来确定标签，进行传输数据和修改数据。
    
        - ---
    
        - 
    
        ### up笔记：
    
        ### Document对象
    
        当网页被加载时，浏览器会创建页面的文档对象模型（*D*ocument *O*bject *M*odel），它将整个页面的所有元素全部映射为JS对象，这样我们就可以在JS中操纵页面中的元素。
    
        ![DOM HTML 树](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303301717839.gif)
    
        比如我现在想要读取页面中某个输入框中的内容，那么我们就需要从DOM中获取此输入框元素的对象：
    
        ```js
        document.getElementById("pwd").value
        ```
    
        通过document对象就能够快速获取当前页面中对应的元素，并且我们也可以快速获取元素中的一些属性。
    
        比如现在我们可以结合事件，来进行密码长度的校验，密码长度小于6则不合法，不合法的密码，会让密码框边框变红，那么首先我们先来编写一个css样式：
    
        ```css
        .illegal-pwd{
            border: red 1px solid !important;
            box-shadow: 0 0 5px red;
        }
        ```
    
        接着我们来编写一下js代码，定义一个函数，此函数接受一个参数（元素本身的对象）检测输入的长度是否大于6，否则就将当前元素的class属性设定为css指定的class：
    
        ```js
        function checkIllegal(e) {
            if(e.value.length < 6) {
                e.setAttribute("class", "illegal-pwd")   
            }else {
                e.removeAttribute("class")
            }
        }
        ```
    
        最后我们将此函数绑定到`oninput`事件即可，注意传入了一个this，这里的this代表的是输入框元素本身：
    
        ```html
        <input id="pwd" oninput="checkIllegal(this)" type="password">
        ```
    
        现在我们在输入的时候，会自动检查密码是否合法。
    
        既然oninput本身也是一个属性，那么实际上我们可以动态进行修改：
    
        ```js
        document.getElementById("pwd").oninput = () => console.info("???")
        ```
    
        那么，我们前面提及的window对象又是什么东西呢？
    
        实际上Window对象范围更加广阔，它甚至直接代表了整个窗口，当然也包含我们的Document对象，我们一般通过Window对象来弹出提示框之类的东西。
    
        ---
    
        ## xhr：
    
        
    
        通过使用XMLHttpRequest对象，来向服务器发送一个HTTP请求，下面是一个最简单的请求格式：
    
        ```js
        let xhr = new XMLHttpRequest();
        xhr.open('GET', 'https://www.baidu.com');
        xhr.send();
        ```
    
        上面的例子中，我们向服务器发起了一次网络请求，但是我们请求的是百度的服务器，并且此请求的方法为GET请求。
    
        我们现在将其绑定到一个按钮上作为事件触发：
    
        ```js
        function http() {
            let xhr = new XMLHttpRequest();
            xhr.open('GET', 'https://www.baidu.com');
            xhr.send();    
        }
        ```
    
        ```html
        <input id="button" type="button" onclick="http()">
        ```
    
        我们可以在网络中查看我们发起的HTTP请求并且查看请求的响应结果，比如上面的请求，会返回百度这个页面的全部HTML代码。
    
        实际上，我们的浏览器在我们输入网址后，也会向对应网站的服务器发起一次HTTP的GET请求。
    
        在浏览器得到页面响应后，会加载当前页面，如果当前页面还引用了其他资源文件，那么会继续向服务器发起请求，直到页面中所有的资源文件全部加载完成后，才会停止。
    
  
  ## 计算机网络基础
  
  在计算机网络（谢希仁 第七版 第264页）中，是这样描述万维网的：
  
  >  万维 （World Wide Web）并非是某种特殊的计算机网络，万维网是一个大规模的联机式信息储藏所，英文简称`Web`，万维网用**<u>链接</u>**的方法，能够非常方便地从互联网上的一个站点访问另一个站点，从而主动地按需求获取丰富的信息。
  
  这句话说的非常官方，但是也蕴藏着许多的信息，首先它指明，我们的互联网上存在许许多多的服务器，而我们通过访问这些服务器就能快速获取服务器为我们提供的信息（比如打开百度就能展示搜索、打开小破站能刷视频、打开微博能查看实时热点）而这些服务器就是由不同的公司在运营。
  
  其次，我们通过浏览器，只需要输入对应的网址或是点击页面中的一个链接，就能够快速地跳转到另一个页面，从而按我们的意愿来访问服务器。
  
  而书中是这样描述万维网的工作方式：
  
  > 万维网以客户服务器的方式工作，浏览器就是安装在用户主机上的万维网客户程序，万维网文档所驻留的主机则运行服务器程序，因此这台主机也称为万维网服务器。**客户程序向服务器程序发出请求，服务器程序向客户程序送回客户所要的万维网文档**，在一个客户程序主窗口上显示出的万维网文档称为页面。
  
  上面提到的客户程序其实就是我们电脑上安装的浏览器，而服务端就是我们即将要去学习的Web服务器，也就是说，我们要明白如何搭建一个Web服务器并向用户发送我们提供的Web页面，在浏览器中显示的，一般就是HTML文档被解析后的样子。
  
  那么，我们的服务器可能不止一个页面，可能会有很多个页面，那么客户端如何知道该去访问哪个服务器的哪个页面呢？这个时候就需要用到`URL`统一资源定位符。互联网上所有的资源，都有一个唯一确定的URL，比如`http://www.baidu.com`
  
  URL的格式为：
  
  > <协议>://<主机>:<端口>/<路径>
  >
  > 协议是指采用什么协议来访问服务器，不同的协议决定了服务器返回信息的格式，我们一般使用HTTP协议。
  >
  > 主机可以是一个域名，也可以是一个IP地址（实际上域名最后会被解析为IP地址进行访问）
  >
  > 端口是当前服务器上Web应用程序开启的端口，我们前面学习TCP通信的时候已经介绍过了，HTTP协议默认使用80端口，因此有时候可以省略。
  >
  > 路径就是我们希望去访问此服务器上的某个文件，不同的路径代表访问不同的资源。
  
  我们接着来了解一下什么是HTTP协议：
  
  > HTTP是面向事务的应用层协议，它是万维网上能够可靠交换文件的重要基础。HTTP不仅传送完成超文本跳转所需的必须信息，而且也传送任何可从互联网上得到的信息，如文本、超文本、声音和图像。
  
  实际上我们之前访问百度、访问自己的网站，所有的传输都是以HTTP作为协议进行的。
  
  我们来看看HTTP的传输原理：
  
  > HTTP使用了面向连接的TCP作为运输层协议，保证了数据的可靠传输。HTTP不必考虑数据在传输过程中被丢弃后又怎样被重传。但是HTTP协议本身是无连接的。也就是说，HTTP虽然使用了TCP连接，但是通信的双方在交换HTTP报文之前不需要先建立HTTP连接。1997年以前使用的是HTTP/1.0协议，之后就是HTTP/1.1协议了。
  
  那么既然HTTP是基于TCP进行通信的，我们首先来回顾一下TCP的通信原理：
  
  ![image-20230328082048030](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303280820122.png)
  
  TCP协议实际上是经历了三次握手再进行通信，也就是说保证整个通信是稳定的，才可以进行数据交换，并且在连接已经建立的过程中，双方随时可以互相发送数据，直到有一方主动关闭连接，这时在进行四次挥手，完成整个TCP通信。
  
  而HTTP和TCP并不是一个层次的通信协议，TCP是传输层协议，而HTTP是应用层协议，因此，实际上HTTP的内容会作为TCP协议的报文被封装，并继续向下一层进行传递，而传输到客户端时，会依次进行解包，还原为最开始的HTTP数据。
  
  ![image-20230328082057524](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303280820593.png)
  
  HTTP使用TCP协议是为了使得数据传输更加可靠，既然它是依靠TCP协议进行数据传输，那么为什么说它本身是无连接的呢？我们来看一下HTTP的传输过程：
  
  > 用户在点击鼠标链接某个万维网文档时，HTTP协议首先要和服务器建立TCP连接。这需要使用三报文握手。当建立TCP连接的三报文握手的前两部分完成后（即经过了一个RTT时间后），万维网客户就把HTTP请求报文作为建立TCP连接的三报文握手中的第三个报文的数据，发送给万维网服务器。服务器收到HTTP请求报文后，就把所请求的文档作为响应报文返回给客户。
  
  ![image-20230328082107176](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303280821259.png)
  
  因此，我们的浏览器请求一个页面，需要两倍的往返时间。
  
  最后，我们再来了解一下HTTP的报文结构：
  
  ![img](https://img2.baidu.com/it/u=1539060868,3030092954&fm=26&fmt=auto)
  
  由客户端向服务端发送是报文称为请求报文，而服务端返回给客户端的称为响应报文，实际上，整个报文全部是以文本形式发送的，通过使用空格和换行来完成分段。
  
  现在，我们已经了解了HTTP协议的全部基础知识，那么什么是Web服务器呢，实际上，它就是一个软件，但是它已经封装了所有的HTTP协议层面的操作，我们无需关心如何使用HTTP协议通信，而是直接基于服务器软件进行开发，我们只需要关心我们的页面数据如何展示、前后端如何交互即可。
  
  ---
  
  - ## tomcat（搭建和管理网站）：
  
  - 1·下载tomcat在官网下载
  
  - 2·添加环境变量：名称：JRE_HOME(必须全大写)，量：JDK目录
  
  - 3·bim目录是可运行程序，打开cmd，将程序拖动到里面，
  
  - 解决乱码：打开conf目录（config）配置设置，打开loging日志文件，将console handler（控制台的意思）的utf-8改为GBK
  
  - 新建用户：conf的tomcat-userlml文件进行注册用户，
  
  - manger目录是放网站源代码，文件…类似于闲小皮面板那样，管理网站，还有在文件那的目录

---

Tomcat（汤姆猫）就是一个典型的Web应用服务器软件，通过运行Tomcat服务器，我们就可以快速部署我们的Web项目，并交由Tomcat进行管理，我们只需要直接通过浏览器访问我们的项目即可。

那么首先，我们需要进行一个简单的环境搭建，我们需要在Tomcat官网下载最新的Tomcat服务端程序：https://tomcat.apache.org/download-10.cgi（下载速度可能有点慢）

- 下载：64-bit Windows zip

下载完成后，解压，并放入桌面，接下来需要配置一下环境变量，打开`高级系统设置`，打开`环境变量`，添加一个新的系统变量，变量名称为`JRE_HOME`，填写JDK的安装目录+/jre，比如Zulujdk默认就是：C:\Program Files\Zulu\zulu-8\jre

设置完成后，我们进入tomcat文件夹bin目录下，并在当前位置打开CMD窗口，将startup.sh拖入窗口按回车运行，如果环境变量配置有误，会提示，若没问题，服务器则正常启动。

如果出现乱码，说明编码格式配置有问题，我们修改一下服务器的配置文件，打开`conf`文件夹，找到`logging.properties`文件，这就是日志的配置文件（我们在前面已经给大家讲解过了）将ConsoleHandler的默认编码格式修改为GBK编码格式：

```properties
java.util.logging.ConsoleHandler.encoding = GBK
```

现在重新启动服务器，就可以正常显示中文了。

服务器启动成功之后，不要关闭，我们打开浏览器，在浏览器中访问：http://localhost:8080/，Tomcat服务器默认是使用8080端口（可以在配置文件中修改），访问成功说明我们的Tomcat环境已经部署成功了。

整个Tomcat目录下，我们已经认识了bin目录（所有可执行文件，包括启动和关闭服务器的脚本）以及conf目录（服务器配置文件目录），那么我们接着来看其他的文件夹：

* lib目录：Tomcat服务端运行的一些依赖，不用关心。
* logs目录：所有的日志信息都在这里。
* temp目录：存放运行时产生的一些临时文件，不用关心。
* work目录：工作目录，Tomcat会将jsp文件转换为java文件（我们后面会讲到，这里暂时不提及）
* webapp目录：所有的Web项目都在这里，每个文件夹都是一个Web应用程序：

我们发现，官方已经给我们预设了一些项目了，访问后默认使用的项目为ROOT项目，也就是我们默认打开的网站。

我们也可以访问example项目，只需要在后面填写路径即可：http://localhost:8080/examples/，或是docs项目（这个是Tomcat的一些文档）http://localhost:8080/docs/

Tomcat还自带管理页面，我们打开：http://localhost:8080/manager，提示需要用户名和密码，由于不知道是什么，我们先点击取消，页面中出现如下内容：

> You are not authorized to view this page. If you have not changed any configuration files, please examine the file `conf/tomcat-users.xml` in your installation. That file must contain the credentials to let you use this webapp.
>
> For example, to add the `manager-gui` role to a user named `tomcat` with a password of `s3cret`, add the following to the config file listed above.
>
> ```
> <role rolename="manager-gui"/>
> <user username="tomcat" password="s3cret" roles="manager-gui"/>
> ```
>
> Note that for Tomcat 7 onwards, the roles required to use the manager application were changed from the single `manager` role to the following four roles. You will need to assign the role(s) required for the functionality you wish to access.
>
> - `manager-gui` - allows access to the HTML GUI and the status pages
> - `manager-script` - allows access to the text interface and the status pages
> - `manager-jmx` - allows access to the JMX proxy and the status pages
> - `manager-status` - allows access to the status pages only
>
> The HTML interface is protected against CSRF but the text and JMX interfaces are not. To maintain the CSRF protection:
>
> - Users with the `manager-gui` role should not be granted either the `manager-script` or `manager-jmx` roles.
> - If the text or jmx interfaces are accessed through a browser (e.g. for testing since these interfaces are intended for tools not humans) then the browser must be closed afterwards to terminate the session.
>
> For more information - please see the [Manager App How-To](http://localhost:8080/docs/manager-howto.html).

现在我们按照上面的提示，去配置文件中进行修改：

```xml
  <role rolename="manager-gui"/>
  <user username="admin" password="admin" roles="manager-gui"/>
```

现在再次打开管理页面，已经可以成功使用此用户进行登陆了。登录后，展示给我们的是一个图形化界面，我们可以快速预览当前服务器的一些信息，包括已经在运行的Web应用程序，甚至还可以查看当前的Web应用程序有没有出现内存泄露。

同样的，还有一个虚拟主机管理页面，用于一台主机搭建多个Web站点，一般情况下使用不到，这里就不做演示了。

我们可以将我们自己的项目也放到webapp文件夹中，这样就可以直接访问到了，我们在webapp目录下新建test文件夹，将我们之前编写的前端代码全部放入其中（包括html文件、js、css、icon等），重启服务器。

我们可以直接通过 http://localhost:8080/test/ 来进行访问。

## 创建web项目：

在新建项目，选择Java Enterprise，选择tomcat服务器

记得tomcat要改依赖，maven的pom.xml文件要改jar包，因为不匹配tomcat10

```xml
<dependency>
    <groupId>jakarta.servlet</groupId>
    <artifactId>jakarta.servlet-api</artifactId>
    <version>5.0.0</version>
    <scope>provided</scope>
</dependency>
```

webapp文件夹是放网页的应用程序的配置文件

idea可以连接tomcat，点击运行，就能直接查看（自己开）网页，还能管理网页，在它的manerger网站：管理网站，可以删除，添加，暂停节点

用maven将项目打包成jar包，将jar包放在tomcat的webapp的文件夹里面，重新启动tomcat，他会自己解析jar包，新建一个文件夹，是一个网站网站。类似于小皮传源文件到网站的文件，root文件夹里面。可以建成一个网站。

- ## 创建一个serrlet

- 1·接入接口servlet

- 2·注解@web servlet（“/xxx”），这个是设置serverlet的访问路径，在浏览器打开后，serverlet设置的路径是后面的分支，url是可以改的，但是url一改，部署也要改

Java Servlet 是运行在 Web 服务器或应用服务器上的程序，它是作为来自 Web 浏览器或其他 HTTP 客户端的请求和 HTTP 服务器上的数据库或应用程序之间的中间层。

 使用 Servlet，您可以收集来自网页表单的用户输入，呈现来自数据库或者其他源的记录，还可以动态创建网页。

![这里写图片描述](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303290005589.png)

根据上面这张图，我们就能知道，servlet在其中承担的作用是controller，控制器，起到对数据进行操作的作用．
顺便补充说明一下，最经典的MVC模型就是JSP+JavaBean+Servlet开发的模式

## servlet的生命周期

- servlet的运行过程：

  - 首先，运行是构造方法先，new一个servlet对象出来，然后执行init的对象的方法，再去执行servlet方法，最后再关闭的时候，它是先去执行destroy方法，也就是客户端断开后，执行，用于销毁前的保存；值得注意的是，客户端打开界面后，刷新，无论刷新多少次，他都只会重新执行servlet的方法，而不会再次执行构造方法和init的方法。除非销毁再开一个新的。所以，mybatis的文件主要放在init的方法里面，尤其是某些xml文件

  - 每次刷新（请求）都会创建一个新的线程出来，新的线程去处理。

  - servlet request是由tomcat去实现；servlet是一个接口

  - 请求头：respondse，返回给页面一些数据

  - httpservletrequest父类是serverrequest

  - 响应头：是客户端发送一些数据给服务端

  - 

  - 

    ---

    我们已经了解了如何注册一个Servlet，那么我们接着来看看，一个Servlet是如何运行的。

    首先我们需要了解，Servlet中的方法各自是在什么时候被调用的，我们先编写一个打印语句来看看：

    ```java
    public class TestServlet implements Servlet {
    
        public TestServlet(){
            System.out.println("我是构造方法！");
        }
    
        @Override
        public void init(ServletConfig servletConfig) throws ServletException {
            System.out.println("我是init");
        }
    
        @Override
        public ServletConfig getServletConfig() {
            System.out.println("我是getServletConfig");
            return null;
        }
    
        @Override
        public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
            System.out.println("我是service");
        }
    
        @Override
        public String getServletInfo() {
            System.out.println("我是getServletInfo");
            return null;
        }
    
        @Override
        public void destroy() {
            System.out.println("我是destroy");
        }
    }
    ```

    我们首先启动一次服务器，然后访问我们定义的页面，然后再关闭服务器，得到如下的顺序：

    > 我是构造方法！
    > 我是init
    > 我是service
    > 我是service（出现两次是因为浏览器请求了2次，是因为有一次是请求favicon.ico，浏览器通病）
    >
    > 我是destroy

    我们可以多次尝试去访问此页面，但是init和构造方法只会执行一次，而每次访问都会执行的是`service`方法，因此，一个Servlet的生命周期为：

    - 首先执行构造方法完成 Servlet 初始化
    - Servlet 初始化后调用 **init ()** 方法。
    - Servlet 调用 **service()** 方法来处理客户端的请求。
    - Servlet 销毁前调用 **destroy()** 方法。
    - 最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的。

    现在我们发现，实际上在Web应用程序运行时，每当浏览器向服务器发起一个请求时，都会创建一个线程执行一次`service`方法，来让我们处理用户的请求，并将结果响应给用户。

    我们发现`service`方法中，还有两个参数，`ServletRequest`和`ServletResponse`，实际上，用户发起的HTTP请求，就被Tomcat服务器封装为了一个`ServletRequest`对象，我们得到是其实是Tomcat服务器帮助我们创建的一个实现类，HTTP请求报文中的所有内容，都可以从`ServletRequest`对象中获取，同理，`ServletResponse`就是我们需要返回给浏览器的HTTP响应报文实体类封装。

    那么我们来看看`ServletRequest`中有哪些内容，我们可以获取请求的一些信息：

    ```java
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        //首先将其转换为HttpServletRequest（继承自ServletRequest，一般是此接口实现）
            HttpServletRequest request = (HttpServletRequest) servletRequest;
            
            System.out.println(request.getProtocol());  //获取协议版本
            System.out.println(request.getRemoteAddr());  //获取访问者的IP地址
      		  System.out.println(request.getMethod());   //获取请求方法
            //获取头部信息
            Enumeration<String> enumeration = request.getHeaderNames();
            while (enumeration.hasMoreElements()){
                String name = enumeration.nextElement();
                System.out.println(name + ": " + request.getHeader(name));
            }
    }
    ```

    我们发现，整个HTTP请求报文中的所有内容，都可以通过`HttpServletRequest`对象来获取，当然，它的作用肯定不仅仅是获取头部信息，我们还可以使用它来完成更多操作，后面会一一讲解。

    那么我们再来看看`ServletResponse`，这个是服务端的响应内容，我们可以在这里填写我们想要发送给浏览器显示的内容：

    ```java
    //转换为HttpServletResponse（同上）
    HttpServletResponse response = (HttpServletResponse) servletResponse;
    //设定内容类型以及编码格式（普通HTML文本使用text/html，之后会讲解文件传输）
    response.setHeader("Content-type", "text/html;charset=UTF-8");
    //获取Writer直接写入内容
    response.getWriter().write("我是响应内容！");
    //所有内容写入完成之后，再发送给浏览器
    ```

    现在我们在浏览器中打开此页面，就能够收到服务器发来的响应内容了。其中，响应头部分，是由Tomcat帮助我们生成的一个默认响应头。

    ![image-20230328233112744](https://gitee.com/ljzcomeon/typora-photo/raw/master/202303282331827.png)

    因此，实际上整个流程就已经很清晰明了了。

    ### 解读和使用HttpServlet

    前面我们已经学习了如何创建、注册和使用Servlet，那么我们继续来深入学习Servlet接口的一些实现类。

    首先`Servlet`有一个直接实现抽象类`GenericServlet`，那么我们来看看此类做了什么事情。

    我们发现，这个类完善了配置文件读取和Servlet信息相关的的操作，但是依然没有去实现service方法，因此此类仅仅是用于完善一个Servlet的基本操作，那么我们接着来看`HttpServlet`，它是遵循HTTP协议的一种Servlet，继承自`GenericServlet`，它根据HTTP协议的规则，完善了service方法。

    在阅读了HttpServlet源码之后，我们发现，其实我们只需要继承HttpServlet来编写我们的Servlet就可以了，并且它已经帮助我们提前实现了一些操作，这样就会给我们省去很多的时间。

    ```java
    @Log
    @WebServlet("/test")
    public class TestServlet extends HttpServlet {
    
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            resp.setContentType("text/html;charset=UTF-8");
            resp.getWriter().write("<h1>恭喜你解锁了全新玩法</h1>");
        }
    }
    ```

    现在，我们只需要重写对应的请求方式，就可以快速完成Servlet的编写。

    ### @WebServlet注解详解

    我们接着来看WebServlet注解，我们前面已经得知，可以直接使用此注解来快速注册一个Servlet，那么我们来想细看看此注解还有什么其他的玩法。

    首先name属性就是Servlet名称，而urlPatterns和value实际上是同样功能，就是代表当前Servlet的访问路径，它不仅仅可以是一个固定值，还可以进行通配符匹配：

    ```java
    @WebServlet("/test/*")
    ```

    上面的路径表示，所有匹配`/test/随便什么`的路径名称，都可以访问此Servlet，我们可以在浏览器中尝试一下。

    也可以进行某个扩展名称的匹配：

    ```java
    @WebServlet("*.js")
    ```

    这样的话，获取任何以js结尾的文件，都会由我们自己定义的Servlet处理。

    那么如果我们的路径为`/`呢？

    ```java
    @WebServlet("/")
    ```

    此路径和Tomcat默认为我们提供的Servlet冲突，会直接替换掉默认的，而使用我们的，此路径的意思为，如果没有找到匹配当前访问路径的Servlet，那么久会使用此Servlet进行处理。

    我们还可以为一个Servlet配置多个访问路径：

    ```java
    @WebServlet({"/test1", "/test2"})
    ```

    我们接着来看loadOnStartup属性，此属性决定了是否在Tomcat启动时就加载此Servlet，默认情况下，Servlet只有在被访问时才会加载，它的默认值为-1，表示不在启动时加载，我们可以将其修改为大于等于0的数，来开启启动时加载。并且数字的大小决定了此Servlet的启动优先级。

    ```java
    @Log
    @WebServlet(value = "/test", loadOnStartup = 1)
    public class TestServlet extends HttpServlet {
    
        @Override
        public void init() throws ServletException {
            super.init();
            log.info("我被初始化了！");
        }
    
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            resp.setContentType("text/html;charset=UTF-8");
            resp.getWriter().write("<h1>恭喜你解锁了全新玩法</h1>");
        }
    }
    ```

    其他内容都是Servlet的一些基本配置，这里就不详细讲解了。

---

### 解读和使用HttpServlet

前面我们已经学习了如何创建、注册和使用Servlet，那么我们继续来深入学习Servlet接口的一些实现类。

首先`Servlet`有一个直接实现抽象类`GenericServlet`，那么我们来看看此类做了什么事情。

我们发现，这个类完善了配置文件读取和Servlet信息相关的的操作，但是依然没有去实现service方法，因此此类仅仅是用于完善一个Servlet的基本操作，那么我们接着来看`HttpServlet`，它是遵循HTTP协议的一种Servlet，继承自`GenericServlet`，它根据HTTP协议的规则，完善了service方法。

在阅读了HttpServlet源码之后，我们发现，其实我们只需要继承HttpServlet来编写我们的Servlet就可以了，并且它已经帮助我们提前实现了一些操作，这样就会给我们省去很多的时间。

```java
@Log
@WebServlet("/test")
public class TestServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        resp.getWriter().write("<h1>恭喜你解锁了全新玩法</h1>");
    }
}
```

现在，我们只需要重写对应的请求方式，就可以快速完成Servlet的编写。



### @WebServlet注解详解

我们接着来看WebServlet注解，我们前面已经得知，可以直接使用此注解来快速注册一个Servlet，那么我们来想细看看此注解还有什么其他的玩法。

==首先name属性就是Servlet名称，而urlPatterns和value实际上是同样功能==，就是代表当前Servlet的访问路径，它不仅仅可以是一个固定值，还可以进行通配符匹配：value主要是多个路径地址，name主要用于只有一个路径地址

```java
@WebServlet("/test/*")
```

上面的路径表示，所有匹配`/test/随便什么`的路径名称，都可以访问此Servlet，我们可以在浏览器中尝试一下。

也可以进行某个扩展名称的匹配：

```java
@WebServlet("*.js")
```

这样的话，获取任何以js结尾的文件，都会由我们自己定义的Servlet处理。

那么如果我们的路径为`/`呢？

```java
@WebServlet("/")
```

此路径和Tomcat默认为我们提供的Servlet冲突，会直接替换掉默认的，而使用我们的，此路径的意思为，如果没有找到匹配当前访问路径的Servlet，那么久会使用此Servlet进行处理。

我们还可以为一个Servlet配置多个访问路径：

```java
@WebServlet({"/test1", "/test2"})
```

我们接着来看loadOnStartup属性，此属性决定了是否在Tomcat启动时就加载此Servlet，默认情况下，Servlet只有在被访问时才会加载，它的默认值为-1，表示不在启动时加载，我们可以将其修改为大于等于0的数，来开启启动时加载。并且数字的大小决定了此Servlet的启动优先级。

```java
@Log
@WebServlet(value = "/test", loadOnStartup = 1)
public class TestServlet extends HttpServlet {

    @Override
    public void init() throws ServletException {
        super.init();
        log.info("我被初始化了！");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        resp.getWriter().write("<h1>恭喜你解锁了全新玩法</h1>");
    }
}
```

其他内容都是Servlet的一些基本配置。重要的是三个，name；value；loadstartup



---

## servlet的使用：

- ### 使用post登录：

- 两种办法可以向服务端发起请求：

- XHR 和 submit 都是用来向服务器发送请求的方式，但它们的实现方式和使用场景有所不同。

  1. XHR（XMLHttpRequest）：XHR 是一种用于在后台与服务器进行数据交换的技术，它可以通过 JavaScript 在不刷新页面的情况下向服务器发送 HTTP 请求并接收响应数据。XHR 可以实现局部刷新，提高网页的用户体验。XHR 请求需要使用 JavaScript 编写，并通过 JavaScript 代码控制请求的发送和接收过程，请求的数据和响应的数据格式可以是任意的，比如 JSON、XML 等。
  2. submit：submit 是 HTML 表单的一个属性，它定义了表单提交的方式和目标 URL。当用户在表单中填写完数据后，点击提交按钮或通过 JavaScript 调用 `form.submit()` 方法时，表单数据将被发送到指定的 URL。submit 请求可以在页面跳转或打开新页面时使用，可以方便地实现页面之间的跳转和数据传递。

  综上所述，XHR 和 submit 的使用场景不同。XHR 适用于需要在不刷新页面的情况下与服务器进行数据交换的场景，而 submit 适用于需要在页面跳转或打开新页面时向服务器发送表单数据的场景。需要根据具体的需求和场景选择适合的方式来实现数据交换。

推荐使用submit，简洁名了，form表格中的action，是与web注解相对应的，可以认为数据传输到此服务端，servlet其实可以看作一个中介，用于前后端的传输，前端将东西打包成表单，进行传输。请求头是客户端，响应头是服务端！通常使用post协议，post的登录方式



### post方法和get方法区别

- 表单使用post方法比较好，其他使用get方法，如果不明确使用什么请求，也就是不明确使用post还是get，则会默认使用get方法

`oGet()` 和 `doPost()` 都是用于处理 HTTP 请求的方法，具体使用哪个方法取决于你希望如何处理请求参数和数据。

通常情况下，当你希望从 URL 参数中获取数据时，应该使用 `doGet()` 方法。因为 `doGet()` 方法的参数是通过 URL 查询字符串传递的，这些参数可以直接从 URL 中解析出来，因此它比较适合处理简单的查询。

当你需要处理复杂的请求参数或需要对传递的数据进行修改时，应该使用 `doPost()` 方法。因为 `doPost()` 方法的参数是通过请求体中传递的，可以包含大量的数据，并且可以使用表单等方式进行组织。因此它比较适合处理需要修改数据状态的操作，如添加、更新或删除数据等。

需要注意的是，`doGet()` 方法的参数是通过 URL 传递的，因此 URL 参数的长度受限于浏览器和服务器的限制，而 `doPost()` 方法的参数是通过请求体传递的，因此可以传递大量的数据。因此在处理大量数据时，应该优先选择 `doPost()` 方法。

如果在Servlet中没有明确指定要使用 `doGet()` 或 `doPost()` 方法来处理HTTP请求，那么默认情况下会使用 `doGet()` 方法。这是因为大多数HTTP请求都是GET请求，并且它们不需要传递太多的数据，因此 `doGet()` 方法更常用。

如果你希望使用 `doPost()` 方法来处理HTTP请求，你需要在Servlet中明确指定，例如通过在 `doGet()` 方法中重定向到 `doPost()` 方法，或者通过在 `web.xml` 配置文件中指定 Servlet 的 `http-methods` 属性为 "POST"。

- ### 文件上传和下载：

- ### XHR进行前后端数据传输：可以更新：

- 网页中的部分内容，可以动态显示，比如网页上有一个时间，旁边有一个按钮，点击按钮就可以刷新当前时间。

  这个时候就需要我们在网页展示时向后端发起请求了，并根据后端响应的结果，动态地更新页面中的内容，要实现此功能，就需要用到JavaScript来帮助我们，首先在js中编写我们的XHR请求，并在请求中完成动态更新：

  ```js
  function updateTime() {
      let xhr = new XMLHttpRequest();
      xhr.onreadystatechange = function() {//事件监听器
          if (xhr.readyState === 4 && xhr.status === 200) {//判断
              document.getElementById("time").innerText = xhr.responseText//使用document对象可以将html和jsp文件数据连接起来
          }
      };
      xhr.open('GET', 'time', true);
      xhr.send();
  }
  ```

  #### 执行顺序：原理：

  ```
  当 updateTime() 函数被调用时，JavaScript 引擎会依次执行以下步骤：
  
  创建一个 XMLHttpRequest 对象，并将其赋值给变量 xhr。
  定义 xhr.onreadystatechange 事件处理程序，以便在服务器返回响应后更新网页内容。
  调用 xhr.open() 方法，指定 HTTP 请求的方法和 URL，以及请求是否是异步的。
  调用 xhr.send() 方法，将 HTTP 请求发送给服务器。
  程序执行到这里，xhr.send() 方法开始向服务器发送请求，并且 JavaScript 引擎开始等待响应。
  在等待响应的过程中，程序继续执行后面的代码，直到遇到其他的异步操作（如定时器、事件监听器等）或者函数结束。
  如果在等待响应的过程中服务器返回了响应，JavaScript 引擎会检查响应状态和内容，并触发 xhr.onreadystatechange 事件。
  如果 xhr.onreadystatechange 事件处理程序已经被注册，那么它会被立即执行，并根据响应内容更新网页中的内容。
  程序继续等待其他的异步操作或者函数结束。
  如果等待一段时间后仍然没有收到服务器的响应，JavaScript 引擎会超时并终止该请求，不会触发 xhr.onreadystatechange 事件。
  总的来说，JavaScript 中的异步操作是基于事件驱动的，而 xhr.onreadystatechange 事件就是在 XMLHttpRequest 对象状态发生变化时被触发的事件之一。在该事件被触发时，JavaScript 引擎会检查 XMLHttpRequest 对象的状态和响应内容，并根据需要执行事件处理程序来更新网页内容。
  ```

  如果没有：

  ```
  xhr.onreadystatechange = function() {//事件监听器
          if (xhr.readyState === 4 && xhr.status === 200) {//判断
              document.getElementById("time").innerText = xhr.responseText//使用document对象可以将html和jsp文件数据连接起来
          }
      };
  ```

  这个一样能运行，只是需要自己手动刷新，重新打开网页：所以是执行完send以后，再去更新xhr.onreadystatechange，下一次现实的时间是上一次的数据

  #### gpt回答：

  ```
  如果你没有注册 xhr.onreadystatechange 事件处理程序，那么虽然你可以使用 XMLHttpRequest 对象向服务器发送请求并获取响应，但是你将无法在响应返回时自动更新网页中的内容。
  
  当 XMLHttpRequest 对象的状态发生变化时，它会触发 readystatechange 事件。如果你没有注册 xhr.onreadystatechange 事件处理程序，那么即使状态发生了变化，也不会执行任何代码。在这种情况下，你需要手动调用 xhr.responseText 来获取服务器响应的内容，并将其用于更新网页中的内容。
  
  然而，手动获取服务器响应的内容并将其用于更新网页的过程往往比较繁琐，并且容易出错。因此，使用 xhr.onreadystatechange 事件处理程序可以使这个过程更加自动化和可靠。
  ```

  - ## 总结servlet与前后端传输的方法个人看法：

  前后端的传输方法：

  ```
  前后端数据传输并不限于 XMLHttpRequest (XHR) 对象。XMLHttpRequest 是一种使用 JavaScript 发送 HTTP 请求和接收 HTTP 响应的常用方式，但是它并不是唯一的方式。下面是一些常见的前后端数据传输方式：
  
  Fetch API：Fetch API 是一种用于发送 HTTP 请求和处理响应的 JavaScript 接口。它提供了一组丰富的功能，包括请求和响应的拦截器、请求的取消、跨域资源共享 (CORS) 支持等等。
  
  WebSocket：WebSocket 是一种基于 TCP 协议的双向通信协议。它允许在浏览器和服务器之间建立持久的连接，并支持双向通信，可以用于实现实时消息推送等功能。
  
  Server-Sent Events (SSE)：Server-Sent Events 是一种基于 HTTP 协议的单向通信协议。它允许服务器向客户端发送事件流，客户端通过监听事件流来接收服务器发送的数据。它适用于实现实时通知、实时数据更新等功能。
  
  WebSocket 和 SSE 库：除了使用原生的 WebSocket 和 SSE 协议外，还可以使用一些流行的 WebSocket 和 SSE 库，如 Socket.IO、Sails.js、SignalR 等等。这些库通常提供了更高级的功能，如自动重连、广播、房间等等，可以让前后端开发更加便捷和高效。
  
  总之，前后端数据传输的方式取决于你的具体需求和技术栈。不同的方式各有优缺点，需要根据实际情况进行选择。
  ```

  

### 但是原理就是：前后端传输数据的原理就是请求头和响应头对吧？只是实现方式不一样而已，可以规定使用post协议还是get协议，还要使用xhr还是使用其他如提交表格那些方法进行传输

```
前后端数据传输的基本原理是通过 HTTP 协议进行通信，包括发送请求和接收响应。请求和响应都是由请求头和响应头构成的，它们包含了关于请求和响应的元数据和其他信息，如请求方法、请求 URL、请求体、响应状态码、响应体等等。

在 HTTP 协议中，请求方法主要有 GET、POST、PUT、DELETE 等等。GET 方法通常用于获取资源，POST 方法通常用于提交表单数据或其他数据，PUT 方法通常用于更新资源，DELETE 方法通常用于删除资源。选择使用哪种方法取决于你的具体需求和设计，需要考虑安全性、性能、可维护性等因素。

在前后端传输数据时，可以使用 XMLHttpRequest (XHR) 对象或其他方法，如 Fetch API、WebSocket、Server-Sent Events 等等。使用 XHR 对象可以通过异步方式向服务器发送请求和接收响应，并且可以在响应返回后自动更新网页中的内容。而使用其他方法则需要根据具体情况进行选择，并且可能需要手动处理响应，如解析 JSON 数据、更新网页中的内容等等。

总之，前后端传输数据的原理是通过 HTTP 协议进行通信，并且可以使用不同的方法和技术来实现。需要根据具体情况进行选择，并且需要遵循一些最佳实践和安全规范，以确保数据传输的安全和可靠性。
```

### servlet的作用：

```
在 Java Web 开发中，Servlet 充当了 Web 服务器和应用程序之间的桥梁，负责处理 HTTP 请求和响应，并将请求转发到相应的 Java 类或方法中进行处理。Servlet 可以通过注解或配置文件的方式映射到特定的 URL，从而实现对特定请求的处理。

当客户端发送 HTTP 请求时，Web 服务器会调用相应的 Servlet，并传递 HttpServletRequest 和 HttpServletResponse 对象作为参数。ServletRequest 和 ServletResponse 对象提供了一组方法，可以用于处理请求和生成响应。在 Servlet 中，可以通过 HttpServletRequest 对象获取请求参数、请求头信息等等，并且可以通过 HttpServletResponse 对象生成响应头、响应体等等。
```

### post与get

```
        //get 查
        //post 曾
        //put 改
        //delete 删
```

- post是提交数据，前端传后端数据。get是提交申请资源。注意这只是经常性而已，并不代表post不能做get的东西，类似。只是推荐而已
- 

- ### 重定向与请求定向：

- 重定向：作用，跳转网页，让浏览器打开指定的网页，可以是自己的，可以是外部的；但是不能传输数据；也可以使用手动跳转，设置状态码是302，如果条件成立的话，set.Header(“location”,”要跳转的地址”)；

- 

- 当我们希望用户登录完成之后，直接跳转到网站的首页，那么这个时候，我们就可以使用重定向来完成。当浏览器收到一个重定向的响应时，会按照重定向响应给出的地址，再次向此地址发出请求。

  实现重定向很简单，只需要调用一个方法即可，我们修改一下登陆成功后执行的代码：

  ```java
  resp.sendRedirect("time");//这个是重定向
  ```

  调用后，响应的状态码会被设置为302，并且响应头中添加了一个Location属性，此属性表示，需要重定向到哪一个网址。

  现在，如果我们成功登陆，那么服务器会发送给我们一个重定向响应，这时，我们的浏览器会去重新请求另一个网址。这样，我们在登陆成功之后，就可以直接帮助用户跳转到用户首页了。

- 请求定向：

- 类似于重定向，但只能跳去自己的servlet，也就是自己的内部，地址前面还要加上‘/’，以为要在根目录下，而重定向，不需要使用‘/’

- 与重定向最大的不同是，重定向不能携带数据，但是请求定向可以携带数据；还要刚才提到的/，还有重定向是使用doget方法，请求定向是使用dopost方法

- 



请求转发其实是一种服务器内部的跳转机制，我们知道，重定向会使得浏览器去重新请求一个页面，而请求转发则是服务器内部进行跳转，它的目的是，直接将本次请求转发给其他Servlet进行处理，并由其他Servlet来返回结果，因此它是在进行内部的转发。

```java
req.getRequestDispatcher("/time").forward(req, resp);//请求转发
```

现在，在登陆成功的时候，我们将请求转发给处理时间的Servlet，注意这里的路径规则和之前的不同，我们需要填写Servlet上指明的路径，并且请求转发只能转发到此应用程序内部的Servlet，不能转发给其他站点或是其他Web应用程序。

现在再次进行登陆操作，我们发现，返回结果为一个405页面，证明了，我们的请求现在是被另一个Servlet进行处理，并且请求的信息全部被转交给另一个Servlet，由于此Servlet不支持POST请求，因此返回405状态码。

那么也就是说，该请求包括请求参数也一起被传递了，那么我们可以尝试获取以下POST请求的参数。

现在我们给此Servlet添加POST请求处理，直接转交给Get请求处理：

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    this.doGet(req, resp);
}
```

再次访问，成功得到结果，但是我们发现，浏览器只发起了一次请求，并没有再次请求新的URL，也就是说，这一次请求直接返回了请求转发后的处理结果。

那么，请求转发有什么好处呢？它可以携带数据！

```java
req.setAttribute("test", "我是请求转发前的数据");
req.getRequestDispatcher("/time").forward(req, resp);
```

```java
System.out.println(req.getAttribute("test"));
```

#### 通过`setAttribute`方法来给当前请求添加一个附加数据，在请求转发后，我们可以直接获取到该数据。

重定向属于2次请求，因此无法使用这种方式来传递数据，那么，如何在重定向之间传递数据呢？我们可以使用即将要介绍的ServletContext对象。

最后总结，两者的区别为：

* 请求转发是一次请求，重定向是两次请求
* 请求转发地址栏不会发生改变， 重定向地址栏会发生改变
* 请求转发可以共享请求参数 ，重定向之后，就获取不了共享参数了
* 请求转发只能转发给内部的Servlet

### 了解ServletContext对象

ServletContext全局唯一，它是属于整个Web应用程序的，我们可以通过`getServletContext()`来获取到此对象。

此对象也能设置附加值：

```java
ServletContext context = getServletContext();
context.setAttribute("test", "我是重定向之前的数据");
resp.sendRedirect("time");
```

```java
System.out.println(getServletContext().getAttribute("test"));
```

因为无论在哪里，无论什么时间，获取到的ServletContext始终是同一个对象，因此我们可以随时随地获取我们添加的属性。

它不仅仅可以用来进行数据传递，还可以做一些其他的事情，比如请求转发：

```java
context.getRequestDispatcher("/time").forward(req, resp);
```

它还可以获取根目录下的资源文件（注意是webapp根目录下的，不是resource中的资源）

### 初始化参数

初始化参数类似于初始化配置需要的一些值，比如我们的数据库连接相关信息，就可以通过初始化参数来给予Servlet，或是一些其他的配置项，也可以使用初始化参数来实现。

我们可以给一个Servlet添加一些初始化参数：

```java
@WebServlet(value = "/login", initParams = {
        @WebInitParam(name = "test", value = "我是一个默认的初始化参数")
})
```

它也是以键值对形式保存的，我们可以直接通过Servlet的`getInitParameter`方法获取：

```java
System.out.println(getInitParameter("test"));
```

但是，这里的初始化参数仅仅是针对于此Servlet，我们也可以定义全局初始化参数，只需要在web.xml编写即可：

```xml
<context-param>
    <param-name>lbwnb</param-name>
    <param-value>我是全局初始化参数</param-value>
</context-param>
```

我们需要使用ServletContext来读取全局初始化参数：

```java
ServletContext context = getServletContext();
System.out.println(context.getInitParameter("lbwnb"));
```

***

## cookie（用于保存一些信息，下次请求，请求头会携带这些信息）

### 常用于登陆的记住我功能

![image-20230403144635659](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304031446750.png)

作用过程：

首先第一次打开网站，客户端的请求头会向服务端请求，然后服务端的响应头给了客户端一个cookiie，客户端会将cookie保存在浏览器缓存，下一次客户端请求头直接带上一次的cookis，除非cookie过期了，可以在maxage设置它的期限，默认是关闭浏览器就会销毁；或者浏览器清空了；cookie最关键的其实是`name`、`value`、`maxAge`、`domain`属性。

- cookes的属性（name，value，maxAge，domain）：
  -  
  - name   -   Cookie的名称，Cookie一旦创建，名称便不可更改
  - value  -   Cookie的值，如果值为Unicode字符，需要为字符编码。如果为二进制数据，则需要使用BASE64编码
  - maxAge  -  Cookie失效的时间，单位秒。如果为正数，则该Cookie在maxAge秒后失效。如果为负数，该Cookie为临时Cookie，关闭浏览器即失效，浏览器也不会以任何形式保存该Cookie。如果为0，表示删除该Cookie。默认为-1。
  - secure  -  该Cookie是否仅被使用安全协议传输。安全协议。安全协议有HTTPS，SSL等，在网络上传输数据之前先将数据加密。默认为false。
  - path  -  Cookie的使用路径。如果设置为“/sessionWeb/”，则只有contextPath为“/sessionWeb”的程序可以访问该Cookie。如果设置为“/”，则本域名下contextPath都可以访问该Cookie。注意最后一个字符必须为“/”。
  - domain  -  可以访问该Cookie的域名。如果设置为“.google.com”，则所有以“google.com”结尾的域名都可以访问该Cookie。注意第一个字符必须为“.”。
  - 

比较常使用的方法：

```
cookie.setMaxAge(20);//设置生命期限，过了就会失效
```

----

---

既然了解了Cookie的作用，我们就可以通过使用Cookie来实现记住我功能，我们可以将用户名和密码全部保存在Cookie中，如果访问我们的首页时携带了这些Cookie，那么我们就可以直接为用户进行登陆，如果登陆成功则直接跳转到首页，如果登陆失败，则清理浏览器中的Cookie。

那么首先，我们先在前端页面的表单中添加一个勾选框：

```html
<div>
    <label>
        <input type="checkbox" placeholder="记住我" name="remember-me">
        记住我
    </label>
</div>
```

接着，我们在登陆成功时进行判断，如果用户勾选了记住我，那么就讲Cookie存储到本地：

```java
if(map.containsKey("remember-me")){   //若勾选了勾选框，那么会此表单信息
    Cookie cookie_username = new Cookie("username", username);
    cookie_username.setMaxAge(30);
    Cookie cookie_password = new Cookie("password", password);
    cookie_password.setMaxAge(30);
    resp.addCookie(cookie_username);
    resp.addCookie(cookie_password);
}
```

然后，我们修改一下默认的请求地址，现在一律通过`http://localhost:8080/yyds/login`进行登陆，那么我们需要添加GET请求的相关处理：

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie[] cookies = req.getCookies();
    if(cookies != null){
        String username = null;
        String password = null;
        for (Cookie cookie : cookies) {
            if(cookie.getName().equals("username")) username = cookie.getValue();
            if(cookie.getName().equals("password")) password = cookie.getValue();
        }
        if(username != null && password != null){
            //登陆校验
            try (SqlSession sqlSession = factory.openSession(true)){
                UserMapper mapper = sqlSession.getMapper(UserMapper.class);
                User user = mapper.getUser(username, password);
                if(user != null){
                    resp.sendRedirect("time");
                    return;   //直接返回
                }
            }
        }
    }
    req.getRequestDispatcher("/").forward(req, resp);   //正常情况还是转发给默认的Servlet帮我们返回静态页面
}
```

现在，30秒内都不需要登陆，访问登陆页面后，会直接跳转到time页面。

## sesion：

```

Cookies 和 Session 都是在 Web 开发中用来存储用户信息的机制，但是它们在实现和用法上有很大的不同。

Cookie 是由 Web 服务器发送到用户的 Web 浏览器中的小文本文件。当用户访问同一服务器时，浏览器会发送 cookie 给服务器，服务器可以读取 cookie 中的信息来识别用户或存储用户的偏好设置等信息。Cookie 的优点是它们可以存储在客户端，减少服务器负载，并且可以在不同的 Web 页面之间共享。Cookie 的缺点是它们容易被攻击者窃取或篡改，并且有一定的大小限制。

Session 是一种服务器端的存储机制，用来存储用户在 Web 应用程序中的信息。当用户访问应用程序时，服务器会创建一个唯一的会话标识符，并将它保存在 cookie 或 URL 参数中。服务器使用该标识符来查找与该用户关联的会话数据，包括用户身份验证信息、用户设置和其他用户特定信息。Session 的优点是它们可以存储更多的信息，而且更安全，因为会话数据存储在服务器上而不是在客户端上。

总的来说，Cookie 通常用于存储不敏感的用户信息，如用户偏好设置，而 Session 通常用于存储敏感的用户信息，如用户身份验证信息。

在不考虑安全的情况下，sesion与cookie是可以互换对吧？
是的，Session 和 Cookie 在某些情况下是可以互换使用的，但是它们之间还是存在一些差别。

Session 存储在服务器端，可以存储大量的数据，可以保存敏感信息，并且存储时间不受 Cookie 的大小和浏览器设置的限制。而 Cookie 存储在客户端浏览器中，存储的数据量有限，不太适合存储大量的数据或敏感信息，并且存储时间受 Cookie 的大小和浏览器设置的限制。

因此，如果需要存储大量的数据或敏感信息，或者需要存储时间较长的数据，建议使用 Session。如果只需要存储少量的数据，并且需要在客户端之间共享，则可以考虑使用 Cookie。
```

---





由于HTTP是无连接的，那么如何能够辨别当前的请求是来自哪个用户发起的呢？Session就是用来处理这种问题的，每个用户的会话都会有一个自己的Session对象，来自同一个浏览器的所有请求，就属于同一个会话。

但是HTTP协议是无连接的呀，那Session是如何做到辨别是否来自同一个浏览器呢？Session实际上是基于Cookie实现的，前面我们了解了Cookie，我们知道，服务端可以将Cookie保存到浏览器，当浏览器下次访问时，就会附带这些Cookie信息。

Session也利用了这一点，它会给浏览器设定一个叫做`JSESSIONID`的Cookie，值是一个随机的排列组合，而此Cookie就对应了你属于哪一个对话，只要我们的浏览器携带此Cookie访问服务器，服务器就会通过Cookie的值进行辨别，得到对应的Session对象，因此，这样就可以追踪到底是哪一个浏览器在访问服务器。

![image-20230403193413163](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304031934278.png)

那么现在，我们在用户登录成功之后，将用户对象添加到Session中，只要是此用户发起的请求，我们都可以从`HttpSession`中读取到存储在会话中的数据：

```java
HttpSession session = req.getSession();
session.setAttribute("user", user);
```

同时，如果用户没有登录就去访问首页，那么我们将发送一个重定向请求，告诉用户，需要先进行登录才可以访问：

```java
HttpSession session = req.getSession();
User user = (User) session.getAttribute("user");
if(user == null) {
    resp.sendRedirect("login");
    return;
}//可以将session看作一个会话，会话里面设置了内容，内容为user，下一次请求头会带着会话，如果在会话中得不到内容，就会报错
```

在访问的过程中，注意观察Cookie变化。

Session并不是永远都存在的，它有着自己的过期时间，默认时间为30分钟，若超过此时间，Session将丢失，我们可以在配置文件中修改过期时间：

```xml
<session-config>
    <session-timeout>1</session-timeout>
</session-config>
```

我们也可以在代码中使用`invalidate`方法来使Session立即失效：

```java
session.invalidate();
```

现在，通过Session，我们就可以更好地控制用户对于资源的访问，只有完成登陆的用户才有资格访问首页。

## Filter（是请求头在进入servlet之前的一道墙）

- 主要实现doFiler方法，生命周期是和servlet一样，都是init，到主方法，再到destroy（摧毁）

- 跟servlet一样，要注册，就是@webfileer（“路径”），访问此路径要经过过滤器

- 匹配路径，匹配通过，就会进入过滤器

- fileterchain.doflie（），过滤链，将请求继续传递给下一个过滤器使用的，如果没有匹配到过滤器后，将会进入servlet里面/

- - 过滤器也是按照顺序的，而且请求通过过滤器后，servlet反应后，传回去是需要再次经过过滤器的，有点类似于递归那样

  - 

  - ---

  - 

有了Session之后，我们就可以很好地控制用户的登陆验证了，只有授权的用户，才可以访问一些页面，但是我们需要一个一个去进行配置，还是太过复杂，能否一次性地过滤掉没有登录验证的用户呢？

过滤器相当于在所有访问前加了一堵墙，来自浏览器的所有访问请求都会首先经过过滤器，只有过滤器允许通过的请求，才可以顺利地到达对应的Servlet，而过滤器不允许的通过的请求，我们可以自由地进行控制是否进行重定向或是请求转发。并且过滤器可以添加很多个，就相当于添加了很多堵墙，我们的请求只有穿过层层阻碍，才能与Servlet相拥，像极了爱情。

![image-20230403192240934](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304031922043.png)

添加一个过滤器非常简单，只需要实现Filter接口，并添加`@WebFilter`注解即可：

```java
@WebFilter("/*")   //路径的匹配规则和Servlet一致，这里表示匹配所有请求
public class TestFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        
    }
}
```

这样我们就成功地添加了一个过滤器，那么添加一句打印语句看看，是否所有的请求都会经过此过滤器：

```java
HttpServletRequest request = (HttpServletRequest) servletRequest;
System.out.println(request.getRequestURL());
```

我们发现，现在我们发起的所有请求，一律需要经过此过滤器，并且所有的请求都没有任何的响应内容。

那么如何让请求可以顺利地到达对应的Servlet，也就是说怎么让这个请求顺利通过呢？我们只需要在最后添加一句：

```java
filterChain.doFilter(servletRequest, servletResponse);
```

那么这行代码是什么意思呢？

由于我们整个应用程序可能存在多个过滤器，那么这行代码的意思实际上是将此请求继续传递给下一个过滤器，当没有下一个过滤器时，才会到达对应的Servlet进行处理，我们可以再来创建一个过滤器看看效果：

```java
@WebFilter("/*")
public class TestFilter2 implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("我是2号过滤器");
        filterChain.doFilter(servletRequest, servletResponse);
    }
}
```

由于过滤器的过滤顺序是按照类名的自然排序进行的，因此我们将第一个过滤器命名进行调整。

我们发现，在经过第一个过滤器之后，会继续前往第二个过滤器，只有两个过滤器全部经过之后，才会到达我们的Servlet中。

![image-20230403192228288](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304031922389.png)

实际上，当`doFilter`方法调用时，就会一直向下直到Servlet，在Servlet处理完成之后，又依次返回到最前面的Filter，类似于递归的结构，我们添加几个输出语句来判断一下：

```java
@Override
public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
    System.out.println("我是2号过滤器");
    filterChain.doFilter(servletRequest, servletResponse);
    System.out.println("我是2号过滤器，处理后");
}
```

```java
@Override
public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
    System.out.println("我是1号过滤器");
    filterChain.doFilter(servletRequest, servletResponse);
    System.out.println("我是1号过滤器，处理后");
}
```

最后验证我们的结论。

同Servlet一样，Filter也有对应的HttpFilter专用类，它针对HTTP请求进行了专门处理，因此我们可以直接使用HttpFilter来编写：

```java
public abstract class HttpFilter extends GenericFilter {
    private static final long serialVersionUID = 7478463438252262094L;

    public HttpFilter() {
    }

    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
        if (req instanceof HttpServletRequest && res instanceof HttpServletResponse) {
            this.doFilter((HttpServletRequest)req, (HttpServletResponse)res, chain);
        } else {
            throw new ServletException("non-HTTP request or response");
        }
    }

    protected void doFilter(HttpServletRequest req, HttpServletResponse res, FilterChain chain) throws IOException, ServletException {
        chain.doFilter(req, res);
    }
}
```

那么现在，我们就可以给我们的应用程序添加一个过滤器，用户在未登录情况下，只允许静态资源和登陆页面请求通过，登陆之后畅行无阻：

```java
@WebFilter("/*")
public class MainFilter extends HttpFilter {
    @Override
    protected void doFilter(HttpServletRequest req, HttpServletResponse res, FilterChain chain) throws IOException, ServletException {
        String url = req.getRequestURL().toString();
        //判断是否为静态资源
        if(!url.endsWith(".js") && !url.endsWith(".css") && !url.endsWith(".png")){
            HttpSession session = req.getSession();
            User user = (User) session.getAttribute("user");
            //判断是否未登陆
            if(user == null && !url.endsWith("login")){
                res.sendRedirect("login");
                return;
            }
        }
        //交给过滤链处理
        chain.doFilter(req, res);
    }
}
```
