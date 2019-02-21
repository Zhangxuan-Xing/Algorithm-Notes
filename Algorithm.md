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

    //先断开  后连接  确保顺序
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
