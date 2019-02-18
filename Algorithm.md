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
