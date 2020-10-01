## 23. Merge k Sorted Lists

You are given an array of `k` linked-lists `lists`, each linked-list is sorted in ascending order.

*Merge all the linked-lists into one sorted linked-list and return it.*

 

**Example 1:**

```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6
```

**Example 2:**

```
Input: lists = []
Output: []
```

**Example 3:**

```
Input: lists = [[]]
Output: []
```

 

**Constraints:**

- `k == lists.length`
- `0 <= k <= 10^4`
- `0 <= lists[i].length <= 500`
- `-10^4 <= lists[i][j] <= 10^4`
- `lists[i]` is sorted in **ascending order**.
- The sum of `lists[i].length` won't exceed `10^4`



- 时间复杂度是O(Nlogk)，k是链表数量，空间复杂度是 O（k）

- 始终将三个链表的头元素进行比较，所以 priorityqueue 中最多有 k 个链表

~~~java
class Solution {
    // 时间复杂度是O(Nlogk)，k是链表数量
    
    public ListNode mergeKLists(ListNode[] lists) {
        if(lists.length == 0)
            return null;
        
        PriorityQueue<ListNode> pq = new PriorityQueue<>((a, b) -> a.val - b.val);
        ListNode res = new ListNode(0);
        
        for(int i = 0; i < lists.length; i++) {
            ListNode cur = lists[i];
            if(cur != null)
                pq.add(cur);
        }
        
        if(pq.isEmpty())
            return null;
            
        ListNode temp = res;
        while(!pq.isEmpty()) {
            temp.next = pq.poll();
            temp = temp.next;
            if(temp.next != null)
                pq.add(temp.next);
        }
        return res.next;
    }
}
~~~

