## 15. 3Sum

### 方法一：Two Pointer

时间复杂度是 O(N ^ 2)，空间复杂度是 O(1)，先进行排序

注意避免重复元素，i 要和 i - 1 处大小比较，l 和 l - 1 处比较，r 和 r + 1 处比较，同时要判断是否溢出边界！！！

~~~java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums.length < 3) {
            return res;
        }
            
        Arrays.sort(nums);
        if (nums[0] > 0) {
            return res;
        }
        
        // 通过判断nums[i] == nums[i-1]来避免将同样的三元组多次加入结果中
        // 注意i-1，或者i+1，一定要检查有没有超过边界
        for (int i = 0; i < nums.length - 2; i++) {
            if (nums[i] > 0) {
                return res;
            }
            if (i > 0 && nums[i] == nums[i-1]) {
                continue;
            }
            int complement = -nums[i];
            int l = i + 1;
            int r = nums.length - 1;
            
            while (l < r) {
                while (l < r && ((nums[l] + nums[r] > complement) || (r < nums.length - 1 && nums[r] == nums[r+1]))) {
                    --r;
                }
                while (l < r && ((nums[l] + nums[r] < complement) || (l > i + 1 && nums[l] == nums[l-1]))) {
                    ++l;
                }
                if ((l < r ) && nums[l] + nums[r] == complement) {
                    res.add(Arrays.asList(nums[i], nums[l], nums[r]));
                    l++;
                    r--;
                }
            }
        }
        return res;
    }
}
~~~

另外一种写法，将 while(i < j) 中的 while 改成了 if。对比之下，while的时候就要时刻注意判断 l < r

~~~java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        int n = nums.length;
        Arrays.sort(nums);
        if(n < 3 || nums[0] > 0 || nums[n-1] < 0)
            return res;
        
        for(int k = 0; k < n - 2; k++) {
            if(nums[k] > 0)
                break;
            if((k > 0) && nums[k] == nums[k-1])   // 避免res中有重复的解,必须是num[k - 1]，必须和前面比较
                continue;
            
            int target = -nums[k];
            int i = k + 1;
            int j = n - 1;
            while(i < j) {
                // 左指针和前一个比较，因为有可能会用到 接下来要遍历的元素
                if(nums[i] + nums[j] < target || (i > k + 1) && (nums[i] == nums[i-1]))
                    i++;
                // 右指针和后一个比较，（同样的道理）
                else if(nums[i] + nums[j] > target || (j < n - 1) && (nums[j] == nums[j+1]))
                    j--;
                else {
                    res.add(Arrays.asList(nums[k], nums[i], nums[j]));
                    i++;
                    j--;
                }
            }
        }
        return res;
    }
}
~~~

### 方法二：查找表

inner for 其实就是 two sum 的应用

~~~java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        Set<Pair> found = new HashSet<>();   // 三元组是否出现过
        
        for (int i = 0; i < nums.length; i++) {
            // two sum中的hashmap一样
            // 只不过这里不需要索引也不需要统计解的个数，存e，需要的话取e就可
            Set<Integer> seen = new HashSet<>();   
            
            for (int j = i + 1; j < nums.length; j++) {   
                int complement = -nums[i] - nums[j];
                if (seen.contains(complement)) {   // contains成立的时候，complement就是起初seen里加进去的nums[j]
                    //重要！！！
                    //min和max值确定的话，这个三元组就是确定的
                    int v1 = Math.min(nums[i], Math.min(nums[j], complement));
                    int v2 = Math.max(nums[i], Math.max(nums[j], complement));
                    if (found.add(new Pair(v1, v2))) {
                        res.add(Arrays.asList(nums[i], complement, nums[j]));
                    }
                } else {
                    seen.add(nums[j]);
                }
            }
        }
        return res;
    }
}
~~~



## 18 4Sum

基于 3Sum，时间复杂度O(N^3)

~~~java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> ans = new ArrayList<>();
        if (nums.length < 4) {
            return ans;
        }
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 3; i++) {
            if (i > 0 && nums[i] == nums[i-1])
                continue;
            for (int j = i + 1; j < nums.length - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j-1])
                continue;
                
                int l = j + 1;
                int r = nums.length - 1;
                int complement = target - nums[i] - nums[j];
                while (l < r) {
                    while ((l < r) && ((nums[l] + nums[r] > complement) || (r < nums.length - 1 && nums[r] == nums[r+1])))
                        r--;
                    while ((l < r) && ((nums[l] + nums[r] < complement) || (l > j + 1 && nums[l] == nums[l-1])))
                        l++;
                    if (l < r && nums[l] + nums[r] == complement) {
                        ans.add(Arrays.asList(nums[i], nums[j], nums[l], nums[r]));
                        l++;
                        r--;
                    }
                }
            }
        }
        return ans;
    }
}
~~~



