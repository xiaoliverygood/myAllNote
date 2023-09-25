# VUE

vue关注得是视图层，遵循SOC原则：

Soc：

HTML+CSS+JS：视图：给用户去看的

网络通讯：Axios

页面跳转：vue -route

状态管理：vuex

vue-UI：组件和框架 

webpack:类似于java得maven那样，打包工具



### css预处理器：样式

使用less这一个语言可以方便改动css，因为如果css写死，改动非常麻烦

----

![image-20230821160413876](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308211604997.png)

![image-20230821164436039](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308211644086.png)

## 基本语法：

### v-bind：

绑定属性，类似于操控dom

![image-20230821164631860](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308211646916.png)

##### 判断使用v-if/v-else/v-else

![image-20230821164930166](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308211649221.png)

![image-20230821164936823](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308211649863.png)

使用v-for指令可以实现for循环，写法类似于增强型for循环，Java的增强型for循环是将in变成：

```java
public class EnhancedForLoopExample {
    public static void main(String[] args) {
        int[] numbers = { 1, 2, 3, 4, 5 };

        // 使用增强型for循环遍历数组
        for (int number : numbers) {
            System.out.println(number);
        }
    }
}
```

###  vue简单入门：

```html
<!DOCTYPE html>
<html>
<head>
    <title>Vue.js Example</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>
<body>
    <div id="app">
        <p>{{ message }}</p>
        <button @click="changeMessage">Change Message</button>
    </div>

    <script>
        new Vue({
            el: '#app',
            data: {
                message: 'Hello, Vue!'
            },
            methods: {
                changeMessage() {
                    this.message = 'New message from Vue!';
                }
            }
        });
    </script>
</body>
</html>

```

让我解释一下每个部分的作用：

1. `el: '#app'`：这是 Vue 对象的一个属性，它表示 Vue 将会控制哪个 DOM 元素。在这个例子中，Vue 将会控制具有 `id` 为 `'app'` 的 DOM 元素。

2. `data: { message: 'Hello, Vue!' }`：这也是 Vue 对象的一个属性，用于定义数据。在这里，我们定义了一个名为 `message` 的数据属性，并将其初始值设置为 `'Hello, Vue!'`。这些数据属性在应用中可以被访问和绑定到界面元素上。

3. `methods: { changeMessage() { this.message = 'New message from Vue!'; } }`：这是 Vue 对象的另一个属性，用于定义方法。在这里，我们定义了一个名为 `changeMessage` 的方法，当按钮被点击时会调用这个方法。在方法内部，我们通过 `this.message` 来改变数据属性 `message` 的值，从而触发界面的更新。

这些属性和方法都是在创建 Vue 对象时进行配置的，它们使得你能够将数据、逻辑和界面绑定在一起，从而创建出交互性更强、易于维护的前端应用程序。

这些都是vm对象的，哪怕data，data代表是vm这个对象的数据，methods代表是这个vm对象的方法

### 数据绑定的体现：

举个例子，假设你有一个 Vue 实例，其中有一个数据属性 `message`：

```
javascriptCopy codenew Vue({
    data: {
        message: 'Hello, Vue!'
    }
});
```

你可以将这个数据属性绑定到界面上：

```
htmlCopy code
<p>{{ message }}</p>
```

当后端数据发生变化时，如果你在 Vue 实例的数据属性中更新了 `message`，相关的界面元素也会自动更新。

然而，在 jQuery 中，数据和界面之间的同步不是自动完成的。如果后端数据变化了，你需要手动找到对应的 DOM 元素，并更新其内容。这可能需要更多的代码来处理。

Vue.js 的数据绑定机制使得前端开发更加便捷和高效，特别是在处理复杂的交互式界面时。而 jQuery 更偏向于提供一些基本的 DOM 操作方法，对于数据绑定需要更多的手动操作。****



 jQuery需要手动操作dom才能实现更新数据

---

v-on等价于js的onclick，表示点击

```vue
<!DOCTYPE html>
<html>
<head>
    <title>Vue.js Event Example</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>
<body>
    <div id="app">
        <button @click="handleClick">Click me</button>
    </div>

    <script>
        new Vue({
            el: '#app',
            methods: {
                handleClick() {
                    alert('Button clicked!');
                }
            }
        });
    </script>
</body>
</html>

```

在这个示例中，我们使用了 `@click` 指令（`v-on:click` 的缩写）来绑定点击事件处理函数 `handleClick`。当按钮被点击时，将会触发 `handleClick` 方法，弹出一个警告框。这与原生 JavaScript 中的 `onclick` 类似，但在 Vue.js 中，你可以更方便地处理各种类型的事件。

---

## vue的属性：

