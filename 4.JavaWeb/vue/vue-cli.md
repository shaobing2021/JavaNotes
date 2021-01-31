## 安装Vuecli3

```cmd
C:\Windows\system32>node -v
v12.18.1
//软件包管理工具
C:\Windows\system32>npm -v
6.14.5
//安装淘宝镜像
//安装vue-cli
//查看vue-list中模板
C:\Windows\system32>vue-list
  Available official templates:
  ★  browserify - A full-featured Browserify + vueify setup with hot-reload, linting & unit testing.
  ★  browserify-simple - A simple Browserify + vueify setup for quick prototyping.
  ★  pwa - PWA template for vue-cli based on the webpack template
  ★  simple - The simplest possible Vue setup in a single HTML file
  ★  webpack - A full-featured Webpack + vue-loader setup with hot reload, linting, testing & css extraction.
  ★  webpack-simple - A simple Webpack + vue-loader setup for quick prototyping.
```

## vue配置

* vue ui
* http://localhost:8000/project/create 
* 开启router、vuex、babel、关闭Foramatter
* 勾选use history mode
* 创建项目，不保存新预设

![image-20200714185815425](https://gitee.com/shaobing2021/typora/raw/master/img/20200731171439.png)

### 目录说明 

## 组件嵌套

注意组件中只能右

```vue
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>{{ greetting() }}</p>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  // props: {
  //   msg: String
  // }
  // data: {
  //   msg: "shaobing"
  // }
  data(){
    return{
      msg: "shaobing",
      wechat: "222222"
    };
  },
  methods: {
    greetting(){
      //注意这里是飘号
      return `我叫${this.msg},我的微信是${this.wechat}`;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
```

### 全局组件

```vue
//components中编写组件
<template>
    <div class="users">
        users
    </div>
</template>
<script>
    export default {
        name: "Users"
    }
</script>
<style scoped>
</style>

//main.js中注册全局组件
//1.引入组件
import Users from './components/Users'
//2.注册全局组件,这样其他地方就可以使用该组件
Vue.component('users',Users)


//app.vue以及其他地方就可以使用该组件了
<template>
  <div id="app">
    <users></users>
  </div>
</template>
<script>
  export default {
    name: "app"
  }
</script>
<style>
</style>

```

### 局部嵌套

```vue
<template>
  <div class="about">
      <m-user/>
  </div>
</template>
<script>
  //import Users后会自动导入
  import Users from "../components/Users";
    export default {
      name: 'About',
      // components: {users:Users}
        components: {"m-user":Users}
      // components: {Users}推荐写法
    }
</script>
```

## 样式

```
//加scoped表示当前样式只对该css生效，就是防止其他组件引入该样式时候混乱，
<style scoped>
h1{
    color: black;
}
</style>
```

## 回调this

```html
created(){
	const that = this //这个this代表vue对象
	res=>
	this //这个this代表回调对象
}
$router.options.routers   //先取router对象中的options设置中的routers路由
```