## 16 3Sum Closest

Given an array `nums` of *n* integers and an integer `target`, find three integers in `nums` such that the sum is closest to `target`. Return the sum of the three integers. You may assume that each input would have exactly one solution.

**Example 1:**

```
Input: nums = [-1,2,1,-4], target = 1
Output: 2
Explanation: The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```



需要定义一个**变量 diff 用来记录差的绝对值**，然后还是要先将数组排个序，然后开始遍历数组，思路跟那道三数之和很相似，都是先确定一个数，然后用两个指针 left 和 right 来滑动寻找另外两个数，每确定两个数，**求出此三数之和之后，先更新 diff，再判断指针如何移动**

~~~java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int ans = 0;
        int diff = Integer.MAX_VALUE;
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 2; i++) {
            int l = i + 1;
            int r = nums.length - 1;
            while (l < r) {
                int sum = nums[i] + nums[l] + nums[r];
                if (diff > Math.abs(sum - target)) {
                    diff = Math.abs(sum - target);
                    ans = sum;
                }
                if (nums[i] + nums[l] + nums[r] > target) {
                    r--;
                } else {
                    l++;
                }
            }
        }
        return ans;
    }
}
~~~







- 283 和 27 都是删除指定元素：先 赋值/swap 再 k++；
- 26 和 80 都是删除duplicate：先 k++ 再赋值/swap
- 这四个题都是==满足题目要求的时候==，进行赋值/swap以及k++
- **都需要一个==索引 i== 来遍历arr**，使得 nums[i] 满足某个条件的时候，改变其他指针，使得区间合法



## 283. Move Zeroes

维护有效区间 [0, k]，这个区间(interval) 内不包含 Integer 0

~~~java
class Solution {
    public void moveZeroes(int[] nums) {
        int k = 0;
        for(int i = 0; i < nums.length; i++) {
            if(nums[i] != 0) {
                swap(nums, i, k);
                k++;
            }
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int t = nums[i];
        nums[i] = nums[j];
        nums[j] = t;
    }
}
~~~

优化版，i 和 k 不相等的时候才swap

~~~java
class Solution {
    public void moveZeroes(int[] nums) {
        int k = 0;
        
        for(int i = 0; i < nums.length; i++) {
            if(nums[i] != 0) {
                if (k != i){
                    swap(nums, i, k);
                }
                k++;
            }
        }
    } 
    
    public void swap(int[] n, int i, int j) {
        int temp = n[i];
        n[i] = n[j];
        n[j] = temp;
    }
}
~~~



## 27 Remove Element

相比 283 只是把 0 换成了 val 而已

~~~java
class Solution {
    public int removeElement(int[] nums, int val) {
        // [0...k)全是不等于val的数
        int k = 0;
        for(int i = 0; i < nums.length; i++){
            if(nums[i] != val){
                if(i != k){
                    // swap(nums, i, k);
                    nums[k] = nums[i];
                }
                ++k;
            }
        }
        return k;
    }
    
