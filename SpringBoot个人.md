# SpringBoot

Springboot的功能：

- 创建独立的spring应用程序

- 直接切入tomcat，内置tomcat。jetty或undertow

- 提供一站式的starter依赖项，简化maven配置，直接导入对应的框架依赖  

- 尽可能自动配置第三方库，基本不需要配置了，就是原本MVC的注解开发，那些配置类，基本上封装好的

- 提供生产就绪功能，如指标、运行状况检查和外部化配置

- 没有代码生成，也没有XML配置的要求（XML是什 么，好吃吗）

- ---

- 不需要配置类，底层实现了，封装好的了

- 依赖版本已经规定好的了，只需要依赖名称就能导入依赖了。

- 只有一个配置文件

- 因为springBoot自动扫描的，所以我们直接创建一个controller就可以，以前webConfig的类中，需要添加扫描包的注解才可以，现在自动扫描，直接注解直接用

- ### mybatis配置数据库：

- ```yaml
  spring:
    datasource:
      url: jdbc:mysql://localhost:3306/mydb
      username: root
      password: password
      driver-class-name: com.mysql.cj.jdbc.Driver
  
  ```

  

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

```

#### Mapper案例：

```java
package com.example.Mapper;

import com.example.entity.Student;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

@Mapper//在Spring Boot注解开发中，使用@Mapper注解标记的接口并不是直接注册为Bean，
/// 而是由MyBatis框架在运行时生成对应的实现类，并将生成的实现类注册为Bean。
public interface TextMapper {
    @Select("select * from Student where id=#{id}")
    public Student getStudentById(int id);


}
//总结：springboot连接数据库：
//1·首先搞定配置文件，就是配置源那里，spring boot通常只有一个配置文件的
//2·sql语句+@Mapper注解：因为Springboot底层实现好了配置类了，并且自动扫描注解所以只需要按上面的那样写就行，而且还不需要注解写接口的实现类
//因为底层有将实现这个接口的实现类注册为bean了，由MyBatis框架在运行时生成对应的实现类，并将生成的实现类注册为Bean
//底层封装好的了，什么包扫描器，注册为bean，仅需要加个@conpenment就好，原本配置类中的@config

```

```java
package com.example;

import com.example.Mapper.TextMapper;
import com.example.entity.Student;
import jakarta.annotation.Resource;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest//这个注解代表 FirstSpringBootApplicationTests加入Ioc容器里面，使用容器的bean，前提是自己也是容器，所以
class FirstSpringBootApplicationTests {

//    @Resource
    @Autowired
    TextMapper textMapper;//不用管它是由实现了，sql数据库认的是sql语句，会用就行，底层怎样实现的后面再看，//
    //在Spring Boot注解开发中，使用@Mapper注解标记的接口并不是直接注册为Bean，而是由MyBatis框架在运行时生成对应的实现类，并将生成的实现类注册为Bean。

    @Test
    public void testMybatis(){
        Student student=textMapper.getStudentById(1);
        System.out.println(student);


    }

    @Test
    void contextLoads() {
    }

}

```

####  Controller：

```java
package com.example.controller;

import com.example.Mapper.TextMapper;
import com.example.entity.Student;
import jakarta.annotation.Resource;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class MainController {
    @RequestMapping("/index")
    @ResponseBody//输入什么直接输出什么，不需要经过视图解析器进行渲染。如这里返回字符串，那就是以字符串的形式输出，但是因为有Json转化，因为配置了，所以用了专业的对象进行输出，也就是转换成Json对象进行输出
    public String index(){//如果在Spring MVC中配置了Jackson或其他类库进行JSON转换，且返回值的数据类型是可转换为JSON的对象，那么该字符串会被自动转换为JSON格式并返回给客户端。
        //否则，该字符串将直接以文本形式返回给客户端。
        return "Hello";
    }
    @RequestMapping("/student")
    @ResponseBody
    public Student studentText(){
        Student student=new Student();
        student.setId(2003);
        student.setName("xiaoli");
        student.setSex("man");
        return student;//自动转换为Json对象，在MVC中要转化，例如： System.out.println(jsonObject.toJSONString());
        //当时现在不需要，自动转化成Json对象
    }
    @Value("czz")//这个不能用于局部变量，所以要在方法体的外面

    String Spel;//因为上面有Value注解，所以注解下面的值就会自动赋值，而注解${}这个表示输入键，赋值的是键对应的值，如果是字符串，则会赋值为字符串，也就是之前Mvc讲到的spel表达式
    @RequestMapping("/spelText")//使用Spel，也就是MVC那块内容，表达式
    @ResponseBody
    public String SpelText(){
        System.out.println("666");

        return Spel;
    }
	@Autowired
    TextMapper textMapper;
    @RequestMapping("/TextMysql")
    @ResponseBody


    public Student TextMysql(){

        Student student=textMapper.getStudentById(1);
        System.out.println(student);

        return student;


    }


}

```

#### 实体类：业务层，：

```java
package com.example.entity;

import lombok.Data;
import org.springframework.stereotype.Service;

@Service//可以将这个类的对象注册为bean，并加入到IOC容器中
@Data
public class Student {
    int id;
    String name;
    String sex;

}

```

#### 启动类：

```java
package com.example;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;

@SpringBootApplication
//@ComponentScan("com.example.Mapper")//不能随便添加这句话，不然自动扫描全部包就会被取消，只扫描你规定要扫描的包
public class FirstSpringBootApplication {

    public static void main(String[] args) {
        SpringApplication.run(FirstSpringBootApplication.class, args);
    }

}


```

## 日志系统： 

Slfj是日志门面，logback是日志实现

打印日志：

如果不用lombok注解的话，要创建一个logger类的对象，而且不能直接new一个对象出来：

```java
Logger logger=LoggerFactcy.getlog(这里填类名，需要该对象打印哪个类的日志，就填入哪一个类)
```

如果有lombok的话，使用@slf4j这个注解（在类的上面），这样就不用使用上面的那一句话。

### 在SpringBoot中打印日志信息

SpringBoot使用的是Slf4j作为日志门面，Logback（[Logback](http://logback.qos.ch/) 是log4j 框架的作者开发的新一代日志框架，它效率更高、能够适应诸多的运行环境，同时天然支持SLF4J）作为日志实现，对应的依赖为：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```

此依赖已经被包含了，所以我们如果需要打印日志，可以像这样：

```java
@RequestMapping("/login")
public String login(){
    Logger logger = LoggerFactory.getLogger(MainController.class);
    logger.info("用户访问了一次登陆界面");
    return "login";
}
```

因为我们使用了Lombok，所以直接一个注解也可以搞定哦：

```java
@Slf4j
@Controller
public class MainController {

    @RequestMapping("/login")
    public String login(){
        log.info("用户访问了一次登陆界面");
        return "login";
    }
```

