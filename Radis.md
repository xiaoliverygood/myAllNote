# Radis

## Nosql的概述：radis远程登录，修改config文件，必须要在最后末尾加！！！

也就是修改加入bind 0.0.0.0，必须要在末尾加入，要不然，识别不了

### 意义：

  为的是快速响应，因为磁盘的读写性能不行，所以要用内存去存储 

### 数据库的划分：

- 键值对数据库
- 列存储数据库
- 文档型数据库
- 图形数据库

##### radis是键值对数据库，他存放信息的位置是内存中

## 开启radis之路：

首先下载radis，官方的是没有window版本，需要到guihub去下载，然后解压压缩包

启动，可以双击radis-server启动（好像是没有配置文件的，这种启动方式）

![image-20230508201546388](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305082015432.png)

第二个启动是在窗口cmd 后面输入radis—server.exe radis.windows.conf这个启动是有配置文件的

![image-20230508201529826](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305082015939.png)

radis数据库的连接：

![image-20230508202038600](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305082020624.png)

如果居于

## 基本操作：像操作hashmap那样进行操作就可以了

不用提前创建一张表，因为它是键值对形式

radis的数据库是按下标索引进行区分的，不是像之前mysql那样有数据库名字，其实号码也就是可以理解为名字，首先进入是0号数据库

默认是16个数据库，可以在配置文件中去修改个数

数据库的切换：

![image-20230508203345665](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305082033690.png)

```xml
select 序号（索引）;
```

mysql的数据库切换是：

```java
use 数据库名称
```

### 插入键值对：

```
//使用set 键 值 这样的形式去插入键值对信息
set key value
//如果是多个键值对信息就 mset key1 value1 key2 value2 key3 value3.。。
```

![image-20230508203715846](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305082037870.png)

### 获取值：get 键的名称

```
get key//这样子就可以获取值
```



---

所以类似于java的![image-20230508203929361](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305082039398.png)

只是put变成了set

### 可以设置过期时间：

```
set key value ex xxx//设置xxx秒后过期，这个键值对px是以毫秒为单位，ex以秒为单位
如果不设置ex/px，默认设置是永久的，也就是不过期
```

一旦过期，则会删除这个键值对，是连键都没有了

### 可以通过ttl来查看还有多久过期：

ttl 键的名称

### 删除数据

使用del 键，这样子就可以删除数据了

### 查看所有的键

```
keys *
```

### 查看键是否存在：

exists 键的名称：如果返回0表示假，也就是不存在，返回1表示真也就是存在

已有的键值对改变键的名字，使用rename 原来的键的名字 新的键的名字

```
rename key newKey
```

type 键的名称，可以查看是什么类型的数据，例如hash set  list，也就是说键对应的值的数据类型

## 数据类型介绍

一个键值对除了存储一个String类型的值以外，还支持多种常用的数据类型。

### Hash

这种类型本质上就是一个HashMap，也就是嵌套了一个HashMap罢了，在Java中就像这样：

```java
#Redis默认存String类似于这样：
Map<String, String> hash = new HashMap<>();
#Redis存Hash类型的数据类似于这样：
Map<String, Map<String, String>> hash = new HashMap<>();
```

它比较适合存储类这样的数据，由于值本身又是一个Map，因此我们可以在此Map中放入类的各种属性和值，以实现一个Hash数据类型存储一个类的数据。

我们可以像这样来添加一个Hash类型的数据：

```sql
hset <key> [<字段> <值>]...
```

我们可以直接获取：

```sql
hget <key> <字段>
-- 如果想要一次性获取所有的字段和值
hgetall <key>
```

同样的，我们也可以判断某个字段是否存在：

```sql
hexists <key> <字段>
```

删除Hash中的某个字段：

```sql
hdel <key>
```

我们发现，在操作一个Hash时，实际上就是我们普通操作命令前面添加一个`h`，这样就能以同样的方式去操作Hash里面存放的键值对了，这里就不一一列出所有的操作了。我们来看看几个比较特殊的。