7个属性，分别是`el`、`data`、`computed`、`template`、`methods`、`render`、`watch`。您提供的解释在很大程度上是正确的，下面我会对每个属性进行简要的解释。

1. **el属性（Element）**：
   `el`属性用于指定Vue实例将要管理的DOM元素。它作为Vue实例的一个选项，表示Vue会将其挂载到特定的DOM元素上，从而控制该DOM元素及其内部的数据和行为。

2. **data属性**：
   `data`属性用于存储Vue实例中的数据。这些数据会与视图进行绑定，当数据发生变化时，视图会自动更新，保持数据与界面同步。

3. **computed属性**：
   `computed`属性用于定义计算属性。计算属性是基于现有数据派生出的属性，它会根据数据的变化自动更新。计算属性通常用于处理一些需要根据其他属性计算而来的数据。

4. **template属性**：
   `template`属性用于定义Vue实例的模板。模板是一段包含了Vue语法的HTML代码，它会被Vue编译器解析为渲染函数，最终渲染成真实的DOM。

5. **methods属性**：
   `methods`属性用于定义在Vue实例中可用的方法。这些方法可以在视图中进行调用，用于处理用户交互、事件响应等业务逻辑。

6. **render属性**：
   `render`属性用于手动编写渲染函数。渲染函数是一个JavaScript函数，用于生成虚拟DOM树，从而构建用户界面。通常在高级场景下使用，例如自定义组件。

7. **watch属性**：
   `watch`属性用于监听数据的变化，并在数据发生变化时执行特定的逻辑。您可以监视某个属性的变化，然后在回调函数中执行需要的操作，例如数据变化时发起异步请求等。

这些属性在Vue.js中确实具有重要的作用，它们帮助开发者更方便地构建响应式的用户界面。如果您还有任何关于Vue.js或其他前端开发方面的问题，请随时提问。

## vue的指令：



1. **v-text**：将元素的`textContent`设置为指定的数据值。例如：`<p v-text="message"></p>`。

2. **v-html**：将元素的`innerHTML`设置为指定的HTML内容，这可以用于渲染富文本。但要注意潜在的安全风险，因为动态插入的HTML可能会导致跨站脚本攻击（XSS）问题。

3. **v-if**：根据表达式的真假条件，决定是否渲染或移除元素。例如：`<p v-if="showMessage">Hello</p>`。

4. **v-else**：与`v-if`搭配使用，表示在条件不满足时渲染的内容。例如：
   ```html
   <p v-if="showMessage">Hello</p>
   <p v-else>Goodbye</p>
   ```

5. **v-show**：根据表达式的真假条件，通过控制元素的`display`属性来显示或隐藏元素，不会真正移除元素。例如：`<p v-show="isVisible">Visible</p>`。

6. **v-for**：循环遍历数组或对象，并为每个元素渲染对应的内容。例如：
   ```html
   <ul>
     <li v-for="item in items">{{ item }}</li>
   </ul>
   ```

7. **v-on**：用于绑定监听事件，可以用来响应用户的交互操作，比如点击、输入等。可以使用简写方式`:eventName`。例如：`<button v-on:click="doSomething">Click Me</button>`。

8. **v-bind**：用于绑定一个或多个属性，可以根据表达式的值动态设置元素属性。也可以使用简写方式`:`。例如：`<a v-bind:href="url">Link</a>`。也就是标签的属性与vue对象的数据进行绑定如 a标签的链接与vue对象的url绑定

9. **v-model**：实现表单元素与数据的双向绑定。当表单元素的值改变时，关联的数据也会相应更新。例如：`<input v-model="message">`。

10. **v-cloak**：这个指令用于防止Vue模板在加载之前出现在未编译状态下闪烁的情况。在CSS中与`[v-cloak]`选择器一起使用，可以确保模板内容正常显示。

这些内置指令使得在Vue模板中操作DOM和实现交互变得更加便捷和灵活。如果您对任何指令或其他Vue.js相关的内容有进一步的疑问，请随时问我。

## vue组件–自定义标签

![image-20230821213135784](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308212131869.png)

组件为了复用，可以拼接组成

![image-20230821213701162](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308212137219.png)

==例如我定义了一个xiali的标签，template代表的我前面xiali标签代表的内容，所以xiali这个标签代表的是<h1>heklo</h1>==



![image-20230821213943205](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308212139388.png)

所以vue文件template这个就是组件模板的意思

![image-20230821214536322](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308212145395.png)

