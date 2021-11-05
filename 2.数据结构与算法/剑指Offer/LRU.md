## [leetcode146题](https://leetcode-cn.com/problems/lru-cache/)

[面试题 16.25. LRU缓存](https://leetcode-cn.com/problems/lru-cache-lcci/)

> 设计和构建一个“最近最少使用”缓存，该缓存会删除最近最少使用的项目。缓存应该从键映射到值(允许你插入和检索特定键对应的值)，并在初始化时指定最大容量。当缓存被填满时，它应该删除最近最少使用的项目。
>
> 它应该支持以下操作： 获取数据 get 和 写入数据 put 。
>
> 获取数据 get(key) - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。
> 写入数据 put(key, value) - 如果密钥不存在，则写入其数据值。当缓存容量达到上限时，它应该在写入新数据之前删除最近最少使用的数据值，从而为新的数据值留出空间。

```
示例:
LRUCache cache = new LRUCache( 2 /* 缓存容量 */ );
cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // 返回  1
cache.put(3, 3);    // 该操作会使得密钥 2 作废
cache.get(2);       // 返回 -1 (未找到)
cache.put(4, 4);    // 该操作会使得密钥 1 作废
cache.get(1);       // 返回 -1 (未找到)
cache.get(3);       // 返回  3
cache.get(4);       // 返回  4
```

### 思路：

1. 使用数组，那么要记录最久未使用的，则需要使用时间标记，获取最久未使用需要遍历。当然也可以选择每次将get的放到最前边，但是该过程移动时间复杂度成了o(N)；
2. 使用单项链表，前节点为head，尾部节点为tail，那么时间get时间复杂度变为o(n)
3. 使用map，那么get，put操作均为o(1),但是没法找到前驱后后驱，所以需要使用双向链表
4. 最终hashmap+double linkedlist
5. java中自带[LinkedHashMap](https://blog.csdn.net/xixingzhe2/article/details/83792613)   [LinkedHashMap实现2](https://www.jianshu.com/p/a8a012195385)

> [高级](https://blog.csdn.net/leo_weile/article/details/90142697)

1. 哨兵模式（dummy tail）
2. [线程安全](https://www.cnblogs.com/zlting/p/10775887.html)
3. CAS操作
4. 考虑ConcurrenthashMap
5. 读写锁，容忍一定精确性损失
6. 环形LRU
7. 提前分配空间的LRU
8. 中间留空洞的做法
9. 分布式的LRU

### 代码实现

1. 首先确定链表+HashMap，链表自己实现
2. 给这个类一个头节点，尾结点，当作哨兵
3. Node结构体，包含前驱，尾驱，以及key，value
4. put的时候，首先判断元素是否重复，重复则更改值。否则要判断大小是否超出限制，超出则需要删除尾结点。然后进行将该节点插入到首部
5. get的时候，如果get不到，则返回-1。否则就将该节点移动到首节点

```java
package leetcode.lc140;

import java.util.HashMap;

public class LRUCache1 {
    private int capacity;
    private HashMap<Integer,Node> map;
    Node head,tail;
    public LRUCache1(int capacity){
        this.capacity = capacity;
        map = new HashMap<>(capacity);
        head = new Node();
        tail = new Node();
        head.next = tail;
        tail.prev = head;

    }
    class Node{
        Node prev;
        Node next;
        private int key;
        private int value;
        public Node(int key,int value){
            this.key = key;
            this.value = value;
        }
        public Node(){
        }
    }
    public int get(int key){
        Node node = map.get(key);
        if (node==null) return -1;
        moveToHead(node);
        return node.value;
    }

    private void moveToHead(Node node){
        removeNode(node);
        addToHead(node);
    }
    private void removeNode(Node node){
        //System.out.println("移除："+node.key);
        node.prev.next = node.next;
        node.next.prev = node.prev;
        //node = null;//能否增加这个使回收
    }
    private void addToHead(Node node){
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }
    public void put(int key,int value){
        Node node = map.get(key);
        if (node!=null){
            node.value = value;
            moveToHead(node);
        }else{
            node = new Node(key,value);
            if (map.size()>=capacity){
                map.remove(tail.prev.key);
                removeNode(tail.prev);

            }
            map.put(key,node);
           // System.out.println(map.size());
            addToHead(node);
        }
    }
    public static void main(String[] args) {
        LRUCache1 lru = new LRUCache1(2);
        lru.put(1,111);
        lru.put(2,222);
        System.out.println(lru.get(1));
        lru.put(3,333);
        System.out.println(lru.get(1));
        System.out.println(lru.get(2));
//        lru.put(4,444);
//        lru.put(5,555);
//        System.out.println("get:"+lru.get(5));
//        lru.put(6,666);
    }
}
```