日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，SpringBoot默认只会打印INFO以上级别的信息。

### 配置Logback日志

Logback官网：https://logback.qos.ch

和JUL一样，Logback也能实现定制化，我们可以编写对应的配置文件，SpringBoot推荐将配置文件名称命名为`logback-spring.xml`表示这是SpringBoot下Logback专用的配置，可以使用SpringBoot 的高级Proﬁle功能，它的内容类似于这样：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- 配置 -->
</configuration>
```

最外层由`configuration`包裹，一旦编写，那么就会替换默认的配置，所以如果内部什么都不写的话，那么会导致我们的SpringBoot项目没有配置任何日志输出方式，控制台也不会打印日志。

我们接着来看如何配置一个控制台日志打印，我们可以直接导入并使用SpringBoot为我们预设好的日志格式，在`org/springframework/boot/logging/logback/defaults.xml`中已经帮我们把日志的输出格式定义好了，我们只需要设置对应的`appender`即可：

```xml
<included>
   <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter" />
   <conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter" />
   <conversionRule conversionWord="wEx" converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter" />

   <property name="CONSOLE_LOG_PATTERN" value="${CONSOLE_LOG_PATTERN:-%clr(%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
   <property name="CONSOLE_LOG_CHARSET" value="${CONSOLE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>
   <property name="FILE_LOG_PATTERN" value="${FILE_LOG_PATTERN:-%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}} ${LOG_LEVEL_PATTERN:-%5p} ${PID:- } --- [%t] %-40.40logger{39} : %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
   <property name="FILE_LOG_CHARSET" value="${FILE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>

   <logger name="org.apache.catalina.startup.DigesterFactory" level="ERROR"/>
   <logger name="org.apache.catalina.util.LifecycleBase" level="ERROR"/>
   <logger name="org.apache.coyote.http11.Http11NioProtocol" level="WARN"/>
   <logger name="org.apache.sshd.common.util.SecurityUtils" level="WARN"/>
   <logger name="org.apache.tomcat.util.net.NioSelectorPool" level="WARN"/>
   <logger name="org.eclipse.jetty.util.component.AbstractLifeCycle" level="ERROR"/>
   <logger name="org.hibernate.validator.internal.util.Version" level="WARN"/>
   <logger name="org.springframework.boot.actuate.endpoint.jmx" level="WARN"/>
</included>
```

导入后，我们利用预设的日志格式创建一个控制台日志打印：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--  导入其他配置文件，作为预设  -->
    <include resource="org/springframework/boot/logging/logback/defaults.xml" />

    <!--  Appender作为日志打印器配置，这里命名随意  -->
    <!--  ch.qos.logback.core.ConsoleAppender是专用于控制台的Appender  -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
            <charset>${CONSOLE_LOG_CHARSET}</charset>
        </encoder>
    </appender>

    <!--  指定日志输出级别，以及启用的Appender，这里就使用了我们上面的ConsoleAppender  -->
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```

配置完成后，我们发现控制台已经可以正常打印日志信息了。

接着我们来看看如何开启文件打印，我们只需要配置一个对应的Appender即可：

```xml
<!--  ch.qos.logback.core.rolling.RollingFileAppender用于文件日志记录，它支持滚动  -->
<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <encoder>
        <pattern>${FILE_LOG_PATTERN}</pattern>
        <charset>${FILE_LOG_CHARSET}</charset>
    </encoder>
    <!--  自定义滚动策略，防止日志文件无限变大，也就是日志文件写到什么时候为止，重新创建一个新的日志文件开始写  -->
    <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
        <!--  文件保存位置以及文件命名规则，这里用到了%d{yyyy-MM-dd}表示当前日期，%i表示这一天的第N个日志  -->
        <FileNamePattern>log/%d{yyyy-MM-dd}-spring-%i.log</FileNamePattern>
        <!--  到期自动清理日志文件  -->
        <cleanHistoryOnStart>true</cleanHistoryOnStart>
        <!--  最大日志保留时间  -->
        <maxHistory>7</maxHistory>
        <!--  最大单个日志文件大小  -->
        <maxFileSize>10MB</maxFileSize>
    </rollingPolicy>
</appender>

<!--  指定日志输出级别，以及启用的Appender，这里就使用了我们上面的ConsoleAppender  -->
<root level="INFO">
    <appender-ref ref="CONSOLE"/>
    <appender-ref ref="FILE"/>
</root>
```

配置完成后，我们可以看到日志文件也能自动生成了。

## 多环境配置：

由于SpringBoot只会读取`application.properties`或是`application.yml`文件，那么怎么才能实现自由切换呢？SpringBoot给我们提供了一种方式，我们可以通过配置文件指定：

```yaml
spring:
  profiles:
    active: dev
```

接着我们分别创建两个环境的配置文件，`application-dev.yml`和`application-prod.yml`分别表示开发环境和生产环境的配置文件，比如开发环境我们使用的服务器端口为8080，而生产环境下可能就需要设置为80或是443端口，那么这个时候就需要不同环境下的配置文件进行区分：

```yaml
server:
  port: 8080
```

```yaml
server:
  port: 80
```

这样我们就可以灵活切换生产环境和开发环境下的配置文件了。

SpringBoot自带的Logback日志系统也是支持多环境配置的，比如我们想在开发环境下输出日志到控制台，而生产环境下只需要输出到文件即可，这时就需要进行环境配置：

```xml
<springProfile name="dev">
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="FILE"/>
    </root>
</springProfile>

<springProfile name="prod">
    <root level="INFO">
        <appender-ref ref="FILE"/>
    </root>
</springProfile>
```

注意`springProfile`是区分大小写的！

那如果我们希望生产环境中不要打包开发环境下的配置文件呢，我们目前虽然可以切换开发环境，但是打包的时候依然是所有配置文件全部打包，这样总感觉还欠缺一点完美，因此，打包的问题就只能找Maven解决了，Maven也可以设置多环境：

```xml
<!--分别设置开发，生产环境-->
<profiles>
    <!-- 开发环境 -->
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <environment>dev</environment>
        </properties>
    </profile>
    <!-- 生产环境 -->
    <profile>
        <id>prod</id>
        <activation>
            <activeByDefault>false</activeByDefault>
        </activation>
        <properties>
            <environment>prod</environment>
        </properties>
    </profile>
</profiles>
```

接着，我们需要根据环境的不同，排除其他环境的配置文件：

```xml
<resources>
<!--排除配置文件-->
    <resource>
        <directory>src/main/resources</directory>
        <!--先排除所有的配置文件-->
        <excludes>
            <!--使用通配符，当然可以定义多个exclude标签进行排除-->
            <exclude>application*.yml</exclude>
        </excludes>
    </resource>

    <!--根据激活条件引入打包所需的配置和文件-->
    <resource>
        <directory>src/main/resources</directory>
        <!--引入所需环境的配置文件-->
        <filtering>true</filtering>
        <includes>
            <include>application.yml</include>
            <!--根据maven选择环境导入配置文件-->
            <include>application-${environment}.yml</include>
        </includes>
    </resource>
</resources>
```

