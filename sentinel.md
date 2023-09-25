# sentinel

## 部署：

运行jar包：

导入依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

然后在配置文件中添加Sentinel相关信息（实际上Sentinel是本地在进行管理，但是我们可以连接到监控页面，这样就可以图形化操作了）：

```yaml
spring:
  application:
    name: userservice
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    sentinel:
      transport:
      	# 添加监控页面地址即可
        dashboard: localhost:8858
```

现在启动我们的服务，然后访问一次服务，这样Sentinel中就会存在信息了（懒加载机制，不会一上来就加载）：就是有访问了，这个模块（服务）才会注册进去stentinel

## 流量控制：

sentinel里面的名词：qps是每秒钟请求多少次，并发线程数是指：同一时间产生的并发线程数

### 限流方案：

- #### 方案一：**快速拒绝**，既然不再接受新的请求，那么我们可以直接返回一个拒绝信息，告诉用户访问频率过高。

- #### 方案二：**预热**，依然基于方案一，但是由于某些情况下高并发请求是在某一时刻突然到来，我们可以缓慢地将阈值提高到指定阈值，形成一个缓冲保护。

- #### 方案三：**排队等待**，不接受新的请求，但是也不直接拒绝，而是进队列先等一下，如果规定时间内能够执行，那么就执行，要是超时就算了。



### 第一种算法：

针对于是否超过流量阈值的判断，这里我们提4种算法：

1. **漏桶算法**

   顾名思义，就像一个桶开了一个小孔，水流进桶中的速度肯定是远大于水流出桶的速度的，这也是最简单的一种限流思路：

   我们知道，桶是有容量的，所以当桶的容量已满时，就装不下水了，这时就只有丢弃请求了。

   利用这种思想，我们就可以写出一个简单的限流算法。 

   ![image-20230922205514464](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309222055494.png)

1. **令牌桶算法**

   只能说有点像信号量机制。现在有一个令牌桶，这个桶是专门存放令牌的，每隔一段时间就向桶中丢入一个令牌（速度由我们指定）当新的请求到达时，将从桶中删除令牌，接着请求就可以通过并给到服务，但是如果桶中的令牌数量不足，那么不会删除令牌，而是让此数据包等待。

   ![image-20230922205449942](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309222054991.png)

   可以试想一下，当流量下降时，令牌桶中的令牌会逐渐积累，这样如果突然出现高并发，那么就能在短时间内拿到大量的令牌。

   这个类似于之前线程锁那个，线程要拿到忘记什么呢，才能去执行。这里的请求要拿到令牌才能通过，

   

1. **固定时间窗口算法**

   我们可以对某一个时间段内的请求进行统计和计数，比如在`14:15`到`14:16`这一分钟内，请求量不能超过`100`，也就是一分钟之内不能超过`100`次请求，那么就可以像下面这样进行划分：

   ![image-20230922205533040](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309222055066.png)

   虽然这种模式看似比较合理，但是试想一下这种情况：

   - 14:15:59的时候来了100个请求
   - 14:16:01的时候又来了100个请求

   出现上面这种情况，符合固定时间窗口算法的规则，所以这200个请求都能正常接受，但是，如果你反应比较快，应该发现了，我们其实希望的是60秒内只有100个请求，但是这种情况却是在3秒内出现了200个请求，很明显已经违背了我们的初衷。

   因此，当遇到临界点时，固定时间窗口算法存在安全隐患。

2. **滑动时间窗口算法**

   相对于固定窗口算法，滑动时间窗口算法更加灵活，它会动态移动窗口，重新进行计算：

   ![image-20230922205547380](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309222055435.png)

   虽然这样能够避免固定时间窗口的临界问题，但是这样显然是比固定窗口更加耗时的。



## 如何使用sentinel实现：

打开面板：然后在模块选择需要想要限流的接口，点击流控

