- ==栈顶元素== 反映了嵌套的层次关系中，==“最近”需要匹配的元素==
- Stack 中经常存放数组的 ==“下标”==
- 或者不方便正序对链表进行操作，可以用 stack 将链表元素进行存储
- 用 stack 模拟系统栈，写非递归程序
- 注意判断 stack.isEmpty()，尤其是 while 循环结束之后，要判断 stack.isEmpty()，看 stack 中是否还有残留的元素
- **Stack 经常用于解决 “匹配” 问题，或者某个在 arr 中的元素想要找 “最近” 的符合条件的某个元素**
- **找每个数字的前小数字或是后小数字**，正是单调栈擅长的
- 单调栈小结： https://www.cnblogs.com/grandyang/p/8887985.html
- 单调栈小结2：https://blog.csdn.net/qq_17550379/article/details/86519771



## 42. Trapping Rain Water

Given *n* non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

![img](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)
The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped. **Thanks Marcos** for contributing this image!

**Example:**

```
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```



一般情况下，如果按照人的思维方式的话，要看每一个位置可以蓄多少水，我们首先会看看，左边最高的柱子有多高（这里的左边最高还包括当前这个位置的，不是真的就是在左边的最高的柱子），右边的柱子最高的有多高，然后取他们两个当中的最小值，减去当前柱子的高度，就是这个位置可以蓄的水的量了。

### 方法一：brute force

<img src="https://zxi.mytechroad.com/blog/wp-content/uploads/2020/01/42-ep300-1.png" alt="img" style="zoom:80%;" />



注意在向两边寻找各自的最大值时，一定也要包含到当前高度 height[i]

~~~java
class Solution {
    // 时间复杂度 O(N ^ 2)，空间复杂度 O(1)
    // 对于当前的 height[i]，分别找到左右最大高度，再取最小值，因为对于 i 位置来说，要看两边高板的极限
    public int trap(int[] height) {
        if(height.length == 0) return 0;
        
        int maxWater = 0;
        for(int i = 0; i < height.length; i++) {
            int maxLeft = 0, maxRight = 0;
            for(int j = 0; j <= i; j++) {      // 注意这里必须是 [0, i]
                maxLeft = Math.max(height[j], maxLeft);
            }
            for(int k = i; k < height.length; k++) {   // 注意这里必须是 [i, n - 1]
                maxRight = Math.max(height[k], maxRight);
            }
            maxWater += Math.min(maxLeft, maxRight) - height[i];
        }
        return maxWater;
    }
}
~~~



### 方法二：DP + prefix sum

在方法一中，我们重复计算了很多次最大值，所以可以考虑运用 DP 来存储最大值，两个DP数组运用了 prefix sum 的思想

用空间换时间，时间复杂度是 O(N)，空间复杂度是 O(N)

<img src="https://zxi.mytechroad.com/blog/wp-content/uploads/2020/01/42-ep300-2.png" alt="img" style="zoom:80%;" />

~~~java
class Solution {
    // 时间复杂度 O(N ^ 2)，空间复杂度 O(1)
    // dp: leftMax 表示从0开始遍历的最大值，rightMax 表示从 n - 1 开始遍历的最大值
    // dp: leftMax[i] = max(height[i], leftMax[i - 1])，注意 i - 1 不能越界
    // dp: leftRight[i] = max(height[i], rightMax[i + 1])，注意 i + 1 不能越界
    public int trap(int[] height) {
        int n = height.length;
        if(n == 0) return 0;
        
        int maxWater = 0;
        int[] leftMax = new int[n];
        int[] rightMax = new int[n];
        
        for(int i = 0; i < n; i++)
            leftMax[i] = (i == 0)? height[i]: Math.max(height[i], leftMax[i - 1]);
        for(int i = n - 1; i >= 0; i--)
            rightMax[i] = (i == n - 1)? height[i]: Math.max(height[i], rightMax[i + 1]);
        
        for(int i = 0; i < n; i++) {
            maxWater += Math.min(leftMax[i], rightMax[i]) - height[i];
        }
        return maxWater;
    }
}
~~~



### 方法三：Two Pointer

因为短板效应，可以安心地移动指针，将 prefix sum 数组转化为了两个指针

相比于方法一，方法一的两个指针对于每一次新的 i index，都从0重新开始寻找正确的值；而方法三的两个指针则是全局的

时间复杂度是 O(N)，空间复杂度是 O(1)

<img src="https://zxi.mytechroad.com/blog/wp-content/uploads/2020/01/42-ep300-3.png" alt="img" style="zoom:80%;" />

leftMax 和 rightMax 分别用于找两边最高的板子，再找短板

~~~java
class Solution {
    // 时间复杂度 O(N)，空间复杂度 O(1)
    public int trap(int[] height) {
        int n = height.length;
        if(n == 0) return 0;
        
        int maxWater = 0;
        int leftMax = height[0];
        int rightMax = height[n - 1]; 
        int l = 0, r = n - 1;
        
        while(l < r) {
            if(leftMax < rightMax) {   // l 继续向前移动
                maxWater += (leftMax - height[l]);
                l++;
                leftMax = Math.max(leftMax, height[l]);
            } else {
                maxWater += (rightMax - height[r]);
                r--;
                rightMax = Math.max(rightMax, height[r]);
            }
        }
        return maxWater;
    }
}
~~~



### 方法四：Stack

遍历高度，如果此时栈为空，或者当前高度小于等于栈顶高度，则把当前高度的坐标压入栈，<u>注意这里不直接把高度压入栈，而是把**坐标**压入栈，这样方便在后来算水平距离</u>。==当遇到比栈顶高度大的时候，**就说明有可能会有坑存在，可以装雨水**==

此时栈里至少有一个高度，如果只有一个的话，那么不能形成坑，直接跳过，如果多余一个的话，那么此时把栈顶元素取出来当作坑，新的栈顶元素就是左边界，当前高度是右边界，只要取二者较小的，减去坑的高度，长度就是右边界坐标减去左边界坐标再减1，二者相乘就是盛水量啦

Stack 中的板高度，一定是保持“递减”的趋势，所以遇到一个更高的板子时，Stack中比这个板子高的都是一次被 pop 出来，知道栈顶的板子比当前的板子高，才将当前的板子 push 进 stack。

