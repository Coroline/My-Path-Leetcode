# Floyd证明

1. 证明如果存在环的话，slow 和 fast 一定会相遇

2. 证明存在环的话，finder从起点出发，slow从相遇点出发，速度都为1，他们一定在环的入口相遇

    

*证明快指针 fast 一定会追上慢指针 slow。*

>slow 在 fast 前一个结点时，两者经过一次移动两者相遇。
假设 slow 在 fast 前 k 个结点时，经过有限次移动两者一定会相遇。
则当 slow 在 fast 前 k+1 个结点时，经过一次移动，变为 slow 在 fast 前 k 个结点，则经过有限次移动两者一定会相遇。

*证明 finder指针 一定会和慢指针 slow 相会于环的起点*

![image.png](https://pic.leetcode-cn.com/381f25f68dd37d17a7de0e1e388e0a28b29ba05c05e9555a484a7bfec11ed457-image.png)

>目标是证明：S = R - M
>
>1. 慢指针走<u>**K步**</u>后与快指针相遇, 此时快指针已经在环内M处, 已经遍历了2K个元素. 
>  所以2K - K = nR.(相当于相遇时, 快指针已经在环内跑了N个圈), 有K = nR.
>2. 当快慢指针相遇时慢指针走了K步, 相当于慢指针进入环内(走了S步后)再走M步.
>  所以K, M, S之间有等量关系:S = K - M.
>3. S = nR - M.
>4. S  = ( (n-1)R + (R - M) ) % R = R - M. 
>



## 287. Find the Duplicate Number

Given an array of integers `nums` containing `n + 1` integers where each integer is in the range `[1, n]` inclusive.

There is only **one duplicate number** in `nums`, return *this duplicate number*.

**Follow-ups:**

1. How can we prove that at least one duplicate number must exist in `nums`? 
2. Can you solve the problem **without** modifying the array `nums`?
3. Can you solve the problem using only constant, `O(1)` extra space?
4. Can you solve the problem with runtime complexity less than `O(n2)`?

 

**Example 1:**

```
Input: nums = [1,3,4,2,2]
Output: 2
```

**Example 2:**

```
Input: nums = [3,1,3,4,2]
Output: 3
```

**Example 3:**

```
Input: nums = [1,1]
Output: 1
```

**Example 4:**

```
Input: nums = [1,1,2]
Output: 1
```

 

**Constraints:**

- `2 <= n <= 3 * 104`
- `nums.length == n + 1`
- `1 <= nums[i] <= n`
- All the integers in `nums` appear only **once** except for **precisely one integer** which appears **two or more** times.



### 方法一：sort

~~~java
class Solution {
    public int findDuplicate(int[] nums) {
        Arrays.sort(nums);
        
        for(int i = 0; i < nums.length - 1; i++) {
            if(nums[i] == nums[i + 1])
                return nums[i];
        }
        return -1;
    }
}
~~~

### 方法二：HashSet

~~~java
class Solution {
    public int findDuplicate(int[] nums) {
        HashSet<Integer> seen = new HashSet<>();
        for(int e: nums) {
            if(seen.contains(e))
                return e;
            else
                seen.add(e);
        }
        return -1;
    }
}
~~~

### 方法三：Binary search （template 2）

==在区间 [1, n] 中搜索，首先求出中点 mid==，**然后遍历整个数组，统计所有<u>小于等于 mid 的数的个数</u>**，如果个数小于等于 mid，则说明重复值在 [mid+1, n] 之间，反之，重复值应在 [1, mid-1] 之间，然后依次类推，直到搜索完成，此时的 low 就是我们要求的重复值

~~~java
class Solution {
    public int findDuplicate(int[] nums) {
        // 二分搜索的方法，Find the smallest m such that len(nums <= m) > m, which means m is the duplicate number，while内要扫描整个 nums
        // m 是没有duplicate的num数组的合法中位数
        int l = 1;
        int r = nums.length;
        while(l < r) {
            int m = l + (r - l) / 2;   // 找 1 - n 的中位数
            int count = 0;
            for(int e: nums) {
                if(e <= m) ++count;
            }
            if(count <= m)
                l = m + 1;
            else
                r = m;
        }
        return l;
    }
}
~~~

### 方法四：Floyd

==将数组转化为链表==，也就是要知道 <u>nums[i] 的 next 是谁</u>：nums[i] = nums[nums[i]] (1, 2, 3, 4,...就符合这样条件)

~~~java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];
        
        do{
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while(slow != fast);
        
        int finder = nums[0];
        while(finder != slow) {
            finder = nums[finder];
            slow = nums[slow];
        }
        
        return finder;
    }
}
~~~



