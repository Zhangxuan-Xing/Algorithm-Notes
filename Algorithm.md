# Examples



## 1）打印出无序数组b在a有序数组中没出现的数

假设a数组长度为N， b数组长度为M，则

**如果b数组短 - O（M*log M）+ O（M+N）**

1. 排序b
2. 双指针滑动

**如a数组短 - O（M*logN）**

   对于b的每个数，在a中进行二分查找

【需要确定的数，才能判断哪种办法更好】

## 2）小和问题

归并 -  分批次求小和

```java
   public static int getSmallSum(int[] num){
        int len = num.length;
        if(num == null  ||  len < 2)
            return 0;
        return mergeSort(num,0,len - 1);
    }

    private static int mergeSort(int[] num, int from, int end) {
        if(from == end)
            return 0;
        int mid = from + (end - from)/2;
        return mergeSort(num,from,mid) + mergeSort(num,mid + 1,end)+ merge(num,from,mid,end);
    }

    private static int merge(int[] num, int from, int mid, int end) {
        int[] help = new int[end - from + 1];
        int i = 0;
        int p1 = from;
        int p2 = mid + 1;
        int result = 0;
        while(p1 <= mid && p2 <= end){
            //与归并相比  仅仅多了result部分
            result += num[p1] < num[p2] ? num[p1]*(end - p2 + 1):0;
            help[i++] =  num[p1] < num[p2] ? num[p1++]:num[p2++];
        }
        while(p1 <= mid){
            help[i++] = num[p1++];
        }
        while(p2 <= end){
            help[i++] = num[p2++];
        }
        for(int j = from;j <= end;j++){
            num[j] = help[j - from];
        }
        return result;
    }
```

## 3）荷兰国旗问题

要求额外空间复杂度O（1），时间复杂度O（N）

> “Partition”问题

分成 小于、等于、大于  三段求解

```java
    public static int[] partition(int[] arr, int from, int to, int num){
        int less = from - 1;
        int more = to + 1;
        int current = from;
        while(current < more){
            if(arr[current] < num)
                swap(arr,++less,current++);
            else if(arr[current] > num)
                swap(arr,--more,current);
            else
                current++;
        }
        //中间相等部分的下标
        return new int[]{less + 1, more - 1 };
    }

    public static void swap(int[] arr, int i, int j){
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
```

## 4）相邻两数桶最大值

```java
    public static int getMax(int[] arr){
        int len = arr.length;
        if(arr == null || len < 2)
            return 0;
        int max = Integer.MIN_VALUE;
        int min = Integer.MAX_VALUE;
        for(int i = 0; i < len; i++){
            min = min < arr[i] ? min : arr[i];
            max = max > arr[i] ? max : arr[i];
        }
        if(max == min)
            return 0;
        boolean[] hasNum = new boolean[len + 1];
        int[] minNum = new int[len + 1];
        int[] maxNum = new int[len + 1];
        for(int i = 0; i < len; i++){
            int index = bucket(arr[i],len,min,max);
            minNum[index] = hasNum[index] ? Math.min(minNum[index],arr[i]) : arr[i];
            maxNum[index] = hasNum[index] ? Math.max(maxNum[index],arr[i]) : arr[i];
            hasNum[index] = true;
        }
        int resGap = 0;
        int lastNum = maxNum[0];
        for(int i = 1; i <= len;i++){
            if(hasNum[i]){
                resGap = Math.max(resGap,minNum[i] - lastNum);
                lastNum = maxNum[i];
            }
        }
        return resGap;
    }

    public static int bucket(long num, long len, long min, long max){
        return (int)((num - min)*len/(max - min));
    }
```

## 5）数组实现队列

```java
    private int[] arr;
    private int size;
    private int start;
    private int end;

    public ArrayQueue(int len){
        if(len < 0)
            throw new  IllegalArgumentException("init size is less than 0");
        arr = new int[len];
        size = 0;
        start = 0;
        end = 0;
    }

    public Integer peek(){
        if(size == 0)
            return null;
        return arr[start];
    }

    public Integer poll(){
        if(size == 0)
            throw new ArrayIndexOutOfBoundsException();
        size--;
        int temp = start;
        start = start == arr.length - 1 ? 0 : start + 1;
        return arr[temp];
    }

    public void push(int num){
        if(size == arr.length)
            throw new ArrayIndexOutOfBoundsException();
        size++;
        arr[end] = num;
        end = end == arr.length - 1 ? 0 : end + 1;
    }
```

## 6）数组实现栈

```java
    private int[] arr;
    private int index = 0;

    public  ArrayStack(int len){
        if(len < 0)
            throw new  IllegalArgumentException("init size is less than 0");
        arr = new int[len];
    }

    public int peek(){
        if(index == 0)
            throw new ArrayIndexOutOfBoundsException();
        return arr[index - 1];
    }

    public int pop(){
        if(index == 0)
            throw new ArrayIndexOutOfBoundsException();
        return arr[--index];
    }

    public void push(int num){
        if(index == arr.length)
            throw new ArrayIndexOutOfBoundsException();
        arr[index++] = num;
    }	
```
## 7）返回最小元素的栈

```java
    Stack<Integer> dataStack = new Stack<>();
    Stack<Integer> minStack = new Stack<>();

    public void push(Integer num){
         if(minStack.isEmpty()){
             minStack.push(num);
         }else if(num < getMin()){
             minStack.push(num);
         }else
             minStack.push(getMin());
         dataStack.push(num);
    }

    public Integer pop(){
        if(dataStack.isEmpty())
            throw  new RuntimeException();
        minStack.pop();
        return dataStack.pop();
    }

    public Integer getMin(){
        if(minStack.isEmpty())
            throw  new RuntimeException();
        return minStack.peek();
    }
```

