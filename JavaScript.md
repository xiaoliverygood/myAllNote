# JavaScript

## 快速入门：

### 第一种写法：在标签里面写JavaScript

![image-20230722144906621](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221449680.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>第一个网页</title>
    <script>
        alert("我是弹窗")
    </script>
</head>
<body>
<h1>wodedaoshi</h1>

</body>
</html>
```

![image-20230722145039533](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221450585.png)

### 使用js文件引入：

![image-20230722145353325](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221453402.png)

1. 创建一个js文件
2. 在script文件写src的地址
3. 就能实现引入外部的js文件，只是外部的js文件引入后，内部的标签里面的js是失效的



## 基本语法入门

### 定义变量：

一切的变量类型都是var

其他和c一个样子

JavaScript严格区分大小写

### 一样是可以嵌套if else

### 控制台console.log（）是打印变量或者其他的东西

![image-20230722152630474](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221526583.png)

也就是说前端的终端是浏览器

### 数据类型：

数值：number、字符串、布尔值、逻辑运算、比较运算（==等于，=====绝对等于，需要类型一样例如1和‘1’）

### 数组

Java的数组是必须相同的，但是这里像py那样不一定相同

var arr=【1，2，3，null，‘h’】

去数组下标越界了，就会显示undefind

### 对象：

对象使用大括号，数组是中括号

###### 对象名称={

##### }其实名称后面跟Json格式一样的

```javascript
alert("我是外部js文件引入的")
var arr=[1,2,4,56,7,8,null]
console.log(arr.at(2))
var person={
   name:"xiaoli",
   age:"12"
}
console.log(person)
```

## 严格检查模式：

前提：编译器需要提前设置Es6语法

在写代码前面加多一句话：‘use strict’；

这个叫严格检查模式，预防JavaScript的随意性导致的一些问题

必须写在JavaScript的第一行

局部变量使用let比较好

![image-20230722171403002](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221714052.png)

![image-20230722171411994](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221714043.png)—-

---

![image-20230722182602137](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221826206.png)

![image-20230722182909982](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221829019.png)

![image-20230722193032394](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221930476.png)

![image-20230722193046554](https://gitee.com/ljzcomeon/typora-photo/raw/master/202307221930602.png)

## 对象：

var 对象名称={键值对的Json格式}

### 访问对象的属性：

1. person.lastName;
2. person["lastName"];

### 可以动态添加和删除属性



## 函数：

函数：（类似于c语言，面向过程）

方法：（对象拥有的）

---

```javascript
function 方法名（形参){
//由于JavaScript的格式有个万能的格式let val这些，所以比c语言的方法是少了返回类型
}//这个是第一种方式定义函数
```

NAN是null的意思 

![image-20230803143606431](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031436570.png)—

---

![image-20230803144048214](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031440286.png)

arguments包含所有的形参，是一个数组，我们可以用多余的参数进行操作

![image-20230803144335435](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031443486.png)

---

### 问题：

- 存在同名变量谁先谁后

- - 查找变量，JavaScript由内到外

  

![image-20230803151854071](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031518157.png)

类似于c语言设置变量，定义在最外面就是全局变量，默认的全局变量都是在window对象上  



![image-20230803153807809](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031538878.png)

也就是对象

![image-20230803154035212](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031540286.png)

![image-20230803154206022](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031542095.png)

---

和Java的区别：

1. 对象是不用new的，直接 ：var 对象名称={键值对的Json格式}
2. 后续还可以对象.属性赋值或者拿值
3. 大多数是跟c类似
4. ==面向过程是就是window作为对象，面向对象时，面向谁，对象就是谁==，Java是面向过程时，Main类，需要哪个对象需要new，new完后还需要对象.属性/方法才能使用，面向过程是就是类，类的方法（加上static的），在自己类里面可以随便调用，在外面的类需要加上类名.属性/方法
5. 函数有this关键字是需要对象去调用的，而不是属于面向过程的函数

![image-20230803163412524](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031634565.png)

---

## 内部对象：

### Date：

![image-20230803164539995](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031645106.png)





---

## Json

在JavaScript一切皆为对象，任何支持js的类型都可以转换为json格式

- 对象使用{}

- 数组使用【】

- 所有的键值对都是使用key：value

  ### 转换：

  ![image-20230803165043625](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031650676.png)

对象转换成Json，一个是转换为json，一个是解析json

![image-20230803165136416](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031651457.png)

![image-20230803165154450](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031651488.png)



---

## 面向对象：

像Java JavaScript c#这些面向对象的编程

java：

类：抽象出来的

对象：

---

JavaScript

对象：

对象可以选择原型对象，跟继承差不多，也就是说，a的原型是b，b的属性和方法，a也可以使用，还可以更换属性值

```JavaScript
// 创建一个原型对象 b
const b = {
  name: "Object B",
  sayHello: function() {
    console.log("Hello from " + this.name + "!");
  }
};

