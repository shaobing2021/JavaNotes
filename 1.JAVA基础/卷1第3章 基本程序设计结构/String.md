

## String

### 常用方法

![常用方法](https://gitee.com/shaobing2021/typora/raw/master/img/20200704212814.png)

## StringBuffer和StringBuilder

### StringBuffer

>  StringBuffer JDK1.0版本提供的类，线程安全，做线程同步检查， 效率较低

### StringBuilder

> StringBuilder JDK1.5版本提供的类，线程不安全，不做线程同步检查，因此效率较高

### 常用方法

| 方法                         | 解释说明                                       |
| ---------------------------- | ---------------------------------------------- |
| StringBuilder append()       | 添加字符序列，并返回自身对象                   |
| StringBuilder delete()       | 删除start至end-1的一段字符序列，并返回自身对象 |
| StringBuilder deleteCharAt() | 移除此序列指定位置上的char，并返回自身对象     |
| StringBuilder insert()       | 指定位置插入字符序列，并返回自身对象           |
| StringBuilder reverse()      | 将字符序列逆序，并返回自身对象                 |
| String toString()            | 将此序列中数据以字符串形式表现                 |



### 字符和字符串转换

* `String.valueof(chars)`  将字符数组转化为字符串
* `new String(chars)`	将字符数组转化为字符串
* `str.toCharArray`    将字符串转化成字符数组

### 数组

> 遍历数组

* Arrays.toString()   
* for-each  增强for
* 普通for

> Arrays类

* Arrays.copyof()

### 