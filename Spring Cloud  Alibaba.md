# Spring Cloud  Alibaba

## 介绍与说明：

![image-20230306230950443](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307172049630.png)

官方网站（中文）：https://spring-cloud-alibaba-group.github.io/github-pages/2021/zh-cn/index.html

> Spring Cloud Alibaba 致力于提供微服务开发的一站式解决方案。此项目包含开发分布式应用服务的必需组件，方便开发者通过 Spring Cloud 编程模型轻松使用这些组件来开发分布式应用服务。
>
> 依托 Spring Cloud Alibaba，您只需要添加一些注解和少量配置，就可以将 Spring Cloud 应用接入阿里分布式应用解决方案，通过阿里中间件来迅速搭建分布式应用系统。

目前 Spring Cloud Alibaba 提供了如下功能:

1. **服务限流降级**：支持 WebServlet、WebFlux, OpenFeign、RestTemplate、Dubbo 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。
2. **服务注册与发现**：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。
3. **分布式配置管理**：支持分布式系统中的外部化配置，配置更改时自动刷新。
4. **Rpc服务**：扩展 Spring Cloud 客户端 RestTemplate 和 OpenFeign，支持调用 Dubbo RPC 服务
5. **消息驱动能力**：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力。
6. **分布式事务**：使用 @GlobalTransactional 注解， 高效并且对业务零侵入地解决分布式事务问题。
7. **阿里云对象存储**：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
8. **分布式任务调度**：提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker（schedulerx-client）上执行。
9. **阿里云短信服务**：覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。

可以看到，SpringCloudAlibaba实际上是对我们的SpringCloud组件增强功能，是SpringCloud的增强框架，可以兼容SpringCloud原生组件和SpringCloudAlibaba的组件。

## Nacos 更加全能的注册中心

Nacos（**Na**ming **Co**nfiguration **S**ervice）是一款阿里巴巴开源的服务注册与发现、配置管理的组件，相当于是Eureka+Config的组合形态。

### 安装与部署

Nacos服务器是独立安装部署的，因此我们需要下载最新的Nacos服务端程序，下载地址：https://github.com/alibaba/nacos

![image-20230306231045825](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307172051242.png)

![image-20230717210330290](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307172103396.png)

---

## 模块注册：类似于模块注册进入注册中心

1. 导入依赖：

   ```xml
   <dependencyManagement>
       <dependencies>
           <dependency>
               <groupId>org.mybatis.spring.boot</groupId>
               <artifactId>mybatis-spring-boot-starter</artifactId>
               <version>2.2.0</version>
           </dependency>
         
             <!-- 这里引入最新的SpringCloud依赖 -->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-dependencies</artifactId>
               <version>2021.0.1</version>
                 <type>pom</type>
               <scope>import</scope>
           </dependency>
   
              <!-- 这里引入最新的SpringCloudAlibaba依赖，2021.0.1.0版本支持SpringBoot2.6.X -->
           <dependency>
               <groupId>com.alibaba.cloud</groupId>
               <artifactId>spring-cloud-alibaba-dependencies</artifactId>
               <version>2021.0.1.0</version>
               <type>pom</type>
               <scope>import</scope>
           </dependency>
       </dependencies>
   </dependencyManagement>
   ```

   这里是导入到父工程的依赖，版本管理，需要注册进去的模块也是需要导入依赖的

2. 需要加入服务注册的模块更改pom文件：

3. ```xml
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   ```

   

   ```yaml
   server:
       # 之后所有的图书服务节点就81XX端口
     port: 8101
   spring:
     datasource:
       driver-class-name: com.mysql.cj.jdbc.Driver
       url: jdbc:mysql://cloudstudy.mysql.cn-chengdu.rds.aliyuncs.com:3306/cloudstudy
       username: test
       password: 123456
     # 应用名称 bookservice
     application:
       name: bookservice
     cloud:
       nacos:
         discovery:
           # 配置Nacos注册中心地址
           server-addr: localhost:8848//如果是远程的话，使用远程地址
   ```

   

4. 修改配置文件（不需要也行，因为默认8848端注册到里面）

5. ![image-20230718135613918](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307181356992.png)



### 实现远程调用：

注意：如果使用负载均衡，这里需要专门导入Load Balance这个依赖，因为这个不像eureka带上的，也就是负载均衡

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<!-- 这里需要单独导入LoadBalancer依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

其他的跟以前一样，都是写接口，然后底层都是这样子去实现的

例如：

