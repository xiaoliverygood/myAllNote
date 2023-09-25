# mongodb

## 安装步骤：

- ### 介绍：

- 非关系型数据库，跟redis很类似，只是redis是键值对的形式存在的，而mongodb是文档形式存在的

  ### 和mysql区别：

  - 首先是不需要预先设置好字段，第二个就是没有表这个概念，mysql中的表类似于集合

  

  特点：
  
  - 满足对数据库的高并发读写
  - 对海量的数据的高效存储和访问
  - 对数据库的高拓展性
  - 灵活的数据结构
  - 缺点：
  - 不支持事务
  - 实现复杂的sql查询比较复杂
  
  ---
  
  

![image-20230918110632275](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181106436.png)

mysql的一行条记录，在mongodb叫一个文档

mongodb不需要主键，自动生成主键（默认主键）（也可以自定义id，只需要在字段给”_id“给这个键加个值就行），不加就是官方自己配的id，但是自己加也不能加相同的，也就是主键唯一

## 操作数据库：

- ```
  use 数据库名称//使用某个数据库，没有的话会自动创建（跟mysql的区别）
  
  db.dropDatabase()//删除当前的数据库
  show tables或者show collections//可以看见当前有哪些集合
  db。集合。drop（）是删除集合
  db.createCollection("创建的集合名称")；
  ```

  

```
db.集合名称.inser({JSon格式的对象});//单个json插入
db.集合名称.inser([{},{}])//多个json对象插入
db.集合名称.updateOne({name:"xiaoli"},{$set:{age:17}})//将name含有xiaoli的年龄赋值17

```

## 查询：

![image-20230918133601613](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181336661.png)

如果要返回什么字段，需要在projection加上，并且指定数字（0是不会展示，1是会展示），id（主键默认为1，就是展示）

如果是条件和projection都没有传入的话，就会全部数据展示出来

### 错误案例：

![image-20230918134134617](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181341644.png)

也就是排除和显示要一致在投影上，除了_id

### 排序：

![image-20230918155500120](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181555149.png)

### 条件选择器：

![image-20230918161015819](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181610861.png)

![image-20230918161213459](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181612482.png)

![image-20230918161559398](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181615446.png)

![image-20230918161955090](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181619116.png)

等价于:

```sql
select * from 表名 where age<28 Or age>30
```

//这个和sql不一样，sql的或者，并且要在中间连接词，但是这个是提前的，$表示操作符

如果是值有多个，可以使用数组形式，数组里面是很多个{}，这样子就能实现例如需要多个或者的值

![image-20230918163133351](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181631377.png)

find第一个json是条件选择，第二个是显示的属性，如果条件选择是单个东西，就只需要一个{}，如果是多个条件就需要连接词语，将连接词语抽出来，再用数组形式

---

### 文档设计：

之前mysql是使用外键或者联系表进行的，但是再mongodb使用嵌套关系就可以了



---

## 基本curd已经在springboot中使用：

导入依赖项：

简单的：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb-reactive</artifactId>
</dependency>
```

```xml
spring.data.mongodb.host=localhost
spring.data.mongodb.port=27017
spring.data.mongodb.database=mydatabase

```



```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
    </parent>
 
    <dependencies>
      	
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-mongodb</artifactId>
        </dependency>
      
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
        </dependency>
      
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
 
    <build>
        <plugins>
            <!-- 设置编译版本为1.8 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

然后继承（注意，mapper是mysql的，但是mongodb是使用Repository，要继承一个接口）



编写实体类：注意需要2个注解，一个@id表示主键一个是@Document表示哪一个集合（跟mysql的实体类非常相似）

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.bson.types.ObjectId;
import org.springframework.data.mongodb.core.index.Indexed;
import org.springframework.data.mongodb.core.mapping.Document;
 
@Data
@AllArgsConstructor
@NoArgsConstructor
@Document("user")//这里最好写上  表示操作哪个集合（表）
public class User {
    @Id
    private ObjectId id;
    
 
    private String username;
    private Integer age;
    private String address;
}
```

如果使用像简单的jpa查询或者插入的语句，这个也就是类似于mysql的mapper接口

![image-20230918191526532](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181915582.png)

## 基本查询：

### 方案一：jpa查询：

与mybatis不一样的是不需要写sql语句，但是方法名字是十分要求高的，涉及到高级查询，还需要使用方案二

无需写sql语句，只是要注意书写规范，方法名字的时候

![image-20230918183837757](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181838825.png)

![image-20230918183528833](https://gitee.com/ljzcomeon/typora-photo/raw/master/202309181835903.png)





### 方案二：mongoTemplate

导入mongoTemplate的包

```
.测试 增删改查（API）
1.首先依赖出入   MongoTemplate（MongoDB依赖中的）

2.新增代码（如果数据库中没有集合（表），会自动创建）

//    新增
    @Test
    public void text01(){
        User user = new User(new ObjectId(),"jack",11,"天津");
//        User user1 = mongoTemplate.insert(user);
        User user1 = mongoTemplate.insert(user, "user");//指定字段添加
        System.out.println(user1);
    }
3.删除

@Test
    public void text02(){
        Query query = new Query();//条件对象
        DeleteResult remove = mongoTemplate.remove(query,User.class);//没条件 全删 后边要加上实体类对应的表
        System.out.println(remove);//两条数据
    }
 @Test//根据条件删除
    public void text03(){
        Query query = new Query(Criteria.where("id").is(new ObjectId("6354084f8136ce1f102b9003")));
        DeleteResult remove = mongoTemplate.remove(query, User.class);
        System.out.println(remove);
    }
    @Test//多条件删除
    public void text04(){
        Query query = new Query(Criteria.where("username").is("jack1").and("age").is(12));
        DeleteResult remove = mongoTemplate.remove(query,User.class);
        System.out.println(remove);

}

4.修改

  @Test
    public void text05(){
        Query query = new Query(Criteria.where("age").gt(12));//条件 age > 12
```



        Update update = new Update();
        update.set("username","huahua");
        UpdateResult updateResult = mongoTemplate.updateMulti(query, update, User.class);
        System.out.println(updateResult);
     
    }
```
5.查询

//    查询 根据id 做查询
    @Test
    public void text06(){
        User user = mongoTemplate.findById(new ObjectId("6354086f8136ce237011c305"), User.class);
        System.out.println(user);
//        全查询
        List<User> userList = mongoTemplate.findAll(User.class);
        for (User user1 : userList) {
            System.out.println(user1);
        }
    }
    @Test//条件查询
    public void text07(){
        Query query = new Query(Criteria.where("username").regex("h"));
        List<User> users = mongoTemplate.find(query, User.class);
        System.out.println(users);
    }
```




    @Test //条件查询  or
    public void text08(){
        Query query = new Query(Criteria.where("").orOperator(
                Criteria.where("age").is(14),
                Criteria.where("address").regex("海")
        ));
     
        List<User> users = mongoTemplate.find(query, User.class);
        for (User user : users) {
            System.out.println(user);
        }
    }

 


    @Test//分页查询
    public void text09(){
        Query query = new Query();
        query.skip(1).limit(3);//第一条数据和第二条数据    从第几页开始  展示几条数据
        query.with(Sort.by(Sort.Direction.DESC,"age"));//降序
        List<User> users = mongoTemplate.find(query, User.class);
        for (User user : users) {
            System.out.println(user);
        }
    }
