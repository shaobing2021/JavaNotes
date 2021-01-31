## 变量声明

### let

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<script>
    //特性1
    {
        var a = 1;  // var声明的变量可以越域
        let b = 2;  // let声明的变量有严格局部作用域
    }
    console.log(a);
    console.log(b);
    //特性2
    var m = 1;
    var m = 2;  //可以声明多次
    let n = 3;  //只能声明一次
    //let n = 4;
    console.log(m)
    console.log(n)
    //特性3
    console.log(x)  //undefined,var会变量提升
    var x = 10;
    console.log(y)  //Uncaught ReferenceError:y before initialization，let不存在变量提升
    let y = 20;
</script>
</body>
</html>
```

### const

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<script>
    //const声明常量之后不允许改变
    const a =1 ;
    // a =4 ;  会报错
</script>
</body>
</html>
```

## 结构表达式

### 数组结构

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        let arr = [1,2,3];
        let a = arr[0];
        let b = arr[1];
        let c = arr[2];
        let [x,y,z] = arr;
        console.log(a,b,c);  //麻烦
        console.log(x,y,z);  //数组结构表达式
    </script>
</body>
</html>
```

### 对象结构

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
      const person={
          name:"jack",
          age:21,
          language:['jvaa','js','css']
      }
      // const name = person.name;
      // const age = person.age;
       // const {name,age,language} = person;
      const {name:abc,age,language} = person;
      console.log(name,age,language)
    </script>
</body>
</html>
```

## 字符串扩展

### API

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        let str = "hello.vue";//底下输出全为true
        console.log(str.startsWith("hello"));
        console.log(str.endsWith(".vue"));
        console.log(str.includes("e"));
        console.log(str.includes("hello"));
    </script>
</body>
</html>
```

### 字符串模板（`号）

### 多行字符

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        let ss = `<div>
                    11`;
        let s = "<div>dfas</div>" +
            "    <div>dfa</div>"
        console.log(ss);
        console.log(s);
    </script>
</body>
</html>
```

### 取值

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        let [a,b]=['java',23];
        let info = `我是${a},今年${b}`;
        let info1 = '我是${a},今年${b}';
        console.log(info);
        console.log(info1);
        function f() {
            return "这是一个函数";
        }
        info = `我是${a},今年${b},函数是${f()}`;
        console.log(info)
    </script>
</body>
</html>
```

## 函数优化

### 函数参数默认值

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        //在ES6以前，我们无法给一个函数参数设置默认值，只能采用变通写法：
        function add(a, b) {
            // 判断b是否为空，为空就给默认值1
            b = b || 1;
            return a + b;
        }
        // 传一个参数
        console.log(add(10));

        //现在可以这么写：直接给参数写上默认值，没传就会自动使用默认值
        function add2(a, b = 1) {
            return a + b;
        }
        console.log(add2(20));

    </script>
</body>
</html>
```

### 不定参数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        function f(...value) {
            console.log(value.length)
            console.log(value[1])
        }
        f(1,3);
        f(1,2,3,4);
    </script>
</body>
</html>
```

### 箭头函数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        //以前声明一个方法
        // var print = function (obj) {
        //     console.log(obj);
        // }
        var print = obj => console.log(obj);
        print("hello");
        var sum = function (a, b) {
            c = a + b;
            return a + c;
        }

        var sum2 = (a, b) => a + b;
        console.log(sum2(11, 12));

        //当函数有多行代码，用代码块
        var sum3 = (a, b) => {
            c = a + b;
            return a + c;
        }
        console.log(sum3(10, 20))

        const person = {
            name: "jack",
            age: 21,
            language: ['java', 'js', 'css']
        }

        function hello(person) {
            console.log("hello," + person.name)
        }

        //箭头函数+解构，正常来写
        var hello3=(person) => console.log("hello",person.name);
        var hello2 = ({name}) => console.log("hello," +name);
        hello2(person);
    </script>
</body>
</html>
```

## 对象优化

### 新增API

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        //类似hashmap遍历
        const person = {
            name: "jack",
            age: 21,
            language: ['java', 'js', 'css']
        }
        console.log(Object.keys(person));//["name", "age", "language"]
        console.log(Object.values(person));//["jack", 21, Array(3)]
        console.log(Object.entries(person));//[Array(2), Array(2), Array(2)]
        //组合
        const target = { a: 1 };
        const source1 = { b: 2 };
        const source2 = { c: 3 };
        //{a:1,b:2,c:3}
        Object.assign(target, source1, source2);
        console.log(target);//["name", "age", "language"]
    </script>
</body>
</html>
```

### 声明对象简写

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        const age = 23
        const name = "张三"
        const person1 = { age: age, name: name }
        const person2 = { age, name }//声明对象简写，因为age和变量名一致
        console.log(person2);
    </script>
</body>
</html>
```

### 对象函数简写

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        const age = 23
        const name = "张三"
        const person1 = { age: age, name: name }
        const person2 = { age, name }//声明对象简写，因为age和变量名一致
        console.log(person2);

        let person3 = {
            name: "jack",
            // 以前：
            eat: function (food) {
                console.log(this.name + "在吃" + food);
            },
            //箭头函数this不能使用，对象.属性
            eat2: food => console.log(person3.name + "在吃" + food),
            eat3(food) {
                console.log(this.name + "在吃" + food);
            }
        }

        person3.eat("香蕉");
        person3.eat2("苹果")
        person3.eat3("橘子");
    </script>
</body>
</html>
```

### 对象拓展运算符

### 深浅拷贝

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // 1、拷贝对象（深拷贝）
        let p1 = { name: "Amy", age: 15 }
        let someone = { ...p1 }
        console.log(someone)  //{name: "Amy", age: 15}
        someone.age = 18;
        console.log(p1.age)
        //这种情况属于浅拷贝，更改s，会更改p属性
        let s = p1;
        s.age = 18;
        console.log(p1.age)
    </script>
