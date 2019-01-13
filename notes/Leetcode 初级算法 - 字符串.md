# Leetcode 初级算法 - 字符串

二级标题格式：\[章节内题号\] \[题库内题号\] \[题目标题\]

## 3 387 字符串中的第一个唯一字符

我的思路：

用了两个数组 一个存储首次出现时间 一个存储总共出现次数

最后把这两个数组同时扫一遍 以此确定第一个唯一字符

（题目中已经说明了只有小写字母会涉及）

其他思路：

直接用 HashMap 存储(字母,次数) 先扫完一次字符串 然后从头开始检查出现次数

或者用 indexOf 和 lastIndexOf 得到字符出现的位置 检测是否重合

```java
public int firstUniqChar(String s) {
    int len = s.length();
    int[] first = new int[26];
    int[] time = new int[26];
    for (int i = 0; i < len; i++) {
        int pos = s.charAt(i)-97; // a->97 in ascii
        if(time[pos]==0){
            first[pos]=i+1;
            time[pos]=1;
        } else {
            time[pos]++;
        }
    }
    int minpos = Integer.MAX_VALUE;
    for (int i = 0; i < 26; i++) {
        if(first[i]<minpos&&time[i]==1){
            minpos=first[i];
        }
    }
    if(minpos==Integer.MAX_VALUE){
        return -1;
    } else {
        return minpos-1;
    }
}
```

 

## 4 242 有效的字母异位词

异位词：两个单词由相同的字母 但是不同的顺序组成 则互为异位词

问题：一定要求字母的出现次数相同吗？(根据测试 似乎是的)

我的思路：先判定长度是否相等 再转成 char array 然后直接 char array 排序比较 (写起来方便一点...)

其他思路：用数组记录每个字母出现的次数 然后比较（复杂度上从O(nlogn)降到了O(n))

```java
public boolean isAnagram(String s, String t) {
    if(s.length()!=t.length()){
        return false;
    }
    char[] a1 = s.toCharArray();
    char[] a2 = t.toCharArray();
    Arrays.sort(a1);
    Arrays.sort(a2);
    for (int i = 0; i < a1.length; i++) {
        if(a1[i]!=a2[i]){
            return false;
        }
    }
    return true;
}
```

## 5 125 验证回文字符串

我的思路：示例看起来不太友好 大小写混合 还有不参与的字符

因此先把字符串转成小写 然后分别从头部和尾部往回比对 遇到不参与比较的字符跳过

其他思路：看到个正则表达式的... 直接用`[^0-9a-zA-Z]+`来判定合法字符

还有的用StringBuffer翻转 然后直接用equals方法比较

```java
public static boolean isPalindrome(String s) {
    int i=0,j=s.length()-1;
    while(i<j){
        while (!isValidChar(s,i)&&i<j) i++;
        while (!isValidChar(s,j)&&i<j) j--;
        System.out.println(i+" "+j);
        System.out.println(s.charAt(i)+" "+s.charAt(j));
        if(i<j){
            if(s.charAt(i)!=s.charAt(j)){
                return false;
            } else {
                i++;j--;
            }
        }
    }
    return true;
}

public static boolean isValidChar(String s, int pos){
    int a = s.charAt(pos);
    return ((48<=a&&a<=57)||(65<=a&&a<=90)||(97<=a&&a<=122));
}
```

## 6 8 字符串转换整数 (atoi)

题目本身写的很详细了 按照这个步骤写就好了

我的思路：跳过开头字符->判断正负号->串联数字->Integer.parseInt->处理异常

被卡的坑：`  -42`  (最后忘了把正负号加回去), `+1` (只处理了负号), `+-2` (???), `"  +0 123"` (应该对是否开始parse进行判断)

最后用了三个boolean来表示状态：是否正数 是否有正负号 是否正在parse