~~~java
class Solution {
    // 时间复杂度 O(N)，空间复杂度 O(N)
    public int trap(int[] height) {
        int n = height.length;
        if(n == 0) return 0;
        
        int maxWater = 0;
        Stack<Integer> stack = new Stack<>();
        int i = 0;
        while(i < n) {
            if(stack.isEmpty() || height[i] <= height[stack.peek()]) {
                stack.push(i);
                i++;
            } else {
                int bottom = stack.pop();
                if(!stack.isEmpty()) {
                    maxWater += (Math.min(height[i], height[stack.peek()]) - height[bottom]) * (i - stack.peek() - 1);
                }
            }
        }
        return maxWater;
    }
}
~~~



## 445. Add Two Numbers II

You are given two **non-empty** linked lists representing two non-negative integers. The most significant digit comes first and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Follow up:**
What if you cannot modify the input lists? In other words, reversing the lists is not allowed.

**Example:**

```
Input: (7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 8 -> 0 -> 7
```

- 我们可以利用栈来保存所有的元素，然后利用栈的后进先出的特点就可以从后往前取数字了

~~~java
public class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        Stack<Integer> s1 = new Stack<Integer>();
        Stack<Integer> s2 = new Stack<Integer>();
        
        while(l1 != null) {
            s1.push(l1.val);
            l1 = l1.next;
        };
        while(l2 != null) {
            s2.push(l2.val);
            l2 = l2.next;
        }
        
        int sum = 0;
        ListNode cur = new ListNode(0);
        while (!s1.empty() || !s2.empty()) {
            if (!s1.empty()) sum += s1.pop();
            if (!s2.empty()) sum += s2.pop();
            cur.val = sum % 10;
            ListNode head = new ListNode(sum / 10);
            head.next = cur;
            cur = head;
            sum /= 10;
        }
        
        return cur.val == 0 ? cur.next : cur;
    }
}
~~~





## 71 simplify path

Given an **absolute path** for a file (Unix-style), simplify it. Or in other words, convert it to the **canonical path**.

In a UNIX-style file system, a period `.` refers to the current directory. Furthermore, a double period `..` moves the directory up a level.

Note that the returned canonical path must always begin with a slash `/`, and there must be only a single slash `/` between two directory names. The last directory name (if it exists) **must not** end with a trailing `/`. Also, the canonical path must be the **shortest** string representing the absolute path.

 

**Example 1:**

```
Input: "/home/"
Output: "/home"
Explanation: Note that there is no trailing slash after the last directory name.
```

**Example 2:**

```
Input: "/../"
Output: "/"
Explanation: Going one level up from the root directory is a no-op, as the root level is the highest level you can go.
```

**Example 3:**

```
Input: "/home//foo/"
Output: "/home/foo"
Explanation: In the canonical path, multiple consecutive slashes are replaced by a single one.
```

**Example 4:**

```
Input: "/a/./b/../../c/"
Output: "/c"
```

**Example 5:**

```
Input: "/a/../../b/../c//.//"
Output: "/c"
```

**Example 6:**

```
Input: "/a//b////c/d//././/.."
Output: "/a/b/c"
```



~~~java
class Solution {
    public String simplifyPath(String path) {
        String[] route = path.split("/");
        Stack<String> stack = new Stack<>();
        
        for(String r: route) {
            if(r.equals("."))
                continue;
            else if(r.equals("..")) {
                if(!stack.isEmpty())
                    stack.pop();
            }
            else if(!r.equals("")){
                stack.push(r);
            }
        }
        
        StringBuilder sb = new StringBuilder();
        if(stack.isEmpty())
            return "/";
            
        while(!stack.isEmpty()) {
            sb.insert(0, stack.pop());
            sb.insert(0, "/");
        }
        
        return sb.toString();
    }
}
~~~





## 341. Flatten Nested List Iterator

Given a nested list of integers, implement an iterator to flatten it.

Each element is either an integer, or a list -- whose elements may also be integers or other lists.

**Example 1:**

```
Input: [[1,1],2,[1,1]]
Output: [1,1,2,1,1]
Explanation: By calling next repeatedly until hasNext returns false, 
             the order of elements returned by next should be: [1,1,2,1,1].
```

**Example 2:**

```
Input: [1,[4,[6]]]
Output: [1,4,6]
Explanation: By calling next repeatedly until hasNext returns false, 
             the order of elements returned by next should be: [1,4,6].
```



可以直接用 递归 的方式来做：

~~~java
public class NestedIterator implements Iterator<Integer> {
    private List<Integer> flattenInts = new ArrayList<>();
    private int pos = 0;

    public NestedIterator(List<NestedInteger> nestedList) {
        flattenList(nestedList);
    }
    
    private void flattenList(List<NestedInteger> nestedList) {
        for(NestedInteger nest_item: nestedList) {
            if(nest_item.isInteger())
                flattenInts.add(nest_item.getInteger());
            else
                flattenList(nest_item.getList());
        }
    }

    @Override
    public Integer next() {
        if(hasNext()) {
            return flattenInts.get(pos++);
        }
        return 0;
    }

    @Override
    public boolean hasNext() {
        return pos < flattenInts.size();
    }
}
~~~



可以用 **stack** 来**“从后到前”**存储 nestedList 中的元素，这样再 next 的时候相比方法一那样创建了一个List来遍历，这种方法时直接对 nextedList遍历的

hasNext 函数只处理 stack top 元素，直到 top 元素变成 int 类型，并且 stack 不为空，就说明还有 next int 数字，所以在 hasNext 中循环的时候只用 peek，不用 pop

~~~java
public class NestedIterator implements Iterator<Integer> {
    Stack<NestedInteger> stack = new Stack<>();

    public NestedIterator(List<NestedInteger> nestedList) {
        prepareStack(nestedList);
    }
    
    private void prepareStack(List<NestedInteger> nestedList) {
        for(int i = nestedList.size() - 1; i >= 0; i--)
            stack.push(nestedList.get(i));
    }

    @Override
    public Integer next() {
        if(hasNext())
            return stack.pop().getInteger();
        return null;
    }

    @Override
    public boolean hasNext() {  // 每次 hasNext 都只对 stack 最上面的元素进行处理，直到 top 是int类型
        while(!stack.isEmpty() && !stack.peek().isInteger()) {
            List<NestedInteger> curList = stack.pop().getList();
            prepareStack(curList);
        }
        return !stack.isEmpty();
    }
}
~~~



## 173. Binary Search Tree Iterator

Implement an iterator over a binary search tree (BST). Your iterator will be initialized with the root node of a BST.

Calling `next()` will return the next smallest number in the BST.

**Example:**

