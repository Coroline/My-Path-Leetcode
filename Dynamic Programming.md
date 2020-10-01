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



**以当前元素 A[i] 为 “结尾”，找到所有以 A[i] 为最后一个元素的 subarray**

起初的方法：动态规划，分两种情况：

- 当前数字 A[i] 比前一个数字 A[i - 1] 要大，直接 **subMin[i - 1] + A[i]**
- 数字 A[i] 比前一个数字 A[i - 1] 要小，从 i - 1 开始向前找第一个比 A[i] 小的数字，又分两种情况：
    - 遍历完后 j < 0：前面的数，都比 A[i] 大，**(i + 1) x A[i]**
    - 遍历完后 j >= 0：**subMin[j] + (i - j) x A[i]**

时间复杂度是 O(N^2)

~~~java
class Solution {
    // subMin[i] 表示以数字 A[i] 结尾的所有子数组最小值之和
    
    public int sumSubarrayMins(int[] A) {
        int n = A.length;
        if(n == 0)
            return 0;
        int M = (int)1e9 + 7;
        
        int[] subMin = new int[n];
        subMin[0] = A[0];
        int res = subMin[0];
        
        for(int i = 1; i < n; i++) {
            if(A[i] >= A[i - 1]) {
                subMin[i] = subMin[i - 1] + A[i];
            } else {
                int j = i - 1;   // 从当前 i 索引前一个位置开始
                while(j >= 0 && A[j] >= A[i]) {
                    j--;
                }
                if(j < 0) {   // 有 i + 1 个以A[i]结尾的 subarray，最小值都是 A[i]
                    subMin[i] = (i + 1) * A[i];
                } else {
                    subMin[i] = subMin[j] + (i - j) * A[i]; 
                }
            }
            res = (res + subMin[i]) % M;
        }
        return res;
    }
}
~~~



解法二：Stack（详情请见 stack 章节）