// 创建一个对象 a，并将其原型设置为 b
const a = Object.create(b);
a.name = "Object A"; // a对象自己的属性，不会影响原型对象 b 的属性
a.sayHello(); // 继承自原型 b 的方法，输出为 "Hello from Object A!"，因为a对象的name属性已经覆盖了原型的name属性

// 修改原型对象 b 的属性
b.name = "Updated Object B";

// 对象 a 继承自原型 b 的方法，输出为 "Hello from Updated Object B!"，因为a对象的name属性并没有覆盖原型的sayHello方法中的name属性
a.sayHello();

```

![image-20230803180459718](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031804762.png)这个说xiaoming的原型是student，所以xiaoming继承了该原型的方法和属性

### 第二种定义对象的方法：

- 第一种是大括号，然后键值对
- 第二种是定义一个类，然后new（很像Java）

![image-20230803181139247](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031811296.png)

```JavaScript
// 对象字面量
var person = {
  name: "John",
  age: 30,
  sayHello: function() {
    console.log("你好，我叫" + this.name + "，我今年" + this.age + "岁。");
  }
};

```

---



## JavaScript对象的创建有两种方法：

1. 对象字面量（Object Literal）:
   第一种方法是使用对象字面量，通过花括号 `{}` 直接定义对象的属性和方法。

```javascript
// 对象字面量
var person = {
  name: "John",
  age: 30,
  sayHello: function() {
    console.log("你好，我叫" + this.name + "，我今年" + this.age + "岁。");
  }
};
```

2. 构造函数与 `new` 关键字：
   第二种方法涉及创建一个构造函数，并使用 `new` 关键字从构造函数实例化对象。

```javascript
// 构造函数
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.sayHello = function() {
    console.log("你好，我叫" + this.name + "，我今年" + this.age + "岁。");
  };
}

// 使用构造函数创建对象
var person1 = new Person("John", 30);
var person2 = new Person("Alice", 25);
```

这两种方法都是有效的，但它们适用于不同的场景：

- 对象字面量适用于需要创建单个对象，而且对象的属性和方法是已知的情况。
- 构造函数与 `new` 适用于需要创建多个具有相似属性和方法的对象，并希望它们共享方法的情况。

在现代JavaScript中，还有另一种创建对象的方法，称为“类”（Class），它实质上是构造函数的语法糖。类提供了一种更结构化和更易读的方式来定义对象及其行为。上面的示例可以使用类来重写，代码如下：

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  sayHello() {
    console.log("你好，我叫" + this.name + "，我今年" + this.age + "岁。");
  }
}

// 使用类创建对象
var person1 = new Person("John", 30);
var person2 = new Person("Alice", 25);
```

类的语法更加易读和理解，但在底层仍然使用了构造函数和原型继承的机制。 

![image-20230803181854044](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031818111.png)

底层一样是原型使用，也就是![image-20230803182000124](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308031820155.png)，也就是原型链

### 原型链：



## 操作BOM对象

### location：

location代表当前的页面的URL信息

![image-20230803200210804](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308032002868.png)

![image-20230803200227008](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308032002096.png)

## 操作DOM

dom：文档对象，也就是html和js连接的桥梁

![image-20230803225027550](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308032250666.png)

---

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>第一个网页</title>
    <!--    //这里是外部引入js文件-->
    <script >
        'use strict';
        alert("我是外部js文件引入的")
        var arr=[1,2,4,56,7,8,null]
        console.log(arr.at(3))

        var person={
            name:"xiaoli",
            age:"12"
        }
        //拿html的东西，对应的东西，比如这个是拿id为h1的html标签的东西
        var h1 = document.getElementById('h1');

    </script>

</head>
<body>
<h1 id="h1">xiaohong</h1>
<p>
    <strong>xiaoxuan</strong>