## 8）转圈打印矩阵 

给定一个整型矩阵matrix， 请按照转圈的方式打印它

例如： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 

打印结果为： 1， 2， 3， 4， 8， 12， 16， 15， 14， 13， 9，5， 6， 7， 11， 10

【要求】 额外空间复杂度为O(1) 

```java
private static void printMatrix(int[][] matrix) {
        int lr = 0;
        int lc = 0;
        int rr = matrix.length - 1;
        int rc = matrix[0].length - 1;
        while(lr <= rr && lc <= rc)
            printNum(matrix, lr++, lc++, rr--, rc--);
    }

    private static void printNum(int[][] matrix, int lr, int lc, int rr, int rc) {
        if(lr == rr){
            for(int i = lc; i <= rc; i++)
                System.out.print(" ." + matrix[lr][i]);
        }else if(lc == rc){
            for(int i = lr; i <= rr; i++)
                System.out.print(" ." + matrix[i][lc]);
        }else{
            int currentR = lr;
            int currentC = lc;
            while(currentC < rc){
                System.out.print(" ." + matrix[lr][currentC]);
                currentC++;
            }
            while(currentR < rr){
                System.out.print(" ." + matrix[currentR][rc]);
                currentR++;
            }
            while(currentC != lc){
                System.out.print(" ." + matrix[rr][currentC]);
                currentC--;
            }
            while(currentR != lr){
                System.out.print(" ." + matrix[currentR][lr]);
                currentR--;
            }
        }
    }
```

## 9）旋转正方形矩阵 

给定一个整型正方形矩阵matrix， 请把该矩阵调整成顺时针旋转90度的样子

【要求】 额外空间复杂度为O(1) 

```java
private static void rotate(int[][] matrix) {
        int lr = 0;
        int lc = 0;
        int rr = matrix.length - 1;
        int rc = matrix[0].length - 1;
        while(lr < rr)
            rotateArray(matrix,lr++,lc++,rr--,rc--);
    }

    private static void rotateArray(int[][] m, int lr, int lc, int rr, int rc) {
        int times = rc - lc;
        int tmp = 0;
        for (int i = 0; i != times; i++) {
            tmp = m[lr][lc + i];
            m[lr][lc + i] = m[rr - i][lc];
            m[rr - i][lc] = m[rr][rc - i];
            m[rr][rc - i] = m[lr + i][rc];
            m[lr + i][rc] = tmp;
        }
    }

    private static void printMatrix(int[][] matrix) {
        for(int i = 0; i < matrix.length; i++)
            for(int j = 0; j < matrix[0].length; j++)
                System.out.print(" " + matrix[i][j]);
    }
```

## 10）“之” 字形打印矩阵 

给定一个矩阵matrix， 按照“之” 字形的方式打印这个矩阵， 例如： 1 2 3 4 5 6 7 8 9 10 11 12
“之” 字形打印的结果为： 1， 2， 5， 9， 6， 3， 4， 7， 10， 11，8， 12

【要求】 额外空间复杂度为O(1) 

```java
private static void printMatrixZigZag(int[][] matrix) {
        int ar = 0;
        int ac = 0;
        int br = 0;
        int bc = 0;
        int endr = matrix.length - 1;
        int endc = matrix[0].length - 1;
        boolean reverse = false;
        while(ar <= endr){
            printNum(matrix, ar, ac, br, bc, reverse);
            ar = ac == endc ? ar + 1 : ar;
            ac = ac == endc ? ac : ac + 1;
            //格外注意此处顺序
            bc = br == endr ? bc + 1 : bc;
            br = br == endr ? br : br + 1;
            reverse = !reverse;
        }
    }

    private static void printNum(int[][] matrix, int ar, int ac, int br, int bc, boolean reverse) {
        if(reverse){
            while(ar <= br && ac >= bc)
                System.out.print("..." + matrix[ar++][ac--]);
        }else{
            while(br >= ar && bc <= ac)
                System.out.print("..." + matrix[br--][bc++]);
        }
    }
```

## 11）判断链表是否为回文结构 

给定一个链表的头节点head， 请判断该链表是否为回文结构

例如： 1->2->1， 返回true。 1->2->2->1， 返回true ； 15->6->15， 返回true。 1->2->3， 返回false

进阶： 如果链表长度为N， 时间复杂度达到O(N)， 额外空间复杂度达到O(1) 

```java
public static class Node {
        public int value;
        public Node next;

        public Node(int data) {
            this.value = data;
        }
    }

    // 利用栈进行对比
    private static boolean isPalindrome1(Node head) {
        Stack<Integer> stack = new Stack<>();
        Node res = head;
        while(res != null){
            stack.push(res.value);
            res = res.next;
        }
        while(head != null){
            if(head.value != stack.pop()){
                return false;
            }
            head = head.next;
        }
        return true;
    }

    //快指针和慢指针  需要额外空间O（1）
    private static boolean isPalindrome2(Node head) {
        if (head == null || head.next == null) {
            return true;
        }
        Node n1 = head;
        Node n2 = head;
        //需要注意的是应该用n2.next 而不是 n1.next
        while(n2.next != null && n2.next.next != null){
            n1 = n1.next;
            n2 = n2.next.next;
        }
        Node n3 = null;
        n2 = n1.next;
        n1.next = null;
        while(n2 != null){
            n3 = n2.next;
            n2.next = n1;
            n1 = n2;
            n2 = n3;
        }
        n3 = n1;
        n2 = head;
        while(n1 != null && n2 != null){
            if(n1.value != n2.value){
                return false;
            }
            n1 = n1.next;
            n2 = n2.next;
        }
        return true;
    }

    public static void printLinkedList(Node node) {
        System.out.print("Linked List: ");
        while (node != null) {
            System.out.print(node.value + " ");
            node = node.next;
        }
        System.out.println();
    }
```
## 12）单向链表-荷兰国旗问题