```java
@EnableFeignClients
@SpringBootApplication
public class BorrowApplication {
    public static void main(String[] args) {
        SpringApplication.run(BorrowApplication.class, args);
    }
}
```

```java
@FeignClient("userservice")
public interface UserClient {
    
    @RequestMapping("/user/{uid}")
    User getUserById(@PathVariable("uid") int uid);
}
```

```java
@FeignClient("bookservice")
public interface BookClient {

    @RequestMapping("/book/{bid}")
    Book getBookById(@PathVariable("bid") int bid);
}
```

---

那么临时和非临时有什么区别呢？

- 临时实例：和Eureka一样，采用心跳机制向Nacos发送请求保持在线状态，一旦心跳停止，代表实例下线，不保留实例信息。
- 非临时实例：由Nacos主动进行联系，如果连接失败，那么不会移除实例信息，而是将健康状态设定为false，相当于会对某个实例状态持续地进行监控。

我们可以通过配置文件进行修改临时实例：

```yaml
spring:
  application:
    name: borrowservice
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
        # 将ephemeral修改为false，表示非临时实例
        ephemeral: false
```

接着我们在Nacos中查看，可以发现实例已经不是临时的了：

![image-20230306231328378](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307181419847.png)

如果这时我们关闭此实例，那么会变成这样：

![image-20230306231337931](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307181419296.png)

只是将健康状态变为false，而不会删除实例的信息。

----

## 个人实现注册进去nacos：

首先导入依赖：在父项目

```xml
        <!-- 这里引入最新的SpringCloudAlibaba依赖，2021.0.1.0版本支持SpringBoot2.6.X -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2021.0.4.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
```

第二个：

配置依赖：

```xml
        <!--        导入nacos注册进去，并被发现-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            <version>2021.0.1.0</version>
        </dependency>
```

第三步：书写配置文件：

```yaml
server:
  port: 8081
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://114.132.67.226:3306/springcloud
    #    zeroDateTimeBehavior=CONVERT_TO_NULL
    username: xiaoli
    password: abC123456@
  cloud:
    nacos:
      discovery:
        server-addr: 159.75.156.115:8848

  application:
    name: borrow-service
```

![image-20230920185506520](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309201855675.png)

---

## 简单使用：

### @FeignClient作用：

为了更加方便的调用不同模块的接口，底层的实现就是通过发送请求，但是每次发送请求很麻烦，所以就有了专门的一个方案：

`@FeignClient` 是一个注解，用于声明一个Feign客户端接口，它允许您轻松地定义和调用远程服务的方法。这个注解是Spring Cloud中的一部分，通常与Spring Cloud Feign一起使用。

使用前请导入：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<!-- 这里需要单独导入LoadBalancer依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

#### 案例：

```java
@FeignClient("userservice")
public interface UserClient {
    
    @RequestMapping("/user/{uid}")
    User getUserById(@PathVariable("uid") int uid);
}
```

##### 这个接口是实现了注册成为bean的

当您使用`@FeignClient`注解来声明一个接口时，Spring Cloud Feign会自动创建一个代理对象（Bean），该代理对象实现了该接口。这个代理对象的工作原理如下：

1. Spring容器会扫描`@FeignClient`注解的接口。
2. 对于每个带有`@FeignClient`注解的接口，Spring Cloud Feign会创建一个代理对象，并将其注册为Spring Bean。
3. 代理对象的工作是将接口方法的调用转发到实际的远程服务。当您调用`UserClient`接口的方法时，实际的HTTP请求会被构建并发送到注册中心中的目标服务（在这种情况下是"userservice"服务）。
4. Feign还提供了负载均衡功能，它将帮助选择一个可用的目标服务实例进行请求分发。

因此，通过使用`@FeignClient`注解，您可以将接口声明为一个Feign客户端，并将其添加到Spring容器中，以便在需要时注入和使用它。代理对象会自动处理与远程服务的通信细节，包括负载均衡和URL构建，使得远程服务调用更加方便，并避免了手动编写大量的HTTP请求和URL地址。这是Spring Cloud Feign的一个强大功能，有助于简化微服务架构中的远程服务调用。

##### 第二个：代表了请求地址的前缀：因为注解的value是注册中心服务名称，所以调用我注册中心userservice进行对吧，避免我写url地址，但是该地址要在注册中心注册了，并且被发现了



### 还要在启动类中添加enableFeignClients

---

### 使用feign客户端的注意事项：（案例）