**![img](https://assets.leetcode.com/uploads/2018/12/25/bst-tree.png)**

```
BSTIterator iterator = new BSTIterator(root);
iterator.next();    // return 3
iterator.next();    // return 7
iterator.hasNext(); // return true
iterator.next();    // return 9
iterator.hasNext(); // return true
iterator.next();    // return 15
iterator.hasNext(); // return true
iterator.next();    // return 20
iterator.hasNext(); // return false
```

 

**Note:**

- `next()` and `hasNext()` should run in average O(1) time and uses O(*h*) memory, where *h* is the height of the tree.
- You may assume that `next()` call will always be valid, that is, there will be at least a next smallest number in the BST when `next()` is called.

~~~java
class BSTIterator {
    // 这个题就是 BST inorder 的变形，将iterative方法的每一步都封装成了函数
    
    Stack<TreeNode> stack;
    TreeNode p;

    public BSTIterator(TreeNode root) {    // 初始化的时候要让 stack 中
        stack = new Stack<>();
        p = root;
        while(p != null) {
            stack.push(p);
            p = p.left;
        }
    }
    
    /** @return the next smallest number */
    public int next() {            // while 循环中的循环体
        while(p != null) {
            stack.push(p);
            p = p.left;
        }
        // p is null
        p = stack.pop();
        int res = p.val;
        p = p.right;
        return res;
    }
    
    /** @return whether we have a next smallest number */
    public boolean hasNext() {         // while 循环可以继续的条件
        return !stack.isEmpty() || p != null;
    }
}
~~~



## 739 Daily Temperatures

Given a list of daily temperatures `T`, return a list such that, for each day in the input, tells you how many days you would have to wait until a warmer temperature. If there is no future day for which this is possible, put `0` instead.

For example, given the list of temperatures `T = [73, 74, 75, 71, 69, 72, 76, 73]`, your output should be `[1, 1, 4, 2, 1, 1, 0, 0]`.

**Note:** The length of `temperatures` will be in the range `[1, 30000]`. Each temperature will be an integer in the range `[30, 100]`.



- ==递减栈==
- **栈中存放**的都是 “**等待被求解**的元素位置”
- 这个题对于 arr 最后的数字，不需要进行单独的判断，因为他的结果一定是0

- 时间复杂度是 O（N），空间复杂度是 O（W），W表示可能的最长递减序列的长度
- **就是在 array 中找第一个比 cur value 大的数，然后找出他们之间的索引差**

~~~java
class Solution {
    // 用递减栈来做，栈里只有递减元素
    // 就是在 array 中找第一个比 cur value 大的数，然后找出他们之间的索引差
    // 栈中存放的是索引
    // 由于当前数字大于栈顶元素的数字，而且一定是第一个大于栈顶元素的数，那么直接求出下标差就是二者的距离了
    
    public int[] dailyTemperatures(int[] T) {
        Stack<Integer> stack = new Stack<>();
        int n = T.length;
        int[] res = new int[n];
        
        for(int i = 0; i < n; i++) {
            while(!stack.isEmpty() && T[stack.peek()] < T[i]) {
                int index = stack.pop();
                res[index] = i - index;
            }
            
            // 否则，满足递减栈的要求
            stack.push(i);
        }
        return res;
    }
}
~~~



## 735 Asteroid Collision

We are given an array `asteroids` of integers representing asteroids in a row.

For each asteroid, the absolute value represents its size, and the sign represents its direction (positive meaning right, negative meaning left). Each asteroid moves at the same speed.

Find out the state of the asteroids after all collisions. If two asteroids meet, the smaller one will explode. If both are the same size, both will explode. Two asteroids moving in the same direction will never meet.

**Example 1:**

```
Input: 
asteroids = [5, 10, -5]
Output: [5, 10]
Explanation: 
The 10 and -5 collide resulting in 10.  The 5 and 10 never collide.
```



**Example 2:**

```
Input: 
asteroids = [8, -8]
Output: []
Explanation: 
The 8 and -8 collide exploding each other.
```



**Example 3:**

```
Input: 
asteroids = [10, 2, -5]
Output: [10]
Explanation: 
The 2 and -5 collide resulting in -5.  The 10 and -5 collide resulting in 10.
```



**Example 4:**

```
Input: 
asteroids = [-2, -1, 1, 2]
Output: [-2, -1, 1, 2]
Explanation: 
The -2 and -1 are moving left, while the 1 and 2 are moving right.
Asteroids moving the same direction never meet, so no asteroids will meet each other.
```



**Note:**

The length of `asteroids` will be at most `10000`.

Each asteroid will be a non-zero integer in the range `[-1000, 1000].`.



我的解法：如果最后 stack 不是空，说明所有 negative 的都被消灭了，直接接着res继续往后加就可以；而在 while 循环中，如果有 negarive number 不能够被 stack 中所有的 正数 消化，那就直接加入 res 中

~~~java
class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        List<Integer> res = new ArrayList<>();
        Stack<Integer> stack = new Stack<>();
        int n = asteroids.length;
        
        for(int i = 0; i < n; i++) {
            int cur = asteroids[i];
            if(cur > 0)
                stack.push(cur);
            else if(cur < 0) {
                while(!stack.isEmpty() && cur < 0) {
                    int top = stack.pop();
                    if(top + cur == 0)
                        cur = 0;
                    else
                        cur = (Math.abs(cur) > Math.abs(top))? cur: top;
                }
                if(cur > 0)
                    stack.push(cur);
                if(stack.isEmpty() && cur < 0)
                    res.add(cur);
            }
        }
        
        int end = res.size();
        while(!stack.isEmpty()) {
            res.add(end, stack.pop());
        }
        
        
        int[] ans = new int[res.size()];
        for(int i = 0; i < res.size(); i++)
            ans[i] = res.get(i);
        
        return ans;
    }
}
~~~



下面是更加简洁的解法：

1. 从左向右扫描，如果遇到正数，就可以放到容器里面； 如果遇到负数，<u>那么看容器里面原来的值， 如果新遇到的质量比较大，那么原来的就可能会连续被撞掉</u>。 那么，适合的数据结构就是堆栈。
2. 具体步骤如下:
    a. 遇到正数， 就加到堆栈里面
    b. 遇到负数，

- 先把堆栈上的绝对值更小的正数全部弹出
- 如果堆栈上有绝对值一样的正数，也弹出， 同时结束当前步骤（不需要把当前数加到堆栈上）
- 否则，就剩下几种情况
- 要么堆栈是空的 –> 需要加入新来的数
- 要么堆栈顶端的数是负数 –> 需要加入新来的数
- 要么堆栈顶端的正数比新来的数绝对值大 –> 不需要加入新来的数

时间复杂度和空间复杂度都是 O(N)