    public void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
~~~



## 26. Remove Duplicates from Sorted Array

Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each element appear only *once* and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

**Example 1:**

```
Given nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```
Given nums = [0,0,1,1,1,2,2,3,3,4],

Your function should return length = 5, with the first five elements of nums being modified to 0, 1, 2, 3, and 4 respectively.

It doesn't matter what values are set beyond the returned length.
```

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

```
// nums is passed in by reference. (i.e., without making a copy)
int len = removeDuplicates(nums);

// any modification to nums in your function would be known by the caller.
// using the length returned by your function, it prints the first len elements.
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

~~~java
class Solution {
    public int removeDuplicates(int[] nums) {
        // 相等的时候k指针时不动的，不相等的时候才对k+1
        // 类似地，[0...k]包含的是到i为止，没有重复元素的区间
        int k = 0;
        for(int i = 1; i < nums.length; i++) {
            if(nums[i] != nums[i - 1]) {
                k++;
                nums[k] = nums[i];
            }
        }
        return k + 1;
    }
}
~~~



## 80. Remove Duplicates from Sorted Array II

Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that duplicates appeared at most *twice* and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

**Example 1:**

```
Given nums = [1,1,1,2,2,3],

Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3 respectively.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```
Given nums = [0,0,1,1,1,1,2,3,3],

Your function should return length = 7, with the first seven elements of nums being modified to 0, 0, 1, 1, 2, 3 and 3 respectively.

It doesn't matter what values are set beyond the returned length.
```

~~~java
class Solution {
    public int removeDuplicates(int[] nums) {
        int k = 0;
        int count = 1;
        
        for(int i = 1; i < nums.length; i++) {
            if(nums[i - 1] == nums[i])
                count++;
            else
                count = 1;
            
            if(count <= 2) {
                k++;
                nums[k] = nums[i];
            }
        }
        return k + 1;
    }
}
~~~



- 三路快排的应用

## 75. Sort Colors

Given an array `nums` with `n` objects colored red, white, or blue, sort them **[in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

Here, we will use the integers `0`, `1`, and `2` to represent the color red, white, and blue respectively.

**Follow up:**

- Could you solve this problem without using the library's sort function?
- Could you come up with a one-pass algorithm using only `O(1)` constant space?

 

**Example 1:**

```
Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

**Example 2:**

```
Input: nums = [2,0,1]
Output: [0,1,2]
```

**Example 3:**

```
Input: nums = [0]
Output: [0]
```

**Example 4:**

```
Input: nums = [1]
Output: [1]
```



- 改变zero和two指针，zero++，two--，zero+1肯定是1，two-1就不确定了

~~~java
class Solution {
    public void sortColors(int[] nums) {
        int zero = 0;
        int two = nums.length - 1;
        int i = 0;
        
        while(i <= two) {   // 注意取值范围
            if(nums[i] == 0) { 
                swap(nums, i, zero);
                zero++;      // swap 之后，i 指向的元素一定是1
                i++;
            } else if (nums[i] == 2) {  
                swap(nums, i, two);
                two--;  // swap 之后 i 指向的新值没有被处理过，所以 i 不便
            } else {
                i++;
            }
            
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int t = nums[i];
        nums[i] = nums[j];
        nums[j] = t;
    }
}
~~~



## 88. Merge Sorted Array

Given two sorted integer arrays *nums1* and *nums2*, merge *nums2* into *nums1* as one sorted array.

**Note:**

- The number of elements initialized in *nums1* and *nums2* are *m* and *n* respectively.
- You may assume that *nums1* has enough space (size that is **equal** to *m* + *n*) to hold additional elements from *nums2*.

**Example:**

```
Input:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

Output: [1,2,2,3,5,6]
```

 

**Constraints:**

- `-10^9 <= nums1[i], nums2[i] <= 10^9`
- `nums1.length == m + n`
- `nums2.length == n`



- （最粗暴的解法：把nums2复制到nums1后面，再整体sort）==System.arraycopy(nums2, 0, nums1, m, n);==    Arrays.sort(nums1);

- 优化方法：**从后面开始遍历，因为后面赋值不会影响前面元素**

    时间复杂度O（m+n）

    空间复杂度O（1）

~~~java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1;
        int j = n - 1;
        int p = m + n - 1;
        
        while(i >= 0 && j >= 0){
            if(nums1[i] >= nums2[j]){
                nums1[p] = nums1[i];
                i--;
            } else {
                nums1[p] = nums2[j];
                j--;
            }
            p--;
        }
        
        // 这里还要额外赋值剩余nums2部分原因：把nums2 -> nums1中，而且都sorted，剩下的一定sorted，
        // nums1剩下没关系！因为本来人家就在nums1里面，但是nums2剩下就是nums1前面缺的部分
        
