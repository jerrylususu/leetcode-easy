# Leetcode 初级算法 - 排序和搜索

二级标题格式：\[章节内题号\] \[题库内题号\] \[题目标题\]

## 1 88 合并两个有序数组

我的思路：直接用三个指针 从nums1的后面往前面移动 从大的合并到小的。

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int c1=m-1,c2=n-1,c3=m+n-1;
    while(c1>=0&&c2>=0){
        if(nums1[c1]<nums2[c2]){
            nums1[c3--]=nums2[c2--];
        } else {
            nums1[c3--]=nums1[c1--];
        }
    }
    if(c1==-1&&c2!=-1){
        while(c2!=-1){
            nums1[c3--]=nums2[c2--];
        }
    }
}
```

其他思路：基本上都相同..顶多是语法上更简单...

## 2 278 第一个错误的版本

我的思路：这一看就是二分... 

**注意** 1. mid需要用 l + (r-l)/2 防止溢出...测试样例比较强 2. 二分退出之后未必一定成功 还需要一个while验证

```java
public int firstBadVersion(int n) {
    int l=0,r=n,mid=-1;
    while(l<=r){
        mid=l+(r-l)/2;
        if(isBadVersion(mid)){
            r=mid-1;
        } else {
            l=mid+1;
        }
    }
    while(!isBadVersion(mid)){
        mid++;
    }
    return mid;
}
```

其他思路：通过恰当设置二分 可以避免最后的while

为什么这样可行？如果当前mid是bad version，那么bad version的区间应该是 [left,mid] 

反之 如果当前mid不是bad version，那么bad version的区间应该是[mid+1,right]

二分搜索本身是inclusive的 所以前者应该是right=mid 后者应该是left=mid+1

同理 搜索的时候l=0, r=len-1

```java
// source:https://leetcode.com/problems/first-bad-version/solution/

public int firstBadVersion(int n) {
    int left = 1;
    int right = n;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (isBadVersion(mid)) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }
    return left;
}
```