其他思路：我这里有点偷懒 用的是自带的解析 应该按照个位十位一层一层加上去 （类似于RK算法那样每次乘10 就不用判断是第几位了） 同时边加 边处理溢出 （处理溢出：与MAX_VALUE进行比较）[来源](https://leetcode.com/problems/string-to-integer-atoi/discuss/4643/Java-Solution-with-4-steps-explanations)

```java
public static int myAtoi(String str) {
    StringBuilder buffer = new StringBuilder();
    int len = str.length();
    boolean isPositive = true, marked = false, doing = false;
    for (int i = 0; i < len; i++) {
        char c = str.charAt(i);
        if(c==' '&&!doing){
            continue;
        }
        if(c=='+'){
            if(!marked){
                marked = true;
                doing = true;
            } else
                break;
        } else if(c=='-') {
            if(!marked) {
                isPositive = false;
                marked = true;
                doing = true;
            }else
                break;
        }else if(48<=c&&c<=57){
            if(!marked){
                marked = true;
            }
            buffer.append(c);
            doing = true;
        }else {
            break;
        }
    }
    int res = 0;
    if(buffer.length()==0){
        return 0;
    }
    try{
        res = Integer.parseInt(buffer.toString());
        res = isPositive?res:-res;
    } catch (NumberFormatException e){
        res = isPositive?Integer.MAX_VALUE:Integer.MIN_VALUE;
    }
    return res;
}
```



```java
// source: https://leetcode.com/problems/string-to-integer-atoi/discuss/4643/Java-Solution-with-4-steps-explanations

public int myAtoi(String str) {
    int index = 0, sign = 1, total = 0;
    //1. Empty string
    if(str.length() == 0 || str.trim().length() == 0) return 0;

    //2. Remove Spaces
    while(str.charAt(index) == ' ' && index < str.length())
        index ++;

    //3. Handle signs
    if(str.charAt(index) == '+' || str.charAt(index) == '-'){
        sign = str.charAt(index) == '+' ? 1 : -1;
        index ++;
    }
    
    //4. Convert number and avoid overflow
    while(index < str.length()){
        int digit = str.charAt(index) - '0';
        if(digit < 0 || digit > 9) break;

        //check if total will be overflow after 10 times and add digit
        if(Integer.MAX_VALUE/10 < total || Integer.MAX_VALUE/10 == total && Integer.MAX_VALUE %10 < digit)
            return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;

        total = 10 * total + digit;
        index ++;
    }
    return total * sign;
}
```



## 7 28 实现strStr()

就是个字符串搜索...

我的思路：直接用了之前的KMP板子（但是似乎很慢？）其实也可以考虑 RK

其他思路：直接暴力 似乎反而还更快...

```java
public int strStr(String haystack, String needle) {
    if(needle.length()==0) return 0;
    return runKMP(haystack,needle,myNext(needle.toCharArray()));
}

public static int[] myNext(char[] p){
    int[] next = new int[p.length+1];
    next[0]=-1;
    int i=0,j=-1;
    while (i<p.length){
        if(j==-1||p[i]==p[j]){
            next[i+1]=j+1;
            i++;j++;
        }else{
            j=next[j];
        }
    }
    return next;
}

public static int runKMP(String t, String p, int[] next){
    int c1=0,c2=0;
    while(c1<t.length()){
        if(c2==-1||t.charAt(c1)==p.charAt(c2)){
            c1++;c2++;
        } else {
            c2=next[c2];
        }
        if(c2==p.length()){
            return c1-c2;
        }
    }
    return -1;
}
```

## 8 38 报数

我的思路：直接用循环 用2个StringBuilder 1个存储上一次的结果 1个计算下一轮

其他思路：基本上都相同...

```java
public static String countAndSay(int n) {
    StringBuilder s1 = new StringBuilder("1");
    StringBuilder s2 = new StringBuilder();
    for (int i = 1; i < n; i++) {
        char digit = s1.charAt(0);
        int len = 1;
        for (int j = 1; j < s1.length(); j++) {
            if(s1.charAt(j)==digit){
                len++;
            } else {
                s2.append(Integer.toString(len));
                s2.append(digit);
                digit = s1.charAt(j);
                len = 1;
            }
        }
        s2.append(Integer.toString(len));
        s2.append(digit);
        s1.delete(0,s1.length());
        s1.append(s2);
        s2.delete(0,s2.length());
    }
    return s1.toString();
}
```



## 9 14 最长公共前缀

我的思路：直接全体一列列比较

坑：注意内外层循环 一开始我只跳出了一层

其他解法：似乎都差不多 还有二分的

也可以直接建立trie树 然后找分叉点

```
public static String longestCommonPrefix(String[] strs) {
    if(strs.length==0){
        return "";
    }
    if(strs.length==1){
        return strs[0];
    }
    int minlen = strs[0].length();
    for (int i = 1; i < strs.length; i++) {
        if(strs[i].length()<minlen){
            minlen = strs[i].length();
        }
    }
    int count = 0;
    outer: for (int i = 0; i < minlen; i++) {
        char c = strs[0].charAt(i);
        boolean same = true;
        for (int j = 1; j < strs.length; j++) {
            if(strs[j].charAt(i)!=c){
                same = false;
                break outer;
            }
        }
        if(same)
            count++;
    }
    return strs[0].substring(0,count);
}
```