给定一个单向链表的头节点head， 节点的值类型是整型， 再给定一个整数pivot。 实现一个调整链表的函数， 将链表调整为左部分都是值小于 pivot的节点， 中间部分都是值等于pivot的节点， 右部分都是值大于 pivot的节点。
除这个要求外， 对调整后的节点顺序没有更多的要求。 例如： 链表9->0->4->5-1， pivot=3。 调整后链表可以是1->0->4->9->5， 也可以是0->1->9->5->4。 总之， 满 足左部分都是小于3的节点， 中间部分都是等于3的节点（本例中这个部
分为空） ， 右部分都是大于3的节点即可。 对某部分内部的节点顺序不做要求 

进阶： 在原问题的要求之上再增加如下两个要求。
在左、 中、 右三个部分的内部也做顺序要求， 要求每部分里的节点从左 到右的顺序与原链表中节点的先后次序一致 

```java
 public static class Node {
        public int value;
        public Node next;

        public Node(int data) {
            this.value = data;
        }
    }

    //Node类型数组  转为荷兰国旗做法  但不能保证稳定性
    private static Node listPartition1(Node head, int pivot) {
        if(head == null)
            return null;
        int i = 0;
        Node current = head;
        while(current != null){
            i++;
            current = current.next;
        }
        Node[] arr = new Node[i];
        current = head;
        for(int j = 0 ; j < i; j++){
            arr[j] = current;
            current = current.next;
        }
        partition(arr,pivot);
        for(int j = 1; j < i; j++)
            arr[j - 1].next = arr[j];
        arr[i - 1].next = null;
        return arr[0];
    }

    private static Node listPartition2(Node head, int pivot) {
        Node ss = null;
        Node se = null;
        Node es = null;
        Node ee = null;
        Node ls = null;
        Node le = null;
        Node curr = head;
        Node temp = null;
        while(curr != null){
            temp = curr.next;
            curr.next = null;
            if(curr.value < pivot){
                if(ss == null){
                    ss = curr;
                    se = curr;
                }else{
                    se.next = curr;
                    se = curr;
                }
            }else if(curr.value == pivot){
                if(es == null){
                    es = curr;
                    ee = curr;
                }else{
                    ee.next = curr;
                    ee = curr;
                }
            }else{
                if(ls == null){
                    ls = curr;
                    le = curr;
                }else{
                    le.next = curr;
                    le = curr;
                }
            }
            curr = temp;
        }
        if(se != null){
            se.next = es;
            ee = ee == null ? es :ee;
        }
        if(ee != null){
            ee.next = ls;
            le = le == null ? ls : le;
        }
        return ss != null ? ss : es != null ? es : ls;
    }

    public static void partition(Node[] arr, int pivot){
        int less = -1;
        int larger = arr.length;
        int curr = 0;
        while(curr < larger){
            if(arr[curr].value < pivot){
                swap(arr,++less,curr++);
            }else if(arr[curr].value > pivot){
                swap(arr,--larger,curr);
            }else
                curr++;
        }
    }

    public static void swap(Node[] nodeArr, int a, int b) {
        Node tmp = nodeArr[a];
        nodeArr[a] = nodeArr[b];
        nodeArr[b] = tmp;
    }

    public static void printLinkedList(Node node) {
        System.out.print("Linked List: ");
        while (node != null) {
            System.out.print(node.value + " ");
            node = node.next;
        }
        System.out.println();
    }
```

## 13）复制含有随机指针节点的链表 

一种特殊的链表节点类描述如下：
public class Node { 

public int value; 

public Node next; 

public Node rand;

public Node(int data) { 

this.value = data; 

}
} 

Node类中的value是节点值， next指针和正常单链表中next指针的意义一 样， 都指向下一个节点， rand指针是Node类中新增的指针， 这个指针可 能指向链表中的任意一个节点， 也可能指向null。 给定一个由Node节点类型组成的无环单链表的头节点head， 请实现一个 函数完成这个链表中所有结构的复制， 并返回复制的新链表的头节点。 进阶：不使用额外的数据结构， 只用有限几个变量， 且在时间复杂度为 O(N)内完成原问题要实现的函数 

## 14)....

## 15)实现二叉树的先序、 中序、 后序遍历