~~~java
class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        Stack<Integer> stack = new Stack<>();
        int n = asteroids.length;
        
        for(int num: asteroids) {
            if(num > 0) {
                stack.push(num);
            } else {     // negative number
                while(!stack.isEmpty() && stack.peek() > 0 && stack.peek() < -num) // 比num小的正数
                    stack.pop();
                if(!stack.isEmpty() && stack.peek() > 0 && stack.peek() == -num) // 抵消
                    stack.pop();
                else if(stack.isEmpty() || stack.peek() < 0)  // top 元素是负数
                    stack.push(num);
            }
        }
        int[] res = new int[stack.size()];
        for(int i = stack.size() - 1; i >= 0; i--)
            res[i] = stack.pop();
        
        return res;
    }
}
~~~





## 844. Backspace String Compare

Given two strings `S` and `T`, return if they are equal when both are typed into empty text editors. `#` means a backspace character.

Note that after backspacing an empty text, the text will continue empty.

**Example 1:**

```
Input: S = "ab#c", T = "ad#c"
Output: true
Explanation: Both S and T become "ac".
```

**Example 2:**

```
Input: S = "ab##", T = "c#d#"
Output: true
Explanation: Both S and T become "".
```

**Example 3:**

```
Input: S = "a##c", T = "#a#c"
Output: true
Explanation: Both S and T become "c".
```

**Example 4:**

```
Input: S = "a#c", T = "b"
Output: false
Explanation: S becomes "c" while T becomes "b".
```

**Note**:

- `1 <= S.length <= 200`
- `1 <= T.length <= 200`
- `S` and `T` only contain lowercase letters and `'#'` characters.

**Follow up:**

- Can you solve it in `O(N)` time and `O(1)` space?





- 利用 Stack

~~~java
class Solution {
    public boolean backspaceCompare(String S, String T) {
        return process(S).equals(process(T));
    }
    
    private String process(String s) {
        Stack<Character> stack = new Stack<>();
        StringBuilder res = new StringBuilder();
        
        for(int i = 0; i < s.length(); i++) {
            if(s.charAt(i) == '#' && !stack.isEmpty())
                stack.pop();
            else if(s.charAt(i) != '#')
                stack.push(s.charAt(i));
        }
        
        while(!stack.isEmpty())
            res.append(stack.pop());
        
        res.reverse();
        return res.toString();
    }
}
~~~



- Two Pointer：i 和 j 指针都从字符串最后往前遍历，并记录 # 的个数；根据当前字符是否是 '#' 和 skip 的次数移动 i 和 j 指针

~~~java
class Solution {
    public boolean backspaceCompare(String S, String T) {
        int i = S.length() - 1;
        int j = T.length() - 1;
        int skipS = 0;
        int skipT = 0;
        
        while(i >= 0 || j >= 0) {
            while(i >= 0) {
                if(S.charAt(i) == '#') {
                    skipS++; 
                    i--;
                } else if(skipS > 0) {
                    skipS--;
                    i--;
                } else {
                    break;
                }
            }
            
            while(j >= 0) {
                if(T.charAt(j) == '#') {
                    skipT++; 
                    j--;
                } else if(skipT > 0) {
                    skipT--;
                    j--;
                } else {
                    break;
                }
            }
            
            if(i >= 0 && j >= 0 && (S.charAt(i) != T.charAt(j))) {  // 当前是“不需要被删除”的字符
                return false;
            }
            if ((i >= 0 && j < 0) || (i < 0 && j >= 0))   // S 和 T 必须要同时 traverse 完成
                return false;

            i--;
            j--;
        }
        return true;
    }
}
~~~



## 907. Sum of Subarray Minimums

Given an array of integers `A`, find the sum of `min(B)`, where `B` ranges over every (contiguous) subarray of `A`.

Since the answer may be large, **return the answer modulo `10^9 + 7`.**

 

**Example 1:**

```
Input: [3,1,2,4]
Output: 17
Explanation: Subarrays are [3], [1], [2], [4], [3,1], [1,2], [2,4], [3,1,2], [1,2,4], [3,1,2,4]. 
Minimums are 3, 1, 2, 4, 1, 1, 2, 1, 1, 1.  Sum is 17.
```

 

**Note:**

1. `1 <= A.length <= 30000`
2. `1 <= A[i] <= 30000`



方法一是：Dynamic programming（详情请见 Dynamic Programming章节）

方法二是：单调栈

这样时间复杂度就从 O(N^2) 降到了 O(N)

~~~java
class Solution {
    // subMin[i] 表示以数字 A[i] 结尾的所有子数组最小值之和
    // stack 开始 push -1 有利于处理 index 越界为-1的情况，也有助于求“索引之差”
    
    public int sumSubarrayMins(int[] A) {
        int n = A.length;
        if(n == 0)
            return 0;
        int M = (int)1e9 + 7;
        int res = 0;
        
        int[] subMin = new int[n + 1];
        Stack<Integer> stack = new Stack<>();
        stack.push(-1);    // push(-1) 是当往前找的index出界（==-1）时的值，就是-1
        
        for(int i = 0; i < n; i++) {
            while(stack.peek() != -1 && A[stack.peek()] >= A[i])
                stack.pop();
            
            // 此时 stack top 的元素就是第一个比 A[i] 小的元素，下面必须用peek，别用pop
            subMin[i + 1] = subMin[stack.peek() + 1] + (i - stack.peek()) * A[i];
            stack.push(i);  // 将每个索引都push进去，因为我们要在所有 i 之前的元素中找
            res = (res + subMin[i + 1]) % M;
        }
        return res;
    }
}
~~~



方法三：两个单调栈和两个一维数组，**只要知道了其前面第一个小于其的数字位置，和后面第一个小于其的数字位置，就能知道当前数字是多少个子数组的最小值**

left[i] 表示以 A[i] 为结束为止且 A[i] 是最小值的子数组的个数，right[i] 表示以 A[i] 为起点且 A[i] 是最小值的子数组的个数

<img src="C:\Users\yawei\AppData\Roaming\Typora\typora-user-images\image-20200930105938411.png" alt="image-20200930105938411" style="zoom:50%;" />

~~~java
class Solution {
    