</body>
</html>
```

### 合并对象

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // 合并对象
        let age1 = { age: 15 }
        let name1 = { name: "Amy" }
        let p2 = {name:"zhangsan"}
        p2 = { ...age1, ...name1 }
        //后面name1的值会覆盖前面的值
        console.log(p2)
    </script>
</body>
</html>
```

## MapReduce

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        //数组中新增了map和reduce方法。
        //map()：接收一个函数，将原数组中的所有元素用这个函数处理后放入新数组返回。
        let arr = ['1', '20', '-5', '3'];

        //  arr = arr.map((item)=>{
        //     return item*2
        //  });
        arr = arr.map(item=> item*2);
        console.log(arr);
        //reduce() 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素，
        //[2, 40, -10, 6]
        //arr.reduce(callback,[initialValue])
        /**
         1、previousValue （上一次调用回调返回的值，或者是提供的初始值（initialValue））
         2、currentValue （数组中当前被处理的元素）
         3、index （当前元素在数组中的索引）
         4、array （调用 reduce 的数组）*/
        let result = arr.reduce((a,b)=>{
            console.log("上一次处理后："+a);
            console.log("当前正在处理："+b);
            return a + b;
        // },100);
        });
        console.log(result)
    </script>
</body>
</html>
```

## 异步操作

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js"></script>
</head>
<body>
    <script>
        //1、查出当前用户信息
        //2、按照当前用户的id查出他的课程
        //3、按照当前课程id查出分数
        // $.ajax({
        //     url: "mock/user.json",
        //     success(data) {
        //         console.log("查询用户：", data);
        //         $.ajax({
        //             url: `mock/user_corse_${data.id}.json`,
        //             success(data) {
        //                 console.log("查询到课程：", data);
        //                 $.ajax({
        //                     url: `mock/corse_score_${data.id}.json`,
        //                     success(data) {
        //                         console.log("查询到分数：", data);
        //                     },
        //                     error(error) {
        //                         console.log("出现异常了：" + error);
        //                     }
        //                 });
        //             },
        //             error(error) {
        //                 console.log("出现异常了：" + error);
        //             }
        //         });
        //     },
        //     error(error) {
        //         console.log("出现异常了：" + error);
        //     }
        // });


        //1、Promise可以封装异步操作
        // let p = new Promise((resolve, reject) => { //传入成功解析，失败拒绝
        //     //1、异步操作
        //     $.ajax({
        //         url: "mock/user.json",
        //         success: function (data) {
        //             console.log("查询用户成功:", data)
        //             resolve(data);
        //         },
        //         error: function (err) {
        //             reject(err);
        //         }
        //     });
        // });

        // p.then((obj) => { //成功以后做什么
        //     return new Promise((resolve, reject) => {
        //         $.ajax({
        //             url: `mock/user_corse_${obj.id}.json`,
        //             success: function (data) {
        //                 console.log("查询用户课程成功:", data)
        //                 resolve(data);
        //             },
        //             error: function (err) {
        //                 reject(err)
        //             }
        //         });
        //     })
        // }).then((data) => { //成功以后干什么
        //     console.log("上一步的结果", data)
        //     $.ajax({
        //         url: `mock/corse_score_${data.id}.json`,
        //         success: function (data) {
        //             console.log("查询课程得分成功:", data)
        //         },
        //         error: function (err) {
        //         }
        //     });
        // })

        function get(url, data) { //自己定义一个方法整合一下
            return new Promise((resolve, reject) => {
                $.ajax({
                    url: url,
                    data: data,
                    success: function (data) {
                        resolve(data);
                    },
                    error: function (err) {
                        reject(err)
                    }
                })
            });
        }
        get("mock/user.json")
            .then((data) => {
                console.log("用户查询成功~~~:", data)
                return get(`mock/user_corse_${data.id}.json`);
            })
            .then((data) => {
                console.log("课程查询成功~~~:", data)
                return get(`mock/corse_score_${data.id}.json`);
            })
            .then((data)=>{
                console.log("课程成绩查询成功~~~:", data)
            })
            .catch((err)=>{ //失败的话catch
                console.log("出现异常",err)
            });

    </script>
</body>

</html>

```

> corse_score_10.json 得分

```json
{
    "id": 100,
    "score": 90
}

```

> user.json 用户

```json
{
    "id": 1,
    "name": "zhangsan",
    "password": "123456"
}

```

> user_corse_1.json 课程

```json
{
    "id": 10,
    "name": "chinese"
}
1234
```

以前嵌套ajax的时候很繁琐。

- 把Ajax封装到Promise中，赋值给let p
- 在Ajax中成功使用resolve(data)，失败使用reject(err)
- p.then().catch()

## 模块化

模块化就是把代码进行拆分，方便重复利用。类似于java中的导包，而JS换了个概念，是导模块。

模块功能主要有两个命令构成 export 和import

- export用于规定模块的对外接口
- import用于导入其他模块提供的功能

> user.js

```js
var name = "jack"
var age = 21
function add(a,b){
    return a + b;
}
export {name,age,add}
```

> hello.js

```js
// export const util = {
//     sum(a, b) {
//         return a + b;
//     }
// }

export default {
    sum(a, b) {
        return a + b;
    }
}
// export {util}

//`export`不仅可以导出对象，一切JS变量都可以导出。比如：基本类型变量、函数、数组、对象。
```

> main.js

```js
import abc from "./hello.js"
import {name,add} from "./user.js"

abc.sum(1,2);
console.log(name);
add(1,3);
```