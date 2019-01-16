# Leetcode 初级算法 - 设计问题

二级标题格式：\[章节内题号\] \[题库内题号\] \[题目标题\]

## 1 384 打乱数组 (Shuffle an Array)

我的思路：一开始我想的是通过Random得到一个随机数 然后用一个boolean[] used来跳过已经被用过的数字... 但后来意识到这样做太慢了... （而且似乎结果会报错...）

然后就改了思路 用类似于选择排序的做法 先从前面[0,n-i-1]个数中间随便选一个数 和后面n-i-1位置上的数字交换 [洗牌算法 Fisher-Yates Algorithm](https://www.w3cplus.com/javascript/shuffling-array-js.html) （小坑：System.arraycopy 全是小写）

```java
class Solution {

    int[] current;
    int[] original;
    
    public Solution(int[] nums) {
        this.original = nums;
    }
    
    /** Resets the array to its original configuration and return it. */
    public int[] reset() {
        this.current = this.original;
        return this.current;
    }
    
    /** Returns a random shuffling of the array. */
    public int[] shuffle() {
        int n = this.original.length;
        this.current = new int[n];
        System.arraycopy(this.original,0,this.current,0,n);
        Random r = new Random();
        for(int i=0;i<n;i++){
            int pos = r.nextInt(n-i);
            swap(this.current,pos,n-1-i);
        }
        return this.current;
    }
    
    public static void swap(int[] arr, int a, int b){
        int tmp = arr[a];
        arr[a]=arr[b];
        arr[b]=tmp;
    }
}
```

其他思路：还能直接暴力...两个list 每次从第一个里随机取出放到第二个...

## 2 155 最小栈

我的思路：显然是双栈法

坑：如果smin是空的 就peek不出来 需要加一个特判

```java
class MinStack {
    
    Stack<Integer> s;
    Stack<Integer> smin;

    /** initialize your data structure here. */
    public MinStack() {
        s = new Stack<>();
        smin = new Stack<>();
    }
    
    public void push(int x) {
        s.push(x);
        if(smin.size()==0) smin.push(x);
        else smin.push(x<smin.peek()?x:smin.peek());
    }
    
    public void pop() {
        s.pop();
        smin.pop();
    }
    
    public int top() {
        return s.peek();
    }
    
    public int getMin() {
        return smin.peek();
    }
}

```

其他思路：可以通过存储最小值和当前值的差值 使用一个栈完成 [来源](https://leetcode.com/problems/min-stack/discuss/49031/Share-my-Java-solution-with-ONLY-ONE-stack)

具体实现：存一个min 栈上存储current-min 如果current比min小就更新min
在pop的时候 如果发现栈顶是一个负数 那么说明最小值在这个操作被更新了 pop的时候不仅需要min+pop() 还需要把最小值恢复到上一个状态 即min=min-peek()

坑：需要全程用Long 输出的时候再转换回去 （最大差值可能是Integer.MAX_VALUE-Integer.MIN_VAUE）

```java
// source:https://leetcode.com/problems/min-stack/discuss/49031/Share-my-Java-solution-with-ONLY-ONE-stack

public class MinStack {
    long min;
    Stack<Long> stack;

    public MinStack(){
        stack=new Stack<>();
    }
    
    public void push(int x) {
        if (stack.isEmpty()){
            stack.push(0L);
            min=x;
        }else{
            stack.push(x-min);//Could be negative if min value needs to change
            if (x<min) min=x;
        }
    }

    public void pop() {
        if (stack.isEmpty()) return;
        
        long pop=stack.pop();
        
        if (pop<0)  min=min-pop;//If negative, increase the min value
        
    }

    public int top() {
        long top=stack.peek();
        if (top>0){
            return (int)(top+min);
        }else{
           return (int)(min);
        }
    }

    public int getMin() {
        return (int)min;
    }
}
```

