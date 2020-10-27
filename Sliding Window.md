模板：https://juejin.im/post/6844903837447225358



## 209. Minimum Size Subarray Sum

Given an array of **n** positive integers and a positive integer **s**, find the minimal length of a **contiguous** subarray of which the sum ≥ **s**. If there isn't one, return 0 instead.

**Example:** 

```
Input: s = 7, nums = [2,3,1,2,4,3]
Output: 2
Explanation: the subarray [4,3] has the minimal length under the problem constraint.
```

**Follow up:**

If you have figured out the *O*(*n*) solution, try coding another solution of which the time complexity is *O*(*n* log *n*). 



### 方法一：滑动窗口

- 维护一个滑动窗口区间，保持区间内的 sum >= s 且尽量 length 要小

~~~java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        int i = 0;
        int j = 0;
        int sum = 0;
        int minLen = nums.length + 1;
        
        while(j < nums.length) {
            while(sum < s && j < nums.length) {  // 必须用while，不能用if
                sum += nums[j];
                j++;
            } 
            while(sum >= s && i < j) {      // 必须用while，不能用if
                minLen = Math.min(j - i, minLen);
                sum -= nums[i];
                i++;
            }
        }
        return (minLen == nums.length + 1)? 0: minLen;
    }
}
~~~



### 方法二：prefix sum

- 建立一个比原数组长一位的 sums 数组，其中 **sums[i] 表示 nums 数组中 [0, i - 1] 的和**，然后对于 sums 中每一个值 sums[i]，用二分查找法**找到子数组的右边界位置 (sum[i] + s)**，使该**子数组之和大于 sums[i] + s**，然后更新最短长度的距离即可
- 时间复杂度是 O(NlogN)

~~~ java
class Solution {
    // 之所以用sums[i] + s作为key来查找是因为这样就可以抛弃掉前i-1位数的和，查找一个从i开始的和大于s的区间
    
    public int minSubArrayLen(int s, int[] nums) {
        int n = nums.length;
        if(n == 0) return 0;
        int minLen = n + 1;
        int[] sum = new int[n + 1];
        for(int i = 1; i <= n; i++) sum[i] = sum[i - 1] + nums[i - 1];
        
        for(int i = 0; i <= n; i++) {
            int sumEnd = binarySearch(i + 1, n, sum[i] + s, sum);
            if(sumEnd == n + 1)  // 没有找到合适的
                continue;
            minLen = Math.min(minLen, sumEnd - i);
        }
        return (minLen == n + 1)? 0: minLen;
    }
    
    private int binarySearch(int lo, int hi, int key, int[] sums) {
        while (lo <= hi) {
           int mid = (lo + hi) / 2;
           if (sums[mid] >= key){
               hi = mid - 1;
           } else {
               lo = mid + 1;
           }
        }
        return lo;
    }
}
~~~



用binary search模板的写法：

~~~java
class Solution {
    // 之所以用sums[i] + s作为key来查找是因为这样就可以抛弃掉前i-1位数的和，查找一个从i开始的和大于s的区间
    
    public int minSubArrayLen(int s, int[] nums) {
        int n = nums.length;
        if(n == 0) return 0;
        int minLen = n + 1;
        int[] sum = new int[n + 1];
        for(int i = 1; i <= n; i++) sum[i] = sum[i - 1] + nums[i - 1];
        
        for(int i = 0; i <= n; i++) {
            if(sum[i] + s > sum[n]) break; 
            int sumEnd = binarySearch(i + 1, n, sum[i] + s, sum);
            minLen = Math.min(minLen, sumEnd - i);
        }
        return (minLen == n + 1)? 0: minLen;
    }
    
    private int binarySearch(int lo, int hi, int key, int[] sums) {
        while (lo < hi) {
           int mid = lo + (hi - lo) / 2;
           if (sums[mid] >= key){
               hi = mid;
           } else {
               lo = mid + 1;
           }
        }
        return lo;
    }
}
~~~



## 3. Longest Substring Without Repeating Characters

Given a string `s`, find the length of the **longest substring** without repeating characters.

 

**Example 1:**

```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

**Example 2:**

```
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

**Example 4:**

```
Input: s = ""
Output: 0
```

 

**Constraints:**

- `0 <= s.length <= 5 * 104`
- `s` consists of English letters, digits, symbols and spaces.



- ==建立每个字符和其最后出现位置之间的映射==，然后需要定义两个变量 res 和 left，其中 res 用来记录最长无重复子串的长度，left 指向该无重复子串左边的起始位置的前一个，由于是前一个，所以初始化就是 -1

