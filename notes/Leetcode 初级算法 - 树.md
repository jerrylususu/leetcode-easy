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