您可以在Feign客户端接口中定义与远程服务的方法名不同的方法名，只要这些方法映射到相同的访问路径即可。Feign客户端通过`@RequestMapping` 或类似的注解来指定要访问的远程服务端点的路径，方法名本身在这里通常没有特殊要求。

例如，假设远程服务有以下端点：

```java
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{uid}")
    public User getUserById(@PathVariable("uid") int uid) {
        // 实现代码
    }

    @PostMapping("/create")
    public void createUser(@RequestBody User user) {
        // 实现代码
    }
}

```

```java
@FeignClient(name = "userservice")
public interface UserClient {

    @GetMapping("/users/{uid}") // 方法名不同，但路径相同
    User fetchUserById(@PathVariable("uid") int uid);

    @PostMapping("/users/create") // 方法名不同，但路径相同
    void addUser(@RequestBody User user);
}

```

在上面的例子中，Feign客户端接口中的方法名 `fetchUserById` 和 `addUser` 与远程服务端点中的方法名不同，但它们的路径是相同的。这是有效的，因为Feign通过路径来映射方法到远程服务的端点。

但请注意，虽然方法名可以不同，但方法签名必须与实际端点的期望方法签名匹配。这包括HTTP方法（例如，GET、POST、PUT、DELETE等）、方法参数（例如，`@PathVariable`、`@RequestParam`、`@RequestBody`等）以及返回类型。方法参数的名称和类型必须与实际端点的期望一致，以便Feign能够正确地序列化和反序列化请求和响应。

##### 总结一下，以下是Feign客户端接口与远程服务端点的一些要求：

1. **方法名可以不同，但路径应该相同：** 方法名可以在Feign客户端接口中定义为不同的名称，但它们应该映射到远程服务的相同路径。
2. **HTTP方法类型要匹配：** 您的方法应该使用与实际端点相同的HTTP方法类型，例如GET、POST、PUT、DELETE等。
3. **方法参数和注解要匹配：** 方法参数、参数的类型以及使用的注解（如`@PathVariable`、`@RequestParam`、`@RequestBody`等）应该与实际端点的期望一致，以便正确传递参数。
4. **方法返回类型要匹配：** 方法的返回类型应该与实际端点的期望一致，以便Feign能够正确地映射响应。

遵循这些规则可以确保Feign能够正确地构建和处理远程服务调用

---

## 集群分区（实现不同服务器的调用）：

选择不同的nacos：

![image-20230921190211227](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309211902331.png)

----

在一个分布式应用中，相同服务的实例可能会在不同的机器、位置上启动，比如我们的用户管理服务，可能在成都有1台服务器部署、重庆有一台服务器部署，而这时，我们在成都的服务器上启动了借阅服务，那么如果我们的借阅服务现在要调用用户服务，就应该优先选择同一个区域的用户服务进行调用，这样会使得响应速度更快。

