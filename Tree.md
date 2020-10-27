- 树的 **层序遍历** 用队列queue，**深层遍历** 用 stack
- 对于二叉树问题用“递归”的话，最好在终止条件之后，先将left和right的递归结果分别写出来，看在left和right是怎样进行递归的，这样更加清楚。之后可以再继续优化
- 对于“满二叉树”，如果深度是d的话，那么节点个数就是**2^d - 1**
- 对于 tree 的题目，要用递归的话，经常假设对于 subtree（左或者右），已经根据函数得到了结果，可以直接用
- 对于存放结果的 List 数组或者其他数据结构，是作为全局变量还是每个递归函数内部的变量，如果对于每次递归含义和形式都一样，那就是内部的变量，例如 LC 257
- 迭代时，可以用一个辅助 TreeNode p，帮助我们遍历整棵树
- BST：（1）low 和 high 上下边界；（2）中序遍历 BST 的节点，是一个递增的序列
- **在 Tree 的递归问题中，我们经常在递归函数递归过程中修改一个全局变量，比如 res，然而递归函数 return 的却是一个局部的解 （例如 124 题）**



## 144. Binary Tree Preorder Traversal

非递归的写法：以下是模板解法，就是按照递归方式逻辑来的，p treeNode 的作用是帮助遍历整个树

~~~java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        if (root == null)
            return ans;
        TreeNode p = root;
        
        while(!stack.isEmpty() || p != null) {
            if(p != null) {
                stack.push(p);
                ans.add(p.val);
                p = p.left;
            } else {
                p = stack.pop();
                p = p.right;
            }
        }
        return ans;
    }
}
~~~



递归的写法：

~~~java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        
        List<Integer> ans = new ArrayList<>();
        if (root == null)
            return ans;
        
        ans.add(root.val);
        ans.addAll(preorderTraversal(root.left));
        ans.addAll(preorderTraversal(root.right));
        return ans;
    }
}
~~~



## 94 Binary Tree Inorder Traversal

~~~java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        if (root == null)
            return ans;
        TreeNode p = root;
        
        while(!stack.isEmpty() || p != null) {
            if(p != null) {
                stack.push(p);
                p = p.left;
            } else {
                p = stack.pop();
                ans.add(p.val);
                p = p.right;
            }
        }
        return ans;
    }
}
~~~



## 145 Binary Tree Postorder Traversal

postorder的顺序是：左 - 右 - root；我们反过来进行遍历：root - 右 - 左，最后再对 List reverse

~~~java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        if (root == null)
            return ans;
        TreeNode p = root;
        
        while(!stack.isEmpty() || p != null) {
            if(p != null) {
                stack.push(p);
                ans.add(p.val);
                p = p.right;
            } else {
                p = stack.pop();
                p = p.left;
            }
        }
            
        Collections.reverse(ans);
        return ans;
    }
}
~~~

也可以直接用 ArrayDeque，添加元素时用 addFirst

~~~java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        Deque<Integer> deque = new ArrayDeque<>();
        Stack<TreeNode> stack = new Stack<>();
        if (root == null)
            return new ArrayList<>(deque);
        TreeNode p = root;
        
        while (!stack.isEmpty() || p != null) {
            if (p != null) {
                stack.push(p);
                deque.addFirst(p.val);
                p = p.right; 
            } else {
                TreeNode node = stack.pop();
                p = node.left;
            }
        }
        return new ArrayList<>(deque);
    }
}
~~~



## 112. Path Sum

Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.

**Note:** A leaf is a node with no children.

**Example:**

Given the below binary tree and `sum = 22`,

```
      5
     / \
    4   8
   /   / \
  11  13  4
 /  \      \
7    2      1
```

return true, as there exist a root-to-leaf path `5->4->11->2` which sum is 22.



递归的方法：

~~~java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if(root == null)   // 空树的情况
            return false;
        if(root.left == null && root.right == null)
            return sum == root.val;
        
        return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
    }
}
~~~



迭代的方法：用Queue存储Pair，Pair(root, sum)

~~~java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if(root == null)   // 空树的情况
            return false;
        Queue<Pair<TreeNode, Integer>> queue = new LinkedList<>();
        queue.add(new Pair(root, sum));    // 一开始应该存root和sum进去，因为之后还要把root poll出来计算sum-node.val
        
        while(!queue.isEmpty()) {
            TreeNode node = queue.peek().getKey();
            int curVal = queue.peek().getValue();
            queue.poll();
            if(curVal - node.val == 0 && (node.right == null && node.left == null))
                return true;
            if(node.left != null) queue.add(new Pair(node.left, curVal - node.val));
            if(node.right != null) queue.add(new Pair(node.right, curVal - node.val));
        }
        return false;
    }
}
~~~