接着，我们可以直接将Maven中的`environment`属性，传递给SpringBoot的配置文件，在构建时替换为对应的值：

```yaml
spring:
  profiles:
    active: '@environment@'  #注意YAML配置文件需要加单引号，否则会报错
```

这样，根据我们Maven环境的切换，SpringBoot的配置文件也会进行对应的切换。

最后我们打开Maven栏目，就可以自由切换了，直接勾选即可，注意切换环境之后要重新加载一下Maven项目，不然不会生效！

***

总结多环境：主要是在自己的一开始的文件，也就是刚开始的配置文件，去指定需要那个文件作为配置文件：注意自己写的配置文件，名称基本上是有要求的：

```yaml

spring:
  profiles:
    active: dev//在resource文件夹里面放着这个文件，也是一个配置文件，名称有要求的，注意
```



## 打包运行

首先：清理target文件夹，然后点击package，因为已经装好了打包插件了，所以点就好了；点然后target文件夹会有一个jar包出现，将jar包文件拿出来，放到服务器或者其他电脑去跑就行了

怎样运行jar包，部署：在jar包的位置打开cmd，输入

```cmd
java -jar jar名称（好像按tab键会自动填充）.jar
```

：这样就好了

# 中间键

## git控制器：

# git

- git的作用，是记录文件的版本，显示修改修改的地方，显示与迭代版本的不一样
- - 创建用户

```git
git config --global user.name "L"
git config --global user.email 123456@qq.com

```

![image-20230325011513051](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112036042.png)

使用git，本地仓库：

- ```git
  git init
  ```

  

在一个文件夹中打开git，并输入这条命令，可以将一个文件夹变成一个本地仓库。

#### 注意事项：

- 在变成本地仓库后，我们的对象是仓库，而不是仓库的文件，比如我提交日志，回滚去某一个记录。我第一个记录是创建一个文件，第二个记录是创建了三个文件，现在我有4个文件，但是我回滚到第一个记录时，只剩下第一个文件，因为我记录点当时仓库是只有一个文件，所以我们的对象是仓库

  ---

  ```
  git status//查看当前的状态，仓库的状态
  ```

  ```
  git add "文件名"添加文件进行追踪，文件放入暂存区，还没放入本地仓库
  ```

  ```
  git commit -m '文件名或者是修改记录标志，如modify first，英文表示第一次修改'
  ```

  ```
  git log 查看提交的日志，记录
  ```

  ```
  git show +提交记录的id，每次提交都会有一个id，（查看修改记录）
  ```

  ---

  - .gitignore文件，后缀名为gitignore，这个文件是是忽视文件，（在这个文件里面记录的文件名称和后缀名，不会被git追踪和显示）
  - 匹配规则：
  - <img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112036676.png" alt="image-20230325143056309" style="zoom:200%;" />

  ---

  ----

  ---

  - ### 回滚：

  - - 回滚的个人理解：

      - 回滚像mysql里面的事务，开始事务后，没有commit的，是可以回到标记点的，那个处理的文件是可以复原的，因为没有提交是使用副本处理，提交才是修改sql里面的内容。那个标记点是要提前标记好的

      - git也是一样有回滚的功能，那个标志点是提交的记录

      - ```
        git reset --hard 标志点，仓库回到标志点位置
        ```

        ```
        git refloy查看所有的分支和操作记录，也就是提交记录
        ```


---

- ## 分支

分支：分支之间互不影响，相互独立，分道扬镳

一个文件，你写你的，我写我的，不同的分支显示不一样，开始分道扬镳，相互独立，互不影响：例如一个文件，两个分支，两个分支进行修改，并且修改内容都不一样，那么每个分支看到的内容就会不一样

- 创建分支：

- ```
  git branch 分支名称
  ```

  ```
  git branch 查看分支
  git branch -d 分支名称//删除分支
  git checkout //切换分支
  ```

  ### 分久必合

  将一个分支的文件，注意是文件，合并到主分支，也就是分支主干：

  首先先切换到主分支master再进行分支的文件的合并

  ```
  git merge 分支名称
  git diff
  ```

  如果文件发生冲突，如主干分支和某个分支的文件名一样，内容不一样，可以用git diff进行查看什么冲突，可以手动去查看，并修复再提交，比如二者都保留，或者删去xxx

  ### 注意：

  - 合并分支，不是两个分支合并，而是分支的文件进行合并，合并后，被合并的分支，文件依然有，分支依然存在

  - ---

  - 变基：（暂时缺失）



- ## 远程仓库：

远程仓库类似于图床，云盘那样，可以同步到github，码云…上面

- #### 1·同步本地仓库：

- - ```
    git remote add origin(远程仓库的名称)+远程仓库的地址 连接远程仓库
    ```

  - ```
    git push origin.分支名称  分支名称一般都是master，是指远程仓库的分支，上传本地仓库到远程仓库
    ```

    ```
    绑定远程仓库分支，（不绑定也没关系，只是上传的时候多敲个分支，如上面的master分支）
    git push --set--upstream 远程仓库分支：本地分支
    这样绑定之后，可以用git push origin这样就能直接上传到远程仓库的master分支上
    ```

  ```
  git clone 远程仓库的地址，这样可以克隆项目到本地仓库
  ```

  

- ### 冲突与拉取

- git的目的最主要是多人合作，但是这也会引发一些问题冲突，如A写了代码提交到远程仓库，但是b写了代码也是要提交到远程仓库，但是两个提交会发生冲突，比如a先提交，远程仓库已经提交成功，但是在b的本地仓库里面，项目是a提交前clone下来的，a写入的东西，b的本地仓库是没有的，这个时候我们要刷新仓库在提交：因为每个用户都是一个分支，分支汇入主支路 

- 解决办法：首先我们要先读取最新的远程仓库，也就是捉取，获得分支，再合并分支，再重新提交

  ```
  git fetch origin 
  git merge origin
  git commit -a -m "midify xxx"
  ```

  第二个办法：不用手工去合并分支：

  ```
  git pull origin//这个是获取+合并，等价于上面的第一个和第二个步骤
  git commit -m "midify xxx"//如果发生冲突要手动解决，两个都一样，如上面介绍到的分支冲突一样的
  ```

  ---

  ---

  

## linux

#### Linux常用的指令

1.ifconfig 查网络地址，连接ssh

2.pwd 查当前目录

3.touch 创建文件夹

4.ls 查看当前目录文件夹有啥

Ls -a列出文件包括隐藏文件，和上面差不多，多了隐藏文件，隐藏文件前面有.