- 阀值类型：qps是指一秒钟最多访问次数，并发线程数是指：同一时间，访问的线程数
- 流控效果是指流控的算法，如上述三种描述：拒绝，预热，排队等待

![image-20230922205858878](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309222058961.png)—

### 第二个控制限流办法：关联：

如果你想要限制接口A的访问，当接口B的流量达到一定阀值时触发限流操作，那么你需要在接口B上设置流控规则，以控制接口B的流量，并且将接口A关联到接口B。这样，当接口B的流量超过设定的阀值时，接口A的访问将受到限制，以保护接口A不受过多的流量冲击。

### 第三个控制方法：可以精准控制到方法：

它能够更加精准的进行流量控制，链路流控模式指的是，当从指定接口过来的资源请求达到限流条件时，开启限流，这里得先讲解一下`@SentinelResource`的使用。

我们可以对某一个方法进行限流控制，无论是谁在何处调用了它，这里需要使用到`@SentinelResource`，一旦方法被标注，那么就会进行监控，比如我们这里创建两个请求映射，都来调用Service的被监控方法：

注意模块要加入配置文件：

```yaml
spring:
  application:
    name: borrowservice
  cloud:
    sentinel:
      transport:
        dashboard: localhost:8858
      # 关闭Context收敛，这样被监控方法可以进行不同链路的单独控制//这个是设置监控接口的，当监控到一定的流量是，会抛出异常（注解和控制面板一起使用）
      web-context-unify: false
```

//注解和sentinel控制面板一起使用

```java
@Service
public class BorrowServiceImpl implements BorrowService{

    @Resource
    BorrowMapper mapper;

    @Resource
    UserClient userClient;

    @Resource
    BookClient bookClient;

    @Override
    @SentinelResource("getBorrow")   //监控此方法，无论被谁执行都在监控范围内，这里给的value是自定义名称，这个注解可以加在任何方法上，包括Controller中的请求映射方法，跟HystrixCommand贼像
    public UserBorrowDetail getUserBorrowDetailByUid(int uid) {
        List<Borrow> borrow = mapper.getBorrowsByUid(uid);
        User user = userClient.getUserById(uid);
        List<Book> bookList = borrow
                .stream()
                .map(b -> bookClient.getBookById(b.getBid()))
                .collect(Collectors.toList());
        return new UserBorrowDetail(user, bookList);
    }
}
```

然后我们在Sentinel控制台中添加流控规则，注意是针对此方法，可以看到已经自动识别到borrow接口下调用了这个方法：