## 404. Sum of Left Leaves

~~~java
class Solution {
    public int sum = 0;  // 定义成一个全局变量，不要把curSum放在calLeftSum的参数中并在sumOfLeft中定义初始化
    // 因为这样最后return sum的时候，sum的值不会被改变，还是0
    
    public int sumOfLeftLeaves(TreeNode root) {
        calLeftSum(root);
        return sum;
    }
    
    public void calLeftSum(TreeNode root) {
        if(root == null)
            return;
        if(root.left != null && (root.left.left == null && root.left.right == null))
            sum += root.left.val;
        
        calLeftSum(root.left);
        calLeftSum(root.right);
    }
}
~~~



## 257 Binary Tree Path

~~~java
class Solution {
    // 因为将List作为递归函数的参数的话，List会随着每一次递归而改变
    // 所以将res定义在递归函数内部，并return，（1）终止return；（2）正常逻辑return
    
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        
        if(root == null)
            return res;
        if(root.left == null && root.right == null) {
            res.add(String.valueOf(root.val));
            return res;
        }
        
        List<String> leftP = binaryTreePaths(root.left);
        for(String s: leftP) {
            StringBuilder sb  = new StringBuilder(String.valueOf(root.val));
            sb.append("->");
            sb.append(s);
            res.add(sb.toString());
        }
        
        List<String> rightP = binaryTreePaths(root.right);
        for(String s: rightP) {
            StringBuilder sb  = new StringBuilder(String.valueOf(root.val));
            sb.append("->");
            sb.append(s);
            res.add(sb.toString());
        }
        return res;
    }
}
~~~



113 和 257 代码类似

## 113 Path Sum II

Given a binary tree and a sum, find all root-to-leaf paths where each path's sum equals the given sum.

**Note:** A leaf is a node with no children.

**Example:**

Given the below binary tree and `sum = 22`,

```
      5
     / \
    4   8
   /   / \
  11  13  4
 /  \    / \
7    2  5   1
```

Return:

```
[
   [5,4,11,2],
   [5,8,4,5]
]
```



- 时间复杂度是 O（N^2），因为理解为对树中每一个节点作为根节点的子树都遍历了一遍
- 空间复杂度最差是O（N）其实应该是O(H)，because we traverse every node

~~~java
class Solution {
    // 递归终止条件：root==null，root为leaf节点且sum满足题意
    // 递归正常流程：遍历左右子树得到的满足题意的List<List>，循环遍历每个List，并add root，最后add近res
    
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> res = new ArrayList<>();
        
        if(root == null)
            return res;
        
        List<Integer> temp = new ArrayList<>();
        if(root.left == null && root.right == null && root.val == sum) {
            temp.add(root.val);
            res.add(temp);
            return res;
        }
        
        List<List<Integer>> leftPath = pathSum(root.left, sum - root.val);
        for(List<Integer> curList: leftPath) {
            curList.add(0, root.val);
            res.add(curList);
        }
        
        List<List<Integer>> rightPath = pathSum(root.right, sum - root.val);
        for(List<Integer> curList: rightPath) {
            curList.add(0, root.val);
            res.add(curList);
        }
        
        return res;
    }
}
~~~



## 437 Path Sum III

You are given a binary tree in which each node contains an integer value.

Find the number of paths that sum to a given value.

The path does not need to start or end at the root or a leaf, but it must go downwards (traveling only from parent nodes to child nodes).

The tree has no more than 1,000 nodes and the values are in the range -1,000,000 to 1,000,000.

**Example:**

```
root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

      10
     /  \
    5   -3
   / \    \
  3   2   11
 / \   \
3  -2   1

Return 3. The paths that sum to 8 are:

1.  5 -> 3
2.  5 -> 2 -> 1
3. -3 -> 11
```



![image-20200919205239451](C:\Users\yawei\AppData\Roaming\Typora\typora-user-images\image-20200919205239451.png)

- findPath函数 --- 路径中包含node节点
- pathSum函数 --- 路径中不一定包含node节点
- 这个题还有prefix sum 的解法

~~~java
class Solution {
    public int pathSum(TreeNode root, int sum) {
        int res = 0;
        if(root == null)
            return res;
        
        // path中包含root节点
        res += findPath(root, sum);
        
        // path中不包含root节点
        res += pathSum(root.left, sum);
        res += pathSum(root.right, sum);
        
        return res;
    }
    