```java
    public static class Node {
        public int value;
        public Node left;
        public Node right;

        public Node(int data) {
            this.value = data;
        }
    }

    private static void preOrderRecur(Node head) {
        if(head == null)
            return ;
        System.out.print(" " + head.value);
        preOrderRecur(head.left);
        preOrderRecur(head.right);
    }

    private static void inOrderRecur(Node head) {
        if(head == null)
            return;
        inOrderRecur(head.left);
        System.out.print(" " + head.value);
        inOrderRecur(head.right);
    }

    private static void posOrderRecur(Node head) {
        if(head == null)
            return;
        posOrderRecur(head.left);
        posOrderRecur(head.right);
        System.out.print(" " + head.value);
    }

    private static void preOrderUnRecur(Node head) {
        if(head == null)
            return;
        Stack<Node> stack = new Stack<>();
        stack.push(head);
        while (!stack.isEmpty()){
            Node temp = stack.pop();
            System.out.print(" " + temp.value);
            if(temp.right != null)
                stack.push(temp.right);
            if(temp.left != null)
                stack.push(temp.left);
        }
    }

    private static void inOrderUnRecur(Node head) {
        if(head == null)
            return;
        Stack<Node> stack = new Stack<>();
        while (!stack.isEmpty() || head != null){
            if(head != null){
                stack.push(head);
                head = head.left;
            }else{
                head = stack.pop();
                System.out.print(" " + head.value);
                head = head.right;
            }
        }
    }

    private static void posOrderUnRecur1(Node head) {
        if(head == null)
            return;
        Stack<Node> stack1 = new Stack<>();
        Stack<Node> stack2 = new Stack<>();
        stack1.push(head);
        while (!stack1.isEmpty()){
            Node temp  = stack1.pop();
            stack2.push(temp);
            if(temp.left != null)
                stack1.push(temp.left);
            if(temp.right != null)
                stack1.push(temp.right);
        }
        while(!stack2.isEmpty())
            System.out.print(" " + stack2.pop().value);
    }
```
## 16)在二叉树中找到一个节点的后继节点 

现在有一种新的二叉树节点类型如下：
public class Node { public int value; public Node left;
public Node right; public Node parent;
public Node(int data) { this.value = data; }
} 

该结构比普通二叉树节点结构多了一个指向父节点的parent指针。 假设有一 棵Node类型的节点组成的二叉树， 树中每个节点的parent指针都正确地指向 自己的父节点， 头节点的parent指向null。 只给一个在二叉树中的某个节点 node， 请实现返回node的后继节点的函数。 在二叉树的中序遍历的序列中， node的下一个节点叫作node的后继节点 

```java
// 左中右
private static Node getSuccessorNode(Node test) {
        if(test == null)
            return null;
        if(test.right != null)
            return getMostLeft(test.right);
        else{
            Node par = test.parent;
            while(par != null && test != par.left){
                test = par.left;
                par = par.parent;
            }
            return test;
        }
    }

    private static Node getMostLeft(Node right) {
        while(right.left != null){
            right = right.left;
        }
        return right;
    }
```

## 17）判断一棵二叉树是否是平衡二叉树 

```java
    static boolean ans = true;

    public static boolean isBalance(Node node){
        getHeight(node, 1, ans);
        return ans;
    }

    public static int getHeight(Node node, int level, boolean isok){
        if(node == null)
            return level;
        int h1 = getHeight(node.left, level + 1, isok);
        if(!isok)
            return level;
        int h2 = getHeight(node.right, level + 1, isok);
        if(!isok)
            return level;
        if(Math.abs(h1 - h2) > 1){
            ans = false;
            isok = false;
        }
        return Math.max(h1, h2);
    }
```

## 18）判断一棵树是否是搜索二叉树 

```java
//层次遍历  ！！！ 层次！！！    
public static boolean isCBT(Node head){
        if(head == null)
            return false;
        Queue<Node> queue = new LinkedList<Node>();
        queue.offer(head);
        boolean leaf = false;
        while(!queue.isEmpty()){
            Node node = queue.poll();
            if(leaf && (node.left != null || node.right != null) || (node.left == null && node.right != null))
                return false;
            if(node.left != null)
                queue.offer(node.left);
            if(node.right != null)
                queue.offer(node.right);
            else
                leaf = true;
        }
        return true;
    }
```
## 19）已知一棵完全二叉树， 求其节点的个数 

要求： 时间复杂度低于O(N)， N为这棵树的节点个数 

```java
    static  int ans = 0;

    public static int nodeNum(Node head){
        if(head == null)
            return 0;
        int rightH = 0;
        if(head.right != null)
           rightH = getMostH(head.right,0);
        int height = getMostH(head,0);
        if(rightH + 1  == height){
            ans += (1 << (height - 1) ) + nodeNum(head.right);
            System.out.println("left full" + ans);
            return ans;
        }else{
            ans += (1 << rightH ) + nodeNum(head.left);
            System.out.println("right full" + ans);
            return ans;
        }

    }

    public static int getMostH(Node head, int level){
        while(head != null){
            head = head.left;
            level++;
        }
        return level;
    }
```

## 20）岛问题

1. 将遍历过的1改为2，清晰区别
2. 如果有连的，一次性全扫完，如此只需要遍历就可以了

```java
    private static int R;
    private static int C;

    private static int countIslands(int[][] m) {
        if(m == null)
            return 0;
        R = m.length;
        C = m[0].length;
        int ans = 0;
        for(int i = 0; i < R; i++)
            for(int j = 0; j < C; j++){
                if(m[i][j] == 1){
                    ans++;
                    infect(m, i ,j);
                }
            }
        return ans;
    }

    public static void infect(int[][] m ,int row, int col){
        if(row >= R || col >= C || col < 0 || row < 0 || m[row][col] != 1)
            return;
        m[row][col] = 2;
        infect(m,row - 1,col);
        infect(m,row + 1,col);
        infect(m,row,col + 1);
        infect(m,row,col - 1);
    }
```

## 21）前缀树