我们现在想要知道Hash中一共存了多少个键值对：

```sql
hlen <key>
```

我们也可以一次性获取所有字段的值：

```sql
hvals <key>
```

唯一需要注意的是，Hash中只能存放字符串值，不允许出现嵌套的的情况。

### hash总结：

其实就是将原本的string：string变成了string：map，这样子的形式

##### 其中string一样是键 （可以理解为最大的键），后面可以跟着map里面的键，也就是键的值的键（可以自己理解为小键）；

注意，在操作键的值的键，注意使用原本的string：string方法的前提下加上h如hset表示这种hash形式；mset还记得吗？是集合就是多个键值对，注意是string：string类型

![image-20230508215754024](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305082157081.png)

我的最大的键是xiao，小的键是name age sex

查看所有的键是大键，小键是不会显示的

![image-20230508215935479](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305082159505.png)

![image-20230508220425018](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305082204042.png)

获取小键的全部键

所有这个常用于登录

---

---

### List

我们接着来看List类型，实际上这个猜都知道，它就是一个列表，而列表中存放一系列的字符串，它支持随机访问，支持双端操作，就像我们使用Java中的LinkedList一样。

我们可以直接向一个已存在或是不存在的List中添加数据，如果不存在，会自动创建：

```sql
-- 向列表头部添加元素
lpush <key> <element>...
-- 向列表尾部添加元素
rpush <key> <element>...
-- 在指定元素前面/后面插入元素
linsert <key> before/after <指定元素> <element>
```

同样的，获取元素也非常简单：

```sql
-- 根据下标获取元素
lindex <key> <下标>
-- 获取并移除头部元素
lpop <key>
-- 获取并移除尾部元素
rpop <key>
-- 获取指定范围内的
lrange <key> start stop
```

注意下标可以使用负数来表示从后到前数的数字（Python：搁这儿抄呢是吧）:

```sql
-- 获取列表a中的全部元素
lrange a 0 -1
```

没想到吧，push和pop还能连着用呢：

```sql
-- 从前一个数组的最后取一个数出来放到另一个数组的头部，并返回元素
rpoplpush 当前数组 目标数组
```

它还支持阻塞操作，类似于生产者和消费者，比如我们想要等待列表中有了数据后再进行pop操作：

```sql
-- 如果列表中没有元素，那么就等待，如果指定时间（秒）内被添加了数据，那么就执行pop操作，如果超时就作废，支持同时等待多个列表，只要其中一个列表有元素了，那么就能执行
blpop <key>... timeout
```

### 总结list：

##### 这个模式也就是可以看作String：List 这样子的模式，也就是说list的名称是string ，string这个键对应的值是list的全部

### Set和SortedSet

Set集合其实就像Java中的HashSet一样（我们在JavaSE中已经讲解过了，HashSet本质上就是利用了一个HashMap，但是Value都是固定对象，仅仅是Key不同）它不允许出现重复元素，不支持随机访问，但是能够利用Hash表提供极高的查找效率。

向Set中添加一个或多个值：

```sql
sadd <key> <value>...
```

查看Set集合中有多少个值：

```sql
scard <key>
```

判断集合中是否包含：

```sql
-- 是否包含指定值
sismember <key> <value>
-- 列出所有值
smembers <key>
```

集合之间的运算：

```sql
-- 集合之间的差集
sdiff <key1> <key2>
-- 集合之间的交集
sinter <key1> <key2>
-- 求并集
sunion <key1> <key2>
-- 将集合之间的差集存到目标集合中
sdiffstore 目标 <key1> <key2>
-- 同上
sinterstore 目标 <key1> <key2>
-- 同上
sunionstore 目标 <key1> <key2>
```

移动指定值到另一个集合中：

```sql
smove <key> 目标 value 
```

移除操作：

```sql
-- 随机移除一个幸运儿
spop <key>
-- 移除指定
srem <key> <value>...
```

那么如果我们要求Set集合中的数据按照我们指定的顺序进行排列怎么办呢？这时就可以使用SortedSet，它支持我们为每个值设定一个分数，分数的大小决定了值的位置，所以它是有序的。