Ls -a-l 在上面基础上加上详细信息

5.clear 清屏

6.cd-返回

7.cd.查看当前目录

Cd ~回到home目录

8.cd ..去上一级目录

9.cd~回到用户目录

10.sudo -s切换为root用户 #是root用户，￥号是普通用户

当前是root用户时，输入exit表示退出root，变成普通用户

Sudo +xxx命令，临时授权root权限给用户

切换用户在root中切换

每一个用户都有用户组：输入id可看当前用户加入的用户组有什么

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112039756.png)

新建用户，没有在用户组添加该新用户sudo属性，该用户不能使用sudo语句

就是这种错误：<img src="C:/Users/L/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png" alt="img" style="zoom: 200%;" />

解决办法：

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112039565.png)

 

 

 

 

11.rm删除文件的

12.su-用户名切换用户的

13.psaawd设置用户密码的

14.useradd用户增加和adduser区别：

添加用户只能root用户来添加，普通用户没有权限，添加用户有俩种方式

在centos系统下，这俩种方式没有区别， **都会在/home下自动创建与用户名同名的用户目录，且都是需要使用 passwd userName 命令来设置用户密码的，只有设置完密码后才可以正常登录。**

在unbantu系统下，这俩种方式是有区别的，使用 useradd userName命令 **不会在/home下自动创建与用户名同名的用户目录，且不会自动选择shell版本，后续也是需要使用 passwd username来设置密码的 。** 而使用 adduser userName 命令的话 **是会在/home目录下自动创建与用户名同名的用户目录，也会自动选择shell版本，且会自动提示输入用户密码，对用户比较友好，后续不需要在使用passwd来设置密码。**

删除用户，并不会删除用户相关的文件

删除用户且一起删除家目录

 

15.man+指令，可以查剩余的参数，如：useradd -b,输入man useradd,里面会有-b，-p分别是啥指令

16.安装宝塔面板：if [ -f /usr/bin/curl ];then curl -sSO https://download.bt.cn/install/install_panel.sh;else wget -O install_panel.sh https://download.bt.cn/install/install_panel.sh;fi;bash install_panel.sh ed8484bec

 

 

17·![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112039396.png)文件管理细读：首先第一个字符，d代表目录也就是文件夹，-代表普通文件

<img src="C:/Users/L/AppData/Local/Temp/msohtmlclip1/01/clip_image010.png" alt="img" style="zoom:200%;" />

后面字符三个三个为一组，每组分别对应，用户，用户组，other。一共三组，如2-5个字符rwx代表用户的权限。R代表读权限，w代表写权限，x代表执行权限。

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112044687.png)

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112044209.png)

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112041815.png)

 

19·touch创建文件，mkdir创建目录，也就是文件夹

20  ls-a-l等价于ll

21·![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112041256.png)

使用chmod去修改权限，u代表user，第一组，g代表grouse，用户组，o代表其他，a代表全部，也就是all +— xxx（权限如rw） xxx（文件名）

方法二：

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112041953.png)

就是省略u/g/o/a+- 直接用数字，一个数字代表一个组，如664代表u是rwx，g是rwx，other是r

 

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112041943.png)

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112041452.png)

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112042933.png)进入当前目录的文件夹

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112042475.png)将上一级目录的text文件复制到当前目录，文件名为xxx（在当前目录叫xxx)

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112042889.png)因为加了个-r，所以将整个目录（文件夹)复制了并保存在当前目录名称为study_copied，后面的是保存路径，不加/之类的就是在本地目录

Mv是移动的指令，和cp类似，rm xxx删除文件 rm -r xxx删除文件夹xxx

 

 

22·top可以查看当前系统情况

23·free可以查看当前内存使用情况

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304112042135.png)

 

 

解压和压缩

这里我们使用`tar`命令来完成文件亚索和解压操作，在Linux中比较常用的是gzip格式，后缀名一般为.gz，tar命令的参数-c表示对文件进行压缩，创建新的压缩文件，-x表示进行解压操作，-z表示以gzip格式进行操作，-v可以在处理过程中输出一些日志信息，-f表示对普通文件进行操作，这里我们创建三个文件并对这三个文件进行打包：

所以一般zcvf压缩 zxvf解压，压缩文件夹，在名称后面加上/

 

Mv xxx hhh 将xxx移动到hhh （移动…都是这种顺序，如cp也是这种顺序，将xxx复制名称为hhh）

Tar -zcvf xxx hhhh kk 将kk和hhhh文件打包压缩为xxx文件（像c语言字符串复制的顺序）

#  redis

## 邮件发送：封装了Mail模块框架

首先：导入依赖，也可以在spring创建时选择：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

第二步：配置文件：（写在项目配置那里，也就是数据库连接那里）

```yaml
spring:
  mail:
  	# 163邮箱的地址为smtp.163.com，直接填写即可
    host: smtp.163.com
    # 你申请的163邮箱
    username: javastudy111@163.com
    # 注意密码是在开启smtp/pop3时自动生成的，记得保存一下，不然就找不到了
    password: AZJTOAWZESLMHTNI
```

第三步：使用对应的类

```java
@SpringBootTest
class SpringBootTestApplicationTests {

  	//JavaMailSender是专门用于发送邮件的对象，自动配置类已经提供了Bean
    @Autowired
    JavaMailSender sender;//springboot已经注册为bean了，加入到Ioc容器里面了

    @Test
    void contextLoads() {
      	//SimpleMailMessage是一个比较简易的邮件封装，支持设置一些比较简单内容
        SimpleMailMessage message = new SimpleMailMessage();
      	//设置邮件标题
        message.setSubject("【电子科技大学教务处】关于近期学校对您的处分决定");
      	//设置邮件内容
        message.setText("发送的内容");
      	//设置邮件发送给谁，可以多个，这里就发给你的QQ邮箱
        message.setTo("你的QQ号@qq.com");
      	//邮件发送者，这里要与配置文件中的保持一致
        message.setFrom("javastudy111@163.com");
      	//OK，万事俱备只欠发送
        sender.send(message);
    }

}//最重要的是这几个方法
```

如果需要添加附件等更多功能，可以使用MimeMessageHelper来帮助我们完成：

```java
@Test
void contextLoads() throws MessagingException {
  	//创建一个MimeMessage
    MimeMessage message = sender.createMimeMessage();
  	//使用MimeMessageHelper来帮我们修改MimeMessage中的信息
    MimeMessageHelper helper = new MimeMessageHelper(message, true);//可以设置助手，帮忙发送邮件，简单化
    helper.setSubject("Test");
    helper.setText("lbwnb");
    helper.setTo("你的QQ号@qq.com");
    helper.setFrom("javastudy111@163.com");
  	//发送修改好的MimeMessage
    sender.send(message);
}
```

## 邮件注册：

## JPA（类似于mybatis的框架，但是更加简洁）