~~~java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        int[] index = new int[128]; // current index of character
        Arrays.fill(index, -1);
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            char c = s.charAt(j);
            if(index[c] != -1)         // 如果出现过，left就是出现的位置+1
                i = Math.max(i, index[c] + 1);
            ans = Math.max(ans, j - i + 1);
            index[c] = j;  
        }
        return ans;
    }
}
~~~



## 438. Find All Anagrams in a String

Given a string **s** and a **non-empty** string **p**, find all the start indices of **p**'s anagrams in **s**.

Strings consists of lowercase English letters only and the length of both strings **s** and **p** will not be larger than 20,100.

The order of output does not matter.

**Example 1:**

```
Input:
s: "cbaebabacd" p: "abc"

Output:
[0, 6]

Explanation:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".
```



**Example 2:**

```
Input:
s: "abab" p: "ab"

Output:
[0, 1, 2]

Explanation:
The substring with start index = 0 is "ab", which is an anagram of "ab".
The substring with start index = 1 is "ba", which is an anagram of "ab".
The substring with start index = 2 is "ab", which is an anagram of "ab".
```



- Arrays.equals(sArr, pArr) 比较两个 array 是否相等
- 用两个数组，分别记录p的字符个数，和s中前p字符串长度的字符个数，然后比较，如果两者相同，则将0加入结果 res 中，然后开始遍历s中剩余的字符，每次右边加入一个新的字符，然后去掉左边的一个旧的字符，每次再比较两个数组是否相同即可

~~~java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        int n = s.length();
        int m = p.length();
        List<Integer> ans = new ArrayList<>();
        int[] pArr = new int[26];
        int[] sArr = new int[26];     //数组已经被初始化为0了
        
        for(char ch: p.toCharArray()){  // 注意这里要把string转化为array
            pArr[(int)(ch - 'a')]++;
        }
        
        for(int i = 0; i < n; i++) {
            sArr[(int)(s.charAt(i) - 'a')]++;    // 不管怎样先把这个char加入sMap
            if(i >= m) {                        // 然后在判断对sMap进行怎样的处理
                char ch = s.charAt(i - m);
                sArr[(int)(ch - 'a')]--;        //相对hashmap，这一步更加简洁
            }       
            //在以上部分，hashMap一直被很好的维护，所以可以直接判断两个hashMap的内容是否相等
            if(Arrays.equals(sArr, pArr)){       
                ans.add(i - m + 1);
            }
        }
        return ans;
    }
}
~~~





## 219. Contains Duplicate II

Given an array of integers and an integer *k*, find out whether there are two distinct indices *i* and *j* in the array such that **nums[i] = nums[j]** and the **absolute** difference between *i* and *j* is at most *k*.

**Example 1:**

```
Input: nums = [1,2,3,1], k = 3
Output: true
```

**Example 2:**

```
Input: nums = [1,0,1,1], k = 1
Output: true
```

**Example 3:**

```
Input: nums = [1,2,3,1,2,3], k = 2
Output: false
```



- 滑动窗口的大小就是 k 的大小，超过这个范围就从 HashSet 中 remove元素

~~~java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Set<Integer> recorder = new HashSet<>();
        
        for (int i = 0; i < nums.length; i++) {
            if (recorder.contains(nums[i]))
                return true;
            recorder.add(nums[i]);
            
            if (recorder.size() == k + 1) {  // 这时window is going to向右扩展一位
                // 即将判断下一个，就要把window左边的e删掉
                recorder.remove(nums[i - k]);
            }
        }
        return false;
    }
}
~~~





## 220. Contains Duplicate III

Given an array of integers, find out whether there are two distinct indices *i* and *j* in the array such that the **absolute** difference between **nums[i]** and **nums[j]** is at most *t* and the **absolute** difference between *i* and *j* is at most *k*.

 

**Example 1:**

```
Input: nums = [1,2,3,1], k = 3, t = 0
Output: true
```

**Example 2:**

```
Input: nums = [1,0,1,1], k = 1, t = 2
Output: true
```

**Example 3:**

```
Input: nums = [1,5,9,1,5,9], k = 2, t = 3
Output: false
```



- 时间复杂度 O( N * log(min(N, k)) )，空间复杂度 O(log(min(N, k)))，取决于 BST 的size，BST 的 upper bound 是由 k 或者 N 决定的

~~~java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        TreeSet<Long> recorder = new TreeSet<>();
        
        for (int i = 0; i < nums.length; i++) {
            if(recorder.ceiling((long)nums[i] - (long)t) != null &&
                    recorder.ceiling((long)nums[i] - (long)t) <= (long)nums[i] + (long)t)
                return true;
            recorder.add((long)nums[i]);
            
            if (recorder.size() == k + 1)
                recorder.remove((long)nums[i-k]);
        }
        return false;
    }
}
~~~

