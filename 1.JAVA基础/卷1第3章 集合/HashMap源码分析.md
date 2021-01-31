### HashMap

* 数组：快速索引
* 链表：动态扩容
* 散列表：数组+列表

#### 介绍

1. HashMap继承体系：继承于Map

2. Node数据结构分析

```
static class Node<K, V> implements Entry<K, V> {
        final int hash;
        final K key;
        V value;
        HashMap.Node<K, V> next;
        Node(int hash, K key, V value, HashMap.Node<K, V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
```

3. 底层存储结构介绍

数组+链表+红黑树（自平衡二叉查找树）

4. 为什么引入红黑树

哈希碰撞很高的时候，链表很长，查找效率很低

5. 为什么要扩容

以空间换时间，如果不扩容，那么就退化成线性查找时间

#### 属性分析

1. 表初始大小    `static final int DEFAULT_INITIAL_CAPACITY = 16;`（1<<4）

2. 表最大大小   `static final int MAXIMUM_CAPACITY = 1073741824;`

3. 负载因子`static final float DEFAULT_LOAD_FACTOR = 0.75F;`
4. 树化阈值（查询过程本来是O（1），随着插入增多变成O（N）），到达8变成树

`static final int TREEIFY_THRESHOLD = 8;`

5. 树降低成为链表 `static final int UNTREEIFY_THRESHOLD = 6;`
6. 树化的另一个参数，哈希表元素超过64，才会允许被树化，这里比较关键

`static final int MIN_TREEIFY_CAPACITY = 64;`

7. 哈希表 `transient HashMap.Node<K, V>[] table;`
8. `transient Set<Entry<K, V>> entrySet;`

9. 当前哈希表元素个数 `transient int size;`

10. 当前哈希表结构修改次数（应该是put或者delete操作，哈希表增加或者删除）   `transient int modCount;`

11. 阈值，超过即扩容 `int threshold;`
12. 负载数 容量*负载因子   即16*0.75`final float loadFactor;`

#### Node节点

```
   final int hash;
        final K key;
        V value;
        HashMap.Node<K, V> next;

        Node(int hash, K key, V value, HashMap.Node<K, V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
        
```

#### 构造方法分析

##### HahsMap(int,float)

传入初始化容量以及负载因子

```java
public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0) {
            throw new IllegalArgumentException("Illegal initial capacity: " + initialCapacity);
        } else {
            if (initialCapacity > 1073741824) {
                initialCapacity = 1073741824;
            }

            if (loadFactor > 0.0F && !Float.isNaN(loadFactor)) {
                this.loadFactor = loadFactor;
                this.threshold = tableSizeFor(initialCapacity);
            } else {
                throw new IllegalArgumentException("Illegal load factor: " + loadFactor);
            }
        }
    }
```

作用：返回一个大于这个值的2.次方数

```
>>>代表无符号右移也叫逻辑右移，即若该数为正，则高位补0，而若该数为负数，则右移后高位同样补0。
```

非常棒的一个算法

```
static final int tableSizeFor(int cap){
	int n = cap -1 ;
	n|=n>>>1;
	n|=n>>>2;
	n|=n>>>4;
	n|=n>>>8;
	n|=n>>>16;
	return (n<0)?1:(n>=MAXIMUM_CAPACITY)？MAXIMUM_CAPACITY:n+1;
}
```

同样算法

```
static final int tableSizeFor(int cap) {
    int n = -1 >>> Integer.numberOfLeadingZeros(cap - 1);
    return n < 0 ? 1 : (n >= 1073741824 ? 1073741824 : n + 1);
}
```

#### put方法

哈希值，key，Value，

主要要分三种情况

* 只有一个位置有元素
* 即链表
* 即红黑树结构

##### put函数

* 首先获取hash值，根据哈希值，key，value放入

```java
public V put(K key, V value) {
        return this.putVal(hash(key), key, value, false, true);
    }
```

##### hash函数

```
让Key的hashcode值的高16位参与运算
0b 0010 1010 1111 0101 0110 1010 0101 0010
^(不同为1，相同为0)
0b 0000 0000 0000 0000 0010 1010 1111 0101 (0110 1010 0101 0010)
0b 0010 1010 1111 0101 0100 0000 1010 0111 
```

