## 282. Expression Add Operators

- 采用 backtrack，难点是如果==处理 * 乘号，以及处理leading zero==
-  backtrack 函数的 <u>**diff**</u> 表示<u>这次recurison和上次相比，curSum值的变化</u>
        // 比如 2+3 x 2，即将要运算到乘以2的时候，上次循环的 curNum = 5, diff = 3, 而如果我们要算这个乘2的时候，新的变化值diff应为 3x2=6，而我们要把之前+3操作的结果去掉，再加上新的diff，即 (5-3)+6=8，即为新表达式 2+3*2 的值
- 字符串转为int型很容易溢出，所以我们用长整型
- 时间复杂度是 O(N * 4 ^ N)

~~~java
private List<String> res = new ArrayList<>();
    
    public List<String> addOperators(String num, int target) {
        backtrack(num, target, 0, 0, "", 0);
        return res;
    }
    
    
    private void backtrack(String num, int target, long diff, long curSum, String out, int index) {
        if(curSum == target && index == num.length()) {
            res.add(out);
            return;
        }
        if(index >= num.length())
            return;
        
        for(int i = index; i < num.length(); i++) {
            String cur = num.substring(index, i + 1);
            if(cur.length() > 1 && cur.charAt(0) == '0')   // leading zero
                return;
            long x = Long.parseLong(cur);
            if(index > 0) {
                // *
                backtrack(num, target, diff * x, curSum - diff + diff * x, out + "*" + cur, i + 1);
                // -
                backtrack(num, target, -x, curSum - x, out + "-" + cur, i + 1);
                // +
                backtrack(num, target, +x, curSum + x, out + "+" + cur, i + 1);
            } else {
                // 如果时第一个数字，不需要在前面加任何"符号"(+, -, *)
                backtrack(num, target, x, x, cur, i + 1);
            }
        }
    }
~~~

