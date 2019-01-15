# Leetcode 初级算法 - 链表

二级标题格式：\[章节内题号\] \[题库内题号\] \[题目标题\]

这一章节学的不是很好...

## 1 70 爬楼梯

我的思路：先想了一个递归的解法 发现跑的非常慢 才意识到需要一个动态规划的解法...

递归 12s 巨慢
```java
public int climbStairs(int n) {
    if(n==0) return 0;
    if(n==1) return 1;
    if(n==2) return 2;
    return climbStairs(n-1)+climbStairs(n-2);
}
```

动态规划 全数组

```java
public int climbStairs(int n) {
    int[] arr = new int[n+1>=3?n+1:3];
    arr[0]=0;arr[1]=1;arr[2]=2;
    for(int i=3;i<=n;i++){
        arr[i]=arr[i-1]+arr[i-2];
    }
    return arr[n];
}
```

其他思路：实际上因为只需要保存最后的两步 所以只需要一个大小为2的数组就好了...  [来源](https://blog.csdn.net/happyaaaaaaaaaaa/article/details/48948247)

```java
public int climbStairs(int n) {
int[] ways = {1, 1};
for (int i = 1; i < n; i++) {
    int temp = ways[1];
    ways[1] += ways[0];
    ways[0] = temp;
}
return ways[1];
```

更神奇的做法：利用斐波那契公式 复杂度可以降到O(log n)（为什么不是O(1)：因为取n次方的复杂度是O(log n) 通过递归的方式来求幂 [平方求幂（快速幂）](https://zh.wikipedia.org/wiki/%E5%B9%B3%E6%96%B9%E6%B1%82%E5%B9%82)  [另一个更容易理解的解释](https://www.geeksforgeeks.org/write-a-c-program-to-calculate-powxn/)）



## 2 121 买卖股票的最佳时机

我的思路：每一步检查当前值和最小值 如果当前值小于最小值 不仅要更新最小值 还要更新最大值... 换言之 最大值只会出现在最小值之后

```java
public int maxProfit(int[] arr) {
    if(arr==null||arr.length==0||arr.length==1) return 0;
    int min=arr[0], max=arr[0], known=0;
    for(int i=1;i<arr.length;i++){
        if(arr[i]<min){
            min = arr[i];
            max = min;
        } else {
            if(arr[i]>max){
                max = arr[i];
            }
            if(max-min>known){
                known = max-min;
            }
        }
    }
    return known;
}
```

其他思路：基本思路不变 但是只需要记录两个：minprice和maxprofit

每一个数组中的值肯定会更新两个值中的一个...

```java
public int maxProfit(int prices[]) {
    int minprice = Integer.MAX_VALUE;
    int maxprofit = 0;
    for (int i = 0; i < prices.length; i++) {
        if (prices[i] < minprice)
            minprice = prices[i]; // no need to modify maxprofit, since it shouldn't change now
        else if (prices[i] - minprice > maxprofit) 
            maxprofit = prices[i] - minprice;
    }
    return maxprofit;
}
```



## 3 53 最大子序和

我的思路：第一反应是n2的解法 复杂度显然太高 直接忽略

然后想到的是前缀数组 求sums[] 然后利用前一问的结果 这样的复杂度是O(n)的 但是实现出来略有麻烦 不够优雅... （坑：对于全部是负数的情况需要特判 从所有数里直接选一个最大的）

分治法的思路没想到...是对两个端点分别二分吗？

```java
public int maxSubArray(int[] nums) {
    if(nums==null||nums.length==0) return 0;
    if(nums.length==1) return nums[0];
    int[] sums = new int[nums.length+1];
    int n = nums.length;
    boolean allneg = true;
    int arrmax = nums[0];
    for(int i=0;i<n;i++){
        sums[i+1]=sums[i]+nums[i];
        if(nums[i]>0) allneg = false;
        if(nums[i]>arrmax) arrmax = nums[i];
    }
    // judge always negative
    if(allneg){
        return arrmax;
    }
    // use last problem's solution
    int minpos=0, maxpos=0;
    int knowndist=0, lastminpos=0, lastmaxpos=0;
    for(int i=1;i<n+1;i++){
        if(sums[i]<sums[minpos]) {
            minpos=i;
            maxpos=i;
        } else if(sums[i]-sums[minpos]>knowndist){
            knowndist=sums[i]-sums[minpos];
            lastminpos=minpos;
            lastmaxpos=i;
            maxpos=i;
        }
    }
    return sums[lastmaxpos]-sums[lastminpos];
}
```

其他思路：我的做法并不是动态规划... 动态规划需要拆分出子问题

在这里实际上子问题是求 A[0,i] 的最小值 用 `maxSubArray(int A[], int i)` 表示 那么就很好得到限制条件 

`maxSubArray(A, i) = maxSubArray(A, i - 1) > 0 ? maxSubArray(A, i - 1) : 0 + A[i]; `

然后就可以写了...用一个DP数组即可...复杂度O(n)

```java
// source: https://leetcode.com/problems/maximum-subarray/discuss/20193/DP-solution-and-some-thoughts
public int maxSubArray(int[] nums) {
    if(nums==null||nums.length==0) return 0;
    if(nums.length==1) return nums[0];
    int[] dp = new int[nums.length];
    dp[0]=nums[0];
    int max=dp[0];
    
    for(int i=1;i<nums.length;i++){
        dp[i]=(dp[i-1]>0?dp[i-1]:0)+nums[i];
        max = Math.max(max,dp[i]);
    }
    return max;
}
```

其他思路：分治法 复杂度 O(n logn) [来源](https://www.geeksforgeeks.org/maximum-subarray-sum-using-divide-and-conquer-algorithm/)

先把整个数组分成左右两半 那么总的最大值=max(左边最大,右边最大,跨过中点的最大)

左右两部分显然都是子问题 直接递归就好 问题是跨过中点的最大如何计算：从中点开始 向左右两侧延伸 最后再拼起来...

递归表达式 T(n) = 2T(n/2) + Θ(n) 复杂度 O(n logn) 

## 4 198 打家劫舍

我的思路：啊...没想出来...

一开始的思路是贪心 但是这样未必能保证全局最优...

但是感觉没法确定子问题...

其他思路：参考[这篇文章](https://leetcode.com/problems/house-robber/discuss/156523/From-good-to-great.-How-to-approach-most-of-DP-problems.) 介绍了解决DP问题的一般思路

1. Find recursive relation
2. Recursive (top-down)
3. Recursive + memo (top-down)
4. Iterative + memo (bottom-up)
5. Iterative + N variables (bottom-up)

以这个题目为例子 

第1步 主要问题是找出递推式

我之前一直在从前向后找 但是实际上应该反过来从后向前

`rob(i) = Math.max( rob(i - 2) + currentHouseValue, rob(i - 1) )`

然后可以很快完成2 3

然后从3到4 注意到memo[0]=max(arr[-2]+arr[0],arr[-1]), memo[1]=~~arr[1]~~ max(arr[-1]+arr[1],arr[0])  (**此处巨坑**) 

（备注 方便理解 arr[-2]=arr[-1]=0  ）

然后的memo[2]就是按照递推公式了

然后从4到5就很简单了 因为每次实际上只需要保留两个值 所以只需要一个size=2的数组就行了

最终的解决：

```java
ublic int rob(int[] nums) {
    if(nums==null||nums.length==0) return 0;
    if(nums.length==1) return nums[0];
    if(nums.length==2) return Math.max(nums[0],nums[1]);
    int[] sum = new int[]{nums[0],Math.max(nums[0],nums[1])};
    int cnt = 2;
    while(cnt<nums.length){
        int tmp = Math.max(sum[0]+nums[cnt],sum[1]);
        sum[0] = sum[1];
        sum[1] = tmp;
        cnt++;
    }
    return Math.max(sum[0],sum[1]);
}
```

其他人的解答

```
// source: https://leetcode.com/problems/house-robber/discuss/156523/From-good-to-great.-How-to-approach-most-of-DP-problems.

public int rob(int[] nums) {
    if (nums.length == 0) return 0;
    int prev1 = 0;
    int prev2 = 0;
    for (int num : nums) {
        int tmp = prev1;
        prev1 = Math.max(prev2 + num, prev1);
        prev2 = tmp;
    }
    return prev1;
}
```