我们可以添加一个带分数的值：

```sql
zadd <key> [<value> <score>]...
```

同样的：

```sql
-- 查询有多少个值
zcard <key>
-- 移除
zrem <key> <value>...
-- 获取区间内的所有
zrange <key> start stop
```

由于所有的值都有一个分数，我们也可以根据分数段来获取：

``` sql
-- 通过分数段查看
zrangebyscore <key> start stop [withscores] [limit]
-- 统计分数段内的数量
zcount <key>  start stop
-- 根据分数获取指定值的排名
zrank <key> <value>
```

https://www.jianshu.com/p/32b9fe8c20e1

##### 总结set：其实也就是类似于上面的那两种方式，就是值变成一个set集合而已，也就是String：set这个方式

## radis持久化（其实就是备份数据）：

1·将数据备份到硬盘上，备份数据 

2·第二种方式就是，存放的是命令，就是插入删除数据等这些命令

 RDB

RDB就是我们所说的第一种解决方案，那么如何将数据保存到本地呢？我们可以使用命令：

```sql
save
-- 注意上面这个命令是直接保存，会占用一定的时间，也可以单独开一个子进程后台执行保存
bgsave
```

执行后，会在服务端目录下生成一个dump.rdb文件，而这个文件中就保存了内存中存放的数据，当服务器重启后，会自动加载里面的内容到对应数据库中。保存后我们可以关闭服务器：

```sql
shutdown
```

重启后可以看到数据依然存在。

![image-20230509204651327](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305092046370.png)

 可以直接读数据，因为保存了在服务端的硬盘文件里，自动加载文件

我们可以在配置文件中设置自动保存，并设定在一段时间内写入多少数据时，执行一次保存操作：

```
save 300 10 # 300秒（5分钟）内有10个写入
save 60 10000 # 60秒（1分钟）内有10000个写入
```

配置的save使用的都是bgsave后台执行。这个自动保存在配置文件中是默认的 

### AOF

虽然RDB能够很好地解决数据持久化问题，但是它的缺点也很明显：每次都需要去完整地保存整个数据库中的数据，同时后台保存过程中也会产生额外的内存开销，最严重的是它并不是实时保存的，如果在自动保存触发之前服务器崩溃，那么依然会导致少量数据的丢失。

而AOF就是另一种方式，它会以日志的形式将我们每次执行的命令都进行保存，服务器重启时会将所有命令依次执行，通过这种重演的方式将数据恢复，这样就能很好解决实时性存储问题。

![rdb和aof区别](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305092307528.png)

但是，我们多久写一次日志呢？我们可以自己配置保存策略，有三种策略：

* always：每次执行写操作都会保存一次
* everysec：每秒保存一次（默认配置），这样就算丢失数据也只会丢一秒以内的数据
* no：看系统心情保存

可以在配置文件中配置：

```sql
# 注意得改成也是
appendonly yes

# appendfsync always
appendfsync everysec
# appendfsync no
```

重启服务器后，可以看到服务器目录下多了一个`appendonly.aof`文件，存储的就是我们执行的命令。

 AOF的缺点也很明显，每次服务器启动都需要进行过程重演，相比RDB更加耗费时间，并且随着我们的操作变多，不断累计，可能到最后我们的aof文件会变得无比巨大，我们需要一个改进方案来优化这些问题。

Redis有一个AOF重写机制进行优化，比如我们执行了这样的语句：

```
lpush test 666
lpush test 777
lpush test 888
```

实际上用一条语句也可以实现：

```
lpush test 666 777 888
```

正是如此，只要我们能够保证最终的重演结果和原有语句的结果一致，无论语句如何修改都可以，所以我们可以通过这种方式将多条语句进行压缩。

我们可以输入命令来手动执行重写操作：

```sql
bgrewriteaof
```

或是在配置文件中配置自动重写：

```
# 百分比计算，这里不多介绍
auto-aof-rewrite-percentage 100
# 当达到这个大小时，触发自动重写
auto-aof-rewrite-min-size 64mb
```