```java
public static class TrieNode{
        int end;
        int path;
        TrieNode[] next;

        public TrieNode(){
            end = 0;
            path = 0;
            next = new TrieNode[26];
        }
    }

    public static class Trie{
        TrieNode head;

        public Trie(){
            head = new TrieNode();
        }

        public void insert(String t){
            if(t == null)
                return;
            char[] str = t.toCharArray();
            TrieNode node = head;
            int index;
            for(int i = 0; i < str.length; i++){
                index = str[i] - 'a';
                if(node.next[index] == null){
                    node.next[index] = new TrieNode();
                }
                node = node.next[index];
                node.path++;
            }
            node.end++;
        }

        public  int search(String t){
            if(t == null)
                return 0;
            char[] str = t.toCharArray();
            TrieNode node = head;
            int index;
            for(int i = 0; i < str.length; i++){
                index = str[i] - 'a';
                if(node.next[index] == null){
                    return 0;
                }
                node = node.next[index];
            }
            return node.end;
        }

        public void delete(String t){
            if(search(t) == 0)
                return;
            char[] str = t.toCharArray();
            TrieNode node = head;
            int index;
            for(int i = 0; i < str.length; i++){
                index = str[i] - 'a';
                if(--node.next[index].path == 0){
                    node.next[index] = null;
                    return;
                }
                node = node.next[index];
            }
            node.end--;
        }

        public int prefixNumber(String t){
            if(t == null)
                return 0;
            char[] str = t.toCharArray();
            TrieNode node = head;
            int index;
            for(int i = 0; i < str.length; i++){
                index = str[i] - 'a';
                if(node.next[index] == null){
                    return 0;
                }
                node = node.next[index];
            }
            return node.path;
        }
    }
```
## 22）并差集

```java
public static class Node {
		// whatever you like
	}

	public static class UnionFindSet {
		public HashMap<Node, Node> fatherMap;
		public HashMap<Node, Integer> sizeMap;

		public UnionFindSet() {
			fatherMap = new HashMap<Node, Node>();
			sizeMap = new HashMap<Node, Integer>();
		}

		public void makeSets(List<Node> nodes) {
			fatherMap.clear();
			sizeMap.clear();
			for (Node node : nodes) {
				fatherMap.put(node, node);
				sizeMap.put(node, 1);
			}
		}

		private Node findHead(Node node) {
			Node father = fatherMap.get(node);
			if (father != node) {
				father = findHead(father);
			}
			fatherMap.put(node, father);
			return father;
		}
		
		public boolean isSameSet(Node a, Node b) {
			return findHead(a) == findHead(b);
		}

		public void union(Node a, Node b) {
			if (a == null || b == null) {
				return;
			}
			Node aHead = findHead(a);
			Node bHead = findHead(b);
			if (aHead != bHead) {
				int aSetSize= sizeMap.get(aHead);
				int bSetSize = sizeMap.get(bHead);
				if (aSetSize <= bSetSize) {
					fatherMap.put(aHead, bHead);
					sizeMap.put(bHead, aSetSize + bSetSize);
				} else {
					fatherMap.put(bHead, aHead);
					sizeMap.put(aHead, aSetSize + bSetSize);
				}
			}
		}

	}
```

## 23）字典排序

例如输入  "b" "ba" "cf" "de" ，结果输出应为“babcfde”

```java
	// 选对合适的贪心算法很重要
	public static class MyComparator implements Comparator<String>{
        @Override
        public int compare(String o1, String o2) {
            return (o1 + o2).compareTo(o2 + o1);
        }
    }

    public static String graSort(String[] strs){
        int len = strs.length;
        if(strs == null || len == 0)
            return "";
        Arrays.sort(strs,new MyComparator());
        String ans = "";
        for(int i = 0; i < len; i++)
            ans += strs[i];
        return  ans;
    }
```
## 24）切分金条

一块金条切成两半， 是需要花费和长度数值一样的铜板的。 比如长度为20的 金条， 不管切成长度多大的两半， 都要花费20个铜板。 一群人想整分整块金 条， 怎么分最省铜板？
例如,给定数组{10,20,30}， 代表一共三个人， 整块金条长度为10+20+30=60. 金条要分成10,20,30三个部分。 如果， 先把长度60的金条分成10和50， 花费60 再把长度50的金条分成20和30，花费50 一共花费110铜板。但是如果， 先把长度60的金条分成30和30， 花费60 再把长度30金条分成10和20， 花费30 一共花费90铜板。
输入一个数组， 返回分割的最小代价 

```java
public static class MaxComparator implements Comparator<Integer>{
        @Override
        public int compare(Integer o1, Integer o2) {
            return o2 - o1;
        }
    }

    public static class MinComparator implements Comparator<Integer>{
        @Override
        public int compare(Integer o1, Integer o2) {
            return o1 - o2;
        }
    }

    private static int minCost(int[] golden) {
        int len = golden.length;
        if(len == 0 || golden == null)
            return 0;
        PriorityQueue<Integer> minQueue = new PriorityQueue<Integer>(new MinComparator());
        PriorityQueue<Integer> maxQueue = new PriorityQueue<Integer>(new MaxComparator());
        for(int i = 0; i < len; i++){
            minQueue.offer(golden[i]);
        }
        int cost = 0;
        while(!minQueue.isEmpty()){
            while (maxQueue.isEmpty()){
                maxQueue.offer(minQueue.poll());
            }
            int minTemp = minQueue.poll();
            int maxTemp = maxQueue.poll();
            maxQueue.offer(maxTemp + minTemp);
            cost += (maxTemp + minTemp);
        }
        return cost;
    }
```

## 26）最多宣讲场次

一些项目要占用一个会议室宣讲， 会议室不能同时容纳两个项目的宣讲。 给你每一个项目开始的时间和结束的时间(给你一个数组， 里面 是一个个具体的项目)， 你来安排宣讲的日程， 要求会议室进行 的宣讲的场次最多。 返回这个最多的宣讲场次 