![image-20230306232304858](https://image.itbaima.net/markdown/2023/03/06/FOzJdtoieAxIvPq.png![image-20230922214821339](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309222148398.png)



我们在浏览器中对这两个接口都进行测试，会发现，无论请求哪个接口，只要调用了Service中的`getUserBorrowDetailByUid`这个方法，都会被限流。注意限流的形式是后台直接抛出异常，至于怎么处理我们后面再说。

这个链路选项实际上就是决定只限流从哪个方向来的调用，比如我们只对`borrow2`这个接口对`getUserBorrowDetailByUid`方法的调用进行限流，那么我们就可以为其指定链路：==（链路是设置从哪个url来和注解结合使用，比如我方法1被两个接口调用），但是我链路设置呢a接口，所以a接口受控，b接口不受控，但是调用的方法都是同一个，受控对象是在链路设置的==



理解你的描述后，看起来你正在讨论流控中的链路控制（Link Control）。链路控制是Sentinel中的一项功能，允许你根据请求的来源，即调用链路，来进行限流。

假如你想要限制`getUserBorrowDetailByUid`方法的调用，但只限制来自接口A的调用，而不限制来自接口B的调用。你可以通过设置链路来实现这一目标。具体步骤如下：

1. 配置规则：首先，在Sentinel的配置中，创建一个规则来限制`getUserBorrowDetailByUid`方法的访问。这个规则将适用于所有调用这个方法的请求。

2. 设置链路：在规则配置中，设置链路，指定它来自于接口A。这样，规则将只对来自接口A的调用生效，而来自接口B的调用将不受限制。

3. 启用规则：确保规则已经生效，并监控Sentinel的控制台或日志以确保规则按预期工作。

通过这种方式，你可以根据链路信息来对不同的调用进行限流，从而实现精确的流控策略，确保系统的稳定性。在你的情况下，你设置了链路为接口A，所以只有来自接口A的调用会受到流控的影响，而来自接口B的调用不会受到影响。这样，你可以实现对`getUserBorrowDetailByUid`方法的有针对性的限流。



如果你在规则中没有明确指定链路，而只设置了对`getUserBorrowDetailByUid`方法的限流规则，那么这个规则将会应用于所有调用这个方法的接口，而不论它们来自于哪个接口。这意味着所有使用这个方法的接口都将受到相同的流控规则的影响，无论调用它们的接口是A还是B或其他接口。

如果你想要实现精确的流控策略，只对特定接口的调用进行限流，那么你需要明确指定链路，将规则限制在特定的调用链路上。如果没有设置链路，规则将适用于该方法的所有调用。链路控制允许你根据调用来源来细化流控策略，以满足不同的需求。

---

### 关联的场景：

关联案例可以用于控制多个接口之间的流量，当一个接口的流量达到一定阀值时，影响其他关联接口的访问。以下是一个关联案例示例：

**场景描述**：
假设你有一个电子商务网站，有两个关键的接口：`/product`用于获取产品信息，和`/order`用于创建订单。你希望在产品详情页面的访问量非常高时，限制订单创建的流量，以避免服务器过载。

**解决方案**：
你可以使用Sentinel的关联流控功能来实现这个需求。

1. **配置规则**：
   - 针对`/product`接口，设置流控规则，当其QPS（每秒请求数）达到一定阀值（例如1000 QPS）时，触发限流操作。

2. **关联流控**：
   - 在`/product`接口的流控规则中，关联`/order`接口，意味着当`/product`的流量达到限制阀值时，将会影响到`/order`接口的访问。

3. **设置限流策略**：
   - 选择适当的限流策略，比如令牌桶或漏桶，以控制`/product`接口的访问速率。

4. **启用规则**：
   - 确保规则已经生效，并监控Sentinel的控制台或日志，以确保规则按预期工作。

通过这种设置，当产品详情页面的访问量超过一定阀值时，`/product`接口的流量将受到限制，从而保护服务器免受过多的请求冲击，同时不会影响到`/order`接口的访问。这样可以确保系统在高峰时期仍然能够正常运行。

---

### 可以自定义系统规则：

系统规则支持以下的模式：

- **Load 自适应**（仅对 Linux/Unix-like 机器生效）：系统的 load1 作为启发指标，进行自适应系统保护。当系统 load1 超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护（BBR 阶段）。系统容量由系统的 `maxQps * minRt` 估算得出。设定参考值一般是 `CPU cores * 2.5`。

- **CPU usage**（1.5.0+ 版本）：当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0-1.0），比较灵敏。

- **平均 RT**：当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒。

- **并发线程数**：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。

- **入口 QPS**：当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护。

- ---



----



## 限流页面处理和处理异常：

### 配置级别限流页面

现在我们已经了解了如何进行限流操作，那么限流状态下的返回结果该怎么修改呢，我们看到被限流之后返回的是Sentinel默认的数据，现在我们希望自定义改如何操作？

这里我们先创建好被限流状态下需要返回的内容，定义一个请求映射：

```java
@RequestMapping("/blocked")
JSONObject blocked(){
    JSONObject object = new JSONObject();
    object.put("code", 403);
    object.put("success", false);
    object.put("massage", "您的请求频率过快，请稍后再试！");
    return object;
}
```

接着我们在配置文件中将此页面设定为限流页面：

```yaml
spring:
  cloud:
    sentinel:
      transport:
        dashboard: localhost:8858
      # 将刚刚编写的请求映射设定为限流页面
      block-page: /blocked
```

这样，当被限流时，就会被重定向到指定页面：

![image-20230922225848945](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309222258976.png)

### 方法级别的限流页面

经过前面的学习我们知道，当某个方法被限流时，会直接在后台抛出异常，那么这种情况我们该怎么处理呢，比如我们之前在Hystrix中可以直接添加一个替代方案，这样当出现异常时会直接执行我们的替代方法并返回，Sentinel也可以。

比如我们还是在`getUserBorrowDetailByUid`方法上进行配置：

```java
@Override
@SentinelResource(value = "getBorrow", blockHandler = "blocked")   //指定blockHandler，也就是被限流之后的替代解决方案，这样就不会使用默认的抛出异常的形式了
public UserBorrowDetail getUserBorrowDetailByUid(int uid) {
    List<Borrow> borrow = mapper.getBorrowsByUid(uid);
    User user = userClient.getUserById(uid);
    List<Book> bookList = borrow
            .stream()
            .map(b -> bookClient.getBookById(b.getBid()))
            .collect(Collectors.toList());
    return new UserBorrowDetail(user, bookList);
}

//替代方案，注意参数和返回值需要保持一致，并且参数最后还需要额外添加一个BlockException
public UserBorrowDetail blocked(int uid, BlockException e) {
    return new UserBorrowDetail(null, Collections.emptyList());
}
```

可以看到，一旦被限流将执行替代方案，最后返回的结果就是：

![image-20230922225904811](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309222259840.png)



注意`blockHandler`只能处理限流情况下抛出的异常，包括下面即将要介绍的热点参数限流也是同理，如果是方法本身抛出的其他类型异常，不在管控范围内，但是可以通过其他参数进行处理：

----

### 异常处理：

我们可以设置方法内异常，然后重定向或者执行指定的异常方法

也就是说，异常处理是进入了方法里面的：

```java
@RequestMapping("/test")
@SentinelResource(value = "test",
        fallback = "except",    //fallback指定出现异常时的替代方案（这个是进入了方法里面，然后出现异常然后就执行except这个方法）
        exceptionsToIgnore = IOException.class)  //忽略那些异常，也就是说这些异常出现时不使用替代方案
String test(){
    throw new RuntimeException("HelloWorld！");
}

//替代方法必须和原方法返回值和参数一致，最后可以添加一个Throwable作为参数接受异常
String except(Throwable t){
    return t.getMessage();
}
```

在 Sentinel 中，方法级别的流控和接口级别的流控主要是通过你的==配置方式==来区分的，具体取决于你是使用注解还是配置文件来定义流控规则。

1. **方法级别的流控**：

   - 当你使用注解（例如 `@SentinelResource` 注解）在具体的方法上配置流控规则时，这被认为是方法级别的流控。

   - 也就是blockHandler这个注解的value设置对应要执行的方法：

   - ### 案例：

   - ```java
     @Override
     @SentinelResource(value = "getBorrow", blockHandler = "blocked")   //指定blockHandler，也就是被限流之后的替代解决方案，这样就不会使用默认的抛出异常的形式了
     public UserBorrowDetail getUserBorrowDetailByUid(int uid) {
         List<Borrow> borrow = mapper.getBorrowsByUid(uid);
         User user = userClient.getUserById(uid);
         List<Book> bookList = borrow
                 .stream()
                 .map(b -> bookClient.getBookById(b.getBid()))
                 .collect(Collectors.toList());
         return new UserBorrowDetail(user, bookList);
     }
     
     //替代方案，注意参数和返回值需要保持一致，并且参数最后还需要额外添加一个BlockException
     public UserBorrowDetail blocked(int uid, BlockException e) {
         return new UserBorrowDetail(null, Collections.emptyList());
     }
     ```

     

   - 通过注解方式配置的流控规则会针对具体的方法生效，允许你对不同的方法应用不同的流控策略。

2. **接口级别的流控**：

   - 当你使用配置文件或其他方式配置规则，以控制某个接口（URL 或资源名）的流量时，这被认为是接口级别的流控。

   - 接口级别的流控规则通常会应用于整个接口的所有方法，无论调用哪个方法都受到相同的流控策略。

   - 也就是设置yaml文件

   - ### 案例：

   - ```yaml
     spring:
       cloud:
         sentinel:
           transport:
             dashboard: localhost:8858
           # 将刚刚编写的请求映射设定为限流页面
           block-page: /blocked
     ```

     ```java
     @RequestMapping("/blocked")
     JSONObject blocked(){
         JSONObject object = new JSONObject();
         object.put("code", 403);
         object.put("success", false);
         object.put("massage", "您的请求频率过快，请稍后再试！");
         return object;
     }
     ```

     

所以，你可以根据具体的需求选择使用方法级别的流控或接口级别的流控，并使用相应的配置方式来定义规则。这种灵活性使你可以根据业务场景来精确控制流量，以确保系统的稳定性和可用性。

### 服务熔断和服务降级区分：

1. - 服务熔断是一种应对分布式系统中服务间调用故障的策略，旨在提高系统的可用性和稳定性。服务熔断的主要思想是，在发生服务调用失败或异常时，暂时阻止对失败服务的继续调用，以避免连锁故障，同时允许部分流量通过以测试服务是否恢复正常。以下是服务熔断的关键概念和原则：

     1. **断路器状态 (Circuit Breaker State)**：
        - 服务熔断使用断路器模式，就像电路中的断路器一样，用于监控和控制对某个服务的访问。断路器可以处于以下三种状态之一：关闭、开启、或半开启。
        - 初始状态通常是关闭状态，允许流量通过。
        - 如果一段时间内发生了一定比例的调用失败（例如，连续的请求失败达到阈值），断路器将切换到开启状态，阻止所有的流量访问服务。
        - 在开启状态下，断路器会定期检测服务是否恢复正常。如果服务调用成功率达到阈值，断路器将切换到半开启状态，允许部分流量通过测试服务是否已经恢复正常。
        - 如果在半开启状态下服务调用仍然失败，断路器将再次切换到开启状态，继续等待服务恢复。

     2. **失败阈值和时间窗口 (Failure Threshold and Time Window)**：
        - 服务熔断需要定义失败阈值，表示一定时间内的失败次数或失败比例，以触发断路器的状态切换。
        - 同时，还需要定义时间窗口，表示统计失败次数的时间段。例如，过去5分钟内的失败次数。

     3. **熔断动作 (Circuit Breaker Actions)**：
        - 当断路器切换到开启状态时，通常会采取一些降级措施，例如返回缺省值、执行备用逻辑，或者返回友好的错误信息。
        - 这些降级措施通常被称为 "fallback"，它们用于在服务不可用时提供一种备选方案，以确保系统的可用性。

     4. **监控和反馈 (Monitoring and Feedback)**：
        - 断路器的状态需要被监控，以及时发现故障和恢复的情况。
        - 监控可以通过指标和报警来实现，以便在断路器状态切换时采取适当的措施。

     服务熔断是分布式系统中的重要策略之一，可以帮助系统应对故障并提高容错能力。通过断路器模式，系统能够在服务不可用时快速降级，以避免连锁故障，同时在服务恢复正常后逐渐恢复正常操作。这有助于提高系统的可靠性和稳定性。

2. **服务降级 (Service Degradation)**：

   - 服务降级是一种应对系统负载过大或异常情况的策略，它是一种应急措施，用于保护系统的稳定性。
   - 在服务降级中，当系统负载过高或者出现异常时，系统会有意减少或关闭某些服务功能，以确保核心功能的正常运行。这可以防止系统崩溃或变得不可用。
   - 服务降级的典型例子包括在高负载时返回有限的信息、关闭不必要的功能、提供基本的服务而不是完整的服务等。

总结来说，服务垄断描述了市场竞争的情况，其中一个服务提供商或一组提供商占据市场主导地位。而服务降级是一种应对高负载或异常情况的策略，它用于保护系统的稳定性，通过降低服务质量或提供有限的服务来应对不可预测的情况。这两个概念描述了不同层面的问题，一个是市场竞争，另一个是系统稳定性。

##### 简单来说就是一个是减少渲染的东西，从而降低服务，比如图片慢渲染等，一个是在发生服务调用失败或异常时，暂时阻止对失败服务的继续调用，以避免连锁故障，同时允许部分流量通过以测试服务是否恢复正常。

----

### 热点参数限流：

可以对参数进行限流：比如我url需要携带参数a或者参数b，我可以设置对于带参数a的进行限流，参数b的不限流

我们还可以对某一热点数据进行精准限流，比如在某一时刻，不同参数被携带访问的频率是不一样的：

- http://localhost:8301/test?a=10 访问88次
- http://localhost:8301/test?b=10 访问0次
- http://localhost:8301/test?c=10 访问3次

由于携带参数`a`的请求比较多，我们就可以只对携带参数`a`的请求进行限流。

这里我们创建一个新的测试请求映射：

```java
@RequestMapping("/test")
@SentinelResource("test")   //注意这里需要添加@SentinelResource才可以，用户资源名称就使用这里定义的资源名称
String findUserBorrows2(@RequestParam(value = "a", required = false) int a,
                        @RequestParam(value = "b", required = false) int b,
                        @RequestParam(value = "c",required = false) int c) {
    return "请求成功！a = "+a+", b = "+b+", c = "+c;
}
```

启动之后，我们在Sentinel里面进行热点配置：

![image-20230924155734700](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309241557758.png)

资源名称是指@SentinelResource("test")   这个注解的值，参数索引是指第几个参数，从0开始计算

这个是参数限制，还可以参数限制，但是可以设计当限制的参数的值为多少时是另一个设置的限流大小，比如我要设置限流参数a的值等于80时，不再限制或者限制次数改变（也就是参数例外项）：

![image-20230924160055982](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309241600035.png)

如上图所示，当请求携带参数`a`，且参数`a`的值为10时，阈值将按照我们指定的特例进行计算。

## 熔断与服务降级：

#### 个人使用感受：

##### 熔断后返回限流页面，如果是接口熔断（资源路径填写是接口），则是配置文件限流页面，如果是方法熔断（就是使用了注解，资源路径是注解的值），那么返回方法限流页面，blockHander的值（方法）

### 参考资料：

当下游服务因为某种原因变得不可用或响应过慢时，上游服务为了保证自己整体服务的可用性，不再继续调用目标服务而是快速返回或是执行自己的替代方案，这便是服务降级。![image-20230924182044619](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309241820679.png)

整个过程分为三个状态：

- 关闭：熔断器不工作，所有请求全部该干嘛干嘛。
- 打开：熔断器工作，所有请求一律降级处理。
- 半开：尝试进行一下下正常流程，要是还不行继续保持打开状态，否则关闭。

那么我们来看看Sentinel中如何进行熔断和降级操作，打开管理页面，我们可以自由新增熔断规则：

其中，熔断策略有三种模式：

1. **慢调用比例：**如果出现那种半天都处理不完的调用，有可能就是服务出现故障，导致卡顿，这个选项是按照最大响应时间（RT）进行判定，如果一次请求的处理时间超过了指定的RT，那么就被判定为`慢调用`，在一个统计时长内，如果请求数目大于最小请求数目，并且被判定为`慢调用`的请求比例已经超过阈值，将触发熔断。经过熔断时长之后，将会进入到半开状态进行试探（这里和Hystrix一致）

**异常比例：**这个与慢调用比例类似，不过这里判断的是出现异常的次数

**异常数：**这个和上面的唯一区别就是，只要达到指定的异常数量，就熔断。

### 服务降级：

#### 方案一：使用流控方式

熔断规则如何设定我们了解了，那么，如何自定义服务降级呢？之前在使用Hystrix的时候，如果出现异常，可以执行我们的替代方案，Sentinel也是可以的。

同样的，我们只需要在`@SentinelResource`中配置`blockHandler`参数（那这里跟前面那个方法限流的配置不是一毛一样吗？没错，因为如果添加了`@SentinelResource`注解，那么这里会进行方法级别细粒度的限制，和之前方法级别限流一样，会在降级之后直接抛出异常，如果不添加则返回默认的限流页面，`blockHandler`的目的就是处理这种Sentinel机制上的异常，所以这里其实和之前的限流配置是一个道理，因此下面熔断配置也应该对`value`自定义名称的资源进行配置，才能作用到此方法上）：

```java
@RequestMapping("/borrow2/{uid}")
@SentinelResource(value = "findUserBorrows2", blockHandler = "test")
UserBorrowDetail findUserBorrows2(@PathVariable("uid") int uid) {
    throw new RuntimeException();
}

UserBorrowDetail test(int uid, BlockException e){
    return new UserBorrowDetail(new User(), Collections.emptyList());
}
```

#### 方案二：使用feign注解顺带的服务降级

最后我们来看一下如何让Feign的也支持Sentinel，前面我们使用Hystrix的时候，就可以直接对Feign的每个接口调用单独进行服务降级，而使用Sentinel，也是可以的，首先我们需要在配置文件中开启支持：

```yml
feign:
  sentinel:
    enabled: true
```

之后的步骤其实和之前是一模一样的，首先创建实现类：

```java
@Component
public class UserClientFallback implements UserClient{
    @Override
    public User getUserById(int uid) {
        User user = new User();
        user.setName("我是替代方案");
        return user;
    }
}
```

然后直接启动就可以了，中途的时候我们吧用户服务全部下掉，可以看到正常使用替代方案：

#### 方案三：使用异常返回替代方案：

使用注解sentinel的fallback执行异常时执行方案

----

您的理解基本正确，让我更详细地解释这些概念：

1. **静态服务降级**：
   - 静态服务降级通常是通过在代码中显式实现降级逻辑来处理服务不可用的情况。这可以是在Feign客户端中为每个方法提供默认值或备用响应，以便在服务故障时返回。
   - 静态服务降级与Feign注解的具体实现方式无关，而是在代码中为每个方法提供替代响应的策略。
   - 静态服务降级的主要特点是它不依赖于实时监控或动态调整，它总是提供预定义的默认响应。

2. **基于异常的服务降级**：
   - 基于异常的服务降级是一种通过捕获异常并提供替代响应来处理服务故障的方法。当服务调用发生异常时，降级处理逻辑会接管并返回备用数据或默认响应。
   - 在Sentinel流量控制中，`blockHandler`通常用于处理异常情况。如果服务调用受到流量控制限制或发生其他异常，`blockHandler`可以用来提供降级响应。

3. **动态服务降级**：
   - 动态服务降级是一种根据实时监控数据和策略来动态地调整服务降级策略的方法。这通常依赖于监控工具或服务治理框架，可以根据负载、响应时间、错误率等指标来自动触发服务降级。
   - Sentinel是一个用于动态服务降级和流量控制的工具，它可以根据实时监控数据来自动触发流量控制、熔断、降级等策略，以确保系统的可靠性和性能。
   

综上所述，基于异常的服务降级通常与流量控制和Sentinel中的`blockHandler`结合使用，而静态服务降级则是一种通过代码显式实现的方法，与Feign注解的具体实现方式无关。动态服务降级则是一种根据实时监控数据动态调整降级策略的方法，通常由监控工具或服务治理框架来支持。

还有就是资源路径填写接口就是基于接口配置的流控，填写注解的value才是基于方法流控

---