### 使用JPA

同样的，我们只需要导入stater依赖即可：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

接着我们可以直接创建一个类，比如账户类，我们只需要把一个账号对应的属性全部定义好即可：

```java
@Data
public class Account {
    int id;
    String username;
    String password;
}
```

接着，我们可以通过注解形式，在属性上添加数据库映射关系，这样就能够让JPA知道我们的实体类对应的数据库表长啥样。

```java
@Data
@Entity   //表示这个类是一个实体类
@Table(name = "users")    //对应的数据库中表名称
public class Account {

    @GeneratedValue(strategy = GenerationType.IDENTITY)   //生成策略，这里配置为自增
    @Column(name = "id")    //对应表中id这一列
    @Id     //此属性为主键
    int id;

    @Column(name = "username")   //对应表中username这一列
    String username;

    @Column(name = "password")   //对应表中password这一列
    String password;
}
```

接着我们来修改一下配置文件：

```yaml
spring:
  jpa:
		#开启SQL语句执行日志信息
    show-sql: true
    hibernate:
    	#配置为自动创建
      ddl-auto: create
```

`ddl-auto`属性用于设置自动表定义，可以实现自动在数据库中为我们创建一个表，表的结构会根据我们定义的实体类决定，它有4种

* create 启动时删数据库中的表，然后创建，退出时不删除数据表 
* create-drop 启动时删数据库中的表，然后创建，退出时删除数据表 如果表不存在报错 
* update 如果启动时表格式不一致则更新表，原有数据保留 
* validate 项目启动表结构进行校验 如果不一致则报错

我们可以在日志中发现，在启动时执行了如下SQL语句：

```
Hibernate: create table users (id integer not null auto_increment, password varchar(255), username varchar(255), primary key (id)) engine=InnoDB
```

而我们的数据库中对应的表已经创建好了。

我们接着来看如何访问我们的表，我们需要创建一个Repository实现类：

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Integer> {
    
}
```

注意JpaRepository有两个泛型，前者是具体操作的对象实体，也就是对应的表，后者是ID的类型，接口中已经定义了比较常用的数据库操作。编写接口继承即可，我们可以直接注入此接口获得实现类：

```java
@SpringBootTest
class JpaTestApplicationTests {

    @Resource
    AccountRepository repository;

    @Test
    void contextLoads() {
      	//直接根据ID查找
        repository.findById(1).ifPresent(System.out::println);
    }

}
```

运行后，成功得到查询结果。我们接着来测试增删操作：

```java
@Test
void addAccount(){
    Account account = new Account();
    account.setUsername("Admin");
    account.setPassword("123456");
    account = repository.save(account);  //返回的结果会包含自动生成的主键值
    System.out.println("插入时，自动生成的主键ID为："+account.getId());
}
```

```java
@Test
void deleteAccount(){
    repository.deleteById(2);   //根据ID删除对应记录
}
```

```java
@Test
void pageAccount() {
    repository.findAll(PageRequest.of(0, 1)).forEach(System.out::println);  //直接分页
}
```

我们发现，使用了JPA之后，整个项目的代码中没有出现任何的SQL语句，可以说是非常方便了，JPA依靠我们提供的注解信息自动完成了所有信息的映射和关联。

相比Mybatis，JPA几乎就是一个全自动的ORM框架，而Mybatis则顶多算是半自动ORM框架。

### 方法名称拼接自定义SQL

虽然接口预置的方法使用起来非常方便，但是如果我们需要进行条件查询等操作或是一些判断，就需要自定义一些方法来实现，同样的，我们不需要编写SQL语句，而是通过方法名称的拼接来实现条件判断，这里列出了所有支持的条件判断名称：

| `Distinct`             | `findDistinctByLastnameAndFirstname`                         | `select distinct … where x.lastname = ?1 and x.firstname = ?2` |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `And`                  | `findByLastnameAndFirstname`                                 | `… where x.lastname = ?1 and x.firstname = ?2`               |
| `Or`                   | `findByLastnameOrFirstname`                                  | `… where x.lastname = ?1 or x.firstname = ?2`                |
| `Is`，`Equals`         | `findByFirstname`,`findByFirstnameIs`,`findByFirstnameEquals` | `… where x.firstname = ?1`                                   |
| `Between`              | `findByStartDateBetween`                                     | `… where x.startDate between ?1 and ?2`                      |
| `LessThan`             | `findByAgeLessThan`                                          | `… where x.age < ?1`                                         |
| `LessThanEqual`        | `findByAgeLessThanEqual`                                     | `… where x.age <= ?1`                                        |
| `GreaterThan`          | `findByAgeGreaterThan`                                       | `… where x.age > ?1`                                         |
| `GreaterThanEqual`     | `findByAgeGreaterThanEqual`                                  | `… where x.age >= ?1`                                        |
| `After`                | `findByStartDateAfter`                                       | `… where x.startDate > ?1`                                   |
| `Before`               | `findByStartDateBefore`                                      | `… where x.startDate < ?1`                                   |
| `IsNull`，`Null`       | `findByAge(Is)Null`                                          | `… where x.age is null`                                      |
| `IsNotNull`，`NotNull` | `findByAge(Is)NotNull`                                       | `… where x.age not null`                                     |
| `Like`                 | `findByFirstnameLike`                                        | `… where x.firstname like ?1`                                |
| `NotLike`              | `findByFirstnameNotLike`                                     | `… where x.firstname not like ?1`                            |
| `StartingWith`         | `findByFirstnameStartingWith`                                | `… where x.firstname like ?1`（参数与附加`%`绑定）           |
| `EndingWith`           | `findByFirstnameEndingWith`                                  | `… where x.firstname like ?1`（参数与前缀`%`绑定）           |
| `Containing`           | `findByFirstnameContaining`                                  | `… where x.firstname like ?1`（参数绑定以`%`包装）           |
| `OrderBy`              | `findByAgeOrderByLastnameDesc`                               | `… where x.age = ?1 order by x.lastname desc`                |
| `Not`                  | `findByLastnameNot`                                          | `… where x.lastname <> ?1`                                   |
| `In`                   | `findByAgeIn(Collection<Age> ages)`                          | `… where x.age in ?1`                                        |
| `NotIn`                | `findByAgeNotIn(Collection<Age> ages)`                       | `… where x.age not in ?1`                                    |
| `True`                 | `findByActiveTrue()`                                         | `… where x.active = true`                                    |
| `False`                | `findByActiveFalse()`                                        | `… where x.active = false`                                   |
| `IgnoreCase`           | `findByFirstnameIgnoreCase`                                  | `… where UPPER(x.firstname) = UPPER(?1)`                     |

比如我们想要实现根据用户名模糊匹配查找用户：

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Integer> {
		//按照表中的规则进行名称拼接，不用刻意去记，IDEA会有提示
    List<Account> findAllByUsernameLike(String str);
}
```