```
 static final int hash(Object key) {
        int h;
        return key == null ? 0 : (h = key.hashCode()) ^ h >>> 16;
    }
    
    15%6==3
    1 1 1 1
    0 1 0 1
    0 1 0 1
```

##### putVal函数

* tab:当前hashMap的散列表
* p：当前散列表的元素
* n：散列表的长度
* 路由寻址结果下标

###### ==与equals区别

1. ==比较基本类型，速度更快可以放在||符号前面
2. equals重写toString方法比较内容，否则也是比较地址

###### 核心代码

![image-20200521192827222](https://gitee.com/shaobing2021/typora/raw/master/img/20200728094848.png)

```java
@Param onlyIfAbsent:判断是否存在相同的key，开始为false
final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
        HashMap.Node[] tab;
         Object p;
        int n;
        //1.判断哈希表是否创建，懒加载，第一次调用需要初始化hashMap对象中最耗内存的散列表
        if ((tab = this.table) == null || (n = tab.length) == 0) {
            n = (tab = this.resize()).length;
        }
        int i;
        //2.寻找当前要插入的数组位置，如果刚好是null，则直接插入
      	//如果此处p！=null，则是要插入位置的元素
  			//Case1
        if ((p = tab[i = n - 1 & hash]) == null) {
            tab[i] = this.newNode(hash, key, value, (HashMap.Node)null);
        } 
        else {
        		//node临时元素
            Object e;
            //表示临时的一个key
            Object k;
            //Case2.1:哈希值相等且（地址或内容相等）,后续需要替换操作
            if (((HashMap.Node)p).hash == hash && ((k = ((HashMap.Node)p).key) == key || key != null && key.equals(k))) {
                e = p;
            }
          //Case2.2如何判断p是否属于TreeNode？？
          else if (p instanceof HashMap.TreeNode) {
                e = ((HashMap.TreeNode)p).putTreeVal(this, tab, hash, key, value);
            } 		
          //Case2.3
          else {
                int binCount = 0;
                while(true) {
                    if ((e = ((HashMap.Node)p).next) == null) {
                      //插入节点，插入在链表末尾，jdk1.7之前插入在末尾
                        ((HashMap.Node)p).next = this.newNode(hash, key, value, (HashMap.Node)null);
                      //到8开始树化
                        if (binCount >= 7) {
                            this.treeifyBin(tab, hash);
                        }
                        break;
                    }
								//如果找到哈希值且（内容或地址相同的元素）,表示后续需要替换操作
                    if (((HashMap.Node)e).hash == hash && ((k = ((HashMap.Node)e).key) == key || key != null && key.equals(k))) {
                        break;/
                    }

                    p = e;
                    ++binCount;
                }
            }
          //头元素或者链表元素与插入元素key相同，说明需要替换，同时返回这个值
            if (e != null) {
                V oldValue = ((HashMap.Node)e).value;
                if (!onlyIfAbsent || oldValue == null) {
                    ((HashMap.Node)e).value = value;
                }

                this.afterNodeAccess((HashMap.Node)e);
                return oldValue;
            }
        }
  			//modCount表示散列表结构被修改的元素，替换Node元素的value不计数，++i性能比i++性能更好？
        ++this.modCount;
 				 //判断是否需要扩容
        if (++this.size > this.threshold) {
            this.resize();
        }

        this.afterNodeInsertion(evict);
        return null;
    }
```

#### resize函数

```java
 final HashMap.Node<K, V>[] resize() {
 				//oldTab:引用扩容前的哈希表
        HashMap.Node<K, V>[] oldTab = this.table;
        //oldCap:扩容前table数组长度
        int oldCap = oldTab == null ? 0 : oldTab.length;
        //oldThr:扩容前的扩容阈值，触发本次扩容的阈值
        int oldThr = this.threshold;
        //newThr扩容之后table数组的大小
        int newThr = 0;
        //扩容之后，下次触发扩容的条件
        int newCap;
        //条件成立，说明hashMap哈希表已经初始化过，是一次正常扩容
        if (oldCap > 0) {
        	//扩容之前的table数组已经达到最大阈值，不扩容，且将扩容条件设置为int最大值
            if (oldCap >= 1073741824) {
                this.threshold = 2147483647;
                return oldTab;
            }
						//如果容量大于16，且能够继续扩容，就扩容左移一位
            if ((newCap = oldCap << 1) < 1073741824 && oldCap >= 16) {
                newThr = oldThr << 1;
            }
        } 
        //在oldCap==0情况下,第一次扩容
        	//oldThr不为0，见构造函数，
        	//1.HashMap(initCap,loadFactory)
        	//2.HashMap(initCap)
        	//3.HashMap(map)
        	//this.threshod = initCaptcity ,导致无法
        else if (oldThr > 0) {
            newCap = oldThr;
        } 
        //相当于未设置初始化容量，直接定容量为16，阈值为12
        else {
            newCap = 16;
            newThr = 12;
        }
			//第一种情况，初始容量小于16设置为4时，那么新阈值为0
			//第二种情况,oldThr>0&&oldCap==0
			//newThr =0,通过newCap和newThr重新计算并且需要int
        if (newThr == 0) {
            float ft = (float)newCap * this.loadFactor;
            newThr = newCap < 1073741824 && ft < 1.07374182E9F ? (int)ft : 2147483647;
        }
			//真正的扩容操作
        this.threshold = newThr;
        //创建一个更长的数组，也有可能第一次创建数组
        HashMap.Node<K, V>[] newTab = new HashMap.Node[newCap];
        this.table = newTab;
        //说明不是第一次扩容，之前有数据
        if (oldTab != null) {
            for(int j = 0; j < oldCap; ++j) {
                HashMap.Node e;
                //说明当前桶位有数据，但是具体是单个数据还是链表还是红黑树并不知道
                if ((e = oldTab[j]) != null) {
                		//方便JVM GC，回收内存
                    oldTab[j] = null;
                    //当前桶只有一个数据，那么就只能用哈希值计算出新数组位置
                    if (e.next == null) {
                        newTab[e.hash & newCap - 1] = e;
                    }
                    //第二种情况：当前节点已经树化
                    else if (e instanceof HashMap.TreeNode) {
                        ((HashMap.TreeNode)e).split(this, newTab, j, oldCap);
                    }
                    //桶位为链表
                    else {
                    //低位链表：存放扩容之后的数组下标位置与当前数组下标位置一直
                        HashMap.Node<K, V> loHead = null;
                        HashMap.Node<K, V> loTail = null;
                     //高位链表：存放扩容之后的数组下标位置+扩容长度与当前数组下标位置一致
                        HashMap.Node<K, V> hiHead = null;
                        HashMap.Node hiTail = null;
                        HashMap.Node next;
                        do {
                            next = e.next;
                            //这里很巧妙：
                            //由于hash值只可能是
                            //...1 1111
                            //...0 1111
                            //如果是0则说明要放到低位当中
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null) {
                                    loHead = e;
                                } else {
                                    loTail.next = e;
                                }

                                loTail = e;
                            } else {
                                if (hiTail == null) {
                                    hiHead = e;
                                } else {
                                    hiTail.next = e;
                                }

                                hiTail = e;
                            }

                            e = next;
                        } while(next != null);
												//这里考虑的情况是：一条链表拆成两条，最后一个元素在高位，那么低位就会由元素指向高位元素，需要置为null
                        if (loTail != null) {
                            loTail.next = null;
                            //定义了一个Node节点，现在为该位置
                            newTab[j] = loHead;
                        }

                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }

        return newTab;
    }
```

#### get函数

```
 public V get(Object key) {
        HashMap.Node e;
        return (e = this.getNode(hash(key), key)) == null ? null : e.value;
    }
```

##### 核心GetNode

```
final HashMap.Node<K, V> getNode(int hash, Object key) {
			//tab：引用当前hashMap的散列表
        HashMap.Node[] tab;
        //first:桶位中的头元素
        HashMap.Node first;
        //n:数组长度
        int n;
        //确保表中有元素才去找
        if ((tab = this.table) != null && (n = tab.length) > 0 && (first = tab[n - 1 & hash]) != null) {
            Object k;
            //1.同为元素即要get的元素
            if (first.hash == hash && ((k = first.key) == key || key != null && key.equals(k))) {
                return first;
            }
						//临时node元素
            HashMap.Node e;
            //判断条件为：当前桶位不止一个元素：可能链表，可能红黑树
            if ((e = first.next) != null) {
            		//桶位升级为红黑树
                if (first instanceof HashMap.TreeNode) {
                    return ((HashMap.TreeNode)first).getTreeNode(hash, key);
                }
						//链表
                do {
                    if (e.hash == hash && ((k = e.key) == key || key != null && key.equals(k))) {
                        return e;
                    }
                } while((e = e.next) != null);
            }
        }

        return null;
    }
```

#### remove函数

其实和get函数差不多

```
 public boolean remove(Object key, Object value) {
        return this.removeNode(hash(key), key, value, true, true) != null;
    }
```

```
public V remove(Object key) {
        HashMap.Node e;
        return (e = this.removeNode(hash(key), key, (Object)null, false, true)) == null ? null : e.value;
    }
```

核心方法

```

//matchValue表示：删除两个参数则true，
final HashMap.Node<K, V> removeNode(int hash, Object key, Object value, boolean matchValue, boolean movable) {
				//引用当前hashMap中的散列表
        HashMap.Node[] tab;
        //当前Node元素
        HashMap.Node p;
        //表示散列表长度
        int n;
        //表示寻址结果
        int index;
        //判断当前表是否有数据，有才remove
        if ((tab = this.table) != null && (n = tab.length) > 0 && (p = tab[index = n - 1 & hash]) != null) {
        //说明桶位有数据，需要进行查找操作，并且删除
        //node表示查找结果
            HashMap.Node<K, V> node = null;
            Object k;
            //第一种情况：即桶位元素就是要删除的元素
            if (p.hash == hash && ((k = p.key) == key || key != null && key.equals(k))) {
                node = p;
            } else {
            //当前Node的下一个元素
                HashMap.Node e;
               //说明桶位还有元素：链表或红黑树
                if ((e = p.next) != null) {
                //红黑树查找操作
                    if (p instanceof HashMap.TreeNode) {
                        node = ((HashMap.TreeNode)p).getTreeNode(hash, key);
                    } else {
                        label88: {
                        //链表查找
                            while(e.hash != hash || (k = e.key) != key && (key == null || !key.equals(k))) {
                                p = e;
                                if ((e = e.next) == null) {
                                    break label88;
                                }
                            }
														//即要删除的元素
                            node = e;
                        }
                    }
                }
            }

            Object v;
            
            //matchvalue为true则要匹配value，就要匹配后面的逻辑，如果为false，则不要匹配Value，不需要匹配后面的逻辑
            if (node != null && (!matchValue || (v = ((HashMap.Node)node).value) == value || value != null && value.equals(v))) {
                if (node instanceof HashMap.TreeNode) {
                    ((HashMap.TreeNode)node).removeTreeNode(this, tab, movable);
                } 
                //第二种情况：桶位元素，头节点即为查找结果，将该元素的下一个元素放至桶中
                else if (node == p) {
                    tab[index] = ((HashMap.Node)node).next;
                } else {
                    p.next = ((HashMap.Node)node).next;
                }

                ++this.modCount;
                --this.size;
                this.afterNodeRemoval((HashMap.Node)node);
                return (HashMap.Node)node;
            }
        }

        return null;
    }
```

#### Replace函数

```
 public boolean replace(K key, V oldValue, V newValue) {
        HashMap.Node e;
        Object v;
        if ((e = this.getNode(hash(key), key)) == null || (v = e.value) != oldValue && (v == null || !v.equals(oldValue))) {
            return false;
        } else {
            e.value = newValue;
            this.afterNodeAccess(e);
            return true;
        }
    }
```



```
public V replace(K key, V value) {
    HashMap.Node e;
    if ((e = this.getNode(hash(key), key)) != null) {
        V oldValue = e.value;
        e.value = value;
        this.afterNodeAccess(e);
        return oldValue;
    } else {
        return null;
    }
}
```

#### 为什么Map长度是2的幂次方

按位“与”的时候，每一位都能 &1 ，也就是和1111……1111111进行与运算