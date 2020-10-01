## 7. Reverse Integer

Given a 32-bit signed integer, reverse digits of an integer.

**Example 1:**

```
Input: 123
Output: 321
```

**Example 2:**

```
Input: -123
Output: -321
```

**Example 3:**

```
Input: 120
Output: 21
```

**Note:**
Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−231, 231 − 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

出现越界的时候，返回 0

~~~java
class Solution {
    public int reverse(int x) {
        int sign = (x < 0)? -1: 1;
        x = Math.abs(x);
        int res = 0;
        while(x > 0) {
            if(res > Integer.MAX_VALUE / 10) return 0;   // 重要，和Integer.MAX_VALUE / 10 比较
            res = res * 10 + x % 10;
            x /= 10;
        }
        return sign * res;
    }
}
~~~