    public int sumSubarrayMins(int[] A) {
        int n = A.length;
        if(n == 0)
            return 0;
        int M = (int)1e9 + 7;
        int res = 0;
        
        int[] left = new int[n];   // 以 A[i] 为结束为止且 A[i] 是最小值的子数组的个数
        int[] right = new int[n];  // 以 A[i] 为起点为止且 A[i] 是最小值的子数组的个数
        Stack<int[]> stPrev = new Stack<>();
        Stack<int[]> stNext = new Stack<>();
        
        for(int i = 0; i < n; i++) {
            // 先处理stPrev和left数组
            while(!stPrev.isEmpty() && stPrev.peek()[0] > A[i]) {
                stPrev.pop();
            }
            left[i] = (stPrev.isEmpty())? (i + 1): (i - stPrev.peek()[1]);
            stPrev.push(new int[]{A[i], i});
            
            right[i] = n - i;
            while(!stNext.isEmpty() && stNext.peek()[0] > A[i]) {
                int[] cur = stNext.pop();
                right[cur[1]] = i - cur[1];
            }
            stNext.push(new int[]{A[i], i});
        }
        
        for(int i = 0; i < n; i++)
            res = (res + A[i] * left[i] * right[i]) % M;
        
        return res;
    }
}
~~~



方法四：只用一个单调栈，用来记录当前数字之前的第一个小的数字的位置

遍历每个数字，**但是要多遍历一个数字，i从0遍历到n，当 i=n 时，cur 赋值为0，否则赋值为 A[i]**。然后判断若栈不为空，且 cur 小于栈顶元素，则取出栈顶元素位置 idx，**由于是单调栈，那么新的栈顶元素就是 A[idx] 前面第一个较小数的位置**，由于此时栈可能为空，所以再去之前要判断一下，若为空，则返回 -1，否则返回栈顶元素，用 idx 减去栈顶元素就是**以 A[idx] 为结尾且最小值为 A[idx]** 的子数组的个数，然后用i减去 idx 就是**以 A[idx] 为起始且最小值为 A[idx]** 的子数组的个数，然后 A[idx] x left x right 就是 A[idx] 这个数字当子数组的最小值之和，累加到结果 res 中并对超大数取余即可

~~~java
class Solution {
    
    public int sumSubarrayMins(int[] A) {
        int n = A.length;
        if(n == 0)
            return 0;
        int M = (int)1e9 + 7;
        int res = 0;

        Stack<Integer> stack = new Stack<>();
        
        for(int i = 0; i <= n; i++) {  // 因为对每个A[idx]，前面最小值索引从stack top可知，后面最小值索引就是 i
            int cur = (i == n)? 0: A[i];
            while(!stack.isEmpty() && A[stack.peek()] > cur) {   // 每次求解的对象是 A[idx]
                int idx = stack.pop();
                int left = idx - ((stack.isEmpty())? -1: stack.peek());
                int right = i - idx;
                res = (res + A[idx] * left * right) % M;  // 所以这里 left 和 right 相乘的也是 A[idx]
            }
            stack.push(i);
        }
        
        return res;
    }
}
~~~





## 946. Validate Stack Sequences

Given two sequences `pushed` and `popped` **with distinct values**, return `true` if and only if this could have been the result of a sequence of push and pop operations on an initially empty stack.

 

**Example 1:**

```
Input: pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
Output: true
Explanation: We might do the following sequence:
push(1), push(2), push(3), push(4), pop() -> 4,
push(5), pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1
```

**Example 2:**

```
Input: pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
Output: false
Explanation: 1 cannot be popped before 2.
```

 

**Constraints:**

- `0 <= pushed.length == popped.length <= 1000`
- `0 <= pushed[i], popped[i] < 1000`
- `pushed` is a permutation of `popped`.
- `pushed` and `popped` have distinct values.



方法一：时间复杂度和空间复杂度都是 O(N)

创建一个空的 Stack，根据两个数组 push 和 pop 数据

~~~java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack<>();
        int n = pushed.length;
        int i = 0;
        
        for(int e: pushed) {
            stack.push(e);
            while(!stack.isEmpty() && stack.peek() == popped[i]) {
                stack.pop();
                i++;
            }
        }
        
        return stack.isEmpty();
    }
}
~~~



方法二：空间复杂度是 O(1)，把 pushed 数组当作 stack 对待

~~~java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        int i = 0;
        int j = 0;
        for(int e: pushed) {
            pushed[i] = e;
            i++;                   // 这里的 i 相当于 stack 的 top 指针
            while(i > 0 && pushed[i - 1] == popped[j]) {
                i--;   // 降低 top 指针位置，相当于 stack 的 pop
                j++;
            }
        }
        
        return i == 0;   // 判断 top 指针是否为0，即 stack 是否为空
    }
}
~~~



## 901. Online Stock Span

Write a class `StockSpanner` which collects daily price quotes for some stock, and returns the *span* of that stock's price for the current day.

The span of the stock's price today is defined as the maximum number of consecutive days (starting from today and going backwards) for which the price of the stock was less than or equal to today's price.

For example, if the price of a stock over the next 7 days were `[100, 80, 60, 70, 60, 75, 85]`, then the stock spans would be `[1, 1, 1, 2, 1, 4, 6]`.

 

**Example 1:**

```
Input: ["StockSpanner","next","next","next","next","next","next","next"], [[],[100],[80],[60],[70],[60],[75],[85]]
Output: [null,1,1,1,2,1,4,6]
Explanation: 
First, S = StockSpanner() is initialized.  Then:
S.next(100) is called and returns 1,
S.next(80) is called and returns 1,
S.next(60) is called and returns 1,
S.next(70) is called and returns 2,
S.next(60) is called and returns 1,
S.next(75) is called and returns 4,
S.next(85) is called and returns 6.

Note that (for example) S.next(75) returned 4, because the last 4 prices
(including today's price of 75) were less than or equal to today's price.
```

 

**Note:**

1. Calls to `StockSpanner.next(int price)` will have `1 <= price <= 10^5`.
2. There will be at most `10000` calls to `StockSpanner.next` per test case.
3. There will be at most `150000` calls to `StockSpanner.next` across all test cases.
4. The total time limit for this problem has been reduced by 75% for C++, and 50% for all other languages.



方法一：用 stack 存放一个 pair，<price, index>，根据 index 索引之差求取长度

~~~java
class StockSpanner {
    Stack<int[]> stack;
    int curIndex;

    public StockSpanner() {
        stack = new Stack<>();
        stack.push(new int[]{0, -1});
        curIndex = 0;
    }
    
    public int next(int price) {
        while(stack.peek()[1] != -1 && stack.peek()[0] <= price) {
            stack.pop();
        }
        int[] larger = stack.peek();
        stack.push(new int[]{price, curIndex});
        curIndex++;
        
        return curIndex - larger[1] - 1;
        
    }
}
~~~



