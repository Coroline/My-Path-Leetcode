- cur.next 一定要检查是否为空 null ？？？
- 链表用递归，可以先确定基本条件，比如基本条件就是开头的几个节点如何变动指针，再假设后面的链表部分已经达到了想要的结果，直接将结果拿出来运用就可以



## 82. Remove Duplicates from Sorted List II

Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only *distinct* numbers from the original list.

Return the linked list sorted as well.

**Example 1:**

```
Input: 1->2->3->3->4->4->5
Output: 1->2->5
```

**Example 2:**

```
Input: 1->1->1->2->3
Output: 2->3
```



- 首先是 递归 的写法

~~~java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null) return null;
    
    if (head.next != null && head.val == head.next.val) {
        while (head.next != null && head.val == head.next.val) {
            head = head.next;
        }
        return deleteDuplicates(head.next);
    } else {
        head.next = deleteDuplicates(head.next);
    }
    return head;
}
~~~





- 注意 prev 和 cur 之间有没有重复的node，分两种情况处理下一步：有重复的 和 没有重复的指针移动方式不同
- prev 指针之前不包含 duplicates

~~~java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null)
            return head;
        
        // invariant: everything before prev does not contains any duplicates !!!
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy; 
        ListNode cur = prev.next;
        
        while (cur != null) {
            while (cur.next != null && cur.val == cur.next.val)
                cur = cur.next;
            
            if (prev.next == cur)    // 表示prev和cur之间没有重复的node
                prev = prev.next;
            else                     // prev和cur之间还有重复的node
                prev.next = cur.next;
            
            cur = cur.next;
        }
        return dummy.next;
    }
}

~~~





## 203. Remove Linked List Elements

Remove all elements from a linked list of integers that have value ***val\***.

**Example:**

```
Input:  1->2->6->3->4->5->6, val = 6
Output: 1->2->3->4->5
```



~~~java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    // 这个题要设置dummy头结点，因为如果val和head的val相等，删除头结点的操作， 和中间的节点不一样
    
    public ListNode removeElements(ListNode head, int val) {
        if(head == null)
            return null;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy;
        ListNode cur = head;
        
        while(cur != null) {
            if(cur.val == val) {
                prev.next = cur.next;
                cur = prev.next;
            } else {
                prev = cur;
                cur = cur.next;
            }
        }
        return dummy.next;
    }
}
~~~





## 21. Merge Two Sorted Lists

Merge two sorted linked lists and return it as a new **sorted** list. The new list should be made by splicing together the nodes of the first two lists.

**Example:**

```
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```



递归的写法：

~~~java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1 == null)
            return l2;
        else if(l2 == null)
            return l1;
        else if(l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
~~~



## 24. Swap Nodes in Pairs

Given a linked list, swap every two adjacent nodes and return its head.

You may **not** modify the values in the list's nodes, only nodes itself may be changed.

 

**Example:**

```
Given 1->2->3->4, you should return the list as 2->1->4->3.
```



~~~java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null)
            return head;
        
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode p = dummy;
        
        while(p.next != null && p.next.next != null) {
            ListNode node1 = p.next;
            ListNode node2 = p.next.next;
            ListNode after = p.next.next.next;
            node2.next = node1;
            node1.next = after;
            p.next = node2;
            p = node1;     // 注意 p = node1，因为这时 node1 就是下一组两个node1的前一个
        }
        return dummy.next;
    }
}
~~~



递归的写法：从头开始的两个node，假设第三个包括其后面的链表已经 swap 完成了，直接修改当前的 head 和 head.next 就可以了

~~~java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null)
            return head;
        
        ListNode node1 = head;
        ListNode node2 = head.next;
        ListNode after = head.next.next;
        
        node2.next = node1;
        node1.next = swapPairs(after);
        
        return node2;
    }
}
~~~





## 25. Reverse Nodes in k-Group

Given a linked list, reverse the nodes of a linked list *k* at a time and return its modified list.

*k* is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of *k* then left-out nodes in the end should remain as it is.



**Example:**

Given this linked list: `1->2->3->4->5`

For *k* = 2, you should return: `2->1->4->3->5`

For *k* = 3, you should return: `3->2->1->4->5`

**Note:**

- Only constant extra memory is allowed.
- You may not alter the values in the list's nodes, only nodes itself may be changed.



递归的写法

~~~java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if(head == null)
            return null;
        
        int count = 0;
        ListNode cur = head;
        while(cur != null) {   // 这个while是为了确保此时链表的长度比 k 要小，就不用翻转直接返回
            cur = cur.next;
            count++;
        }
        if(count < k)
            return head;
        
        cur = head;
        ListNode prev = null;  
        for(int i = 0; i < k; i++) {    // 假设后面已经翻转好了，就只对前面的 k 个 reverse就好啦
            ListNode after = cur.next;
            cur.next = prev;
            prev = cur;
            cur = after;
        }
        head.next = reverseKGroup(cur, k);
        return prev;
    }
}
~~~





## 148. Sort List

Given the `head` of a linked list, return *the list after sorting it in **ascending order***.

**Follow up:** Can you sort the linked list in `O(n logn)` time and `O(1)` memory (i.e. constant space)?

 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/09/14/sort_list_1.jpg" alt="img" style="zoom:33%;" />

```
Input: head = [4,2,1,3]
Output: [1,2,3,4]
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2020/09/14/sort_list_2.jpg" alt="img" style="zoom:33%;" />

```
Input: head = [-1,5,3,4,0]
Output: [-1,0,3,4,5]
```

**Example 3:**

```
Input: head = []
Output: []
```

 

**Constraints:**

- The number of nodes in the list is in the range `[0, 5 * 104]`.
- `-105 <= Node.val <= 105`





- 使用归并排序，将原本的链表通过 “快慢指针” 分为两个部分，假设这两个部分都已经 sort 完成，直接对 sort 完成之后的两个 list 进行 merge
- 时间复杂度是 O(NlogN)，空间复杂度是 O(logN)，用于存储递归栈

~~~java
public class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode slow = head, fast = head, pre = head;
        while (fast != null && fast.next != null) {
            pre = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        pre.next = null;
        return merge(sortList(head), sortList(slow));
    }
    
    
    public ListNode merge(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1);
        ListNode cur = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        if (l1 != null) cur.next = l1;
        if (l2 != null) cur.next = l2;
        return dummy.next;
    }
}
~~~

