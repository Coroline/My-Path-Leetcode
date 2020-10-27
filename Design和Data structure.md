## Min Stack

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

- push(x) -- Push element x onto stack.
- pop() -- Removes the element on top of the stack.
- top() -- Get the top element.
- getMin() -- Retrieve the minimum element in the stack.

 

**Example 1:**

```
Input
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

Output
[null,null,null,null,-3,null,0,-2]

Explanation
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top();    // return 0
minStack.getMin(); // return -2
```

 

**Constraints:**

- Methods `pop`, `top` and `getMin` operations will always be called on **non-empty** stacks.



方法一：两个 stack

We  use two stack. The first stack is used to store x values, it's just like a normal stack. For example, if we push 0, 1, 2... to the stack, we will push all those x vals into stack. The second stack is utilized to keep track of the most recent min val as we push every x. So the top element on the min_vals stack is the most recent min val. Therefore, for top/getMin, we can just peek() from the two stack. But for push and pop, for stack, we can use it's normal push and pop. But we also need to judge whether the most recent min val needs to be changed, in other words, we need to know if we will pop the top element from the min_vals stack, so that the old min val can become the top element.

~~~java
class MinStack {
    Stack<Integer> stack;
    Stack<Integer> min_vals;

    /** initialize your data structure here. */
    public MinStack() {
        stack = new Stack<>();
        min_vals = new Stack<>();
    }
    
    // 注意这里的 if 一定要是 <=，如果只有 < 的话，比如依次 push 了0，1，0，getMin 时会出现 stackEmpty
    // 一定要判断 stack 是否为空
    public void push(int x) {
        if(min_vals.isEmpty() || x <= min_vals.peek()) { 
            min_vals.push(x);
        }
        stack.push(x);
    }
    
    // 这里必须写成 equals，如果写成 == 会出错
    // 因为 stack peek 出来的是 Integer Object，如果想用 ==，必须先把 stack 中的 pop 出来赋给一个 int
    public void pop() {
        if(stack.peek().equals(min_vals.peek())) {
            min_vals.pop();
        }
        stack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return min_vals.peek();
    }
}
~~~



方法二：一个 stack 和一个 min_val 变量

Instead of using a stack to store the most recent min vals, we can use an variable to keep track of the min_val. As we push/pop x, we need to compare x to min_val, or compare top element on stack with min_val, then update min_vals value.

~~~java
class MinStack {
    Stack<Integer> stack;
    int min_val;

    /** initialize your data structure here. */
    public MinStack() {
        stack = new Stack<>();
        min_val = Integer.MAX_VALUE;
    }
    
    public void push(int x) {
        // only push the old minimum value when the current 
        // minimum value changes after pushing the new value x
        if(x <= min_val) {
            stack.push(min_val);
            min_val = x;
        }
        stack.push(x);
    }
    
    public void pop() {
        // if pop operation could result in the changing of the current minimum value, 
        // pop twice and change the current minimum value to the last minimum value.
        if(stack.pop() == min_val) {
            min_val = stack.pop();
        }
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return min_val;
    }
}
~~~



## Max Stack

Design a max stack that supports push, pop, top, peekMax and popMax.



1. push(x) -- Push element x onto stack.
2. pop() -- Remove the element on top of the stack and return it.
3. top() -- Get the element on the top.
4. peekMax() -- Retrieve the maximum element in the stack.
5. popMax() -- Retrieve the maximum element in the stack, and remove it. If you find more than one maximum elements, only remove the top-most one.



**Example 1:**

```
MaxStack stack = new MaxStack();
stack.push(5); 
stack.push(1);
stack.push(5);
stack.top(); -> 5
stack.popMax(); -> 5
stack.top(); -> 1
stack.peekMax(); -> 5
stack.pop(); -> 1
stack.top(); -> 5
```



**Note:**

1. -1e7 <= x <= 1e7
2. Number of operations won't exceed 10000.
3. The last four operations won't be called when stack is empty.

~~~java
class MaxStack {
    Stack<Integer> stack;
    Stack<Integer> max_vals;

    /** initialize your data structure here. */
    public MaxStack() {
        stack = new Stack<>();
        max_vals = new Stack<>();
    }
    
    public void push(int x) {
        if(max_vals.isEmpty() || x >= max_vals.peek()) {
            max_vals.push(x);
        }
        stack.push(x);
    }
    
    public int pop() {
        if(stack.peek().equals(max_vals.peek())) {
            max_vals.pop();
        }
        return stack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int peekMax() {
        return max_vals.peek();
    }
    
    // 注意此时容易犯的一个错误是，没有同时更新 max_vals stack，所以我们直接调用 push() 函数即可
    public int popMax() {
        Stack<Integer> temp = new Stack<>();
        while(!max_vals.peek().equals(stack.peek())) {
            temp.push(stack.pop());
        }
        int maxVal = max_vals.pop();
        stack.pop();
        
        while(!temp.isEmpty())
            push(temp.pop());    // 这里必须要用 “新定义的 push” 方法，同时更新 stack 和 max_vals
        return maxVal;
    }
}
~~~