方法二：还是用 stack 存放一个 pair，<price, count>，count 直接累加连续满足题意得天数。而且这是个单调递减栈，stack 中得数字都是递增的

~~~java
class StockSpanner {
    Stack<int[]> stack;

    public StockSpanner() {
        stack = new Stack<>();
    }
    
    public int next(int price) {
        int count = 1;            // number of consecutive days 初始化为 1
        while(!stack.isEmpty() && stack.peek()[0] <= price) {
            count += stack.pop()[1];
        }
        stack.push(new int[]{price, count});
        return count;
    }
}
~~~





## 1130. Minimum Cost Tree From Leaf Values

Given an array `arr` of positive integers, consider all binary trees such that:

- Each node has either 0 or 2 children;
- The values of `arr` correspond to the values of each **leaf** in an in-order traversal of the tree. *(Recall that a node is a leaf if and only if it has 0 children.)*
- The value of each non-leaf node is equal to the product of the largest leaf value in its left and right subtree respectively.

Among all possible binary trees considered, return the smallest possible sum of the values of each non-leaf node. It is guaranteed this sum fits into a 32-bit integer.

 

**Example 1:**

```
Input: arr = [6,2,4]
Output: 32
Explanation:
There are two possible trees.  The first has non-leaf node sum 36, and the second has non-leaf node sum 32.

    24            24
   /  \          /  \
  12   4        6    8
 /  \               / \
6    2             2   4
```

 

**Constraints:**

- `2 <= arr.length <= 40`
- `1 <= arr[i] <= 15`
- It is guaranteed that the answer fits into a 32-bit signed integer (ie. it is less than `2^31`).



方法一：（不建议采取）DP

这道题关键点是要**理解中序遍历的意思**：左子树就是该数组某个元素K的所有左边元素，右子树就是其所有右边元素，他们两边挑选出最大值的乘积就是题目中要求的一个非叶子节点，也就是左右子树的一个根。 然后再根据动态规划，再去求(0到K)和(K+1到n-1)的子问题的最小值。 即：dp[i, j] = dp[i, k] + dp[k + 1, j] + max(A[i, k]) * max(A[k + 1, j]) 跟leetcode1039算一个套路。

时间复杂度 O(N^3)，空间复杂度 O(N^2)

~~~

~~~



This is not a dp problem at all.



Because dp solution test all ways to build up the tree,
including many unnecessay tries.
Honestly speaking, it's kinda of brute force.
Yes, brute force testing, with memorization.



**这个题和 1019， 503 很像**

如果要想非叶子节点的 sum 最小，那么就要让比较大的值尽量少乘。比如题中给的例子，第一个 tree 6参与了两次乘法，最后 sum 肯定会比较大；而第二个 tree 是2和4参与了两次乘法，肯定比第一个 tree 要小



所以：我们要让小的数尽量多地参与到乘法运算当中，即：小的数尽量在 tree 更深层地地方，也就是从下往上构建 tree 的时候，有限选择更小的数字，让更大的数字靠近上层

连续的三个数字形成一个凹形，比如6，2，4

~~~java
class Solution {
    // 其实就是 find next larger element
    // 采用的递减栈
    
    public int mctFromLeafValues(int[] arr) {
        Stack<Integer> stack = new Stack<>();
        stack.push(Integer.MAX_VALUE);  // 为了处理 stack 中只剩下两个元素的情况，让栈中仍然保留三个值
        int res = 0;
        
        for(int e: arr) {
            while(stack.peek() <= e) {   // 找到了一个凹形
                int valley = stack.pop();
                res += valley * Math.min(stack.peek(), e);
            }
            stack.push(e);
        }
        
        while(stack.size() > 2) {
            res += stack.pop() * stack.peek();  // 先 pop 再 peek
        }
        return res;
    }
}
~~~



## 496. Next Greater Element I

You are given two arrays **(without duplicates)** `nums1` and `nums2` where `nums1`’s elements are subset of `nums2`. Find all the next greater numbers for `nums1`'s elements in the corresponding places of `nums2`.

The Next Greater Number of a number **x** in `nums1` is the first greater number to its right in `nums2`. If it does not exist, output -1 for this number.

**Example 1:**

```
Input: nums1 = [4,1,2], nums2 = [1,3,4,2].
Output: [-1,3,-1]
Explanation:
    For number 4 in the first array, you cannot find the next greater number for it in the second array, so output -1.
    For number 1 in the first array, the next greater number for it in the second array is 3.
    For number 2 in the first array, there is no next greater number for it in the second array, so output -1.
```



**Example 2:**

```
Input: nums1 = [2,4], nums2 = [1,2,3,4].
Output: [3,-1]
Explanation:
    For number 2 in the first array, the next greater number for it in the second array is 3.
    For number 4 in the first array, there is no next greater number for it in the second array, so output -1.
```



**Note:**

1. All elements in `nums1` and `nums2` are unique.
2. The length of both `nums1` and `nums2` would not exceed 1000.



时间复杂度和空间复杂度都是 O(N)

~~~java
class Solution {
    // 用的是递减栈
    // 哈希表建立每个数字和其右边第一个较大数之间的映射，没有的话就是-1。
    // 我们遍历原数组中的所有数字，如果此时栈不为空，且栈顶元素小于当前数字，说明当前数字就是栈顶元素的右边第一个较大数，那么建立二者的映射，并且去除当前栈顶元素，最后将当前遍历到的数字压入栈。
    // 当所有数字都建立了映射，那么最后我们可以直接通过哈希表快速的找到子集合中数字的右边较大值
    
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Stack<Integer> stack = new Stack<>();
        HashMap<Integer, Integer> map = new HashMap<>();
        int[] res = new int[nums1.length];
        
        for(int i = 0; i < nums2.length; i++) {
            while(!stack.isEmpty() && stack.peek() < nums2[i]) {
                map.put(stack.pop(), nums2[i]);
            }
            stack.push(nums2[i]);
        }
        
        while(!stack.isEmpty())
            map.put(stack.pop(), -1);
        
        for(int i = 0; i < nums1.length; i++)
            res[i] = map.get(nums1[i]);
        
        return res;
    }
}
~~~



## 682. Baseball Game

You're now a baseball game point recorder.

Given a list of strings, each string can be one of the 4 following types:

