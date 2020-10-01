## 127 Word Ladder

Given two words (*beginWord* and *endWord*), and a dictionary's word list, find the length of shortest transformation sequence from *beginWord* to *endWord*, such that:

1. Only one letter can be changed at a time.
2. Each transformed word must exist in the word list.

**Note:**

- Return 0 if there is no such transformation sequence.
- All words have the same length.
- All words contain only lowercase alphabetic characters.
- You may assume no duplicates in the word list.
- You may assume *beginWord* and *endWord* are non-empty and are not the same.

**Example 1:**

```
Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
```

**Example 2:**

```
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

Output: 0

Explanation: The endWord "cog" is not in wordList, therefore no possible transformation.
```



- 时间复杂度和空间复杂度都是 O(M^2 * N)，其中 M 是每个 word 的长度，N 是word列表的长度（因为考虑到了 substring消耗的时间，所以多 * 了一个 M）

这题也是，一层一层地扩散开找可以替换的单词，也要有 seen！避免形成回路，每遍历一层如果每找到结果，step++

seen 和 queue 一开始都要 add beginWord

~~~java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        if(!wordList.contains(endWord) || wordList.size() == 0) return 0;
        
        HashSet<String> wordDict = new HashSet<>();
        for(String word: wordList) {
            wordDict.add(word);
        }
        HashSet<String> seen = new HashSet<>();
        
        Queue<String> queue = new LinkedList<>();
        seen.add(beginWord);
        queue.add(beginWord);
        
        int steps = 0;
        while(!queue.isEmpty()) {
            int s = queue.size();
            for(int i = 0; i < s; i++) {
                String curWord = queue.poll();
                if(curWord.equals(endWord))
                    return steps + 1;
                
                for(int j = 0; j < curWord.length(); j++) {
                    char[] curLetter = curWord.toCharArray();   // 这个一定要写在这个 for 里面
                    for(char c = 'a'; c <= 'z'; c++) {
                        if(c == curLetter[j]) continue;
                        curLetter[j] = c;
                        String newWord = new String(curLetter);
                        
                        if(wordDict.contains(newWord) && !seen.contains(newWord)) {
                            queue.add(newWord);
                            seen.add(newWord);
                        }
                    }
                }
            }
            steps++;
        }
        
        return 0;
    }
}
~~~

用 # 来替换单词中的一个字母，例如 hot：#ot, h#t, ho#

~~~
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        if(!wordList.contains(endWord) || wordList.size() == 0)
            return 0;
        
        HashSet<String> visited = new HashSet<>();
        LinkedList<String> queue = new LinkedList<>();
        HashMap<String, List<String>> interState = new HashMap<>();
        
        for(String word: wordList) {
            for(int i = 0; i < word.length(); i++) {
                String transform = word.substring(0, i) + "*" + word.substring(i + 1);
                interState.computeIfAbsent(transform, k -> new ArrayList<>()).add(word);
            }
        }
        
        visited.add(beginWord);
        queue.add(beginWord);
        int steps = 1;
        
        while(!queue.isEmpty()) {
            int s = queue.size();
            for(int i = 0; i < s; i++) {
                String curWord = queue.poll();
                if(curWord.equals(endWord))
                    return steps;
                for(int j = 0; j < curWord.length(); j++) {
                    String curInter = curWord.substring(0, j) + "*" + curWord.substring(j + 1);
                    for(String neighbor: interState.getOrDefault(curInter, new ArrayList<>())) {
                        if(!visited.contains(neighbor)) {
                            visited.add(neighbor);
                            queue.add(neighbor);
                        }
                    }
                }
            }
            steps++;
        }
        return 0;
    }
}
~~~



## 126 Word Ladder II