```java
public static class Program {
		public int start;
		public int end;

		public Program(int start, int end) {
			this.start = start;
			this.end = end;
		}
	}

	public static class ProgramComparator implements Comparator<Program> {

		@Override
		public int compare(Program o1, Program o2) {
			return o1.end - o2.end;
		}

	}

	public static int bestArrange(Program[] programs, int start) {
		Arrays.sort(programs, new ProgramComparator());
		int result = 0;
		for (int i = 0; i < programs.length; i++) {
			if (start <= programs[i].start) {
				result++;
				start = programs[i].end;
			}
		}
		return result;
	}
```
## 27）打印一个字符串的全部子序列，包括空字符串 

```java
static List list = new ArrayList();

    public static void getSubquences(char[] tempS, int index){
        if(index == tempS.length){
            list.add(String.valueOf(tempS));
            return;
        }
        char temp = tempS[index];
        getSubquences(tempS,index + 1);
        tempS[index] = ' ';
        getSubquences(tempS,index+1);
        tempS[index] = temp;
    }

    public static void main(String[] args) {
        String s = "abs";
        char[] arry = s.toCharArray();
        getSubquences(arry,0);
        for(Object l : list)
            System.out.println(l);
    }
```
## 28）二维数组最小路径和

给你一个二维数组， 二维数组中的每个数都是正数， 要求从左上角走到右下角， 每一步只能向右或者向下。 沿途经过的数字要累加起来。 返回最小的路径和 

```java
    // 开辟dp缓存 避免重复计算
	public static int minPath(int[][] m){
        if(m == null)
            return 0;
        int R = m.length;
        int C = m[0].length;
        int[][] dp = new int[R][C];
        dp[R - 1][C - 1] = m[R -1][C - 1];
        for(int i = R - 2; i >= 0; i--){
            dp[i][C - 1] = m[i][C - 1] + dp[i + 1][C - 1];
        }
        for(int i = C - 2; i >= 0; i--){
            dp[R - 1][i] = m[R - 1][i] + dp[R - 1][i + 1];
        }
        for(int i = R - 2; i >= 0 ; i--)
            for(int j = C - 2; j >= 0;j--){
                dp[i][j] = Math.min(m[i][j] + dp[i+1][j],m[i][j]+dp[i][j+1]);
            }
        return dp[0][0];
    }
```
## 29）滑动窗口

如上应返回 555467

```java
private static int[] getMaxW(int[] arr, int window) {
        if(arr == null || window > arr.length || window < 1)
            return null;
        LinkedList<Integer> max = new LinkedList<>();
        int[] ans = new int[arr.length - window + 1];
        int index = 0;
        for(int i = 0; i < arr.length;i++){
            while (!max.isEmpty() && arr[max.peekLast()] < arr[i]){
                max.pollFirst();
            }
            max.addLast(i);
            if(max.peekFirst() == i - window){
                max.pollFirst();
            }
            if(i >= window - 1)
                ans[index++] = arr[max.peekFirst()];
        }
        return ans;
    }
```
## 30）子数组最大差值小于某阈值，求满足条件的子数组个数

解法思路：

 　　本题其实是滑动窗口的变形。主体思路为：

　　１．从第一个元素开始依次向后遍历，同时维护两个窗口（由于要同时操作窗口的头部和尾部，故采用双端队列）：

　　　　　　最大值窗口（递减），头部永远存最大值

　　　　　　最小值窗口（递增），头部永远存最小值

　　２．比较两个窗口的头部元素差值，若差值大于阈值，即可跳出内循环。

　　３．跳出内循环后，检查头部元素是否过期，若过期，则清除。

复杂度：

　　时间复杂度：O(n)，注意虽然是两层循环，但元素只从滑动窗口尾部进，从头部清除，只是顺序扫描了一遍。

　　空间复杂度：O(n),这里利用两个滑动窗口分别保存最大值和最小值。

```java
    private static int getNum(int[] arr, int num) {
        if(arr == null || arr.length < 1)
            return 0;
        int res = 0;
        int i = 0;
        int j = 0;
        LinkedList<Integer> max = new LinkedList<>();
        LinkedList<Integer> min = new LinkedList<>();
        while(i < arr.length) {
            while (j < arr.length) {
                while (!min.isEmpty() && arr[min.peekLast()] >= arr[j]) {
                    min.pollLast();
                }
                min.addLast(j);
                while (!max.isEmpty() && arr[max.peekLast()] <= arr[j]) {
                    max.pollLast();
                }
                max.addLast(j);
                if (arr[max.peekFirst()] - arr[min.peekFirst()] > num)
                    break;
                j++;
            }
            if(min.peekFirst() == i)
                min.pollFirst();
            if(max.peekFirst() == i)
                max.pollFirst();
            res += j - i;
            i++;
        }
        return res;
    }
```
## 31）最大子矩阵大小

```java
public static int maxRecSize(int[][] map){
        if(map == null || map.length == 0 || map[0].length == 0)
            return 0;
        int maxArea = 0;
        int rowL = map.length;
        int colL = map[0].length;
        int[] height = new int[colL];
        for(int i = 0; i < rowL; i++){
            for(int j = 0; j < colL; j++){
                height[j] += map[i][j] == 0 ? 0 : height[j] + 1;
            }
            maxArea = Math.max(maxArea,getArea(height));
        }
        return  maxArea;
    }

    public static int getArea(int[] height){
        if(height == null || height.length == 0)
            return 0;
        Stack<Integer> stack = new Stack<>();
        int maxArea = 0;
        int len = height.length;
        for(int i = 0; i < len; i++){
            while (!stack.isEmpty() && height[stack.peek()] >= height[i]){
                int j = stack.pop();
                int k = stack.isEmpty() ? -1 : stack.peek();
                int areaTemp = (i - k -1)*height[j];
                maxArea = Math.max(maxArea,areaTemp);
            }
            stack.add(i);
        }
        while (!stack.isEmpty()){
            int j = stack.pop();
            int k = stack.isEmpty() ? -1 : stack.peek();
            int areaTemp = (len - k -1)*height[j];
            maxArea = Math.max(maxArea,areaTemp);
        }
        return  maxArea;
    }
```
## 32）累加和为aim的最长子数组