```java
@Test
void test() {
    repository.findAllByUsernameLike("%T%").forEach(System.out::println);
}
```

又比如我们想同时根据用户名和ID一起查询：

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Integer> {

    Account findByIdAndUsername(int id, String username);
  //可以使用Optional类进行包装，Optional<Account> findByIdAndUsername(int id, String username);
    
    List<Account> findAllByUsernameLike(String str);
}
```

```java
@Test
void test() {
    System.out.println(repository.findByIdAndUsername(1, "Test"));
}
```

比如我们想判断数据库中是否存在某个ID的用户：

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Integer> {

    Account findByIdAndUsername(int id, String username);

    List<Account> findAllByUsernameLike(String str);

    boolean existsAccountById(int id);
}
```

```java
@Test
void test() {
    System.out.println(repository.existsAccountByUsername("Test"));
}
```

注意自定义条件操作的方法名称一定要遵循规则，不然会出现异常：

```
Caused by: org.springframework.data.repository.query.QueryCreationException: Could not create query for public abstract  ...
```

### 关联查询

在实际开发中，比较常见的场景还有关联查询，也就是我们会在表中添加一个外键字段，而此外键字段又指向了另一个表中的数据，当我们查询数据时，可能会需要将关联数据也一并获取，比如我们想要查询某个用户的详细信息，一般用户简略信息会单独存放一个表，而用户详细信息会单独存放在另一个表中。当然，除了用户详细信息之外，可能在某些电商平台还会有用户的购买记录、用户的购物车，交流社区中的用户帖子、用户评论等，这些都是需要根据用户信息进行关联查询的内容。