证明环的入口，一定就是 duplicate number 存在的地方

```
解决本题需要的主要技巧就是要注意到：由于数组的n + 1个元素范围从1到n，我们可以将数组考虑成一个从集合{1, 2, ..., n}到其本身的函数f。这个函数的定义为f(i) = A[i]。基于这个设定，重复元素对应于一对下标i != j满足 f(i) = f(j)。"""""""我们的任务就变成了寻找一对(i, j)。一旦我们找到这个值对，只需通过f(i) = A[i]即可获得重复元素。""""""""

但是我们怎样寻找这个重复值呢？这变成了计算机科学界一个广为人知的“环检测”问题。问题的一般形式如下：给定一个函数f，序列x_i的定义为

    x_0     = k       (for some k)
    x_1     = f(x_0)
    x_2     = f(f(x_0))
    ...
    x_{n+1} = f(x_n)

假设函数f从定义域映射到它本身，此时会有"""3种情况"""。首先，如果定义域是无穷的，则序列是无限长并且没有循环的。例如，函数 f(n) = n + 1，在整数范围内满足这个性质 - 没有数字是重复的。 第二， 序列可能是一个闭合循环，这意味着存在一个i使得x_0 = x_i。在这个例子中，序列在一组值内无限循环。第三，序列有可能是的“ρ型的”，此时序列看起来像下面这样：
	  x_0 -> x_1 -> ... x_k -> x_{k+1} ... -> x_{k+j}
	  
	  x_0 -> x_1 -> ... x_k -> x_{k+1} ... -> x_{k+j}
       ^                                         |
       |                                         |
       +-----------------------------------------+
       
      x_0 -> x_1 -> ... x_k -> x_{k+1} ... -> x_{k+j}
                         ^                       |
                         |                       |
                         +-----------------------+

也就是说，序列从一列链条型的元素开始进入一个环，然后无限循环。我们将环的起点称为环的“入口”。

对于从数组中寻找重复元素这个问题，考虑序列从位置n开始重复调用函数f。亦即从数组的最后一个元素开始，然后移动到其元素值对应的下标处，并且重复此过程。可以得到：此序列是ρ型的。要明白这一点，需要注意到其中一定有环，""""""""""""因为数组是有限的并且当访问n个元素时，一定会对某个元素访问两次。无论从数组的哪一个位置开始，这都是成立的。""""""""""

另外，注意由于数组元素范围1到n，因此不存在值为0的元素。进而，从数组的第一个元素开始调用一次函数f之后，再也不会回到这里。这意味着第一个元素不会是环的一部分，但如果我们继续重复调用函数f，最终总会访问某个节点两次。从0节点开始的链条与环形相接，使得其形状一定是ρ型。""""""""""(也就是我后面某个index对应的值，和前面的某个value一样了，说明一定是找到了一对(i, j)，i != j，f(i) == f(j))"""""""""

此外，考虑一下环的入口。由于节点位于环的入口，一定存在两个输入，其对应的函数f的输出值都等于入口元素下标。要使其成立，一定存在两个下标i != j，满足f(i) = f(j)，亦即A[i] = A[j]。因而环的入口一定是重复值。
```