如7,3,2,1,1,7,7,7  求aim的最长子数组的长度为4

```java
    public static int maxLength(int[] arr, int aim){
        if(arr == null || arr.length == 0)
            return 0;
        int sum = 0;
        int maxLen = 0;
        HashMap<Integer,Integer> map = new HashMap<>();
        map.put(0,-1);
        for(int i = 0,len = arr.length; i < len; i++){
            sum += arr[i];
            if(map.containsKey(sum - aim)){
                int last = map.get(sum - aim);
                maxLen = Math.max(i - last,maxLen);
            }
            if(!map.containsKey(sum))
                map.put(sum,i);
        }
        return  maxLen;
    }
```
## 34）最大子搜索二叉树

```java
    public static class ReturnData{
        int size;
        Node node;
        int max;
        int min;

        public ReturnData(int size, Node node, int max, int min){
            this.size = size;
            this.max = max;
            this.min = min;
            this.node = node;
        }
    }

    public static Node biggestSubBST(Node head){
        ReturnData ans = process(head);
        return ans.node;
    }

    public static ReturnData process(Node head){
        if(head == null)
            return new ReturnData(0,null,Integer.MIN_VALUE,Integer.MAX_VALUE);
        ReturnData left = process(head.left);
        ReturnData right = process(head.right);
        if(left.node == head.left && right.node == head.right
        && head.value > left.max && head.value < right.min){
                return new ReturnData(left.size + 1 + right.size,head,
                        right.max > head.value ? right.max : head.value,
                        left.min < head.value ? left.min :head.value);
        }
        if(left.size > right.size)
            return new ReturnData(left.size,left.node,left.max,left.min);
        return new ReturnData(right.size,right.node,right.max,right.min);
    }
```
## 35）二叉树的最远距离

二叉树中， 一个节点可以往上走和往下走， 那么从节点A总能走到节点B。
节点A走到节点B的距离为： A走到B最短路径上的节点个数。
求一棵二叉树上的最远距离 

```java
    public static class ReturnData{
        public int h;
        public int maxD;

        public ReturnData(int h,int maxD){
            this.h = h;
            this.maxD = maxD;
        }
    }

    public static int maxDistance(Node head){
        if(head == null)
            return 0;
        ReturnData ans = process(head);
        return ans.maxD;
    }

    private static ReturnData process(Node head) {
        if(head == null)
            return new ReturnData(0,0);
        ReturnData ldata = process(head.left);
        ReturnData rdata = process(head.right);
        int curH = ldata.h + rdata.h + 1;
        int maxD = Math.max(curH,Math.max(ldata.maxD, rdata.maxD));
        int maxH = Math.max(rdata.h,ldata.h) + 1;
        return new ReturnData(maxH,maxD);
    }
```
## 36）最大活跃值

一个公司的上下节关系是一棵多叉树， 这个公司要举办晚会， 你作为组织者已经摸清了大家的心理： 一个员工的直
接上级如果到场， 这个员工肯定不会来。 每个员工都有一个活跃度的值， 决定谁来你会给这个员工发邀请函， 怎么
让舞会的气氛最活跃？ 返回最大的活跃值。
举例：
给定一个矩阵来表述这种关系
matrix =
{ 1,6
1,5
1,4
} 这个矩阵的含义是：
matrix[0] = {1 , 6}， 表示0这个员工的直接上级为1,0这个员工自己的活跃度为6
matrix[1] = {1 , 5}， 表示1这个员工的直接上级为1（他自己是这个公司的最大boss） ,1这个员工自己的活跃度为5
matrix[2] = {1 , 4}， 表示2这个员工的直接上级为1,2这个员工自己的活跃度为4
为了让晚会活跃度最大， 应该让1不来， 0和2来。 最后返回活跃度为10 

```java
public static class Node{
        int happy;
        List<Node> nexts;

        public Node(int happy){
            this.happy = happy;
            nexts = new ArrayList<>();
        }
    }

    public static class ReturnData{
        public int accept;
        public int refuse;

        public ReturnData(int a, int r){
            this.accept = a;
            this.refuse = r;
        }
    }

    public static ReturnData process(Node head){
        if(head == null)
            return new ReturnData(0,0);
        int accept = head.happy;
        int refuse = 0;
        for(int i = 0; i < head.nexts.size(); i++){
            Node temp = head.nexts.get(i);
            ReturnData rd = process(temp);
            accept += rd.refuse;
            refuse += Math.max(rd.accept, rd.refuse);
        }
        return new ReturnData(accept,refuse);
    }

    public static int maxHappy(Node head){
        ReturnData ans = process(head);
        return Math.max(ans.accept,ans.refuse);
    }
```
## 37）博弈取数

