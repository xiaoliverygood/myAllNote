# JWT

## 概念性的东西（JWt是JSon+Web+Token的缩写）：

- jwt信息由三部分组成，首先是head，然后是payload，最后是对前面的两个信息进行加密signature ；

- 最后得到的是一串长长的字符串，字符串分为三段，他们之间的联系是用.进行连接的

---

## 如何使用JWT

- ### 1·导入相关的依赖

```xml
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>3.19.2</version>
        </dependency>
```

----

jdk1.8以上：

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.2</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
```





为什么JwtBuider 接口能直接Jwts.buider,是因为Jwts实现了这个接口的方法

### GPT回答：

在jjwt库中，`JwtBuilder`接口的方法实际上是由`DefaultJwtBuilder`类实现的。

`DefaultJwtBuilder`类是`io.jsonwebtoken.impl.DefaultJwtBuilder`的完整类名。它实现了`JwtBuilder`接口，并提供了设置JWT各个部分的方法，例如`setSubject()`、`setExpiration()`、`claim()`等。

也就是说：`Jwts`类是jjwt库提供的一个包装类，它提供了用于创建、解析和验证JWT的静态方法。

`JwtBuilder`接口提供了各种方法来设置JWT的各个部分，例如设置主题（subject）、过期时间（expiration）、签发时间（issued at）、添加自定义声明（claims）等。

在使用`JwtBuilder`创建JWT时，您可以按需使用这些方法来设置JWT的各个部分。例如：

```java
JwtBuilder jwtBuilder = Jwts.builder()
    .setSubject("user123")
    .setExpiration(new Date(System.currentTimeMillis() + 3600000))
    .claim("name", "John Doe")
    .claim("role", "admin");
```

上述示例代码创建了一个JWT，并设置了主题为"user123"，过期时间为当前时间加上1小时，还添加了两个自定义声明，一个是"name"，值为"John Doe"，另一个是"role"，值为"admin"。

完成设置后，您可以通过调用`jwtBuilder.compact()`来生成最终的JWT字符串：

```java
String jwt = jwtBuilder.compact();
```

生成的JWT字符串可以用于身份验证和授权，您可以将其发送给客户端，并在后续的请求中使用该JWT进行身份验证。

### 案例分析使用：

#### （加密）给前端加密后的Token

```Java
 @Test
    void contextLoads() {
                JwtBuilder jwtBuilder= Jwts.builder();//创建一个jwt对象
        // 创建安全的HS256密钥
        SecretKey key = Keys.secretKeyFor(SignatureAlgorithm.HS256);


        String jwtToken = jwtBuilder
                .setHeaderParam("type", "application")//设置头部信息
                .claim("userName","tom")//自定义的信息
                .claim("role", "admin") //这两句是设置token的载荷信息
//                .setExpiration(new Date()+Date)//这个是过期时间
                .setId(UUID.randomUUID().toString())//设置这个对象的id
                //设置加密信息
            .signWith(key)//设置签名算法,设置HS256算法进行加密\
            //输出加密后的一个字符串，可以理解为token，其实简单理解就是之前的UUId生成的token信息，然后放在redis上，每次都是需要通过token信息进行获取
        //相对于的UserId或者能找到用户的关键字段，但是现在就是对这些全部信息进行封装组成，直接能拿到一个对象，就是这个JWT的字符串可以解析出对象出来，
        //以前那个字符串（UUID）生成的token值可以是对应回UserId，然后需要UserId去查找回用户对象出来
                .compact();
        System.out.println(jwtToken);
    }

```

![image-20230527150305020](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305271503154.png)

----



### 解密

：可以将之前加密的信息进行获取

![image-20230527151352272](https://gitee.com/ljzcomeon/typora-photo/raw/master/202305271513414.png)

---

## 总结：

在我学习之前，我以为这个是对redis的封装，但是学习之后，发现它和redis并不相同：

### JWT：

这个是负责对信息的加密和解密，解密时，无论信息是否存在，他都是可以解密的，因为这个说白了就是给了一个数据，数据进行算法加密，然后保存下来的数据。所以它的功能主要是保证信息的安全，而不是做登陆验证

好比如，我有一段明文，进行加密后变成暗文，我把名明文给删除了，但是暗文依然可以解密，所以这个不太适合做登录状态和登录认证



### redis：

这个是以键值对的形式保存在内存中的，可以说是键值对数据库，所以这个是非常适合做持久化数据验证那样的业务的，因为它是可以删除的，可以变化的。而不是像JWT那样，该改变了信息源，还可以进行暗文转明文

### GPT回答：

JWT（JSON Web Token）和Redis是两个不同的概念和技术，拥有不同的功能和用途。

JWT是一种用于在各个系统之间传递认证和授权信息的令牌机制。它使用JSON格式存储数据，并通过签名或加密来确保数据的完整性和安全性。JWT通常用于在无状态的分布式系统中进行身份验证，允许客户端在每个请求中携带令牌，而无需在服务端存储会话信息。JWT具有自包含性，即令牌中包含了相关的用户身份信息，服务端可以通过解析令牌来验证用户的身份和权限。

Redis是一个开源的内存数据存储系统，它支持不同类型的数据结构（如字符串、哈希、列表、集合等），并提供持久化功能。Redis可以用作数据库、缓存、消息中间件等多种用途。在身份验证和会话管理方面，Redis常被用作存储会话信息的后端，用于存储用户的登录状态、会话数据、权限信息等。

因此，JWT和Redis是两个独立的技术，拥有不同的功能和应用场景。JWT主要用于身份验证和授权，通过令牌在客户端和服务端之间传递信息；而Redis主要用于数据存储和缓存，可以在分布式系统中存储会话信息等。在某些场景下，您可以同时使用JWT和Redis来实现身份验证和会话管理的完整解决方案。



除了token验证，还可以session进行验证，因为登录成功后，可以设置session，这样下次访问拿个session进行比对就好了