至此，我们就完成了两种持久化方案的介绍，最后我们再来进行一下总结：

* #### AOF：

  * 优点：存储速度快、消耗资源少、支持实时存储
  * 缺点：加载速度慢、数据体积大

* #### RDB：

  * 优点：加载速度快、数据体积小
  * 缺点：存储速度慢大量消耗资源、会发生数据丢失

---

## 事务和锁机制：

#### 开启事务：

输入multi表示开启事务模式：最后提交才会执行，而mysql是模拟执行，有返回的数据，而radis是保存命令，不会去真正的执行，也没有模拟执行返回的数据；但是相同的是，mysql与radis最终真正的去执行，是提交后，才会写入数据库的。

#### 提交事务：

输入exec表示提交事务，这样子才会去真正的写入radis数据库，也是跟mysql一样的原理

#### 取消事务：不像mysql那样可以回滚：

mysql可以回滚，可以取消事务，但是radis只能取消事务；==使用discard这个命令表示取消事务==

### 锁：

radis使用的是乐观锁：

实际上这个概念对我们来说已经不算是陌生了。实际上在Redis中也会出现多个命令同时竞争同一个数据的情况，比如现在有两条命令同时执行，他们都要去修改a的值，那么这个时候就只能动用锁机制来保证同一时间只能有一个命令操作。

### 乐观锁与悲观锁区别：

#### 官方理解：

- 悲观锁：时刻认为别人会来抢占资源，禁止一切外来访问，直到释放锁，具有强烈的排他性质。
- 乐观锁：并不认为会有人来抢占资源，所以会直接对数据进行操作，在操作时再去验证是否有其他人抢占资源。

#### 个人理解：

乐观锁，正如上面所说，非常乐观，不会认为我执行某条数据的增删改查是，别人也会执行，所以他不会对数据上锁，也就是锁到真正要操作数据时才会认证是否正确，是否可以执行：

案例：例如我对a这个键进行修改数据，由原本的200变成300，正在这个时候，B用户也是进行将a这个键的值改为100；这个样子的的话，看谁手速快，改了先，a就是谁的值，比如A用户改了先吧，B用户就会改不了，因为B用户操作之前就会先认证a这个键的值是不是200，因为200改成300的用户先操作，所以a的键变成了300，所以验证失败就改不了。这个就是乐观锁，只有真正去操作的时候才会去验证

但是同样的问题，遇上悲观锁，那就是不一样了，悲观锁，非常悲观，认为是时时刻刻都有人去跟他抢夺资源；

所以A用户将200改成300的那一个瞬间，就会上锁，改成功后才会释放锁上锁了就不会访问，任何操作都不可以

---

---



## 使用Java与Redis交互

既然了解了如何通过命令窗口操作Redis数据库，那么我们如何使用Java来操作呢？

这里我们需要使用到Jedis框架，它能够实现Java与Redis数据库的交互，依赖：

```xml
<dependencies>
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>4.0.0</version>
    </dependency>
</dependencies>
```

### 基本操作

我们来看看如何连接Redis数据库，非常简单，只需要创建一个对象即可：

```java
public static void main(String[] args) {
    //创建Jedis对象
    Jedis jedis = new Jedis("localhost", 6379);
  	
  	//使用之后关闭连接
  	jedis.close();
}
```

通过Jedis对象，我们就可以直接调用命令的同名方法来执行Redis命令了，比如：

```java
public static void main(String[] args) {
    //直接使用try-with-resouse，省去close
    try(Jedis jedis = new Jedis("192.168.10.3", 6379)){
        jedis.set("test", "lbwnb");   //等同于 set test lbwnb 命令
        System.out.println(jedis.get("test"));  //等同于 get test 命令
    }
}
```

Hash类型的数据也是这样：