有一排正数，玩家A和玩家B都可以看到。每位玩家在拿走数字的时候，都只能从最左和最右的数中选择一个。玩家A先拿，玩家B再拿，两人交替拿走所有的数字，两人都力争自己拿到的数的总和比对方多。请返回最后获胜者的分数。
例如：
5,2,3,4
玩家A先拿，当前他只能拿走5或者4。
如果玩家A拿走5，那么剩下2，3，4。轮到玩家B，此时玩家B可以选择2或4中的一个，…
如果玩家A拿走4，那么剩下5，2，3。轮到玩家B，此时玩家B可以选择5或3中的一个，…

```java
public static void main(String[] args) {
        int[] test = {5,2,3,4,51,23,5,6};
        System.out.println(getWinOne(test));
        System.out.println(getWinTwo(test));
    }

    private static int getWinTwo(int[] arr) {
        int len = arr.length;
        if(arr == null || len == 0)
            return 0;
        int sum = 0;
        for(int i = 0; i < len; i++)
            sum += arr[i];
        int[][] dp = new int[len][len];
        for(int i = 0; i < len; i++)
            dp[i][i] = arr[i];
        for (int i = 0; i < len - 1; i++)
            dp[i][i+1] = Math.max(arr[i],arr[i + 1]);
        for (int k = 2; k < arr.length; k++) {
            for (int j = k; j < arr.length; j++) {
                int i = j - k;
                dp[i][j] = Math.max(arr[i] + Math.min(dp[i + 2][j], dp[i + 1][j - 1]),
                        arr[j] + Math.min(dp[i + 1][j - 1], dp[i][j - 2]));
            }
        }
        return Math.max(dp[0][len - 1], sum - dp[0][len - 1]);
    }

    private static int getWinOne(int[] test) {
        int len = test.length;
        if(test == null || len == 0)
            return 0;
        int sum = 0;
        for(int i = 0; i < len; i++)
            sum += test[i];
        int maxA = firstP(test, 0, len - 1);
        return Math.max(maxA, sum - maxA);
    }

    public static int firstP(int[] arr, int from, int to){
        if(from == to)
            return arr[from];
        if(to - from == 1)
            return Math.max(arr[from],arr[to]);
        int max = 0;
        max = Math.max(arr[from] + Math.min(firstP(arr,from + 1, to - 1),firstP(arr, from + 2, to)),
                    arr[to] + Math.min(firstP(arr, from + 1, to - 1),firstP(arr, from, to - 2)));
        return max;
    }
```
## 38）Top K问题

```java
    public static class Node{
        String str;
        int times;

        public Node(String s, int times){
            this.str = s;
            this.times = times;
        }
    }

    public static class TopKRecord{
        Node[] heap;
        int index;
        HashMap<String,Node> strNodeMap;
        HashMap<Node,Integer> indexNodeMap;

        public TopKRecord(int size){
            heap = new Node[size];
            index = 0;
            strNodeMap = new HashMap<>();
            indexNodeMap = new HashMap<>();
        }

        public void add(String str){
            if(str == null)
                return;
            int preIndex = -1;
            Node currNode = null;
            if(!strNodeMap.containsKey(str)){
                currNode = new Node(str,1);
                strNodeMap.put(str,currNode);
                indexNodeMap.put(currNode,-1);
            }else{
                currNode = strNodeMap.get(str);
                preIndex = indexNodeMap.get(currNode);
                currNode.times++;
            }
            if(preIndex == -1){
                if(index == heap.length){
                    if(currNode.times > heap[0].times){
                        indexNodeMap.put(currNode,0);
                        indexNodeMap.put(heap[0],-1);
                        heap[0] = currNode;
                        heapify(0,index);
                    }
                }else{
                    indexNodeMap.put(currNode,index);
                    heap[index] = currNode;
                    heapInsert(index++);
                }
            }else{
                heapify(preIndex,index);
            }
        }

        public void heapInsert(int index){
            while(index > 0){
                int parent = (index - 1)/2;
                if(heap[parent].times > heap[index].times){
                    swap(parent,index);
                    index = parent;
                }else
                    break;
            }
        }

        public void heapify(int index,int size){
            int l = index*2 + 1;
            int r = index*2 + 1;
            int smallest = index;
            while (l < size){
                if(heap[smallest].times > heap[l].times)
                   smallest = l;
                if(r < size && heap[smallest].times > heap[r].times)
                    smallest = r;
                if(smallest != index)
                    swap(smallest, index);
                else
                    break;
                index = smallest;
                l = index*2 + 1;
                r = index*2 + 2;
            }

        }

        public void printTopK(){
            for(int i = 0; i != heap.length; i++){
                if(heap[i] == null)
                    break;
                System.out.println(heap[i].str + "..."+ heap[i].times);
            }
        }

        public void swap(int index1, int index2) {
            indexNodeMap.put(heap[index1], index2);
            indexNodeMap.put(heap[index2], index1);
            Node tmp = heap[index1];
            heap[index1] = heap[index2];
            heap[index2] = tmp;
        }
    }
```
## 39）从上往下打印二叉树节点

从上往下打印出二叉树的每个节点，同层节点从左至右打印。

```java
    public class Solution {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        Queue<Integer> queue = new LinkedList<Integer>();
        ArrayList<Integer> list = new ArrayList<Integer>();
        if(root == null)
            return list;
        queue.add(root.val);
        allTree(root,queue);
        for(Integer q : queue){
            list.add(q);
        }
        return list;
    }
    
    public static void allTree(TreeNode root,Queue<Integer> queue) {
        if(root == null)
            return ;
        if(root.left != null)
            queue.add(root.left.val);
        if(root.right != null)
            queue.add(root.right.val);
        if(root.left != null)
            allTree(root.left,queue);
        if(root.right != null)
            allTree(root.right,queue);
    }
}
```