![image-20230306231411711](https://image.itbaima.net/markdown/2023/03/06/szyGRrEfZ1KWmpj.png)



因此，我们可以对部署在不同机房的服务进行分区，可以看到实例的分区是默认：

![image-20230306231402008](https://image.itbaima.net/markdown/2023/03/06/wlO9dQ1NtKCxFTi.png)



我们可以直接在配置文件中进行修改：

```yaml
spring:
  application:
    name: borrowservice
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
        # 修改为重庆地区的集群
        cluster-name: Chongqing
```

当然由于我们这里使用的是不同的启动配置，直接在启动配置中添加环境变量`spring.cloud.nacos.discovery.cluster-name`也行，这里我们将用户服务和图书服务两个区域都分配一个，借阅服务就配置为成都地区：(如果使用不同的服务器，不需要写这个)

![image-20230306231435388](https://image.itbaima.net/markdown/2023/03/06/cwIhdCMmATELvlN.png)



修改完成之后，我们来尝试重新启动一下（Nacos也要重启），观察Nacos中集群分布情况：

![image-20230306231443247](https://image.itbaima.net/markdown/2023/03/06/jrYo3epaLMyQnu4.png)



可以看到现在有两个集群，并且都有一个实例正在运行。我们接着去调用借阅服务，但是发现并没有按照区域进行优先调用，而依然使用的是轮询模式的负载均衡调用。

我们必须要提供Nacos的负载均衡实现才能开启区域优先调用机制，只需要在配制文件中进行修改即可：

```yaml
spring:
  application:
    name: borrowservice
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
        cluster-name: Chengdu
    # 将loadbalancer的nacos支持开启，集成Nacos负载均衡
    loadbalancer:
      nacos:
        enabled: true
```

现在我们重启借阅服务，会发现优先调用的是同区域的用户和图书服务，现在我们可以将成都地区的服务下线：

![image-20230306231453470](https://image.itbaima.net/markdown/2023/03/06/s1ko9UcD4mMQ5fW.png)



可以看到，在下线之后，由于本区域内没有可用服务了，借阅服务将会调用重庆区域的用户服务。

除了根据区域优先调用之外，同一个区域内的实例也可以单独设置权重，Nacos会优先选择权重更大的实例进行调用，我们可以直接在管理页面中进行配置：

![image-20230306231500731](https://image.itbaima.net/markdown/2023/03/06/1pAckEZN5ltXKWG.png)



或是在配置文件中进行配置：

```yml
spring:
  application:
    name: borrowservice
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
        cluster-name: Chengdu
        # 权重大小，越大越优先调用，默认为1
        weight: 0.5
```

通过配置权重，某些性能不太好的机器就能够更少地被使用，而更多的使用那些网络良好性能更高的主机上的实例。

---

## 配置中心：

首秀按需求导入依赖：

```
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bootstrap</artifactId>
            <version>3.1.0</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.nacos</groupId>
            <artifactId>nacos-client</artifactId>
            <version>1.4.3</version>
        </dependency>
```

nacos-client：是nacos连接，nacos-config是配置中心，bootstrap是指加入云配置，bootstrap在application.yaml之前

接着我们在借阅服务中添加`bootstrap.yml`文件：

```yaml
spring:
  application:
  	# 服务名称和配置文件保持一致（也就是配置中心的配置文件名字）
    name: borrowservice
  profiles:
  	# 环境也是和配置文件保持一致（可以不写或者多个bootstrap配置文件，书写格式跟application一样）
    active: dev
  cloud:
    nacos:
      config:
      	# 配置文件后缀名
        file-extension: yml
        # 配置中心服务器地址，也就是Nacos地址
        server-addr: localhost:8848
```

可以实现了热更新，就是不需要去动后端的配置文件，但是要重新运行整个项目或者jar包/或者加上在控制加上@RefreshScope这个注解

---

## 读取配置文件的方式：

### SpringBoot中使用@Value注解读取配置文件的属性值

SpringBoot项目中使用 @Value 注解[读取配置文件](https://so.csdn.net/so/search?q=读取配置文件&spm=1001.2101.3001.7020)的属性值，将值赋给类中的属性，这是很常见的操作，示例如下：

```java
@Component
public class User {

    @Value("${test.username}")
    public String username;
}

```

application.[yaml](https://so.csdn.net/so/search?q=yaml&spm=1001.2101.3001.7020) 配置文件

```yaml
test:
  username: tom

```

使用 @Value 注解有两点注意事项：

**注意事项一**：对象要交给 Spring 容器管理，也就是需要在类上加 @Component 等注解；

**注意事项二**：在**静态变量**上直接添加 @Value 注解是无效的。若要给静态变量赋值，可以在 set() 方法上加 @value 注解

## 注意事项一

若对象不是由 Spring 容器管理，而是由我们手动 new 出来，那么 @Value 注解不能生效。示例如下：

```java
public class User {
    @Value("${test.username}")
    public String username;
}

```

```yaml
test:
  username: tom

```

然后取不到值

---

如果想要静态变量或者静态对象：

```java
@Component
public class User {

    public static String username;

    @Value("${test.username}")
    public void setUsername(String username) {
        User.username = username;
    }
}
```

### 方法二：使用注解你另一个注解：@ConfigurationProperties(prefix = "student")student是配置文件的属性值

yaml文件

```yaml
student:
  name: John Doe
  age: 20

```

```
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;

@Configuration
@ConfigurationProperties(prefix = "student")
public class StudentConfig {
    private String name;
    private int age;

    // Getters and setters

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

```

```
"https://guangxiaoqing.com/wxpay/notify"

"https://guangxiaoqing.com/wxpay/refundNotify"
```

---

## 命名空间：

通俗点理解就是隔绝，比如我A模块在另一个空间，B模块不在另一个空间，所以a模块访问不到b模块，因为服务注册在其他的空间，所以找不到A模块的服务，或者a找不到b模块的服务，所以命名空间是相互独立的空间

如何注册在其他的命名空间：在配置文件加入nameSpace键值对

![image-20230921204443934](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309212044988.png)

---

##  搭建nacos集群：