    public int findPath(TreeNode node, int sum) {
        int res = 0;
        if(node == null)
            return 0;
        if(node.val == sum)
            res += 1;   // 这里只把res+1，不return，因为有可能加上下面的节点仍==sum
        return res + findPath(node.left, sum - node.val) + findPath(node.right, sum - node.val);
    }
}
~~~

prefix sum的解法：

~~~java
class Solution {
    // prefix sum解法的时间和空间复杂度都是 O(N)
    
    int count = 0;
    int k;
    HashMap<Integer, Integer> h = new HashMap();
    
    public void preorder(TreeNode node, int currSum) {
        if (node == null)
            return;
        
        // current prefix sum
        currSum += node.val;

        if (currSum == k)
            count++;
        
        // number of times the curr_sum − k has occured already, 
        // determines the number of times a path with sum k has occured upto the current node
        count += h.getOrDefault(currSum - k, 0);
        
        // add the current sum into hashmap to use it during the child nodes processing
        h.put(currSum, h.getOrDefault(currSum, 0) + 1);

        preorder(node.left, currSum);
        preorder(node.right, currSum);

        // remove the current sum from the hashmap in order not to use it during  parallel subtree process
        h.put(currSum, h.get(currSum) - 1);  // 这里有点像回溯
    }    
            
    public int pathSum(TreeNode root, int sum) {
        k = sum;
        preorder(root, 0);
        return count;
    }
}
~~~





## 129 Sum Root to Leaf Numbers

Given a binary tree containing digits from `0-9` only, each root-to-leaf path could represent a number.

An example is the root-to-leaf path `1->2->3` which represents the number `123`.

Find the total sum of all root-to-leaf numbers.

**Note:** A leaf is a node with no children.

**Example:**

```
Input: [1,2,3]
    1
   / \
  2   3
Output: 25
Explanation:
The root-to-leaf path 1->2 represents the number 12.
The root-to-leaf path 1->3 represents the number 13.
Therefore, sum = 12 + 13 = 25.
```

**Example 2:**

```
Input: [4,9,0,5,1]
    4
   / \
  9   0
 / \
5   1
Output: 1026
Explanation:
The root-to-leaf path 4->9->5 represents the number 495.
The root-to-leaf path 4->9->1 represents the number 491.
The root-to-leaf path 4->0 represents the number 40.
Therefore, sum = 495 + 491 + 40 = 1026.
```



找的是 sum 值，将 sum 作为递归函数的”参数“，进入下一层时能够 x 10，但是**回到这一层的时候还是原来的值**

只有到了 叶子leaf节点 才能用当前值改变最后的 sum 值

- 递归的写法：

~~~java
class Solution {
    public int sumNumbers(TreeNode root) {
        int sum = 0;
        return updateSum(root, 0);
    }
    
    private int updateSum(TreeNode root, int sum) {
        if(root == null)
            return 0;
        
        sum = sum * 10 + root.val;
        if(root.left == null && root.right == null)
            return sum;
        
        return updateSum(root.left, sum) + updateSum(root.right, sum);
    }
}
~~~

- 迭代的写法：curVal 用于将当前值传递给下一层；sum 是每条路径和的 sum

~~~java
class Solution {
    // 找所有的leaf节点
    // 对于每一个节点，进行递归
    // 递归返回条件：node是null或者leaf节点。中间部分：如果当前元素不为leaf，对左右节点进行递归
    
    public int sumNumbers(TreeNode root) {
        if(root == null)
            return 0;
        Queue<Pair<TreeNode, Integer>> queue = new LinkedList<>();
        int sum = 0;
        queue.add(new Pair(root, 0));
        
        while(!queue.isEmpty()) {
            TreeNode node = queue.peek().getKey();
            int curVal = queue.peek().getValue();
            queue.poll();
            curVal = curVal * 10 + node.val;
            if(node.left == null && node.right == null)
                sum += curVal;
            else {
                if(node.left != null) queue.add(new Pair(node.left, curVal));
                if(node.right != null) queue.add(new Pair(node.right, curVal));
            }
        }
        return sum;
    }
}
~~~



## 235. Lowest Common Ancestor of a Binary Search Tree

递归的解法：

