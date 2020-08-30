[必做题](https://leetcode-cn.com/explore/featured/card/top-interview-questions-easy/25/math/)

### 数组

> ## 题目描述
>
> 输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

思路：从左往右，从上往下，从右往左，从下往上输出，当up>down||right>left就要跳出循环

```java
package roosevelt;

import java.util.ArrayList;

public class PrintMatrix {

    public static void main(String[] args) {
        int[][] matrix = {{1,2,3,4},{5,6,7,8},{9,10,11,12},{13,14,15,16}};
        ArrayList<Integer> list = printMatrix(matrix);
        for (Integer integer : list) {
            System.out.print(integer+" ");
        }
    }
    public static ArrayList<Integer> printMatrix(int [][] matrix) {
        int up = 0;
        int down = matrix.length-1;
        int left = 0 ;
        int right = matrix[0].length-1;
        ArrayList<Integer> list = new ArrayList<>();
        while (true){
            //从做左往右
            for (int i=left;i<=right;i++){
                list.add(matrix[up][i]);
            }
            up++;
            if (up>down||left>right){
                break;
            }
            //从上往下
            for (int i=up;i<=down;i++){
                list.add(matrix[i][right]);
            }

            right--;
            if (up>down||left>right){
                break;
            }
            //从右往左
            for (int i=right;i>=left;i--){
                list.add(matrix[down][i]);
            }

            down--;
            if (up>down||left>right){
                break;
            }
            //从下往上
            for (int i = down; i >= up; i--) {
                list.add(matrix[i][left]);
            }
            left++;
            if (up>down||left>right){
                break;
            }
        }

        return list;
    }
}
```

### 链表

#### 复杂链表的复制

> 输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针random指向一个随机节点），请对此链表进行深拷贝，并返回拷贝后的头结点。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

思路：

1. 遍历两次，首先将创造新节点并和原节点通过HashMap保存，然后再给新节点赋值next和random
2. 同样遍历两次，将next赋值完毕，再赋值random

为什么是遍历两次：提前生成每一个节点

为什么要用hashmap：寻找random下一节点

```java
import java.util.HashMap;
class RandomListNode{
    int label;
    RandomListNode next = null;
    RandomListNode random = null;
    RandomListNode(int label) {
        this.label = label;
    }
}
public class Solution {
    public static void main(String[] args) {
        RandomListNode pHead = new RandomListNode(1);
        RandomListNode p1 = new RandomListNode(2);
        RandomListNode p2 = new RandomListNode(3);
        pHead.next = p1;
        pHead.random = p2;
        p2.random = p1;
        new Solution().Clone1(pHead);
    }
    //解法一：
    public RandomListNode Clone1(RandomListNode pHead)
    {
        if (pHead == null) return null;
        RandomListNode root = new RandomListNode(pHead.label);
        RandomListNode r = root;
        RandomListNode p = pHead;
        HashMap<RandomListNode, RandomListNode> map = new HashMap<>();
        map.put(p, r);
        //注意该链表是有终点，不会一直循环
        while (p.next!=null ){
            r.next = new RandomListNode(p.next.label);
            p = p.next;
            r = r.next;
            map.put(p,r);
        }
        r = root;
        p = pHead;
        while (p.next!=null){
            r.random = map.get(p.random);
            p = p.next;
            r = r.next;
        }
        return root;
    }
    //解法二：相比情况一节约空间
    public RandomListNode Clone2(RandomListNode pHead){
        if (pHead == null) return null;
        RandomListNode p = pHead;
        HashMap<RandomListNode, RandomListNode> map = new HashMap<>();
        while (p!=null){
            map.put(p, new RandomListNode(p.label));
            p = p.next;
        }
        p = pHead;
        while (p!=null){
            map.get(p).random = map.get(p.random);
            map.get(p).next = map.get(p.next);
            p=p.next;
        }
        return map.get(pHead);
    }
}
```

### 栈

栈具有的方法：

* pop
* push
* peek：返回栈顶元素
* empty

> ## 题目描述
>
> 定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。
>
> 注意：保证测试中不会当栈为空的时候，对栈调用pop()或者min()或者top()方法。

思路：一个数据栈，一个最小栈，压入数据的时候保证最小栈中栈顶为最小的元素即可

```java
package roosevelt;

import java.util.Stack;

public class PrintMatrixIn {
    private Stack<Integer> dataStack = new Stack<>();
    private Stack<Integer> minStack = new Stack<>();
    public void push(int Node){
        dataStack.push(Node);
        if (minStack.empty()||minStack.peek()>=Node){
            minStack.push(Node);
        }else {
            minStack.push(minStack.peek());
        }
    }
    public void pop() {
        if (!dataStack.empty()){
            dataStack.pop();
            minStack.pop();
        }
    }

    public int top() {
        return dataStack.peek();
    }

    public int min() {
        return minStack.peek();
    }
}
```

#### 合并两个排序的链表（递归，迭代）

题目描述

```java
package Linklist;
/**
 * 递归三要素：
 * 1.递归函数功能
 * 2.递归结束条件
 * 3.函数等价关系式（对于链表来说，就是考虑走一遍list.next）
 * 知乎链接：https://www.zhihu.com/question/31412436
 */
public class Merge {
}
class Solution{
    public ListNode Merge(ListNode list1,ListNode list2) {
        //2.寻找递归结束条件
        if (list1==null){
            return list1;
        }
        if (list2==null){
            return list2;
        }
        //3.找出函数等价关系式
        if (list1.val<list2.val){
            list1.next = Merge(list1.next,list2);
            return list1;
        }else {
            list2.next = Merge(list1,list2.next);
            return list2;
        }
        //1.明确函数目的
    }

    /**
     * 利用迭代
     * @param list1
     * @param list2
     * @return
     */
    public ListNode Merge1(ListNode list1,ListNode list2){
        //临界条件
        if (list1==null){
            return list2;
        }
        if (list2==null){
            return list1;
        }
        //随意一个值，返回dummy.next即可
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;
        //1.主循环假设使用cur,list1和list2可能不同长，防止空指针异常
        while(list1!=null && list2!=null){
            if (list1.val<list2.val){
                cur.next =list1;
                list1 = list1.next;
            }else {
                cur.next = list2;
                list2 = list2.next;
            }
            cur = cur.next;
        }
        cur.next = list1==null?list2:list1;
        return dummy.next;
    }
}
class ListNode{
    int val;
    ListNode next = null;
    ListNode(int val){
        this.val = val;
    }
}
```

#### 面试题31

> ## 题目描述
>
> 输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

思路：使用辅助栈，不断从pushA中压入栈，判断栈顶元素是否为popA中栈底元素，是的话就移动，将辅助栈栈顶元素弹出，最后判断辅助栈元素是否为空

```java
package roosevelt;
import java.util.ArrayList;
import java.util.Stack;

public class Solution {
    public static void main(String[] args) {
        int[] pushA = {1,2,3,4,5};
//        int[] popA = {4,5,3,2,1};
        int[] popA = {4,3,5,1,2};
        System.out.println(IsPopOrder(pushA,popA));
    }
    public static boolean IsPopOrder(int [] pushA,int [] popA) {
        Stack<Integer> stack = new Stack<>();
        int j = 0;
        for (int i = 0; i < pushA.length; i++) {
            stack.push(pushA[i]);
            while (!stack.empty()&&stack.peek()==popA[j]){
                j++;
                stack.pop();
            }
        }
        return stack.empty();
    }
}
```

### 树讲解

> 中序遍历

#### 二叉搜索树与双向链表

> 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

思路：首先想到中序遍历

那么遍历到左子树叶节点的时候，开始构建

* 遍历到左叶子节点的情况下，记录链表头节点
* 其他情况下：父节点和该节点开始构建
* 最后遍历到右叶子节点，即尾部，和头部进行构建

技巧：==由于要记录父节点，以及头部节点，均需要方法方法外定义变量==

```java

class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;
    }
}


public class Solution {
    public TreeNode pre;
    public TreeNode head;
  //推荐方法
    public TreeNode Convert(TreeNode pRootOfTree){
        if (pRootOfTree == null) return null;
//        if (pRootOfTree.left==null&&pRootOfTree.right==null) return pRootOfTree;
        Convert(pRootOfTree.left);

        if (pre==null){
            head = pRootOfTree;
        }else {
            pRootOfTree.left =pre;
            pre.right = pRootOfTree;
        }
        pre = pRootOfTree;
        Convert(pRootOfTree.right);
//        pre.right = head;  不需要将尾部节点和头节点连起来
        return head;
    }
    public static void main(String[] args) {
        TreeNode root = new TreeNode(10); //3--4--
        TreeNode t1 = new TreeNode(5);
        TreeNode t2 = new TreeNode(12);
        TreeNode t3 = new TreeNode(4);
        TreeNode t4 = new TreeNode(7);
        TreeNode t5 = new TreeNode(1);
        root.left = t1;
        root.right = t2;
        t1.left = t3;
        t1.right = t4;
        t2.left = t5;
        TreeNode r = new Solution().Convert(root);
        System.out.println(r.val);
    }
    public TreeNode Convert1(TreeNode pRootOfTree) {
        if (pRootOfTree==null) return null;
        mid(pRootOfTree);
//        head.left = pre;
//        pre.right = head;
        return head;
    }
    //中序遍历
    public void mid(TreeNode cur){
        if(cur==null) return;
        mid(cur.left);

        if (pre == null) {
            head = cur;
        }
        else {
            pre.right = cur;
            cur.left = pre;
        }
        pre = cur;
        mid(cur.right);
    }
}
```

### 递归

#### 字符串的排列

> 输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

思路：很容易想到的方法就是递归 

ABCDEFG

从A位置开始，后续都和A进行交换   进而继续该操作

```java
import java.util.ArrayList;
import java.util.Arrays;

public class Solution {
    public static void main(String[] args) {
        String s = "abc";
        ArrayList<String> lists = new Solution().Permutation(s);
        System.out.println(lists);
        for (String list : lists) {
            System.out.println(list);
        }
        String str = "";
        System.out.println("str==null？"+(str==null));
        System.out.println("str.length==0"+(str.length()==0));
        lists = new Solution().Permutation("");
        System.out.println(lists);
    }
    public ArrayList<String> res = new ArrayList<>();
    public ArrayList<String> Permutation(String str) {
        if (str==null||str.length()==0){
            return res;
        }
        recur(str.toCharArray(),0,str.length());
        return res;
    }
    public void recur(char[] chars,int start,int end) {
        if (start>end) return;
        for (int i = start; i < end; i++) {
            swap(chars,i,start);
          //考虑一下，如果这里不复制一份会怎样，那么会导致i++的时候出现问题
            recur(Arrays.copyOf(chars,chars.length),start+1,end);
        }
        if (!res.contains(new String(chars))){
            res.add(new String(chars));
        }
    }
    public void swap(char[] chars,int i ,int j){
        char temp = chars[j];
        chars[j] = chars[i];
        chars[i] = temp;
    }
}
```



```



import java.util.ArrayList;
import java.util.Arrays;
import java.util.Iterator;

public class Solution {
    public static void main(String[] args) {
        String s = "abc";
//        char[] chars = new char[s.length()];
//        chars[1]='a';
//        chars[2]='b';
//        System.out.println(new String(chars));

//        System.out.println(s);
        ArrayList<String> lists = new Solution().Permutation(s);
//        lists.stream().forEach(System.out::println);
        Iterator<String> iter = lists.iterator();
//        while (iter.hasNext()){
//            iter.next();
//            System.out.println(iter);
//        }
        for (String list : lists) {
            System.out.println(list);
        }
    }
    public ArrayList<String> res = new ArrayList<>();
//    public char[] newchars = new char[3];
    public ArrayList<String> Permutation(String str) {
        char[] chars = str.toCharArray();
        char[] newchars = new char[chars.length];
        recur(chars,0,chars.length,newchars);
        return res;
    }
    public void recur(char[] chars,int start,int end,char[] newchars){
        if (start>end) return;
        for (int i = start; i < end; i++) {
            newchars[i] = chars[i];
            char temp = chars[i];
            chars[i] = chars[start];
            chars[start] = temp;
            recur(Arrays.copyOf(chars,chars.length),start+1,end,Arrays.copyOf(newchars,newchars.length));
        }
//        String temp = chars.toString();
        String temp = new String(newchars);
//        System.out.println(temp);
        if (!res.contains(temp)){
            res.add(temp);
        }
    }
}
```

### 树

#### 树的子结构

输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

1. 在树A中找到与树B根节点一样值的节点R
   * 从A节点，或者A左右节点找
2. 判断节点R是否包含和树B一样的结构
   * 即判断A左节点与A右节点与B左右节点值相同
3. 如果B遍历完，都和R一样，则说明是子结构，否则，不是子结构

```java
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
    public TreeNode(int val) {
        this.val = val;
    }
}
*/
public class Solution {
     public boolean HasSubtree(TreeNode root1,TreeNode root2) {
           boolean flag = false;
        if (root1!=null&&root2!=null){
            if (root1.val==root2.val){
                flag = recur(root1,root2);
            }
            if (flag==false){
                flag =  HasSubtree(root1.left,root2)||HasSubtree(root1.right,root2);
            }
        }
        return flag;
    }
    public static boolean recur(TreeNode root1,TreeNode root2){
        if (root2 == null){
            return true;
        }
        if (root1 == null){
            return false;
        }
     
        if (root1.val!=root2.val){
            return false;
        }
        return recur(root1.left,root2.left) && recur
          (root1.right,root2.right);
    }
}
```

1. 如果AB节点值相同，则判断结构是否相同，相同则要改成true
2. 如果AB节点值不同，则判断A左右节点是否与B节点值相同
3. 找到R相同的节点，如果B节点为null，则说明为真
4. 如果R节点遍历完毕，B节点遍历完毕则说明结构不同
5. 如果R节点子结构与B不同，则返回false；

#### 二叉树的镜像

题目描述

###### 题目描述

操作给定的二叉树，将其变换为源二叉树的镜像。

###### 输入描述:

```xml
二叉树的镜像定义：源二叉树 
    	    8
    	   /  \
    	  6   10
    	 / \  / \
    	5  7 9 11
    	镜像二叉树
    	    8
    	   /  \
    	  10   6
    	 / \  / \
    	11 9 7  5
```

#### 从上往下打印二叉树

> ## 题目描述
>
> 从上往下打印出二叉树的每个节点，同层节点从左至右打印

讲解：即广度搜索，注意的是这里写代码出现ConcurrentModificationException错误，由于遍历容器的时候对其进行了修改。

```java
import java.util.ArrayList;
import java.util.Vector;

class TreeNode {
     int val = 0;
     TreeNode left = null;
     TreeNode right = null;
     public TreeNode(int val) {
     this.val = val;
    }
 }
public class Solution {
    public static void main(String[] args) {
        TreeNode root = new TreeNode(1);
        TreeNode t1 = new TreeNode(2);
        TreeNode t2 = new TreeNode(3);
        TreeNode t3 = new TreeNode(4);
        root.left = t1;
        root.right = t2;
        t2.right = t3;
        root = null;
        System.out.println(PrintFromTopToBottom(root));
    }
    public static ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<Integer> list = new ArrayList<>();
        ArrayList<TreeNode> queue = new ArrayList<>();
        if (root==null){
            return list;
        }
        queue.add(root);
        /*
        java.util.ConcurrentModificationException
        for (TreeNode currentNode : queue) {
            list.add(currentNode.val);
            if (currentNode.left!=null){
                queue.add(currentNode.left);
            }
            if (currentNode.right!=null){
                queue.add(currentNode.right);
            }
            queue.remove(currentNode);
        }
         */
        while (!queue.isEmpty()){
            TreeNode cur =   queue.remove(0);;
            list.add(cur.val);
            if (cur.left!=null){
                queue.add(cur.left);
            }
            if (cur.right!=null){
                queue.add(cur.right);
            }
          
        }
        return list;
    }
}
```

#### leetCode102

#### 二叉树的后续遍历

> ## 题目描述
>
> 输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

思路：左右中，那么最右边一个即根节点，确保左子树右子树都小于该节点，然后找到左右子树分界线，递归

```java
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        //当数组为空时，则返回false；
        if (sequence.length==0||sequence == null) return false;
        return helpVerify(sequence,0,sequence.length-1);
    }
    private static boolean helpVerify(int[] sequence,int start,int end){
        //临界条件：只有一个元素，返回为真
        if (end-start<=1) return true;
        //根节点
        int root = sequence[end];
        int i;
        //找到分界线，左子树
        for (i = start; i < end; i++) {
            if (sequence[i]>=root){
                break;
            }
        }
        //判断右子树满足二叉搜索树否，不满足则返回false
        for (int j = end-1;j >= i; j--){
            if (root>sequence[j]){
                return false;
            }
        }
//        return true;
        return helpVerify(sequence,start,i-1) && helpVerify(sequence,i,end-1);
    }
}
```