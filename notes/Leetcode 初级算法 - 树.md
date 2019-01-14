# Leetcode 初级算法 - 树

二级标题格式：\[章节内题号\] \[题库内题号\] \[题目标题\]

## 1 104 二叉树的最大深度

我的思路：直接递归 注意下 base case

```java
public int maxDepth(TreeNode root) {
    if(root==null) return 0;
    if(root.left==null && root.right==null) return 1;
    if(root.left==null) return maxDepth(root.right)+1;
    else if(root.right==null) return maxDepth(root.left)+1;
    else return Math.max(maxDepth(root.left),maxDepth(root.right))+1;
}
```

其他思路：递归可以写的更简单一点

```java
// source: https://leetcode.com/problems/maximum-depth-of-binary-tree/solution/

class Solution {
  public int maxDepth(TreeNode root) {
    if (root == null) {
      return 0;
    } else {
      int left_height = maxDepth(root.left);
      int right_height = maxDepth(root.right);
      return java.lang.Math.max(left_height, right_height) + 1;
    }
  }
}
```

也可以用DFS+栈来实现 初始层记录为深度1 越往底部深度增加



## 2 98 验证二叉搜索树

我的思路：中序遍历是否满足递增

先写了一个很糟糕的版本 用 list 存储中序遍历结果（因为不想写非递归做法）

```java
public boolean isValidBST(TreeNode root) {
    LinkedList<Integer> li = new LinkedList<>();

    if(root==null) return true;

    inOrderTraversal(root,li);

    int i = li.pollFirst();
    while(li.size()>0){
        if(!(i<li.peekFirst())){
            return false;
        } else {
            i=li.pollFirst();
        }
    }
    return true;
}

public static void inOrderTraversal(TreeNode root,List<Integer> li){
    if(root.left!=null) inOrderTraversal(root.left,li);
    li.add(root.val);
    if(root.right!=null) inOrderTraversal(root.right,li);
}
```

**注意** 错误思路：左右都是BST且中间节点满足左<中<右 **不能证明** 这是一个BST！

反例：`[10,5,15,null,null,6,20]`

思路2：还是基于递归的中序遍历 但是可以中途判定不合法就直接停止了 不需要遍历完整个树

坑：居然有这样的测试数据`[-2147483648]` 因此不能单纯用`Integer.MIN_VALUE`作为占位符了 需要单独开一个boolean标识是否写入了第一个数

```java
public boolean isValidBST(TreeNode root) {
    if(root==null) return true;
    int[] t = new int[1];
    t[0]=0;
    boolean[] chk = new boolean[2]; // 1 for final check, 2 for init status
    chk[0] = true;
    chk[1] = false;

    inOrderTraversal(root,t,chk);

    return chk[0];
}

public static void inOrderTraversal(TreeNode root,int[] t, boolean[] chk){
    if(root.left!=null) inOrderTraversal(root.left,t,chk);
    if(!chk[1]){
        t[0] = root.val; chk[1]=true;
    } else {
        if(root.val>t[0]&&chk[0]==true) t[0]=root.val;
        else {chk[0]=false; return;} 
    }
    if(root.right!=null) inOrderTraversal(root.right,t,chk);
}
```

其他思路：