![img](https://gitee.com/ljzcomeon/typora-photo/raw/master/202304120908684.jpeg)

我们知道，在JPA中，每张表实际上就是一个实体类的映射，而表之间的关联关系，也可以看作对象之间的依赖关系，比如用户表中包含了用户详细信息的ID字段作为外键，那么实际上就是用户表实体中包括了用户详细信息实体对象：

```java
@Data
@Entity
@Table(name = "users_detail")
public class AccountDetail {

    @Column(name = "id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Id
    int id;

    @Column(name = "address")
    String address;

    @Column(name = "email")
    String email;

    @Column(name = "phone")
    String phone;

    @Column(name = "real_name")
    String realName;
}
```

而用户信息和用户详细信息之间形成了一对一的关系，那么这时我们就可以直接在类中指定这种关系：

```java
@Data
@Entity
@Table(name = "users")
public class Account {

    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    @Id
    int id;

    @Column(name = "username")
    String username;

    @Column(name = "password")
    String password;

    @JoinColumn(name = "detail_id")   //指定存储外键的字段名称
    @OneToOne    //声明为一对一关系
    AccountDetail detail;
}
```

在修改实体类信息后，我们发现在启动时也进行了更新，日志如下：

```
Hibernate: alter table users add column detail_id integer
Hibernate: create table users_detail (id integer not null auto_increment, address varchar(255), email varchar(255), phone varchar(255), real_name varchar(255), primary key (id)) engine=InnoDB
Hibernate: alter table users add constraint FK7gb021edkxf3mdv5bs75ni6jd foreign key (detail_id) references users_detail (id)
```

是不是感觉非常方便！都懒得去手动改表结构了。

接着我们往用户详细信息中添加一些数据，一会我们可以直接进行查询：

```java
@Test
void pageAccount() {
    repository.findById(1).ifPresent(System.out::println);
}
```

查询后，可以发现，得到如下结果：

```
Hibernate: select account0_.id as id1_0_0_, account0_.detail_id as detail_i4_0_0_, account0_.password as password2_0_0_, account0_.username as username3_0_0_, accountdet1_.id as id1_1_1_, accountdet1_.address as address2_1_1_, accountdet1_.email as email3_1_1_, accountdet1_.phone as phone4_1_1_, accountdet1_.real_name as real_nam5_1_1_ from users account0_ left outer join users_detail accountdet1_ on account0_.detail_id=accountdet1_.id where account0_.id=?
Account(id=1, username=Test, password=123456, detail=AccountDetail(id=1, address=四川省成都市青羊区, email=8371289@qq.com, phone=1234567890, realName=本伟))
```

也就是，在建立关系之后，我们查询Account对象时，会自动将关联数据的结果也一并进行查询。

那要是我们只想要Account的数据，不想要用户详细信息数据怎么办呢？我希望在我要用的时候再获取详细信息，这样可以节省一些网络开销，我们可以设置懒加载，这样只有在需要时才会向数据库获取：

```java
@JoinColumn(name = "detail_id")
@OneToOne(fetch = FetchType.LAZY)    //将获取类型改为LAZY
AccountDetail detail;
```

接着我们测试一下：

```java
@Transactional   //懒加载属性需要在事务环境下获取，因为repository方法调用完后Session会立即关闭
@Test
void pageAccount() {
    repository.findById(1).ifPresent(account -> {
        System.out.println(account.getUsername());   //获取用户名
        System.out.println(account.getDetail());  //获取详细信息（懒加载）
    });
}
```

接着我们来看看控制台输出了什么：

```
Hibernate: select account0_.id as id1_0_0_, account0_.detail_id as detail_i4_0_0_, account0_.password as password2_0_0_, account0_.username as username3_0_0_ from users account0_ where account0_.id=?
Test
Hibernate: select accountdet0_.id as id1_1_0_, accountdet0_.address as address2_1_0_, accountdet0_.email as email3_1_0_, accountdet0_.phone as phone4_1_0_, accountdet0_.real_name as real_nam5_1_0_ from users_detail accountdet0_ where accountdet0_.id=?
AccountDetail(id=1, address=四川省成都市青羊区, email=8371289@qq.com, phone=1234567890, realName=卢本)
```

可以看到，获取用户名之前，并没有去查询用户的详细信息，而是当我们获取详细信息时才进行查询并返回AccountDetail对象。

那么我们是否也可以在添加数据时，利用实体类之间的关联信息，一次性添加两张表的数据呢？可以，但是我们需要稍微修改一下级联关联操作设定：

```java
@JoinColumn(name = "detail_id")
@OneToOne(fetch = FetchType.LAZY, cascade = CascadeType.ALL) //设置关联操作为ALL
AccountDetail detail;
```

* ALL：所有操作都进行关联操作
* PERSIST：插入操作时才进行关联操作
* REMOVE：删除操作时才进行关联操作
* MERGE：修改操作时才进行关联操作

可以多个并存，接着我们来进行一下测试：

```java
@Test
void addAccount(){
    Account account = new Account();
    account.setUsername("Nike");
    account.setPassword("123456");
    AccountDetail detail = new AccountDetail();
    detail.setAddress("重庆市渝中区解放碑");
    detail.setPhone("1234567890");
    detail.setEmail("73281937@qq.com");
    detail.setRealName("张三");
  	account.setDetail(detail);
    account = repository.save(account);
    System.out.println("插入时，自动生成的主键ID为："+account.getId()+"，外键ID为："+account.getDetail().getId());
}
```

可以看到日志结果：

```
Hibernate: insert into users_detail (address, email, phone, real_name) values (?, ?, ?, ?)
Hibernate: insert into users (detail_id, password, username) values (?, ?, ?)
插入时，自动生成的主键ID为：6，外键ID为：3
```

结束后会发现数据库中两张表都同时存在数据。

接着我们来看一对多关联，比如每个用户的成绩信息：

```java
@JoinColumn(name = "uid")  //注意这里的name指的是Score表中的uid字段对应的就是当前的主键，会将uid外键设置为当前的主键
@OneToMany(fetch = FetchType.LAZY, cascade = CascadeType.REMOVE)   //在移除Account时，一并移除所有的成绩信息，依然使用懒加载
List<Score> scoreList;
```

```java
@Data
@Entity
@Table(name = "users_score")   //成绩表，注意只存成绩，不存学科信息，学科信息id做外键
public class Score {

    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    @Id
    int id;

    @OneToOne   //一对一对应到学科上
    @JoinColumn(name = "cid")
    Subject subject;

    @Column(name = "socre")
    double score;

    @Column(name = "uid")
    int uid;
}
```

```java
@Data
@Entity
@Table(name = "subjects")   //学科信息表
public class Subject {

    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cid")
    @Id
    int cid;

    @Column(name = "name")
    String name;

    @Column(name = "teacher")
    String teacher;

    @Column(name = "time")
    int time;
}
```

在数据库中填写相应数据，接着我们就可以查询用户的成绩信息了：

```java
@Transactional
@Test
void test() {
    repository.findById(1).ifPresent(account -> {
        account.getScoreList().forEach(System.out::println);
    });
}
```

成功得到用户所有的成绩信息，包括得分和学科信息。

同样的，我们还可以将对应成绩中的教师信息单独分出一张表存储，并建立多对一的关系，因为多门课程可能由同一个老师教授（千万别搞晕了，一定要理清楚关联关系，同时也是考验你的基础扎不扎实）：

```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "tid")   //存储教师ID的字段，和一对一是一样的，也会当前表中创个外键
Teacher teacher;
```

接着就是教师实体类了：

```java
@Data
@Entity
@Table(name = "teachers")
public class Teacher {

    @Column(name = "id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Id
    int id;

    @Column(name = "name")
    String name;

    @Column(name = "sex")
    String sex;
}
```

最后我们再进行一下测试：

```java
@Transactional
@Test
void test() {
    repository.findById(3).ifPresent(account -> {
        account.getScoreList().forEach(score -> {
            System.out.println("课程名称："+score.getSubject().getName());
            System.out.println("得分："+score.getScore());
            System.out.println("任课教师："+score.getSubject().getTeacher().getName());
        });
    });
}
```

成功得到多对一的教师信息。

最后我们再来看最复杂的情况，现在我们一门课程可以由多个老师教授，而一个老师也可以教授多个课程，那么这种情况就是很明显的多对多场景，现在又该如何定义呢？我们可以像之前一样，插入一张中间表表示教授关系，这个表中专门存储哪个老师教哪个科目：

```java
@ManyToMany(fetch = FetchType.LAZY)   //多对多场景
@JoinTable(name = "teach_relation",     //多对多中间关联表
        joinColumns = @JoinColumn(name = "cid"),    //当前实体主键在关联表中的字段名称
        inverseJoinColumns = @JoinColumn(name = "tid")   //教师实体主键在关联表中的字段名称
)
List<Teacher> teacher;
```

接着，JPA会自动创建一张中间表，并自动设置外键，我们就可以将多对多关联信息编写在其中了。

### JPQL自定义SQL语句

虽然SpringDataJPA能够简化大部分数据获取场景，但是难免会有一些特殊的场景，需要使用复杂查询才能够去完成，这时你又会发现，如果要实现，只能用回Mybatis了，因为我们需要自己手动编写SQL语句，过度依赖SpringDataJPA会使得SQL语句不可控。

使用JPA，我们也可以像Mybatis那样，直接编写SQL语句，不过它是JPQL语言，与原生SQL语句很类似，但是它是面向对象的，当然我们也可以编写原生SQL语句。

比如我们要更新用户表中指定ID用户的密码：

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Integer> {

    @Transactional    //DML操作需要事务环境，可以不在这里声明，但是调用时一定要处于事务环境下
    @Modifying     //表示这是一个DML操作
    @Query("update Account set password = ?2 where id = ?1") //这里操作的是一个实体类对应的表，参数使用?代表，后面接第n个参数
    int updatePasswordById(int id, String newPassword);
}
```

```java
@Test
void updateAccount(){
    repository.updatePasswordById(1, "654321");
}
```

现在我想使用原生SQL来实现根据用户名称修改密码：

```java
@Transactional
@Modifying
@Query(value = "update users set password = :pwd where username = :name", nativeQuery = true) //使用原生SQL，和Mybatis一样，这里使用 :名称 表示参数，当然也可以继续用上面那种方式。
int updatePasswordByUsername(@Param("name") String username,   //我们可以使用@Param指定名称
                             @Param("pwd") String newPassword);
```

```java
@Test
void updateAccount(){
    repository.updatePasswordByUsername("Admin", "654321");
}
```

通过编写原生SQL，在一定程度上弥补了SQL不可控的问题。

虽然JPA能够为我们带来非常便捷的开发体验，但是正式因为太便捷了，保姆级的体验有时也会适得其反，可能项目开发到后期特别庞大时，就只能从底层SQL语句开始进行优化，而由于JPA尽可能地在屏蔽我们对SQL语句的编写，所以后期优化是个大问题，并且Hibernate相对于Mybatis来说，更加重量级。不过，在微服务的时代，单体项目一般不会太大，而JPA的劣势并没有太明显地体现出来。

---

### 个人总结：

使用方法：

首先导入环境依赖，pom文件加入依赖：

配置文件（也就是springboot配置数据那里）

````
接着我们来修改一下配置文件：

```yaml
spring:
  jpa:
		#开启SQL语句执行日志信息
    show-sql: true
    hibernate:
    	#配置为自动创建
      ddl-auto: create
```

`ddl-auto`属性用于设置自动表定义，可以实现自动在数据库中为我们创建一个表，表的结构会根据我们定义的实体类决定，它有4种

* create 启动时删数据库中的表，然后创建，退出时不删除数据表 
* create-drop 启动时删数据库中的表，然后创建，退出时删除数据表 如果表不存在报错 
* update 如果启动时表格式不一致则更新表，原有数据保留 
* validate 项目启动表结构进行校验 如果不一致则报错
````

然后创建实体类，注意属性必须要与mysql的字段保持一致

3·写入注解：

```java
@Data//使用lombok
@Entity   //表示这个类是一个实体类
@Table(name = "users")    //对应的数据库中表名称
public class Account {

    @GeneratedValue(strategy = GenerationType.IDENTITY)   //生成策略，这里配置为自增
    @Column(name = "id")    //对应表中id这一列
    @Id     //此属性为主键
    int id;

    @Column(name = "username")   //对应表中username这一列
    String username;

    @Column(name = "password")   //对应表中password这一列
    String password;
}
```

4·加个接口，Mapper，这个接口不需要写东西，继承就行

我们需要创建一个Repository实现类：

```java
@Repository//因为底层有实现类，可以注册为bean，类似于mybatis的Mapper那个接口
public interface AccountRepository extends JpaRepository<Account, Integer> {
    
}
//@Repository 是一个在 Java Spring 框架中使用的注解，用于标注类为持久化层（数据访问层）的组件，通常与数据库交互。它的作用是告诉 Spring 框架，被标注的类是用于进行数据访问的组件，它可以处理数据库操作、持久化、事务等相关的操作。

//具体而言，@Repository 注解的主要用途包括：

//数据访问：@Repository 用于标注用于进行数据库操作的类，如数据的增删改查、数据持久化等。这些类通常包含对数据库的操作，例如 SQL 查询、使用 ORM（对象关系映射）工具与数据库交互等。

//异常转换：@Repository 注解还可以将数据库相关的异常转换为 Spring 框架的统一异常体系，这样可以在数据访问出错时进行统一的异常处理，例如将数据库操作的异常转换为 Spring 框架的 DataAccessException 异常，从而使应用程序更加容错和稳健。

//事务管理：@Repository 注解还可以与 @Transactional 注解一同使用，用于在数据访问层开启事务，从而实现对数据库操作的事务管理。通过在 @Repository 标注的类上添加 @Transactional 注解，可以确保在数据库操作时，如果发生异常，则会进行事务回滚，保持数据的一致性。

//需要注意的是，@Repository 注解通常与 Spring 框架的其他注解一同使用，例如 @Autowired、@Qualifier 等，用于实现依赖注入、组件扫描等功能。同时，@Repository 注解只是一个标记注解，它本身并不提供具体的功能，而是作为 Spring 框架的一部分，用于对数据访问层组件进行标注和管理。
```

注意JpaRepository有两个泛型，前者是具体操作的对象实体，也就是对应的表，后者是ID的类型，接口中已经定义了比较常用的数据库操作。编写接口继承即可，我们可以直接注入此接口获得实现类：

### 走进Swagger

Swagger的主要功能如下：

- 支持 API 自动生成同步的在线文档：使用 Swagger 后可以直接通过代码生成文档，不再需要自己手动编写接口文档了，对程序员来说非常方便，可以节约写文档的时间去学习新技术。
- 提供 Web 页面在线测试 API：光有文档还不够，Swagger 生成的文档还支持在线测试。参数和格式都定好了，直接在界面上输入参数对应的值即可在线测试接口。

结合Spring框架（Spring-fox），Swagger可以很轻松地利用注解以及扫描机制，来快速生成在线文档，以实现当我们项目启动之后，前端开发人员就可以打开Swagger提供的前端页面，查看和测试接口。依赖如下：

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

SpringBoot 2.6以上版本修改了路径匹配规则，但是Swagger3还不支持，这里换回之前的，不然启动直接报错：

```yaml
spring:
	mvc:
		pathmatch:
      matching-strategy: ant_path_matcher
```

项目启动后，我们可以直接打开：http://localhost:8080/swagger-ui/index.html，这个页面（要是觉得丑，UI是可以换的，支持第三方）会显示所有的API文档，包括接口的路径、支持的方法、接口的描述等，并且我们可以直接对API接口进行测试，非常方便。

我们可以创建一个配置类去配置页面的相关信息：

```java
@Configuration
public class SwaggerConfiguration {

    @Bean
    public Docket docket() {
        return new Docket(DocumentationType.OAS_30).apiInfo(
                new ApiInfoBuilder()
                        .contact(new Contact("你的名字", "https://www.bilibili.com", "javastudy111*@163.com"))
                        .title("图书管理系统 - 在线API接口文档")
                        .build()
        );
    }
}
```

### 接口信息配置

虽然Swagger的UI界面已经可以很好地展示后端提供的接口信息了，但是非常的混乱，我们来看看如何配置接口的一些描述信息。

首先我们的页面中完全不需要显示ErrorController相关的API，所以我们配置一下选择哪些Controller才会生成API信息：

```java
@Bean
public Docket docket() {
    ApiInfo info = new ApiInfoBuilder()
            .contact(new Contact("你的名字", "https://www.bilibili.com", "javastudy111@163.com"))
            .title("图书管理系统 - 在线API接口文档")
            .description("这是一个图书管理系统的后端API文档，欢迎前端人员查阅！")
            .build();
    return new Docket(DocumentationType.OAS_30)
            .apiInfo(info)
            .select()       //对项目中的所有API接口进行选择
            .apis(RequestHandlerSelectors.basePackage("com.example.controller"))
            .build();
}
```

接着我们来看看如何为一个Controller编写API描述信息：

```java
@Api(tags = "账户验证接口", description = "包括用户登录、注册、验证码请求等操作。")
@RestController
@RequestMapping("/api/auth")
public class AuthApiController {
```

我们可以直接在类名称上面添加`@Api`注解，并填写相关信息，来为当前的Controller设置描述信息。

接着我们可以为所有的请求映射配置描述信息：

```java
@ApiResponses({
        @ApiResponse(code = 200, message = "邮件发送成功"),  
        @ApiResponse(code = 500, message = "邮件发送失败")   //不同返回状态码描述
})
@ApiOperation("请求邮件验证码")   //接口描述
@GetMapping("/verify-code")
public RestBean<Void> verifyCode(@ApiParam("邮箱地址")   //请求参数的描述
                                 @RequestParam("email") String email){
```

```java
@ApiIgnore     //忽略此请求映射
@PostMapping("/login-success")
public RestBean<Void> loginSuccess(){
    return new RestBean<>(200, "登陆成功");
}
```

我们也可以为实体类配置相关的描述信息：

```java
@Data
@ApiModel(description = "响应实体封装类")
@AllArgsConstructor
public class RestBean<T> {

    @ApiModelProperty("状态码")
    int code;
    @ApiModelProperty("状态码描述")
    String reason;
    @ApiModelProperty("数据实体")
    T data;

    public RestBean(int code, String reason) {
        this.code = code;
        this.reason = reason;
    }
}
```

这样，就可以在文档中查看实体类简介以及各个属性的介绍了。