~~~java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null)
            return null;
        if(root.val == p.val || root.val == q.val)
            return (root.val == p.val)? p: q;
        if((root.val > p.val && root.val < q.val) || (root.val < p.val && root.val > q.val))
            return root;
        if(root.val > p.val && root.val > q.val)
            return lowestCommonAncestor(root.left, p, q);
        else
            return lowestCommonAncestor(root.right, p, q);
    }
}
~~~

接下来是迭代的解法：用一个辅助 TreeNode 帮助迭代，因为清楚应该左走还是右走，所以不需要 stack

~~~java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null)
            return null;
         int pVal = p.val;
         int qVal = q.val;
         TreeNode node = root;
        
        while(node != null) {
            if(node.val > pVal && node.val > qVal)
                node = node.left;
            else if(node.val < pVal && node.val < qVal)
                node = node.right;
            else
                return node;
        }
        return null;
    }
}
~~~



## 98. Validate Binary Search Tree

Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

- The left subtree of a node contains only nodes with keys **less than** the node's key.
- The right subtree of a node contains only nodes with keys **greater than** the node's key.
- Both the left and right subtrees must also be binary search trees.

 

**Example 1:**

```
    2
   / \
  1   3

Input: [2,1,3]
Output: true
```

**Example 2:**

```
    5
   / \
  1   4
     / \
    3   6

Input: [5,1,4,null,null,3,6]
Output: false
Explanation: The root node's value is 5 but its right child's value is 4.
```



用递归，采用 low 和 high 的上下边界判断 value 是否在合法的范围内；in order to cover the range of -2^31 ~ 2^31-1

~~~java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return check(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }
    
    private boolean check(TreeNode root, long low, long high) {
        if(root == null)
            return true;
        if(root.val <= low || root.val >= high)
            return false;
        
        return check(root.left, low, root.val) && check(root.right, root.val, high);
    }
}
~~~



迭代的解法：中序遍历BST节点值依次递增，中序遍历过程中依次改变 low 的值，进行下一轮的判断

~~~java
class Solution {
    // 根据中序遍历：左-root-右的顺序，遍历BST节点值应该是递增的
    // If next element in inorder traversal
    // is smaller than the previous one
    // that's not BST.
    
    public boolean isValidBST(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        double inorder = -Double.MAX_VALUE;    // 用浮点数的最小值
        TreeNode p = root;
        
        while(!stack.isEmpty() || p != null) {
            if(p != null) {
                stack.push(p);
                p = p.left;
            } else {
                p = stack.pop();
                if(p.val <= inorder) 
                    return false;
                inorder = p.val;
                p = p.right;
            }
        }
        return true;
    }     
}
~~~



## 236. Lowest Common Ancestor of a Binary Tree

~~~java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null || root.val == p.val || root.val == q.val)
            return root;
        
        TreeNode left = lowestCommonAncestor(root.left, p,  q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        
        if(left != null && right != null)
            return root;
        if(left == null && right != null)
            return right;
        if(left != null && right == null)
            return left;
        return null;
    }
}
~~~



## 450 Delete Node in a BST







## 124. Binary Tree Maximum Path Sum

Given a **non-empty** binary tree, find the maximum path sum.

For this problem, a path is defined as any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The path must contain **at least one node** and does not need to go through the root.

**Example 1:**

```
Input: [1,2,3]

       1
      / \
     2   3

Output: 6
```

**Example 2:**

```
Input: [-10,9,20,null,null,15,7]

   -10
   / \
  9  20
    /  \
   15   7

Output: 42
```





~~~java
class Solution {
    // "递归函数返回值"就可以定义为以当前结点为根结点，到叶节点的最大路径之和
    // 经过一个节点的最大路径 = max(其左孩子为顶点的最大路径, 0) + max(右孩子为顶点的最大路径, 0) + 该节点的值。
    // 在dfs中改变要 return 的全局变量
    // 时间复杂度 O(N)，空间复杂度 O(H)
    
    public int res;
    
    public int maxPathSum(TreeNode root) {
        res = Integer.MIN_VALUE;
        rootLeafMaxPath(root);
        return res;
    }
    
    
    public int rootLeafMaxPath(TreeNode root) {
        if(root == null) return 0;
        int left = Math.max(rootLeafMaxPath(root.left), 0);  // 取 max 是因为如果左子树返回的解 < 0，那就不包含左树 
        int right = Math.max(rootLeafMaxPath(root.right), 0);  // 同上
        res = Math.max(res, left + right + root.val);
        return Math.max(left, right) + root.val;  // 最后返回的时候，left 和 right 只能选一边和 root 组合
    }
}
~~~