1. `Integer` (one round's score): Directly represents the number of points you get in this round.
2. `"+"` (one round's score): Represents that the points you get in this round are the sum of the last two `valid` round's points.
3. `"D"` (one round's score): Represents that the points you get in this round are the doubled data of the last `valid` round's points.
4. `"C"` (an operation, which isn't a round's score): Represents the last `valid` round's points you get were invalid and should be removed.



Each round's operation is permanent and could have an impact on the round before and the round after.

You need to return the sum of the points you could get in all the rounds.

**Example 1:**

```
Input: ["5","2","C","D","+"]
Output: 30
Explanation: 
Round 1: You could get 5 points. The sum is: 5.
Round 2: You could get 2 points. The sum is: 7.
Operation 1: The round 2's data was invalid. The sum is: 5.  
Round 3: You could get 10 points (the round 2's data has been removed). The sum is: 15.
Round 4: You could get 5 + 10 = 15 points. The sum is: 30.
```



**Example 2:**

```
Input: ["5","-2","4","C","D","9","+","+"]
Output: 27
Explanation: 
Round 1: You could get 5 points. The sum is: 5.
Round 2: You could get -2 points. The sum is: 3.
Round 3: You could get 4 points. The sum is: 7.
Operation 1: The round 3's data is invalid. The sum is: 3.  
Round 4: You could get -4 points (the round 3's data has been removed). The sum is: -1.
Round 5: You could get 9 points. The sum is: 8.
Round 6: You could get -4 + 9 = 5 points. The sum is 13.
Round 7: You could get 9 + 5 = 14 points. The sum is 27.
```



**Note:**

The size of the input list will be between 1 and 1000.

Every integer represented in the list will be between -30000 and 30000.



直接用栈解决：

~~~java
class Solution {
    public int calPoints(String[] ops) {
        Stack<Integer> stack = new Stack<>();
        for(String s: ops) {
            if(s.equals("+")) {
                int top = stack.pop();
                int newTop = top + stack.peek();
                stack.push(top);
                stack.push(newTop);
            } else if(s.equals("C")) {
                stack.pop();
            } else if(s.equals("D")) {
                stack.push(2 * stack.peek());
            } else {
                stack.push(Integer.parseInt(s));
            }
        }
        
        int res = 0;
        while(!stack.isEmpty())
            res += stack.pop();
        
        return res;
    }
}
~~~



## 1381. Design a Stack With Increment Operation

方法一：直接用数组 array 模拟栈的过程

~~~java
class CustomStack {
    int[] stack;
    int top;
    int capacity;
        
    public CustomStack(int maxSize) {
        top = -1;
        capacity = maxSize;
        stack = new int[capacity];
    }
    
    public void push(int x) {
        if(top < capacity - 1) {
            top++;
            stack[top] = x;
        }
    }
    
    public int pop() {
        if(top == -1)
            return -1;
        return stack[top--];
    }
    
    public void increment(int k, int val) {
        int i = 0;
        while(i < k && i <= top) {
            stack[i] += val;
            i++;
        }
    }
}
~~~



方法二：将 increment 的时间复杂度降低为 O(1)

我们**需要一个数组来辅助记录每一个位置的数字被加上了多少**，这样**出栈的时候直接是栈中元素和当前辅助数组元素的和**。如果直接记录的话，时间复杂度是O(k)的。我们可以简化成O(1)的操作：每次只在辅助数组**第k个位置上加上val**，等到这个元素出栈的时候，我们将该辅助数组上的arr[k]加到arr[k - 1]上，同时将arr[k]清零

~~~java
class CustomStack {
    int capacity;
    Stack<Integer> stack;
    int[] inc;

    public CustomStack(int maxSize) {
        capacity = maxSize;
        stack = new Stack<>();
        inc = new int[maxSize];
    }
    
    public void push(int x) {
        if(stack.size() < capacity)
            stack.push(x);
    }
    
    public int pop() {
        int i = stack.size() - 1;   // stack 顶部元素在 inc 中的索引
        if(i < 0) {
            return -1;
        } 
        if(i > 0) {
            inc[i - 1] += inc[i];   // 把 i 位置上额外加上的值，传递到前一个位置上
        }
        int res = stack.pop() + inc[i];
        inc[i] = 0;    // 注意 inc[i] 在使用之后，要重新赋值为0
        return res;
    }
    
    public void increment(int k, int val) {
        int i = Math.min(k, stack.size()) - 1;
        if(i >= 0)
            inc[i] += val;   // 只对第 k 个或者栈顶元素的 inc 进行 increment
    }
}
~~~



## 921. Minimum Add to Make Parentheses Valid

Given a string `S` of `'('` and `')'` parentheses, we add the minimum number of parentheses ( `'('` or `')'`, and in any positions ) so that the resulting parentheses string is valid.

Formally, a parentheses string is valid if and only if:

- It is the empty string, or
- It can be written as `AB` (`A` concatenated with `B`), where `A` and `B` are valid strings, or
- It can be written as `(A)`, where `A` is a valid string.

Given a parentheses string, return the minimum number of parentheses we must add to make the resulting string valid.

 

**Example 1:**

```
Input: "())"
Output: 1
```

**Example 2:**

```
Input: "((("
Output: 3
```

**Example 3:**

```
Input: "()"
Output: 0
```

**Example 4:**

```
Input: "()))(("
Output: 4
```

 

**Note:**

1. `S.length <= 1000`
2. `S` only consists of `'('` and `')'` characters.



~~~java
class Solution {
    public int minAddToMakeValid(String S) {
        Stack<Character> stack = new Stack<>();
        int res = 0;
        
        for(int i = 0; i < S.length(); i++) {
            char cur = S.charAt(i);
            if(cur == '(') {
                stack.push(cur);
            } else {
                if(!stack.isEmpty()) {
                    stack.pop();
                    continue;
                } else {
                    res++;
                }
            }
        }
        
        while(!stack.isEmpty()) {
            res++;
            stack.pop();
        }
        
        return res;
    }
}
~~~





## 1019. Next Greater Node In Linked List

We are given a linked list with `head` as the first node. Let's number the nodes in the list: `node_1, node_2, node_3, ...` etc.

Each node may have a *next larger* **value**: for `node_i`, `next_larger(node_i)` is the `node_j.val` such that `j > i`, `node_j.val > node_i.val`, and `j` is the smallest possible choice. If such a `j` does not exist, the next larger value is `0`.

Return an array of integers `answer`, where `answer[i] = next_larger(node_{i+1})`.

Note that in the example **inputs** (not outputs) below, arrays such as `[2,1,5]` represent the serialization of a linked list with a head node value of 2, second node value of 1, and third node value of 5.

 

**Example 1:**

```
Input: [2,1,5]
Output: [5,5,0]
```

**Example 2:**

```
Input: [2,7,4,3,5]
Output: [7,0,5,5,0]
```

**Example 3:**

```
Input: [1,7,5,1,9,2,5,1]
Output: [7,9,9,9,0,5,0,0]
```

 

**Note:**

1. `1 <= node.val <= 10^9` for each node in the linked list.
2. The given list has length in the range `[0, 10000]`.



~~~java
class Solution {
    public int[] nextLargerNodes(ListNode head) {
        if(head == null)
            return new int[]{};
        Stack<int[]> stack = new Stack<>();
        ListNode cur = head;
        int len = 0;
        while(cur != null) {
            cur = cur.next;
            len++;
        }
        int[] res = new int[len];
        cur = head;
        int i = 0;
        while(cur != null) {
            while(!stack.isEmpty() && stack.peek()[0] < cur.val) {
                res[stack.pop()[1]] = cur.val;
            }
            stack.push(new int[]{cur.val, i});
            i++;
            cur = cur.next;
        }
        
        return res;
    }
}
~~~



## 456. 132 Pattern

Given an array of `n` integers `nums`, a **132 pattern** is a subsequence of three integers `nums[i]`, `nums[j]` and `nums[k]` such that `i < j < k` and `nums[i] < nums[k] < nums[j]`.

Return *`true` if there is a **132 pattern** in `nums`, otherwise return `false`.*

 

**Example 1:**

```
Input: nums = [1,2,3,4]
Output: false
Explanation: There is no 132 pattern in the sequence.
```

**Example 2:**

```
Input: nums = [3,1,4,2]
Output: true
Explanation: There is a 132 pattern in the sequence: [1, 4, 2].
```

**Example 3:**

```
Input: nums = [-1,3,2,0]
Output: true
Explanation: There are three 132 patterns in the sequence: [-1, 3, 2], [-1, 3, 0] and [-1, 2, 0].
```

 

**Constraints:**

- `n == nums.length`
- `1 <= n <= 3 * 104`
- `-109 <= nums[i] <= 109`



方法一：优化版 brute force，时间复杂度是 O（N^2）

~~~java
class Solution {
    // 先固定一个数字，然后去遍历另外两个数字。我们先确定哪个数字呢，当然是最小的那个啦
    // 我们维护一个变量 mn，初始化为整型最大值，然后在遍历数字的时候，每次用当前数字来更新 mn，然后我们判断，若 mn 为当前数字就跳过，因为需要找到数字j的位置，数字j是大于数字i的，mn 表示的就是数字i。这样数字i和数字j都确定了之后，就要来遍历数字k了，范围是从数组的最后一个位置到数字j之间，只要中间的任何一个数字满足题目要求的关系，就直接返回 true 即可
    
    public boolean find132pattern(int[] nums) {
        int minVal = Integer.MAX_VALUE;
        for(int j = 0; j < nums.length; j++) {
            minVal = Math.min(minVal, nums[j]); // 更新最小值，nums[j]就是当前指定的最大值，同时找到 max 和 min
            for(int k = j + 1; k < nums.length; k++) {
                if(minVal < nums[k] && nums[k] < nums[j])
                    return true;
            }
        }
        return false;
    }
}
~~~



方法二：维护一个 third 变量和 stack

我们在遍历的时候，**如果当前数字小于 third，即 pattern 132 中的1找到了，我们直接返回 true 即可**，因为已经找到了，注意我们应该**从后往前遍历数组**。如果**当前数字大于栈顶元素，那么我们将栈顶数字取出，赋值给 third，然后将该数字压入栈，这样保证了栈里的元素仍然都是大于 third 的，我们想要的顺序依旧存在**，进一步来说，栈里存放的都是可以维持坐标 second > third 的 second 值，其中的任何一个值都是大于当前的 third 值，如果有更大的值进来，那就等于形成了一个更优的 second > third 的这样一个组合，并且这时弹出的 third 值比以前的 third 值更大，为什么要保证 third 值更大，因为这样才可以更容易的满足当前的值 first 比 third 值小这个条件

~~~java
class Solution {   
    // 我们维护一个栈和一个变量 third
    // 其中 third 就是第三个数字，也是 pattern 132 中的 2，初始化为整型最小值
    // 栈里面按顺序放所有大于 third 的数字，也是 pattern 132 中的 3
    
    public boolean find132pattern(int[] nums) {
        Stack<Integer> stack = new Stack<>();
        int third = Integer.MIN_VALUE;  // third 记录的是 132 中的 2 位置上的值
        
        for(int i = nums.length - 1; i >= 0; i--) {
            if(nums[i] < third)  // 找到 1 位置上的值了
                return true;
            while(!stack.isEmpty() && stack.peek() < nums[i]) {  // 找到了一个更大的数可以放栈顶
                third = stack.pop();
            }
            stack.push(nums[i]);
        }
        return false;
    }
}
~~~





## 1190. Reverse Substrings Between Each Pair of Parentheses

You are given a string `s` that consists of lower case English letters and brackets. 

Reverse the strings in each pair of matching parentheses, starting from the innermost one.

Your result should **not** contain any brackets.

 

**Example 1:**

```
Input: s = "(abcd)"
Output: "dcba"
```

**Example 2:**

```
Input: s = "(u(love)i)"
Output: "iloveu"
Explanation: The substring "love" is reversed first, then the whole string is reversed.
```

**Example 3:**

```
Input: s = "(ed(et(oc))el)"
Output: "leetcode"
Explanation: First, we reverse the substring "oc", then "etco", and finally, the whole string.
```

**Example 4:**

```
Input: s = "a(bcdefghijkl(mno)p)q"
Output: "apmnolkjihgfedcbq"
```

 

**Constraints:**

- `0 <= s.length <= 2000`
- `s` only contains lower case English characters and parentheses.
- It's guaranteed that all parentheses are balanced.



定义一个全局的 res 变量，并且在最后也会返回这个 res 变量

~~~java
class Solution {
    public String reverseParentheses(String s) {
        Stack<String> stack = new Stack<>();
        int n = s.length();
        String res = "";
        
        for(int i = 0; i < n; i++) {
            char cur = s.charAt(i);
            if(cur >= 'a' && cur <= 'z') {
                res += cur;
            }
            if(cur == '(') {   // 遇到 open bracket 才 push 到栈中
                stack.push(res);
                res = "";
            } else if(cur == ')') {  // 遇到 close bracket 就从栈中不断地弹出前面的 string 拼接
                String p = stack.pop();
                String r = new StringBuilder(res).reverse().toString();
                res = p + r; 
            }
        }
        return res;
    }
}
~~~