```java
public static void main(String[] args) {
    try(Jedis jedis = new Jedis("192.168.10.3", 6379)){
        jedis.hset("hhh", "name", "sxc");   //等同于 hset hhh name sxc
        jedis.hset("hhh", "sex", "19");    //等同于 hset hhh age 19
        jedis.hgetAll("hhh").forEach((k, v) -> System.out.println(k+": "+v));
    }
}
```

我们接着来看看列表操作：

```java
public static void main(String[] args) {
    try(Jedis jedis = new Jedis("192.168.10.3", 6379)){
        jedis.lpush("mylist", "111", "222", "333");  //等同于 lpush mylist 111 222 333 命令
        jedis.lrange("mylist", 0, -1)
                .forEach(System.out::println);    //等同于 lrange mylist 0 -1
    }
}
```

实际上我们只需要按照对应的操作去调用同名方法即可，所有的类型封装Jedis已经帮助我们完成了。

普通Maven项目：先jbdc那样连接，导依赖，创建对象就可以了这不重要，重要是下面的springboot整合

---

## springboot使用radis

#### 导依赖：

在选项中选择：也可以自己导入依赖，注意是springboot的依赖，而不是之前普通maven的依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



#### 在配置文件中写配置信息，像mysql那样：

```yaml
spring:
  redis:
  	#Redis服务器地址
    host: 192.168.10.3
    #端口
    port: 6379
    #使用几号数据库
    database: 0
```

在配置文件中写相关的radis的地址信息

![image-20230510132121933](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305101321007.png)



#### 使用：

```java

    @Autowired
    StringRedisTemplate template;

    @Test
    void contextLoads() {
        ValueOperations<String, String> operations = template.opsForValue();
        operations.set("c", "xxxxx");   //设置值
        System.out.println(operations.get("c"));   //获取值
      	
        template.delete("c");    //删除键
        System.out.println(template.hasKey("c"));   //判断是否包含键
    }
```

==实际上所有的值的操作都被封装到了`ValueOperations`对象中，而普通的键操作直接通过模板对象就可以使用了，大致使用方式其实和Jedis一致。==

### autoWrite注入bean的类的底层：不用管，这个是源码级别



```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnClass({RedisOperations.class})
@EnableConfigurationProperties({RedisProperties.class})
@Import({LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class})
public class RedisAutoConfiguration {
    public RedisAutoConfiguration() {
    }

    @Bean
    @ConditionalOnMissingBean(
        name = {"redisTemplate"}
    )
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<Object, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

    @Bean
    @ConditionalOnMissingBean
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
        return new StringRedisTemplate(redisConnectionFactory);
    }
}
```

那么如何去使用这两个模板类呢？我们可以直接注入`StringRedisTemplate`来使用模板：

----

### springboot开启事务操作：

我们接着来看看事务操作，由于Spring没有专门的Redis事务管理器，所以只能借用JDBC提供的，只不过无所谓，正常情况下反正我们也要用到这玩意：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

```java
@Service
public class RedisService {

    @Resource
    StringRedisTemplate template;

    @PostConstruct
    public void init(){
        template.setEnableTransactionSupport(true);   //需要开启事务
    }

    @Transactional    //需要添加此注解
    public void test(){
        template.multi();
        template.opsForValue().set("d", "xxxxx");
        template.exec();
    }
}
```

我们还可以为RedisTemplate对象配置一个Serializer来实现对象的JSON存储：

```java
@Test
void contextLoad2() {
    //注意Student需要实现序列化接口才能存入Redis
    template.opsForValue().set("student", new Student());
    System.out.println(template.opsForValue().get("student"));
}
```

---

----

```
@Autowired
    StringRedisTemplate template;
    @Test
    public void test15(){
        ValueOperations<String, String> operations = template.opsForValue();//这个operations是对opsForevalue进行封装，也就是说其实 ValueOperations是对opsForValue()的方法进行封装，opsForValue()里面还有方法的
        operations.set("c", "xxxxx");   //设置值
        System.*out*.println(operations.get("c"));   //获取值
        System.*out*.println(operations.get("c"));
//        template.delete("c");    //删除键
        System.*out*.println(template.hasKey("c"));   //判断是否包含键
    }
```

