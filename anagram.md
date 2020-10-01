## 242 Valid Anagram
Given two strings s and t , write a function to determine if t is an anagram of s.

Example 1:

>Input: s = "anagram", t = "anagaram"
Output: true
Example 2:

>Input: s = "rat", t = "car"
Output: false

Note:
You may assume the string contains only lowercase alphabets.

>Follow up:
What if the inputs contain unicode characters? How would you adapt your solution to such case?

- **如果只有英文字母**，不需要hashtable，直接用26个空间的int数组
- 首先对**长度**进行判断
- 遍历 t 时，先对 counter 数组 --， -- 之后只要 counter[c] < 0，那么就有可能是：s有t没有的c，或者t有s没有的c。

**Follow Up：**
直接用hashmap存储

```java
class Solution {
    // 时间复杂度是O(N)，空间复杂度是O(1)，因为最多也就26个英文字母
    // 也可以直接用26大小的int数组
    
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length())
            return false;
        
        int[] counter = new int[26];
        char[] letters = s.toCharArray();
        for(int i = 0; i < letters.length; i++)
            counter[letters[i] - 'a']++;
        
        char[] lettersT = t.toCharArray();
        for(char c: lettersT) {
            counter[c - 'a']--;
            if(counter[c - 'a'] < 0)  // 现在s和t的长度肯定是相等的，如果s中有t中没出现的char
                return false;    // 那t肯定会用a-z来填充，是的s和t的长度相等，如aaabx, aaabg
                                // 填充的char要么在s中出现过，要么s中没有
        }
        return true;
    }
}
```



## 49. Group Anagrams
Given an array of strings strs, group the anagrams together. You can return the answer in any order.

An Anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

 

Example 1:

>Input: strs = ["eat","tea","tan","ate","nat","bat"]
Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
Example 2:

>Input: strs = [""]
Output: [[""]]
Example 3:

>Input: strs = ["a"]
Output: [["a"]]

```java
class Solution {
    // 时间复杂度和空间复杂度都是O(NK)，N是string个数，K是最长string的长度
    
    public List<List<String>> groupAnagrams(String[] strs) {
        List<List<String>> res = new ArrayList<>();
        HashMap<String, List<String>> counter = new HashMap<>();
        int[] letter = new int[26];
        
        for(String s: strs) {
            Arrays.fill(letter, 0);
            for(int i = 0; i < s.length(); i++)
                letter[s.charAt(i) - 'a']++;
            
            StringBuilder sb = new StringBuilder();  // 用 string 编码26个字母的int数组内容
            for(int i = 0; i < 26; i++) {
                if(letter[i] == 0) continue;
                sb.append(i + 'a');   // 表示当前是什么字母
                sb.append(letter[i]); // 表示当前字母的个数
            }
            
            counter.computeIfAbsent(sb.toString(), k -> new ArrayList<>()).add(s);
        }
        
        for(Map.Entry<String, List<String>> entry: counter.entrySet()) {
            res.add(entry.getValue());
        }
        return res;
    }
}
```
