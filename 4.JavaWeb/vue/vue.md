[vue视频](![img](file:///C:\Users\admin\AppData\Roaming\Tencent\QQTempSys\8LDO48C$8@[GWU0353$FOVS.png)https://www.bilibili.com/video/BV125411W73W)

[Jquery文档](https://www.jquery123.com/)

[较好的VUE视频](https://www.bilibili.com/video/BV1VE411877C?p=25&t=3)

## 简介

| 前端       | 后端       |
| ---------- | ---------- |
| javascript | java       |
| npm        | maven      |
| webpack    | gradle     |
| vue        | springboot |

* 打开IDEA-->settings-->plugins-->安装vue插件

## 理解MVVM

* 编写第一个vue程序
* 理解ViewModel
* 从页面控制台可以更改view层信息

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>vue程序</title>

</head>
<body>
<!--view层，模板-->
<div id="app">
    {{message}}
</div>
<!--这里个人理解通常为视图层，在vue中变成viewmodel层，视图数据模型和展现模型
双向绑定就是：前端数据改变，data中数据会改变
同理data数据中发生改变，前端数据也会改变
而且这个过程是不需要刷新的，虚拟dom，理解重要的MVVM模式
核心：DOM监听和数据绑定-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    let vue = new Vue({
        el:"#app",
        //model：模板
        data:{
            message:"hello,vue"
        }
    });
</script>
</body>
</html>
<!--
console输入
vue.message="hello"
改变视图
"hello"
-->
```

* 理解一下v:bind

## 条件

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>vue程序</title>

</head>
<body>
<div id="app">
    <!--    if-else-->
    <h1 v-if="ok">True</h1>
    <h1 v-else>False</h1>
    <!--   else if 三个===表示全等，值和类型都相等-->
    <h1 v-if="type==='A'">A</h1>
    <h1 v-else-if="type==='B'">b</h1>
    <h1 v-else>C</h1>
<!--    for循环-->
    <li v-for="item in items">
        {{item.message}}
    </li>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    new Vue({
        el:"#app",
        //model：模板
        data:{
            ok:false,
            type: 'A',
            items: [
                {message: "shaobing"},
                {message: "luozheng"}
            ]
        }
    });
</script>
</body>
</html>
```

### v-for

```vue
<template>
  <div id="app">
    <Users></Users>
    <ul>
      //如果不写v-bind:key 则会爆红
      <li v-for="(user,index) in users" :key="index">{{user}}</li>
    </ul>
<!--    <users></users>-->
  </div>
</template>
<script>
  import Users from "./components/Users";
  export default {
    name: "app",
    components:{Users},
    data() {
      return{
        users: ["gou","zhu","niu"]
      }
    }
  }
</script>
<style>
</style>
```

## 事件

### v-on|v-bind

> 事件绑定和html中绑定data

```vue
<!DOCTYPE html>
<html lang="en" xmlns:v-on="http://www.w3.org/1999/xhtml" xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Demo</title>
</head>
<body>
<div id="app">
    <button v-on:click="sayHi">点击</button>
<!--    由于此处不能使用{{ys1}}，因此需要使用v-bind:  可以简写成：color-->
    <font size="5" v-bind:color="ys1">shaobing</font>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    new Vue({
        el: "#app",
        data: {
            message: "shoabing",
            ys1: "red"
        },
        methods: {
            sayHi: function () {
                //this代表vue这个对象
                alert(this.message)
            }
        }
    });
</script>
</body>
</html>
```

### v-model

> 理解双向绑定

```vue
<!DOCTYPE html>
<html lang="en" xmlns:v-on="http://www.w3.org/1999/xhtml" xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Demo</title>
</head>
<body>
<!--体会双向绑定的精髓，首先model有数据，view绑定
然后view更改，model也会进行更改-->
<div id="app">
    输入文本：<input type="text" v-model="message" >{{message}}
    输入文本：<textarea v-model="message" ></textarea>{{message}}
    <br>
    <input type="radio" name="sex" value="男" v-model="sex" checked> 男
    <input type="radio" name="sex" value="女" v-model="sex"> 女
   <p>{{sex}}</p>
    <br>
    <select v-model="sex">
        <option value="" disabled>请选择</option>
        <option>a</option>
        <option>b</option>
        <option>c</option>
        <option>d</option>
    </select>
    {{sex}}
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    new Vue({
        el: "#app",
        data: {
            sex: 'c',   //声明初始值
            message: "123"
        }
    });
</script>
</body>
</html>
```

注意点：v-model会忽略表单元素的value、checked、selected特性初始值，将vue实例作为数据来源

## 组件

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Demo3</title>
</head>
<div id="app">
<!--    组件是view，items是model，view和model之间关联需要viewmodel，需要v-bind绑定-->
    <shaobing v-for="item in items" v-bind:item="item"></shaobing>
</div>
<body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
<!--    注册一个组件-->
    Vue.component("shaobing",{
        //这是一个组件，要想获取item中的值，没法直接获取，但是可以通过props绑定得到
        //可以理解为两个函数，需要一个函数传参过去，通过props接收
        props: ["item"],
        template: '<li>{{item}}</li>'
    });
    new Vue({
        el: "#app",
        data: {
            items: ["java","linux","qianduan"]
        }
    });
</script>
</body>
</html>
```

## 自定义事件内容分发

如果组件中定义一个删除事件，要删除vue实例中对象内容

但是vue组件不能从vue实例中直接删除元素

因此只能通过前端绑定一个事件

前端绑定vue实例中方法`remove="removeItems(index)`

组件绑定前端`this.$emit('remove',index)`

vue实例和前端相关联

父传子用props，子传父$emit

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Demo2</title>
</head>
<!--如何去删除一个元素，元素是通过子组件显示，但是items在父组件中，父子组件均定义一个删除方法，父子组件如何绑定同一个事件-->
<div id="app">
    <todo>
        <todo-title slot="todo-title" :title="title"></todo-title>
        <todo-items slot="todo-items" :item="item" v-for="(item,index) in items" :index="index"
        v-on:remove="removeItems(index)"></todo-items>
    </todo>
</div>

<body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
    Vue.component("todo",{
        props: [''],
        //如果不加slot则不会显示content中内容，
        template: '<div><slot name="todo-title"></slot><ul><slot name="todo-items"></slot></ul></div>'
    });
    Vue.component("todo-title",{
        props: ['title'],
        template: '<div>{{title}}</div>'
    });
    Vue.component("todo-items",{
        props: ['item','index'],
        //只能绑定当前组件方法
        template: '<li>{{index}}---{{item}}<button @click="remove">删除</button></li>',
        methods: {
            remove: function (index) {
                this.$emit('remove',index)
            }
        }
    });
    new Vue({
        el: "#app",
        data: {
            items: ["java","linux","python"],
            title: "shaobing"
        },
        methods: {
            removeItems: function (index) {
                //删除一个元素,如果(index,2)则是删除两个元素
                this.items.splice(index,1);
            }
        }
    });
</script>
</body>
</html>
```