</p>
</body>
</html>
```

### 更新节点：

![image-20230803233316498](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308032333568.png)

选择innerText=“xxx”，这样子就能改变标题了

主义的是div1是JavaScript里面的![image-20230803233403565](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308032334606.png)

操作的确实是一个变量，但在这里，这个变量存储的是一个HTML元素对象的引用。当您使用`document.getElementById("div1")`获取一个HTML元素后，您将其存储在变量`div1`中。这个变量`div1`代表了该HTML元素对象，而不仅仅是一串HTML代码或文本。

HTML元素对象是由浏览器提供的接口，它允许您通过JavaScript来操纵和控制网页中的元素。通过这些对象，您可以访问和修改元素的属性、内容、样式和行为。

例如，当您输入`div1.innerText = "新的文本内容"`时，实际上是在通过JavaScript代码告诉浏览器更新`div1`所代表的HTML元素对象的`innerText`属性，从而更改该元素的显示文本。

JavaScript和浏览器的协作使得您可以在网页上进行动态的、实时的交互。您可以通过JavaScript操作HTML元素对象来实现更丰富的用户体验和交互式功能。这种能力使得JavaScript成为了前端开发中不可或缺的一部分。

您说得对。DOM（文档对象模型）是一种用于HTML文档的编程接口，它允许JavaScript通过操作HTML元素对象来更改网页内容和结构。在DOM中，JavaScript可以访问和修改HTML元素、属性、样式以及处理事件等，但它并不能直接修改后端数据库或其他服务器端数据。

DOM是运行在浏览器环境中的一种客户端技术，它只影响在浏览器中渲染的页面，并不能直接与后端数据库进行交互。要修改后端数据库，您需要使用服务器端编程语言（如Java、Python、PHP等）来处理数据库操作，并通过网络传输数据与前端进行交互。

在您提到的Java中，`String id = 数据库拿值`是用于在Java程序中声明一个字符串变量，并将数据库中的某个值赋值给它。这只是Java程序中的一种变量操作，并不会直接影响数据库的内容。要更改数据库的内容，您需要使用Java中的数据库连接技术，如JDBC（Java Database Connectivity），来执行INSERT、UPDATE或DELETE等数据库操作。

总结起来，DOM是用于在浏览器环境中操作前端HTML元素的技术，而后端数据库的操作需要使用服务器端编程语言和数据库连接技术来实现。前后端的数据交互需要通过网络通信来实现。

### 总结：

dom就是操控JavaScript和html的联系，实现动态网页，之所以叫节点是因为Html的标签像树一样，一层套一层的

---



### 删除节点：

### 方式一：

删除节点的步骤：

1. 找出父节点
2. 通过父节点移除子节点（里面有remove方法）

删除了就看不到该结点（也就是等价于HTML标签没了）

![image-20230804103431861](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041034908.png)

### 方式二：

例如数组，但是注意，数组的下标是随节点的插入和删除而改变的

![image-20230804103355147](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041033210.png)

---

### 插入节点：

我们获得了某个Dom节点，假设这个dom节点是空的，我们通过 innerHTML 就可以增加一个元素了，但是这个DOM 节点已经存在元素了，我们就不能这么干了! 会产生覆盖

```JavaScript
<p id="js">Javascript</p><div id="Tist">
<p id="se">JavaSE</p>
<p id="ee">JavaEE</p>
<p id="me">JavaME</p></div>
<script>
var js = document.getElementById('js');
var Tist = document.getElementById('list');
list.appendchild(js);// 追加到后面
</script>
```

使用dom可以追加元素，追加到后面

> appendChild

插入新的DOM节点

> insertBefore

将节点插入指定位置

### Dom创建一个新的标签

```javascript
<script>
var js = document.getElementById('js'); //已经存在的节点var 1ist = document.getElementById('1ist');//通过JS 创建一个新的节点
var newp = document.createElement('p');// 创建一个p标签newP.id = 'newp';
newP.innerText = 'He11o,Kuangshen';
</script>
```

效果等同于在HTML里面创建一个p的标签，id为此id，内容为此内容

## 操作表单：

### 表单是什么：

- 文本框 text

- 下拉框 select标签

- 单选框 redio

- 多选框 checkbox

- 密码框 password

- …

  

### 表单目的：

提交信息

![image-20230804132853482](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041328545.png)

使用dom可以获得输入框的值，已经输入的

---

![image-20230804133216921](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041332013.png)

### 提交表单

使用input标签的submit或者button中的submit类型



## Jquery：

```javascript
<!DOCTYPE htm1><htm1 1ang="en">
<head>
<meta charset="UTF-8">
<title>Title</title>
<!--<script src=""https://cdn .bootcss.com/jquery/3.4.1/core.js"></script>-->
<script src="1ib/jquery-3.4.1.js"></script></head>
<body>
点我
<script>
//选择器就是dss的选贼器
    $('#test-jquery').click(function () {alert('he11o,jquery');
3
</script>
</body></htm1>
```

![image-20230804160200946](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041602006.png)

$表示jquery

(selector)表示选择器，

#号代表输入,记得使用单引号括起来包含#号的内容（—id选择器）

![image-20230804160755578](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041607634.png)

![image-20230804160807671](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041608713.png)

mysql是#{这里表示输入参数}动态sql

.后面表示的是动作，可以是函数之类的

### 事件：

鼠标事件，键盘事件，其他事件

![image-20230804162503047](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041625109.png)

### 操作Dom

![image-20230804163356585](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041633648.png)

ul是li的父节点

![image-20230804163457785](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308041634837.png)

----

