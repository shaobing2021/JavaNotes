### Collection

#### 常用方法

![表9-1 Collection接口中定义的方法.png](https://gitee.com/shaobing2021/typora/raw/master/img/20200702143951.png)

### List

> List是有序、可重复的容器

#### 常用方法

![List常用方法](https://gitee.com/shaobing2021/typora/raw/master/img/20200702144437.png)

#### 作用

* 可以当作栈来使用add，remove(list.size()-1)
* 可以当作队列使用add，remove(0)
* 同样可以当作双端队列

#### 子类

> ArrayList

* ArrayList底层是用数组实现的存储。 特点：查询效率高，增删效率低，线程不安全

> LinkedList

* 特点：查询效率低，增删效率高，线程不安全

> Vector

 底层是用数组实现的List，相关的方法都加了同步检查，因此“线程安全,效率低”

#### 遍历

```java
for(int i=0;i<list.size();i++){//list为集合的对象名
    String temp = (String)list.get(i);
    System.out.println(temp);
}
//增强for
for (String temp : list) {
System.out.println(temp);
}
for(Iterator iter= list.iterator();iter.hasNext();){
    String temp = (String)iter.next();
    System.out.println(temp);
}
Iterator  iter =list.iterator();
while(iter.hasNext()){
    Object  obj =  iter.next();
    iter.remove();//如果要遍历时，删除集合中的元素，建议使用这种方式！
    System.out.println(obj);
}
list.forEach(System.out::println);
```



#### 使用建议

1. 需要线程安全时，用Vector。

2. 不存在线程安全问题时，并且查找较多用ArrayList(一般使用它)。

3. 不存在线程安全问题时，增加或删除元素较多用LinkedList。

### Set

> 无序、不可重复

新元素如果和Set中某个元素通过equals()方法对比为true，则不能加入;甚至，Set中也只能放入一个null元素，不能多个,常用方法和Collection保持完全一致

#### 子类

> HashSet

HashSet是采用哈希算法实现，底层实际是用HashMap实现的

map中key都是不可重复的，因此，Set天然具有“不可重复”的特性

> TreeSet

 TreeSet底层实际是用TreeMap实现的，内部维持了一个简化版的TreeMap，通过key来存储Set的元素

### Map

> Map就是用来存储“键(key)-值(value) 对”的。 Map类中存储的“键值对”通过键来标识，所以“键对象”不能重复

#### 常用方法

![Map常用方法](https://gitee.com/shaobing2021/typora/raw/master/img/20200702152846.png)

#### 子类

> HashMap 线程不安全，效率高。允许key或value为null
>
> 线程安全，效率低。不允许key或value为nul

 HashMap采用哈希算法实现，是Map接口最常用的实现类

#### 遍历

```java
//iter遍历  增强for循环
        for (String s : map.keySet()) {
            System.out.println(s+" "+map.get(s));
        }
        System.out.println("---------");
        //同iter  keyset
        Iterator<String> iterator = map.keySet().iterator();
        while (iterator.hasNext()){
            String key = iterator.next();
            System.out.println(key+" "+map.get(key));
        }
        //entryset
        Set<Map.Entry<String, Integer>> entries = map.entrySet();
        for (Map.Entry<String, Integer> entry : entries) {
            System.out.println(entry);
        }
```

### Queue

> 优先级队列PrioriQueue

#### 重点

* 默认为最小堆（根节点为最小的数）
* 可以通过Comparable和Comparator接口给对象排序
* 插入元素不能为空

#### 常用方法

| 方法                 | 说明                                   |
| -------------------- | -------------------------------------- |
| boolean add(E e)     | 插入一个元素                           |
| boolean offer(E e)   | 插入一个元素                           |
| boolean remove(E e)  | 删除一个元素                           |
| E remove()           | 删除一个队顶元素，并返回删除的元素     |
| E poll()             | 删除一个队顶元素元素，并返回删除的元素 |
| E peek()             | 查询队顶元素                           |
| int indexOf(E e)     | 查询对象o的索引                        |
| boolean contain(E e) | 判断是否包含某个元素                   |

## 遍历

增强for循环用的也是迭代器，所以不能便遍历边通过容器删除，只能通过迭代器删除

```
public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<Integer>();
        list.add(1);
        list.add(2);
        list.add(3);
        Iterator<Integer> iter = list.iterator();
//        while (iter.hasNext()){
//            Integer temp = iter.next();
//            list.remove(temp);
//        }
//        for (Integer integer : list) {
//            list.remove(integer);
//        }
//        for (int i = 0; i < list.size(); i++) {
//            Integer integer = list.get(i);
//            System.out.println(integer);
//            list.remove(integer);
//            i--;
//        }
        while (iter.hasNext()){
            Integer next = iter.next();
            System.out.println(next);
            iter.remove();
        }
    }
```

