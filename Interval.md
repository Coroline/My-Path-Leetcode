- 新加入的区间和当前区间产生重复的情况有两种，一种是新加入区间的==前半段重复==，并且，另一种是新加入区间的==后半段重复==。
- 比如当前区间如果是[3, 8)，那么第一种情况下新加入区间就是[6, 9)，那么触发条件就是当前区间的起始时间小于等于新加入区间的起始时间，并且结束时间大于新加入区间的结束时间。      第二种情况下新加入区间就是[2,5)，那么触发条件就是当前区间的起始时间大于等于新加入区间的起始时间，并且起始时间小于新加入区间的结束时间
- 如下图，只要 **红线 < 蓝线，就overlap**

<img src="http://zxi.mytechroad.com/blog/wp-content/uploads/2017/11/729-ep112.png" alt="img" style="zoom:80%;" />



## 729. My Calendar I

Implement a `MyCalendar` class to store your events. A new event can be added if adding the event will not cause a double booking.

Your class will have the method, `book(int start, int end)`. Formally, this represents a booking on the half open interval `[start, end)`, the range of real numbers `x` such that `start <= x < end`.

A *double booking* happens when two events have some non-empty intersection (ie., there is some time that is common to both events.)

For each call to the method `MyCalendar.book`, return `true` if the event can be added to the calendar successfully without causing a double booking. Otherwise, return `false` and do not add the event to the calendar.

Your class will be called like this: `MyCalendar cal = new MyCalendar();` `MyCalendar.book(start, end)`

**Example 1:**

```
MyCalendar();
MyCalendar.book(10, 20); // returns true
MyCalendar.book(15, 25); // returns false
MyCalendar.book(20, 30); // returns true
Explanation: 
The first event can be booked.  The second can't because time 15 is already booked by another event.
The third event can be booked, as the first event takes every time less than 20, but not including 20.
```

 

**Note:**

- The number of calls to `MyCalendar.book` per test case will be at most `1000`.
- In calls to `MyCalendar.book(start, end)`, `start` and `end` are integers in the range `[0, 10^9]`.

 ### 方法一：brute force

建立一个List，存放 true 的interval，来了一个新的区间时，将这个新区间和 List 中所有的 interval 进行比较，看是否有 overlap

~~~java
class MyCalendar {
    private List<int[]> intervals;

    public MyCalendar() {
        intervals = new ArrayList<>();
    }
    
    public boolean book(int start, int end) {
        for(int[] inter: intervals) {
            int s = inter[0];
            int e = inter[1];
            if(s <= start && e > start) return false;
            if(s >= start && s < end) return false;
        }
        intervals.add(new int[]{start, end});
        return true;
    }
}
~~~

对方法一进行优化，两个 if 合并为一个 if

~~~java

    public MyCalendar() {
        intervals = new ArrayList<>();
    }
    
    public boolean book(int start, int end) {
        for(int[] inter: intervals) {
            if(Math.max(inter[0], start) < Math.min(inter[1], end))
                return false;
        }
        intervals.add(new int[]{start, end});
        return true;
    }
}

/**
 * Your MyCalendar object will be instantiated and called as such:
 * MyCalendar obj = new MyCalendar();
 * boolean param_1 = obj.book(start,end);
 */
~~~

### 方法二：binary search （用的是 treeMap）

时间复杂度是 O(NlogN)，空间复杂度是 O(N)

If we maintained our events in **sorted order**, we could <u>check whether an event could be booked in O*(log*N*) time</u> (where N is the number of events already booked) by binary searching for where the event should be placed. We would also have to insert the event in our sorted

When inserting the interval `[start, end)`, we check if there is a conflict on each side with neighboring intervals: we would like `calendar.get(prev)) <= start <= end <= next` for the booking to be valid (==or for `prev` or `next` to be **null** respectively==.)

~~~java
class MyCalendar {
    private TreeMap<Integer, Integer> calendar;

    public MyCalendar() {
        calendar = new TreeMap<>();
    }
    
    public boolean book(int start, int end) {
        Integer prev = calendar.floorKey(start);    // prev <= start
        Integer next = calendar.ceilingKey(start);  // next >= start
        // 以下是 没有 overlap 的情况
        if((prev == null || calendar.get(prev) <= start) && (next == null || next >= end)) {
            calendar.put(start, end);
            return true;
        }
        return false;
    }
}
~~~



