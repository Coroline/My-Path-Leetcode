## 1124. Longest Well-Performing Interval

We are given `hours`, a list of the number of hours worked per day for a given employee.

A day is considered to be a *tiring day* if and only if the number of hours worked is (strictly) greater than `8`.

A *well-performing interval* is an interval of days for which the number of tiring days is strictly larger than the number of non-tiring days.

Return the length of the longest well-performing interval.

 

**Example 1:**

```
Input: hours = [9,9,6,0,6,6,9]
Output: 3
Explanation: The longest well-performing interval is [9,9,6].
```

 

**Constraints:**

- `1 <= hours.length <= 10000`
- `0 <= hours[i] <= 16`



实际上转化后的问题就是[Leetcode 560：和为K的数组（最详细的解法！！！）](https://blog.csdn.net/qq_17550379/article/details/83510121)特例。在这个问题中`K=1`，为什么呢？不应该是`K>0`都可以吗？是的，确实是这样，但是`pre_sum-1`一定出现在`pre_sum-K`（其中`K>1`）的最前面（因为我们从`pre_sum>0`的时候开始记录第一个结果），所以使用`K=1`的话一定最优。

**本质:** 1. 若到当前位置连续和`sum`是正数:整个数组就满足条件; 2. 若`sum<=0`, 那么从头开始整个数组不满足条件; 此时希望找到**最前面**前缀和为`sum-1`对应的index `i`, 那么从`i+1`开始到当前位置就是longest subarray;此时子数组的元素和也就是`1`.

`sum-x`并不需要关心, 因为如果是最长子数组, 子数组的和一定是`1`, 所以只用关心`sum-1`.

~~~java
class Solution {
    public int longestWPI(int[] hours) {
        HashMap<Integer, Integer> record = new HashMap<>();
        int score = 0;
        int res = 0;
        
        for(int i = 0; i < hours.length; i++) {
            score += hours[i] > 8? 1: -1;
            if(score > 0)
                res = i + 1;   // + 1 是因为题目要的是长度
            else {
                if(!record.containsKey(score))   // 可以直接简化为 record.putIfAbsent(score, i);
                    record.put(score, i);
                if(record.containsKey(score - 1)) {
                    res = Math.max(res, i - record.get(score - 1));
                }
            }
        }
        return res;
    }
}
~~~