```vue
<!DOCTYPE html>
<html>
<head>
    <title>Vue.js Example</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>
<body>
<div id="app">
<!--    <p></p>-->
    <li v-for="its in words"  >
<!--        v-bind:key="its"-->
        {{its}}
    </li>
<!--   <button @click="changeMessage">Change Message</button>-->
<!--    <xiali></xiali>-->
</div>
<script>
    //第一个vue组件component
    // Vue.component(
    //     "xiali",{
    //         template: "<h1>heklo</h1>"
    //
    // }
    // )
    new Vue({
        el: '#app',
        data: {
            message: 'Hello, Vue!',
            words: ["xiaoxuan","xiaohiwji","xiaoli"]
        },
        methods: {
            changeMessage() {
                this.message = 'New message from Vue!';
            }
        }
    });
</script>
</body>
</html>

```

![image-20230821215234594](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308212152623.png)

v-bind是指标签的属性绑定：

```vue
<!--        v-bind:key="its"-->//这里是指标签的key属性的值是its，在这里不加不影响
```

---

````
当你在父组件中使用子组件时，你需要在父组件的模板中将子组件放置到适当的位置。你的代码中的模板如下：

```html
<div id="app">
    <ul>
        <xiali v-for="item in items" :are="item" :key="item"></xiali>
    </ul>
</div>
```

我会结合你之前提供的案例来解释这段代码。

假设你有一个 Vue 应用，其中包含一个父组件，你想要在父组件的模板中使用名为 "xiali" 的子组件来渲染一个列表。这个列表是从父组件的数据中动态生成的。

1. `<div id="app">`：这是整个 Vue 应用的挂载点。这个元素是你的 Vue 实例将会控制的部分。你可以通过 id 选择器 "#app" 来找到这个元素。

2. `<ul>`：这是一个无序列表元素，你将在这个列表中渲染子组件。

3. `<xiali v-for="item in items" :are="item" :key="item"></xiali>`：
   - `xiali`：这是你之前定义的子组件。
   - `v-for="item in items"`：这是 Vue 的循环指令，它遍历父组件中的 `items` 数组，对数组中的每个元素执行一次循环。对于每个循环，它会在模板中创建一个子组件实例。
   - `:are="item"`：这是一个绑定到子组件 "xiali" 的 "are" 属性上的属性绑定。它将循环变量 `item` 的值传递给子组件的 "are" 属性，从而将每个数据项的内容传递给子组件。
   - `:key="item"`：这是为了给循环的每个子组件提供一个唯一的标识符。这在 Vue 的循环渲染优化中是很重要的，确保每个子组件都有一个唯一的标识。

综合起来，这段代码的作用是在父组件的模板中循环遍历父组件的 `items` 数组，为数组中的每个元素创建一个子组件 `<xiali>`，并通过 "are" 属性将每个数据项的值传递给子组件，从而生成一个列表。每个子组件渲染为一个列表项，列表项的内容是从父组件的数据中动态获取的。
````





```javascript
// 子组件定义
Vue.component(
    "xiali",{
        props:["are"],
        template: "<li>{{are}}</li>"
    }
);

// 父组件中使用子组件
new Vue({
    el: "#app", // 这里是父组件的挂载点
    data: {
        items: ["Item 1", "Item 2", "Item 3"]
    }
});

```

在子组件中，`props:["are"]` 定义了一个名为 "are" 的属性，这个属性可以从父组件传递给子组件的标签上。当你在父组件中使用子组件的标签（例如 `<xiali>`），通过绑定属性，你可以将数据传递到子组件中。

在你的例子中，`props:["are"]` 表示子组件 "xiali" 可以接收一个名为 "are" 的属性。然后在父组件中，通过使用 `:are="item"` 这样的语法，将父组件的 `items` 数组中的每个数据项传递给子组件的 "are" 属性。这样子组件就能够根据传递进来的数据动态渲染内容。

所以，`props` 允许你在子组件中定义一些可接收的属性，从而实现从父组件向子组件传递数据。这是 Vue 组件之间通信的一种方式。

```vue
Vue.component("xiali", {
    props: ["are"], // 声明属性
    template: "<li>{{ are }}</li>"
});

```

---

```vue
<!DOCTYPE html>
<html>
<head>
    <title>Vue Component Example</title>
</head>
<body>
<div id="app">
    <xiali v-for="its in items" v-bind:are="its"></xiali>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
<script>
Vue.component(
    "xiali",{
        props:["are"],
        template: "<li>{{are}}</li>"
    }
);

new Vue({
    el: "#app",
    data: {
        items: ["Item 1", "Item 2", "Item 3"]
    }
});
</script>
</body>
</html>

```

## 网络通信：Axios或者jQuery（少用，因为操作dom太复杂）





## 计算属性：

![image-20230822090823072](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308220908159.png)

![image-20230822090905243](https://gitee.com/ljzcomeon/typora-photo/raw/master/202308220909325.png)