        // arraycopy函数：源数组，源数组复制起始位置，目标数组，目标数组起始位置，复制长度
        System.arraycopy(nums2, 0, nums1, 0, j + 1);
    }
}
~~~



## 215. Kth Largest Element in an Array

Find the **k**th largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

**Example 1:**

```
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

**Example 2:**

```
Input: [3,2,3,1,2,4,5,5,6] and k = 4
Output: 4
```

**Note:**
You may assume k is always valid, 1 ≤ k ≤ array's length.



- 利用快速排序

- 因为左半部分的所有值都大于右半部分的任意值，所以我们求出中枢点的位置，如果正好是 k-1，那么直接返回该位置上的数字；如果大于 k-1，说明要求的数字在左半部分，更新右边界，再求新的中枢点位置；反之则更新右半部分，求中枢点的位置，

     

    时间复杂度O(N)，空间复杂度O(1)

     

    简单来说，就是**根据 partition index 和 k-1 比较，决定去哪一边递归**

~~~java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        // 3,2,1,5,6,4  -  4,5,6,3,1,2
        int left = 0;
        int right = nums.length - 1;
        
        while(true){            // 注意这种写法
            int partitionIdx = partition(nums, left, right);
            if(partitionIdx == k - 1){     // 这里注意要是"k-1"，"索引 == 第几个 - 1"
                return nums[k-1];
            } else if(partitionIdx > k - 1){
                right = partitionIdx - 1;   //直接改变左右指针就可以了
            } else {                        //因为while一开始就要找partitionIdx
                left = partitionIdx + 1;
            }
        }
    }
    
    
//     public int quickSort(int[] nums, int left, int right, int k){  
//         if(left < right){
//             int partitionIdx = partition(nums, left, right);

//             if(partitionIdx == k - 1){
//                 return nums[k];
//             } else if(partitionIdx > k){
//                 return quickSort(nums, left, partitionIdx - 1, k);
//             } else {
//                 return quickSort(nums, partitionIdx + 1, right, k);
//             }
//         } else {
//             return nums[left];
//         }
//     }
    
    public int partition(int[] nums, int left, int right){  // 从大到小排序
        int pivot = nums[left];
        int index = left + 1;
        
        for(int i = index; i <= right; i++){
            if(nums[i] > pivot){
                swap(nums, i, index);
                index++;
            }
        }
        swap(nums, left, --index);
        return index;          
    }
    
    public void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;            //这里注意是temp，不要写错了啊啊啊
    }
}
~~~



## 11. Container With Most Water

Given *n* non-negative integers *a1*, *a2*, ..., *an* , where each represents a point at coordinate (*i*, *ai*). *n* vertical lines are drawn such that the two endpoints of line *i* is at (*i*, *ai*) and (*i*, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

**Note:** You may not slant the container and *n* is at least 2.

 

<img src="https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg" alt="img" style="zoom:67%;" />

The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.

 

**Example:**

```
Input: [1,8,6,2,5,4,8,3,7]
Output: 49
```



~~~java
class Solution {
    public int maxArea(int[] height) {
        int ans = Integer.MIN_VALUE;
        int i = 0;
        int j = height.length - 1;
        
        while (i < j){            
            int curHeight = Math.min(height[i], height[j]);  // 计算当前值
            int curWater = curHeight * (j - i);
            ans = Math.max(ans, curWater);
            
            if (height[i] < height[j]) {   // 更改索引，准备下一轮的计算
                i++;
            } else {
                j--;
            }
        }
        return ans;
    }
}
~~~





## 447. Number of Boomerangs

Given *n* points in the plane that are all pairwise distinct, a "boomerang" is a tuple of points `(i, j, k)` such that the distance between `i` and `j` equals the distance between `i` and `k` (**the order of the tuple matters**).

Find the number of boomerangs. You may assume that *n* will be at most **500** and coordinates of points are all in the range **[-10000, 10000]** (inclusive).

**Example:**

```
Input:
[[0,0],[1,0],[2,0]]

Output:
2

Explanation:
The two boomerangs are [[1,0],[0,0],[2,0]] and [[1,0],[2,0],[0,0]]
```



- i 是一个“枢纽”，对于每个点 i，遍历其余点到 i 的距离

- HashMap 存储的是“解的个数”，让每个点都做一次点a，然后遍历其他所有点，统计和a距离相等的点有多少个

~~~java
class Solution {
    public int numberOfBoomerangs(int[][] points) {
        int count = 0;
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < points.length; i++) {
            for (int j = 0; j < points.length; j++) {  //j从0开始，不是从i+1开始的，因为 i 点改变了
                if (i == j) {
                    continue;
                }
                int dis = distance(points[i], points[j]);
                map.put(dis, map.getOrDefault(dis, 0) + 1);
            }
            for (Map.Entry<Integer, Integer> entry: map.entrySet()) {
                int curCount = entry.getValue();
                if (curCount > 1) {
                    count += (curCount - 1) * curCount;
                }
            }
            map.clear();   // hashmap声明在两个for之外的话，就用map.clear，这样运行更快！
        }
        return count;
    }
    
    public int distance(int[] p1, int[] p2) {
        int dx = p1[0] - p2[0];
        int dy = p1[1] - p2[1];
        return dx*dx + dy*dy;    // 这里用Math.pow的话，不能把double转化为int，所以直接两个相乘
    }
}
~~~



- 447 和 149 Max Points On a Line，都是用了两层 loop，对于每一个点（first loop），都单独创建一个 HashMap，存储 <斜率/距离，个数>



## 19 Remove Nth Node From End of List

Given a linked list, remove the *n*-th node from the end of list and return its head.

**Example:**

```
Given linked list: 1->2->3->4->5, and n = 2.

After removing the second node from the end, the linked list becomes 1->2->3->5.
```

**Note:**

Given *n* will always be valid.

**Follow up:**

Could you do this in one pass?



两个pass的解法：先用第一个循环找到链表长度；第二个循环从头开始走 l - n 步到被删除节点的前一个节点

~~~java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if(head == null)
            return null;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        
        ListNode cur = head;
        int len = 0;
        while(cur != null) {
            len++;
            cur = cur.next;
        }
        
        int k = len - n;
        cur = dummy;
        while(k > 0) {
            cur = cur.next;
            k--;
        }
        
        cur.next = cur.next.next;
        return dummy.next;
    }
}
~~~

one pass解法：双指针，prev 和 cur 指针之间相差 n 步，先将 cur 向前移动 n 步，再同时移动 prev 和 cur 指针

~~~java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if(head == null)
            return null;
        
        ListNode pre = head;
        ListNode cur = head;
        while(n > 0) {
            n--;
            cur = cur.next;
        }
        
        if(cur == null)
            return head.next;
        
        while(cur.next != null) {
            cur = cur.next;
            pre = pre.next;
        }
        
        pre.next = pre.next.next;  // 不能写成 pre.next = cur，不能通过 [1,2]，1 的test case
        return head;
    }
}
~~~



## 61. Rotate List

Given a linked list, rotate the list to the right by *k* places, where *k* is non-negative.

**Example 1:**

```
Input: 1->2->3->4->5->NULL, k = 2
Output: 4->5->1->2->3->NULL
Explanation:
rotate 1 steps to the right: 5->1->2->3->4->NULL
rotate 2 steps to the right: 4->5->1->2->3->NULL
```

**Example 2:**

```
Input: 0->1->2->NULL, k = 4
Output: 2->0->1->NULL
Explanation:
rotate 1 steps to the right: 2->0->1->NULL
rotate 2 steps to the right: 1->2->0->NULL
rotate 3 steps to the right: 0->1->2->NULL
rotate 4 steps to the right: 2->0->1->NULL
```



首先计算有多少个周期

~~~java
class Solution {
    // 这个题注意“周期性”，因为有可能转了不到k次就回到了链表原来的状态
    // 有个corner case：[1], k=1， 这时在for中就要判断curr和dummy.next是不是同一个节点，否则会导致dummy指向null (prev.next= null)
    
    public ListNode rotateRight(ListNode head, int k) {
        if(head == null)
            return null;
        
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy;
        ListNode curr = head;
        int len = 0;
        while(curr.next != null) {
            prev = curr;
            curr = curr.next;
            len++;
        }
        len++;
        k = k % len;
        
        for(int i = 0; i < k; i++) {            
            if(dummy.next != curr) {
                curr.next = dummy.next;
                dummy.next = curr;
                prev.next = null;
            }
            while(curr.next != null) {
                prev = curr;
                curr = curr.next;
            }
        }
        return dummy.next;
    }
}
~~~



将 LinkedList 组成一个环

~~~java
class Solution {
    // 这个题注意“周期性”，因为有可能转了不到k次就回到了链表原来的状态
    // 有个corner case：[1], k=1， 这时在for中就要判断curr和dummy.next是不是同一个节点，否则会导致dummy指向null (prev.next= null)
    
    public ListNode rotateRight(ListNode head, int k) {
        if(head == null)
            return null;
        
        ListNode curr = head;
        int len = 1;
        while(curr.next != null) {
            curr = curr.next;
            len++;
        }
        curr.next = head;
        
        ListNode tail = head;
        for(int i = 0; i < len - k % len - 1; i++) {
            tail = tail.next;
        }
        ListNode newHead = tail.next;
        tail.next = null;   // break the ring
        return newHead;

    }
}
~~~

