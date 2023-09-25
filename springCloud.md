# springCloud

## 微服务简单理解：

就是将多个接口拆分（多个接口在同一个Springboot项目里面），拆分为多个springboot项目

所以可以拆分到多个机器里面去运行

![image-20230614150117298](https://gitee.com/ljzcomeon/typora-photo/raw/master/202306141501442.png)

SpringCloud整体架构的亮点是非常明显的，分布式架构下的各个场景，都有对应的组件来处理，比如基于Netflix（奈飞）的开源分布式解决方案提供的组件：

- Eureka  -  实现服务治理（服务注册与发现），我们可以对所有的微服务进行集中管理，包括他们的运行状态、信息等。

- Ribbon(过时了)，LoadBalancer(现在主要是使用这个)  -  为服务之间相互调用提供负载均衡算法（现在被SpringCloudLoadBalancer取代）

- Hystrix  -  断路器，保护系统，控制故障范围。暂时可以跟家里电闸的保险丝类比，当触电危险发生时能够防止进一步的发展。

- Zuul（现在已经过时了）现在主要是使用Gateway   -     api网关，路由，负载均衡等多种作用，就像我们的路由器，可能有很多个设备都连接了路由器，但是数据包要转发给谁则是由路由器在进行（已经被SpringCloudGateway取代）

- Config  -  配置管理，可以实现配置文件集中管理

- ----

- 

### 如何创建一个springCloud项目:

- 首先创建SpringBoot项目，不要选择任何配置

- 删除不要的文件，只留下Pom文件还有idea文件，可以保留igtinire文件，但是src也是不要的

- 把启动配置给去掉

- 创建子项目

- 新建模块，点击maven（不要点击Spring）

- 点击新建，然后选择父项父项是要最开始那个

- 使用服务管理多个springboot

- 业务层其他和以前的springboot一样

  ## 服务之间的调用–使用Http请求

- 前面我们完成了用户信息查询和图书信息查询，现在我们来接着完成借阅服务。

  借阅服务是一个关联性比较强的服务，它不仅仅需要查询借阅信息，同时可能还需要获取借阅信息下的详细信息，比如具体那个用户借阅了哪本书，并且用户和书籍的详情也需要同时出现，那么这种情况下，我们就需要去访问除了借阅表以外的用户表和图书表。

![image-20230713224217489](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307132242648.png)

简单理解就是使用https请求进行拿去数据；例如下列的请求

![image-20230713224302513](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307132243613.png)

### 问题：

#### 1·实体类不在同一个模块，没法导入

解决办法：新建一个模块，将几个模块需要使用到的实体类都导入到这个模块——common这个模块就是专门放实体类的

然后在pom文件里面导入这个模块

<img src="https://gitee.com/ljzcomeon/typora-photo/raw/master/202307132249161.png" alt="image-20230713224947112" style="zoom: 67%;" />

![image-20230713224918444](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307132249472.png)

#### 2·如何通过Http进行获取信息：

![image-20230713225636752](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307132256808.png)

通过RestTemplate这个类进行获取请求的返回信息，RestTemplate这个是有一个方法，返回信息是可以映射到某个类，这样子就避免了Json转化的问题

```java
@Service
public class BorrowServiceImpl implements BorrowService{

    @Resource
    BorrowMapper mapper;

    @Override
    public UserBorrowDetail getUserBorrowDetailByUid(int uid) {
        List<Borrow> borrow = mapper.getBorrowsByUid(uid);
        //RestTemplate支持多种方式的远程调用
        RestTemplate template = new RestTemplate();
        //这里通过调用getForObject来请求其他服务，并将结果自动进行封装
        //获取User信息
        User user = template.getForObject("http://localhost:8082/user/"+uid, User.class);
        //获取每一本书的详细信息
        List<Book> bookList = borrow
                .stream()
                .map(b -> template.getForObject("http://localhost:8080/book/"+b.getBid(), Book.class))
                .collect(Collectors.toList());
        return new UserBorrowDetail(user, bookList);
    }
}
```



---

## RestTemplate使用：



---

## Eureka 注册中心

官方文档：https://docs.spring.io/spring-cloud-netflix/docs/current/reference/html/

---

Eureka能够自动注册并发现微服务，然后对服务的状态、信息进行集中管理，这样当我们需要获取其他服务的信息时，我们只需要向Eureka进行查询就可以了。

就是说不需要向之前那样使用RestTemplate去请求访问拿数据，可以通过这个注册中心进行数据的获取

![image-20230306225607857](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307142140052.png)

优点：

- 首先可以有效地解耦，比如我的用户模块，登录接口的api改变了，不用全部模块去查找使用了之前的那个api去修改,类似于以前没有Ioc容器，使用都是需要去new一个对象，每次都是新的对象，现在不需要了，直接去Ioc容器里面获取，每次都不是新的对象，这样的好处是性能的提升之外，最大的优点是解除了强强关联性，也就是解耦
- ![image-20230714214630404](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307142146441.png)





### 步骤：

前提：新建eureka的模块

- 1·首先导入springCloud依赖：

- ```xml
     <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-dependencies</artifactId>
              <version>2021.0.1</version>
              <type>pom</type>
              <scope>import</scope>
          </dependency>
  ```

  

导入到父项目，版本管理，需要使用的时候，就可以直接在使用的模块进行依赖的导入，但是不用导入版本号，因为已经固定了版本的信息

- 2·eureka的pom文件导入：

- ```xml
  <dependencies>
      <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
      </dependency>
  </dependencies>
  ```

  

当在父项目中导入spring-cloud-dependencies时，它会提供了一组Spring Cloud相关的依赖管理，包括了不同组件的版本信息。这样，您在子项目中就可以直接引入spring-cloud-starter-netflix-eureka-server依赖，而无需指定具体的版本信息。因为父项目已经提供了统一的版本管理，子项目会自动继承父项目的依赖版本。

这种方式可以简化项目配置和管理，确保各个子项目使用的Spring Cloud组件版本一致，避免了版本冲突和依赖不一致的问题。同时，当需要升级或更换某个Spring Cloud组件时，只需在父项目中更新相应的版本，所有的子项目都会自动应用新的版本。

因此，您可以放心地在子项目中直接导入spring-cloud-starter-netflix-eureka-server依赖，而不需要指定版本信息。

3·配置文件写好：启动端口之类的

```yaml
server:
  port: 8888
eureka:
  # 开启之前需要修改一下客户端设置（虽然是服务端
  client:
    # 由于我们是作为服务端角色，所以不需要获取服务端，改为false，默认为true;
    #改为false后不会把自己也注册到注册中心里面,后面做集群是可以的把自己注册到注册中心里面
    fetch-registry: false
    # 暂时不需要将自己也注册到Eureka
    register-with-eureka: false
    # 将eureka服务端指向自己
    service-url:
      defaultZone: http://localhost:8888/eureka

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://8.130.43.168:3306/test_spring_cloud
    #    zeroDateTimeBehavior=CONVERT_TO_NULL
    username: root
    password: abC123456!
```

![image-20230714223942570](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307142239674.png)

这样子就能打开后台，这个是可以观察到每个服务每个模块的系统之类的，类似于运维面板的宝塔面板之类的，可以实时观察数据

### 其他模块加入到eureka里面

1. 首先导入依赖

2. 在配置文件写端口号的url（和在eureka的url一样）

3. 写好名字，这个模块是什么名称

   

![image-20230715194731024](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307151947096.png)

![image-20230715194751486](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307151947524.png)



### 调用服务：

![image-20230715201541351](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307152015398.png)

依然是通过restTemple进行调用，只是那个地址不再是写死，而是在配置文件的名称后面接的是控制器的名字也就是说包括端口前面的那串地址都是用那个模块的name（在yaml文件的spring name），也就是在eureka的容器里面（名称跟spring Name一致）所对应的接口，这样子改就方便多了，就达到了解耦的目的

![image-20230715201928131](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307152019200.png)

现在有了Eureka之后，我们可以直接向其进行查询，得到对应的微服务地址，这里直接将服务名称替换即可：

```java
@Service
public class BorrowServiceImpl implements BorrowService {

    @Resource
    BorrowMapper mapper;

    @Resource
    RestTemplate template;

    @Override
    public UserBorrowDetail getUserBorrowDetailByUid(int uid) {
        List<Borrow> borrow = mapper.getBorrowsByUid(uid);

        //这里不用再写IP，直接写服务名称userservice
        User user = template.getForObject("http://userservice/user/"+uid, User.class);
        //这里不用再写IP，直接写服务名称bookservice
        List<Book> bookList = borrow
                .stream()
                .map(b -> template.getForObject("http://bookservice/book/"+b.getBid(), Book.class))
                .collect(Collectors.toList());
        return new UserBorrowDetail(user, bookList);
    }
}
```

接着我们手动将RestTemplate声明为一个Bean，然后添加`@LoadBalanced`注解，这样Eureka就会对服务的调用进行自动发现，并提供负载均衡：

```java
@Configuration
public class BeanConfig {
    @Bean
    @LoadBalanced
    RestTemplate template(){
        return new RestTemplate();
    }
}
```

现在我们就可以正常调用了：

![image-20230306225713484](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307152148504.png)



### 可以多个eureka（也就是集群），防止一个eureka挂了，导致整个系统崩了

## 负载均衡：

#### 个人简单理解：

1. ​	负载均衡器位于客户端和后端服务器之间，它接收到客户端发来的请求并将其转发给后端的服务器。负载均衡器使用一些算法和策略来决定将请求转发给哪个服务器。这样做的目的是确保每个服务器都能够处理相对均衡的请求数量，以防止某些服务器负载过高而导致性能下降或故障。

2. ​	举个例子，假设有一个负载均衡器和三个后端服务器。当客户端发送请求时，负载均衡器会根据负载均衡算法（如轮询、最少连接等）选择其中一个后端服务器，并将请求转发给它。这样可以实现请求的分发和负载均衡，确保每个服务器都能处理适量的请求。

3. ​	此外，负载均衡器还可以具备其他功能，如健康检查、会话保持等，以增强系统的可用性和稳定性。

4. ​	总结起来，负载均衡是通过在多个服务器之间分发请求来均衡工作负载，以提高系统的性能和可用性。它并不是指在同一个控制器下使用多个端口或线程处理请求，而是通过将请求分发给多个服务器来实现负载均衡。

   ### Gpt的解释：

负载均衡和Servlet的调度中心之间存在关系，因为负载均衡器可以作为Servlet的请求调度中心。

在传统的Java Web应用中，Servlet是用于处理Web请求的Java组件。当客户端发送请求时，Servlet容器（如Tomcat）会负责接收请求，并将其分发给相应的Servlet进行处理。

负载均衡器可以位于Servlet容器前面，充当请求的调度中心。当客户端发送请求时，负载均衡器接收到请求，并根据一定的算法和策略，将请求分发给不同的Servlet容器或后端服务器。这样可以实现对请求的负载均衡，确保每个Servlet容器或后端服务器都能处理适量的请求。

负载均衡器可以根据服务器的负载情况、性能指标、连接数等因素进行动态调整和分配。它可以监测后端服务器的健康状态，并将请求转发到可用的服务器上，从而提高系统的可用性和性能。

通过将负载均衡器作为Servlet的调度中心，可以实现以下好处：

1. 高可用性：如果某个Servlet容器或后端服务器发生故障，负载均衡器可以将请求转发到其他可用的服务器上，保证服务的连续性。

2. 负载均衡：负载均衡器可以根据后端服务器的负载情况，将请求分发到负载较低的服务器上，均衡负载，避免单个服务器过载。

3. 扩展性：通过添加更多的Servlet容器或后端服务器，负载均衡器可以扩展系统的容量，应对不断增长的请求。

因此，负载均衡器在Servlet应用中可以作为请求的调度中心，提供负载均衡和高可用性的功能。它可以将请求分发给不同的Servlet容器或后端服务器，确保系统能够高效处理并发请求。

##### 使用负载均衡，是否会使用servlet的调度中心

​	使用负载均衡器来进行请求的调度，而不需要额外的调度中心。负载均衡器可以根据一定的算法和配置，将同一个URL的请求分发到不同的后端服务器上，以实现负载均衡和高可用性。

对于同一个URL的请求，可以使用单个端口来处理。负载均衡器可以通过监听该端口，接收所有来自客户端的请求，并根据负载均衡算法将请求分发到后端服务器上。

举个例子，假设有==一个负载均衡器和三个后端服务器==，它们都监听同一个端口。当客户端发送请求时，负载均衡器接收到请求，并根据算法（如轮询、最少连接等）选择其中一个后端服务器，并将请求转发给它。这样，所有的请求都可以通过同一个端口进行处理，而负载均衡器会负责将请求分发给不同的服务器，实现负载均衡。

通过这种方式，可以方便地实现负载均衡和高可用性，同时使用单个端口来处理同一个URL的请求。负载均衡器作为请求的调度中心，根据负载均衡策略将请求分发到后端服务器上，提供高性能和可扩展性的服务。

​	==如果不使用负载均衡，而是直接通过客户端请求来调用三个后端服务器，那么每个服务器都可以被直接调用。==客户端可以根据需要选择其中一个服务器发送请求，并将请求发送到该服务器的特定端口。

在这种情况下，负载均衡的调度功能是不需要的。每个后端服务器都可以独立地处理请求，客户端可以直接与其中任何一个服务器进行通信。这种方式适用于小规模系统或对负载均衡要求较低的情况。

然而，当系统需要处理大量并发请求、实现高可用性、提高性能和扩展性时，引入负载均衡是非常有益的。负载均衡可以根据服务器的负载情况将请求分发到适当的服务器上，避免某些服务器过载。它还可以提供故障恢复和自动切换的功能，确保服务的连续性。

因此，是否使用负载均衡取决于系统的需求。如果对负载均衡没有特别要求，每个服务器都可以直接调用。但是，当需要处理大量请求、提高性能和可用性时，引入负载均衡是一个有效的选择。

##### 负载均衡是否与端口有关

负载均衡与端口无关，而是与服务器的分发和调度机制相关。负载均衡器可以在单个端口上接收来自客户端的请求，并根据负载均衡算法将请求分发到后端的多个服务器上。

使用多个端口的优势在于更灵活地配置和管理后端服务器。每个端口可以对应一个后端服务器或一组后端服务器，这样可以更细粒度地控制请求的分发和负载均衡。如果某个后端服务器的负载较高，可以通过配置负载均衡器，将请求发送到其他端口对应的服务器上，实现更均衡的负载。

此外，使用多个端口还可以为不同的服务或功能提供独立的入口点，使得系统的组件更清晰、可维护性更高。

综上所述，负载均衡与端口无关，可以通过单个端口或多个端口实现。使用多个端口可以提供更灵活的配置和管理方式，使得负载均衡更加精细和可控。

==就是跟银行排队办业务一样，有1个窗口代表一个服务器，客户就是请求，如果是只有一个服务器，只能乖乖排队，一个一个来，但是如果是多个窗口，按顺序下去，哪个窗口没有人就去哪一个==



==当您在一个系统中使用多个模块，并将它们部署在不同的端口上，这可以被视为多个服务器的情况。==

在这种情况下，每个模块都可以被看作是一个独立的服务器，它们使用不同的端口监听来自客户端的请求。负载均衡器可以将请求分发到这些不同的模块（服务器）上，以实现负载均衡和高可用性。

举个例子，假设您有一个系统，其中包含了用户管理模块和订单管理模块，它们分别监听不同的端口，例如用户管理模块使用端口 8080，订单管理模块使用端口 8090。那么可以将用户管理模块和订单管理模块看作是两个独立的服务器，每个服务器对应一个模块，并通过负载均衡器将请求分发给它们。

通过使用多个模块和不同的端口，您可以将系统的功能模块化，并且能够更好地进行负载均衡。负载均衡器可以根据负载均衡算法，将请求分发到相应的模块上，实现请求的均衡处理。

因此，将多个模块部署在不同的端口上，可以被视为使用多个服务器的方式，通过负载均衡器来实现负载均衡和高可用性。

----

## LoadBalancer 负载均衡

前面我们讲解了如何对服务进行拆分、如何通过Eureka服务器进行服务注册与发现，那么现在我们来看看，它的负载均衡到底是如何实现的，实际上之前演示的负载均衡是依靠LoadBalancer实现的。

在2020年前的SpringCloud版本是采用Ribbon作为负载均衡实现，但是2020年的版本之后SpringCloud把Ribbon移除了，进而用自己编写的LoadBalancer替代。

那么，负载均衡是如何进行的呢？

### 负载均衡底层实现：

实际上，在添加`@LoadBalanced`注解之后，会启用拦截器对我们发起的服务调用请求进行拦截（注意这里是针对我们发起的请求进行拦截），叫做`LoadBalancerInterceptor`，不是我们之前自己写的拦截器，它实现`ClientHttpRequestInterceptor`接口：

```java
@FunctionalInterface
public interface ClientHttpRequestInterceptor {
    ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) throws IOException;
}
```

负载均衡的核心主要是对`intercept`方法的实现：

```java
public ClientHttpResponse intercept(final HttpRequest request, final byte[] body, final ClientHttpRequestExecution execution) throws IOException {
    URI originalUri = request.getURI();
    String serviceName = originalUri.getHost();
    Assert.state(serviceName != null, "Request URI does not contain a valid hostname: " + originalUri);
    return (ClientHttpResponse)this.loadBalancer.execute(serviceName, this.requestFactory.createRequest(request, body, execution));
}
```

![image-20230306225949475](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161508704.png)

![image-20230306230000711](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161509758.png)

服务端会在发起请求时执行这些拦截器。

那么这个拦截器做了什么事情呢，首先我们要明确，我们给过来的请求地址，并不是一个有效的主机名称，而是服务名称，那么怎么才能得到真正需要访问的主机名称呢，肯定是得找Eureka获取的。

我们来看看`loadBalancer.execute()`做了什么，它的具体实现为`BlockingLoadBalancerClient`：

```java
//从上面给进来了服务的名称和具体的请求实体
public <T> T execute(String serviceId, LoadBalancerRequest<T> request) throws IOException {
    String hint = this.getHint(serviceId);
    LoadBalancerRequestAdapter<T, DefaultRequestContext> lbRequest = new LoadBalancerRequestAdapter(request, new DefaultRequestContext(request, hint));
    Set<LoadBalancerLifecycle> supportedLifecycleProcessors = this.getSupportedLifecycleProcessors(serviceId);
    supportedLifecycleProcessors.forEach((lifecycle) -> {
        lifecycle.onStart(lbRequest);
    });
      //可以看到在这里会调用choose方法自动获取对应的服务实例信息
    ServiceInstance serviceInstance = this.choose(serviceId, lbRequest);
    if (serviceInstance == null) {
        supportedLifecycleProcessors.forEach((lifecycle) -> {
            lifecycle.onComplete(new CompletionContext(Status.DISCARD, lbRequest, new EmptyResponse()));
        });
          //没有发现任何此服务的实例就抛异常（之前的测试中可能已经遇到了）
        throw new IllegalStateException("No instances available for " + serviceId);
    } else {
          //成功获取到对应服务的实例，这时就可以发起HTTP请求获取信息了
        return this.execute(serviceId, serviceInstance, lbRequest);
    }
}
```

所以，实际上在进行负载均衡的时候，会向Eureka发起请求，选择一个可用的对应服务，然后会返回此服务的主机地址等信息：

![image-20230306230014040](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161509893.png)

### 自定义负载均衡策略

LoadBalancer默认提供了两种负载均衡策略：

- RandomLoadBalancer - 随机分配策略
- **(默认)** RoundRobinLoadBalancer - 轮询分配策略

现在我们希望修改默认的负载均衡策略，可以进行指定，比如我们现在希望用户服务采用随机分配策略，我们需要先创建随机分配策略的配置类（不用加`@Configuration`）：

```java
public class LoadBalancerConfig {
      //将官方提供的 RandomLoadBalancer 注册为Bean
    @Bean
    public ReactorLoadBalancer<ServiceInstance> randomLoadBalancer(Environment environment, LoadBalancerClientFactory loadBalancerClientFactory){
        String name = environment.getProperty(LoadBalancerClientFactory.PROPERTY_NAME);
        return new RandomLoadBalancer(loadBalancerClientFactory.getLazyProvider(name, ServiceInstanceListSupplier.class), name);
    }
}
```

接着我们需要为对应的服务指定负载均衡策略，直接使用注解即可：

```java
@Configuration
@LoadBalancerClient(value = "userservice",      //指定为 userservice 服务，只要是调用此服务都会使用我们指定的策略
                    configuration = LoadBalancerConfig.class)   //指定我们刚刚定义好的配置类
public class BeanConfig {
    @Bean
    @LoadBalanced
    RestTemplate template(){
        return new RestTemplate();//将http的客户端/也就是restTemple注册为Bean的时候进行修改
    }
}
```

接着我们在`BlockingLoadBalancerClient`中添加断点，观察是否采用我们指定的策略进行请求：

![image-20230306230030016](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161511038.png)

![](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161511934.png)

发现访问userservice服务的策略已经更改为我们指定的策略了。

修改底层太复杂，使用到配置类进行配置分配规则，所以就诞生了工具类进行操作

### OpenFeign实现负载均衡

官方文档：https://docs.spring.io/spring-cloud-openfeign/docs/current/reference/html/

Feign和RestTemplate一样，也是HTTP客户端请求工具，但是它的使用方式更加便捷。

1. 首先是依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

1. 接着在启动类添加`@EnableFeignClients`注解：（案例）

```java
@SpringBootApplication
@EnableFeignClients
public class BorrowApplication {
    public static void main(String[] args) {
        SpringApplication.run(BorrowApplication.class, args);
    }
}
```

1. 创建一个接口，是专门连接客户端，也就是之前使用RestTemple这样功能的包和接口

2. 那么现在我们需要调用其他微服务提供的接口，该怎么做呢？我们直接创建一个对应服务的接口类即可：

   ```java
   @FeignClient("userservice")   //声明为userservice服务的HTTP请求客户端，也就是注册eureka的注册名称，也就是yaml文件里面写的模块名称，也就是RestTemple里面的URL地址一致的，之前的那个地址是http：//xxx/zzzz/zzz
   public interface UserClient {
          //路径保证和其他微服务提供的一致即可
       @RequestMapping("/user/{uid}")
       User getUserById(@PathVariable("uid") int uid);  //参数和返回值也保持一致，跟控制器哪里是保持一致的
   }
   
   /**
   等价于：
   **/
     RestTemplate template = new RestTemplate();
   User user = template.getForObject("http://userservice/user/"+uid, User.class);
   其实它的底层就是这样子，封装好了，那个@@FeignClient("userservice") 等价于URL"http://userservice；跟控制器那个一样的  @RequestMapping("/user/{uid}")等价于/user/"+uid，这里的uid是之前已经拿到了，是通过方法块，所以这个类似于mybatis和mysql，都是对某个东西的封装，底层就是RestTemple，然后UserService是通过yaml（也就是通过eureka拿取得），一样那个注解已经将接口得实现类注入了bean，只需要使用Mapper那样子去拿取就行，也是寻找接口对应得实现类，只是这个就是底层了
   ```

### GPT理解：

@FeignClient("userservice")表示这是一个对名为"userservice"的服务的HTTP请求客户端。它会自动注册到Eureka服务注册中心中的该服务。接口中的@RequestMapping("/user/{uid}")指定了请求路径，而getUserById方法定义了具体的GET请求。

Feign的底层确实使用了RestTemplate来发送HTTP请求。当你调用getUserById方法时，Feign会根据接口的定义和注解信息，构建对"userservice"服务的HTTP请求，并将返回的结果转换为User对象。

因此，你可以将Feign理解为对RestTemplate的更高级别的封装，通过声明式的方式来定义和使用HTTP请求接口，从而简化了与其他微服务之间的通信。同时，使用Eureka服务注册中心可以方便地找到要调用的服务的实例。

---

## Hystrix 服务熔断

官方文档：https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/1.3.5.RELEASE/single/spring-cloud-netflix.html#_circuit_breaker_hystrix_clients

我们知道，微服务之间是可以进行相互调用的，那么如果出现了下面的情况会导致什么问题？

![image-20230306230121344](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161617523.png)

由于位于最底端的服务提供者E发生故障，那么此时会直接导致服务ABCD全线崩溃，就像雪崩了一样

![image-20230306230128050](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161617567.png)

这种问题实际上是不可避免的，由于多种因素，比如网络卡顿、系统故障、硬件问题等，都存在一定可能，会导致这种极端的情况发生。因此，我们需要寻找一个应对这种极端情况的解决方案。

为了解决分布式系统的雪崩问题，SpringCloud提供了Hystrix熔断器组件(已经凉了)，他就像我们家中的保险丝一样，当电流过载就会直接熔断，防止危险进一步发生，从而保证家庭用电安全。可以想象一下，如果整条链路上的服务已经全线崩溃，这时还在不断地有大量的请求到达，需要各个服务进行处理，肯定是会使得情况越来越糟糕的。

### 服务降级与服务熔断的区分：

1. 服务降级：

   可以理解为没有将请求消息传递给服务失败的模块服务器，返回另外的值，例如我正常是a服务请求b服务，拿到数据后再进行出来，再返回最终结果给请求的用户，现在知道了b服务出现了问题，a都不发送请求给b服务了，这样子防止了大量数据堆积，因为b服务出现问题，传不回来数据，然后就一直等待，但是又要接收其他请求，这样子就会出现堵塞，a不发给b服务就会使用备选方案，这样的叫做服务降级

2. 服务熔断：

   和服务降级一样的是没有将请求消息传递给服务失败的模块服务器，也不像服务降级，直接返回500报错，就像家里的保险丝，断了，然后a服务也不接收请求了，除非b服务可以了，修复了

   降级多了请求，还是不行就会自动进入熔断，过段时间再次去尝试，如果行了，关闭熔断器，还是不行就直接保持熔断状态

总结起来，服务降级是在服务故障或异常时返回备选响应，以保证系统的可用性和性能。而服务熔断是一种保护机制，在错误率超过阈值时停止对故障服务的调用，以避免连锁故障，并在一段时间后重新尝试请求。



----

### 服务降级

首先我们来看看服务降级，注意一定要区分开服务降级和服务熔断的区别，服务降级并不会直接返回错误，而是可以提供一个补救措施，正常响应给请求者。这样相当于服务依然可用，但是服务能力肯定是下降了的。

我们就基于借阅管理服务来进行讲解，我们不开启用户服务和图书服务，表示用户服务和图书服务已经挂掉了。

这里我们导入Hystrix的依赖（此项目已经停止维护，SpringCloud依赖中已经不自带了，所以说需要自己单独导入）：

```xml
   <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
             <version>2.2.10.RELEASE</version>
    </dependency>
复制代码
```

接着我们需要在启动类添加注解开启：

```java
@SpringBootApplication
@EnableHystrix   //启用Hystrix
public class BorrowApplication {
    public static void main(String[] args) {
        SpringApplication.run(BorrowApplication.class, args);
    }
}
复制代码
```

那么现在，由于用户服务和图书服务不可用，所以查询借阅信息的请求肯定是没办法正常响应的，这时我们可以提供一个备选方案，也就是说当服务出现异常时，返回我们的备选方案：

```java
@RestController
public class BorrowController {

    @Resource
    BorrowService service;

    @HystrixCommand(fallbackMethod = "onError")    //使用@HystrixCommand来指定备选方案
    @RequestMapping("/borrow/{uid}")
    UserBorrowDetail findUserBorrows(@PathVariable("uid") int uid){
        return service.getUserBorrowDetailByUid(uid);
    }
        
      //备选方案，这里直接返回空列表了
      //注意参数和返回值要和上面的一致
    UserBorrowDetail onError(int uid){
        return new UserBorrowDetail(null, Collections.emptyList());
    }
}
复制代码
```

可以看到，虽然我们的服务无法正常运行了，但是依然可以给浏览器正常返回响应数据：

![image-20230306230144106](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161724382.png)

![image-20230306230151243](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161726103.png)

服务降级是一种比较温柔的解决方案，虽然服务本身的不可用，但是能够保证正常响应数据。

### 服务熔断

熔断机制是应对雪崩效应的一种微服务链路保护机制，当检测出链路的某个微服务不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回”错误”的响应信息。当检测到该节点微服务响应正常后恢复调用链路。

实际上，熔断就是在降级的基础上进一步升级形成的，也就是说，在一段时间内多次调用失败，那么就直接升级为熔断。

我们可以添加两条输出语句：

```java
@RestController
public class BorrowController {

    @Resource
    BorrowService service;

    @HystrixCommand(fallbackMethod = "onError")
    @RequestMapping("/borrow/{uid}")
    UserBorrowDetail findUserBorrows(@PathVariable("uid") int uid){
        System.out.println("开始向其他服务获取信息");
        return service.getUserBorrowDetailByUid(uid);
    }

    UserBorrowDetail onError(int uid){
        System.out.println("服务错误，进入备选方法！");
        return new UserBorrowDetail(null, Collections.emptyList());
    }
}
复制代码
```

接着，我们在浏览器中疯狂点击刷新按钮，对此服务疯狂发起请求，可以看到后台：

![image-20230306230205508](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161726488.png)

一开始的时候，会正常地去调用Controller对应的方法`findUserBorrows`，发现失败然后进入备选方法，但是我们发现在持续请求一段时间之后，没有再调用这个方法，而是直接调用备选方案，这便是升级到了熔断状态。

我们可以继续不断点击，继续不断地发起请求：

![image-20230306230223193](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161725621.png)

可以看到，过了一段时间之后，会尝试正常执行一次`findUserBorrows`，但是依然是失败状态，所以继续保持熔断状态。

所以得到结论，它能够对一段时间内出现的错误进行侦测，当侦测到出错次数过多时，熔断器会打开，所有的请求会直接响应失败，一段时间后，只执行一定数量的请求，如果还是出现错误，那么则继续保持打开状态，否则说明服务恢复正常运行，关闭熔断器。

我们可以测试一下，开启另外两个服务之后，继续点击：

![image-20230306230235955](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161725301.png)

可以看到，当另外两个服务正常运行之后，当再次尝试调用`findUserBorrows`之后会成功，于是熔断机制就关闭了，服务恢复运行。

总结一下：

![image-20230716172534853](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161725939.png)

## 使用openFeign实现降级：

### OpenFeign实现降级

Hystrix也可以配合Feign进行降级，我们可以对应接口中定义的远程调用单独进行降级操作。

比如我们还是以用户服务挂掉为例，那么这个时候肯定是会远程调用失败的，也就是说我们的Controller中的方法在执行过程中会直接抛出异常，进而被Hystrix监控到并进行服务降级。

而实际上导致方法执行异常的根源就是远程调用失败，所以我们换个思路，既然用户服务调用失败，那么我就给这个远程调用添加一个替代方案，如果此远程调用失败，那么就直接上替代方案。那么怎么实现替代方案呢？我们知道Feign都是以接口的形式来声明远程调用，那么既然远程调用已经失效，我们就自行对其进行实现，创建一个实现类，对原有的接口方法进行替代方案实现：

```java
@Component   //注意，需要将其注册为Bean，Feign才能自动注入
public class UserFallbackClient implements UserClient{
    @Override
    public User getUserById(int uid) {   //这里我们自行对其进行实现，并返回我们的替代方案
        User user = new User();
        user.setName("我是替代方案");
        return user;
    }
}
```

实现完成后，我们只需要在原有的接口中指定失败替代实现即可：

```java
//fallback参数指定为我们刚刚编写的实现类
@FeignClient(value = "userservice", fallback = UserFallbackClient.class)
public interface UserClient {

    @RequestMapping("/user/{uid}")
    User getUserById(@PathVariable("uid") int uid);
}
复制代码
```

现在去掉`BorrowController`的`@HystrixCommand`注解和备选方法：

```java
@RestController
public class BorrowController {

    @Resource
    BorrowService service;

    @RequestMapping("/borrow/{uid}")
    UserBorrowDetail findUserBorrows(@PathVariable("uid") int uid){
        return service.getUserBorrowDetailByUid(uid);
    }
}
复制代码
```

最后我们在配置文件中开启熔断支持：

```yaml
feign:
  circuitbreaker:
    enabled: true
复制代码
```

启动服务，调用接口试试看：

![image-20230306230301021](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161829311.png)

![image-20230306230310524](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307161829043.png)

可以看到，现在已经采用我们的替代方案作为结果。

### 个人总结步骤

1. 首先就是创建接口，跟上面的一样，就是使用接口对服务的调取–openfign,(接口的内容跟控制器类似)

2. 出了上面服务接口调用一样的配置外，在@FeignClient后面有效的信息：例如(value = "userservice", fallback = UserFallbackClient.class)，value就是之前不用加服务熔断前的服务名称，fallback就是如果服务不在线，则会使用UserFallbackClient这个类（该类继承了刚才那个服务接口，实现了方法）

3. 在配置文件中书写熔断的支持：

   ```yaml
   feign:
     circuitbreaker:
       enabled: true
   ```

   

## 监控页面部署：就是将服务搞成监控

----

## Gateway 路由网关

官网地址：https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/

说到路由，想必各位一定最先想到的就是家里的路由器了，那么我们家里的路由器充当的是一个什么角色呢？

我们知道，如果我们需要连接互联网，那么就需要将手机或是电脑连接到家里的路由器才可以，而路由器则连接光猫，光猫再通过光纤连接到互联网，也就是说，互联网方向发送过来的数据，需要经过路由器才能到达我们的设备。而路由器充当的就是数据包中转站，所有的局域网设备都无法直接与互联网连接，而是需要经过路由器进行中转，我们一般说路由器下的网络是内网，而互联网那一端是外网。

![image-20230306230515898](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307170929753.png)

我们的局域网设备，无法被互联网上的其他设备直接访问，肯定是能够保证到安全性的。并互联网发送过来的数据，需要经过路由器进行解析，识别到底是哪一个设备的数据包，然后再发送给对应的设备。

而我们的微服务也是这样，一般情况下，可能并不是所有的微服务都需要直接暴露给外部调用，这时我们就可以使用路由机制，添加一层防护，让所有的请求全部通过路由来转发到各个微服务，并且转发给多个相同微服务实例也可以实现负载均衡。

![image-20230306230524100](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307170930862.png)

在之前，路由的实现一般使用Zuul，但是已经停更，而现在新出现了由SpringCloud官方开发的Gateway路由，它相比Zuul不仅性能上得到了一定的提升，并且是官方推出，契合性也会更好，所以我们这里就主要讲解Gateway。

### 部署网关：

1. 创建GateWay模块，导入依赖：

2. 第一个依赖就是网关的依赖，而第二个则跟其他微服务一样，需要注册到Eureka才能生效，注意别添加Web依赖，使用的是WebFlux框架。

   ```xml
   <dependencies>
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-gateway</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
   </dependencies>
   ```

   

3. 完善配置文件：

   ```yaml
   server:
     port: 8500
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:8801/eureka, http://localhost:8802/eureka
   spring:
     application:
       name: gateway
   ```

   现在就可以启动了：

   ![image-20230306230532720](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307170936256.png)

4. 但是现在还没有配置任何的路由功能，我们接着将路由功能进行配置：

   ```yaml
   spring:
     cloud:
       gateway:
           # 配置路由，注意这里是个列表，每一项都包含了很多信息
         routes:
           - id: borrow-service   # 路由名称
             uri: lb://borrowservice  # 路由的地址，lb表示使用负载均衡到微服务，也可以使用http正常转发
             predicates: # 路由规则，断言什么请求会被路由
               - Path=/borrow/**  # 只要是访问的这个路径，一律都被路由到上面指定的服务
   ```

---

路由规则的详细列表（断言工厂列表）在这里：https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gateway-request-predicates-factories，可以指定多种类型，包括指定时间段、Cookie携带情况、Header携带情况、访问的域名地址、访问的方法、路径、参数、访问者IP等。也可以使用配置类进行配置，但是还是推荐直接配置文件，省事。

这样我们就可以将不需要外网直接访问的微服务全部放到内网环境下，而只依靠网关来对外进行交涉。

### 个人对路由网关的理解：

路由网关的作用就是接收转化，像原本图书控制器服务的是8300端口，现在我路由网关是8500端口，我配置了8500端口的URL后面带xxx的话转化到哪个地址，这个地址是内部的（外面看不到的，也就是写模块），所以路由网关的功能是接收转发

----

## 路由过滤器

路由过滤器支持以某种方式修改传入的 HTTP 请求或传出的 HTTP 响应，路由过滤器的范围是某一个路由，跟之前的断言一样，Spring Cloud Gateway 也包含许多内置的路由过滤器工厂，详细列表：https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gatewayfilter-factories

跟之前所说的过滤器是一样的，也就是说，请求到路由之前是会经过过滤器再进入的

步骤：

1. 修改配置文件：

2. 过滤工厂：https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gatewayfilter-factories

3. ```yaml
   spring:
     application:
       name: gateway
     cloud:
       gateway:
         routes:
         - id: borrow-service
           uri: lb://borrowservice
           predicates:
           - Path=/borrow/**
         # 继续添加新的路由配置，这里就以书籍管理服务为例
         # 注意-要对齐routes:
         - id: book-service
           uri: lb://bookservice
           predicates:
           - Path=/book/**
           filters:   # 添加过滤器
           - AddRequestHeader=Test, HelloWorld!
           # AddRequestHeader 就是添加请求头信息，其他工厂请查阅官网
   ```

   

除了针对于某一个路由配置过滤器之外，我们也可以自定义全局过滤器，它能够作用于全局。但是我们需要通过代码的方式进行编写，比如我们要实现拦截没有携带指定请求参数的请求：

```java
@Component   //需要注册为Bean
public class TestFilter implements GlobalFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {   //只需要实现此方法
        return null;
    }
}
复制代码
```

接着我们编写判断：

```java
@Override
public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
    //先获取ServerHttpRequest对象，注意不是HttpServletRequest
    ServerHttpRequest request = exchange.getRequest();
    //打印一下所有的请求参数
    System.out.println(request.getQueryParams());
    //判断是否包含test参数，且参数值为1
    List<String> value = request.getQueryParams().get("test");
    if(value != null && value.contains("1")) {
        //将ServerWebExchange向过滤链的下一级传递（跟JavaWeb中介绍的过滤器其实是差不多的）
        return chain.filter(exchange);
    }else {
        //直接在这里不再向下传递，然后返回响应
        return exchange.getResponse().setComplete();
    }
}
复制代码
```

可以看到结果：

![image-20230306230634856](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307171454968.png)

![image-20230306230645619](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307171454800.png)

成功实现规则判断和拦截操作。

当然，过滤器肯定是可以存在很多个的，所以我们可以手动指定过滤器之间的顺序：

```java
@Component
public class TestFilter implements GlobalFilter, Ordered {   //实现Ordered接口
  
    @Override
    public int getOrder() {
        return 0;
    }
复制代码
```

注意Order的值越小优先级越高，并且无论是在配置文件中编写的单个路由过滤器还是全局路由过滤器，都会受到Order值影响（单个路由的过滤器Order值按从上往下的顺序从1开始递增），最终是按照Order值决定哪个过滤器优先执行，当Order值一样时 全局路由过滤器执行 `优于` 单独的路由过滤器执行。

## Config 配置中心

**官方文档：**https://docs.spring.io/spring-cloud-config/docs/current/reference/html/

#### Spring Cloud Config 为分布式系统中的外部配置提供服务器端和客户端支持。使用 Config Server，您可以集中管理所有环境中应用程序的外部配置。

![image-20230306230655437](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307171527676.png)

### 部署配置中心：

步骤：

1. 创建新的模块导入依赖：

   ```xml
   <dependencies>
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-config-server</artifactId>
       </dependency>
         <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
   </dependencies>
   ```

   

2. 新建启动类：

3. ```java
   @SpringBootApplication
   @EnableConfigServer
   public class ConfigApplication {
       public static void main(String[] args) {
           SpringApplication.run(ConfigApplication.class, args);
       }
   }
   ```

4. 修改配置文件：

5. ```yaml
   server:
     port: 8700
   spring:
     application:
       name: configserver
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:8801/eureka, http://localhost:8802/eureka
   ```

   

先启动一次看看，能不能成功：

![image-20230306230706452](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307171751110.png)

主要是让它能正常跑起来先

这里我们以本地仓库为例（就不用GitHub了，卡到怀疑人生了），首先在项目目录下创建一个本地Git仓库，打开终端，在桌面上创建一个新的本地仓库：

![image-20230306230726846](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307171754460.png)

然后我们在文件夹中随便创建一些配置文件，注意名称最好是{服务名称}-{环境}.yml：

![image-20230306230735629](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307171754872.png)

然后我们在配置文件中，添加本地仓库的一些信息（远程仓库同理），详细使用教程：https://docs.spring.io/spring-cloud-config/docs/current/reference/html/#_git_backend

```yaml
spring:
  cloud:
    config:
      server:
        git:
            # 这里填写的是本地仓库地址，远程仓库直接填写远程仓库地址 http://git...
          uri: file://${user.home}/Desktop/config-repo
          # 默认分支设定为你自己本地或是远程分支的名称
          default-label: main
```

然后启动我们的配置服务器，通过以下格式进行访问：

- http://localhost:8700/{服务名称}/{环境}/{Git分支}
- http://localhost:8700/{Git分支}/{服务名称}-{环境}.yml

比如我们要访问图书服务的生产环境代码，可以使用 http://localhost:8700/bookservice/prod/main 链接，它会显示详细信息：

![image-20230306230748280](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307171758917.png)

也可以使用 http://localhost:8700/main/bookservice-prod.yml 链接，它仅显示配置文件原文：

![image-20230306230755591](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307171758986.png)

当然，除了使用Git来保存之外，还支持一些其他的方式，详细情况请查阅官网。

这就配置好了服务端了（也就是这个config这个模块（配置中心）），接下来就是如何去读取了，也就是使用（就是其他模块如何去读取使用）

### 客户端的使用：

服务端配置完成之后，我们接着来配置一下客户端，那么现在我们的服务既然需要从服务器读取配置文件，那么就需要进行一些配置，我们删除原来的`application.yml`文件（也可以保留，最后无论是远端配置还是本地配置都会被加载），改用`bootstrap.yml`（在application.yml之前加载，可以实现配置文件远程获取）：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

```yaml
spring:
  cloud:
    config:
        # 名称，其实就是文件名称
      name: bookservice
      # 配置服务器的地址
      uri: http://localhost:8700
      # 环境
      profile: prod
      # 分支
      label: main
```

配置完成之后，启动图书服务：

![image-20230306230806269](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307171809416.png)

可以看到已经从远端获取到了配置，并进行启动。



个人总结客户端如何实现：

1. 导入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-config</artifactId>
   </dependency>
   
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-bootstrap</artifactId>
   </dependency>
   ```

   

2. 创建新的文件bootstrap.yml，（支持两个文件一起加载），在application.yml之前加载，可以实现配置文件远程获取）

```yaml
spring:
  cloud:
    config:
        # 名称，其实就是文件名称
      name: bookservice
      # 配置服务器的地址，也就是刚才的配置模块
      uri: http://localhost:8700
      # 环境
      profile: prod
      # 分支
      label: main
```

