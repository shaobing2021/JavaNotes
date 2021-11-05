## 异步通信

* 理解使用axios属性通信获取值
* 理解使用data()函数保存值
* 将data()中值进行存储

```vue
<!DOCTYPE html>
<html lang="en" xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Demo4</title>
<!--    解决闪烁问题-->
    <style>
        [v-clock]{
            display: none;
        }
    </style>
</head>
<div id="app" v-clock>
    <div>{{info.name}}</div>
    <div>{{info.age}}</div>
    <div>{{info.address}}</div>
    <div>{{info.address.street}}</div>
<!--    绑定html中属性值-->
    <a v-bind:href="info.url">百度</a>
</div>
<body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
    new Vue({
        el: "#app",
        data(){
            return{
                //请求返回参数必须和json字符串一致
                info: {
                    name: '',
                    age: '',
                    address: {
                        street: '',
                        road: ''
                    }

                }
            }
        },
        mounted(){
            axios.get('../data.json').then(response=>(this.info=response.data))
        }
    });
</script>
</body>
</html>
```

Json

```json
<!DOCTYPE html>
<html lang="en" xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Demo4</title>
<!--    解决闪烁问题-->
    <style>
        [v-clock]{
            display: none;
        }
    </style>
</head>
<div id="app" v-clock>
    <div>{{info.name}}</div>
    <div>{{info.age}}</div>
    <div>{{info.address}}</div>
    <div>{{info.address.street}}</div>
<!--    绑定html中属性值-->
    <a v-bind:href="info.url">百度</a>
</div>
<body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
    new Vue({
        el: "#app",
        data(){
            return{
                //请求返回参数必须和json字符串一致
                info: {
                    name: '',
                    age: '',
                    address: {
                        street: '',
                        road: ''
                    }

                }
            }
        },
        mounted(){
            axios.get('../data.json').then(response=>(this.info=response.data))
        }
    });
</script>
</body>
</html>
```

## 计算属性

==Computed==

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Demo2</title>
</head>
<div id="app">
<!--    这两个区别：1.如下，2：computed相当于缓存，会存储下来，值不会改变，只有刷新才会重新计算，而methods每次都会重新计算-->
<!--    methods通过方法调用-->
    <p>{{currentTime1()}}</p>
<!--    computed通过属性调-->
    <p>{{currentTime2}}</p>
</div>
<body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
    Vue.component("")
    let vue = new Vue({
        el: "#app",
        data: {

        },
        methods: {
            currentTime1: function () {
                return Date.now();//返回当前时间戳
            }
        },
        computed:{
            //重名之后只会走methods中
            currentTime2: function () {
                return Date.now();
            }
        }
    });
</script>
</body>
</html>
```