不基于中序遍历 而是直接比较每一层和下一层的数值大小 [来源](https://leetcode.com/problems/validate-binary-search-tree/discuss/32109/My-simple-Java-solution-in-3-lines)

基于中序遍历 但是用非递归写法（用DFS+栈实现）[来源](https://leetcode.com/problems/validate-binary-search-tree/discuss/32112/Learn-one-iterative-inorder-traversal-apply-it-to-multiple-tree-questions-(Java-Solution))

补充：中序遍历非递归写法

```java
// source: https://leetcode.com/problems/validate-binary-search-tree/discuss/32112/Learn-one-iterative-inorder-traversal-apply-it-to-multiple-tree-questions-(Java-Solution)

public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> list = new ArrayList<>();
    if(root == null) return list;
    Stack<TreeNode> stack = new Stack<>();
    while(root != null || !stack.empty()){
        while(root != null){
            stack.push(root);
            root = root.left;
        }
        root = stack.pop();
        list.add(root.val);
        root = root.right;
        
    }
    return list;
}
```

## 3 101 对称二叉树

我的思路：直接一个递归 关键是如何让递归的左右是对称的

理论上先跑中序遍历 然后比较对称性也行 不过那个太慢了...

```java
public boolean isSymmetric(TreeNode root) {
    if(root==null) return true;
    return itChk(root.left,root.right);
}

public static boolean itChk(TreeNode l,TreeNode r){
    if(l==null&&r==null) return true;
    if(l!=null&&r!=null) {
        if(l.val!=r.val) return false;
        else return (itChk(l.left,r.right))&&(itChk(l.right,r.left));
    }
    return false;
}
```

其他思路：循环式 用一个队列来处理

每次先取出前两个进行比较 然后分别加入左左 右右 左右 右左

```java
//source: https://leetcode.com/problems/symmetric-tree/solution/

public boolean isSymmetric(TreeNode root) {
    Queue<TreeNode> q = new LinkedList<>();
    q.add(root);
    q.add(root);
    while (!q.isEmpty()) {
        TreeNode t1 = q.poll();
        TreeNode t2 = q.poll();
        if (t1 == null && t2 == null) continue;
        if (t1 == null || t2 == null) return false;
        if (t1.val != t2.val) return false;
        q.add(t1.left);
        q.add(t2.right);
        q.add(t1.right);
        q.add(t2.left);
    }
    return true;
}
```

## 4 102 二叉树的层次遍历

我的思路：~~这个因为需要把每一层分开 因此没法直接队列+BST解决~~ 所以还是用了DFS 递归+标志位记录

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> li = new ArrayList<>();
    gen(root,0,li);
    return li;
}

public static void gen(TreeNode root, int depth, List<List<Integer>> li){
    if(root==null){
        return;
    }
    if(!(li.size()-1>=depth)){ // haven't been this level before
        List<Integer> l = new LinkedList<>();
        l.add(root.val);
        li.add(l);
    } else {
        li.get(depth).add(root.val);
    }
    gen(root.left,depth+1,li);
    gen(root.right,depth+1,li);
}
```

其他思路：我错了... BST还是可以解决的... 事实上不用while 而用for来处理每一层就可以计数了...

```java
public class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        List<List<Integer>> wrapList = new LinkedList<List<Integer>>();
        
        if(root == null) return wrapList;
        
        queue.offer(root);
        while(!queue.isEmpty()){
            int levelNum = queue.size(); // number of nodes in a certain level
            List<Integer> subList = new LinkedList<Integer>();
            for(int i=0; i<levelNum; i++) {
                if(queue.peek().left != null) queue.offer(queue.peek().left);
                if(queue.peek().right != null) queue.offer(queue.peek().right);
                subList.add(queue.poll().val);
            }
            wrapList.add(subList);
        }
        return wrapList;
    }
}
```

## 5 108 将有序数组转换为二叉搜索树

我的思路：这是手写AVL？看来十分麻烦了... 最后放弃了 直接面对编辑器写不出来...

其他思路：实际上想的太复杂了...对于有序的数组 可以直接通过二分构建... 因为数组是有序的 所以中点的值一定比左边大 右边小 满足BST要求... 同时因为每次取得是中点 左右的个数是相同的 又因为逐层取 所以应该能保证层数相差不超过1？

~~**注意** 这里需要左右端点都取 不然会卡在00 01或者0-1的无限循环...~~

```java
public TreeNode sortedArrayToBST(int[] nums) {
    if(nums==null||nums.length==0) return null;
    return add(nums,0,nums.length-1);
}

public static TreeNode add(int[] nums, int l, int r){
    if(l>r) return null;
    int midpos = (l+r)/2;
    TreeNode root = new TreeNode(nums[midpos]);
    root.left = add(nums,l,midpos-1);
    root.right = add(nums,midpos+1,r);
    return root;
}
```

修正：实际上还是可以左闭右开的 只要注意下条件 (之前一直超时是因为root是个引用 改root会把上层也修改 加一个tmp就能解决了...)

```java
public static TreeNode sortedArrayToBST(int[] nums) {
    if(nums.length==0) return null;
    return add(null,nums,0,nums.length);
}

public static TreeNode add(TreeNode root, int[] nums, int left, int right){
    if(left>=right) return null;
    int midpos = (left+right)/2;
    TreeNode tmp = new TreeNode(nums[midpos]);
    if(root==null){
        root = tmp;
    }
    tmp.left = add(tmp,nums,left,midpos);
    tmp.right = add(tmp,nums,midpos+1,right);
    return tmp;
}
```

