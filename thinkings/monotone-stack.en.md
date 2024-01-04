# Monotonic stack

As the name implies, a monotonic stack is a type of stack. So, to understand monotonic stacks, one must first thoroughly understand stacks.

## What is a stack?

![](https://p.ipic.vip/nkmnv8.jpg)

A stack is a limited data structure, characterized by allowing new content to be inserted or removed from only one direction, which we call the top of the stack. Accessing content from any other position is not allowed.

The most notable characteristic of a stack is LIFO (Last In, First Out).

For example:

A stack is like a drawer for books. Pushing onto the stack is akin to putting a book into the drawer, where the newest book is always on top. Popping from the stack is like taking a book out of the drawer, always starting from the topmost book. Hence, the book taken out is always the last one that was put in.

### Common Stack Operations

1. **Push**: Placing an element onto the top of the stack.
2. **Pop**: Popping the top element from the stack.
3. **Top**: Getting the value of the top element.
4. **isEmpty**: Determining whether the stack has any elements.

### Time Complexity of Common Operations

Since stack operations are only performed at the end, if we simulate a stack with an array, we can easily achieve a time complexity of O(1). Of course, it can also be implemented with a linked list, i.e., a linked stack.

1. Push - O(1)
2. Pop - O(1)

![](https://p.ipic.vip/35ede1.jpg)

### Application

- Function Call Stacks
- Browser Forward and Backward Navigation
- Parenthesis Matching
- Monotonic Stacks for finding the next greater (or smaller) element
  
### Topic recommendation

- [394. String decoding](https://leetcode.com/problems/decode-string/description/)
- [946. Verify stack sequence](https://leetcode.com/problems/validate-stack-sequences/description/)
- [1381. Design a stack that supports incremental operations](https://leetcode.com/problems/design-a-stack-with-increment-operation/submissions/)

## What exactly is a monotonous stack?

A monotonous stack is a special kind of stack. A stack is already a limited data structure, and a monotonous stack imposes one more limitation on top of it (additional limitation).

Monotonic stacks require that the elements in the stack are either monotonically increasing or decreasing.

> Whether it's strictly increasing or decreasing depends on the actual situation.

Here, I'll use [a,b,c] to represent a stack, where the left side is the bottom of the stack and the right side is the top of the stack. Whether it's monotonically increasing or decreasing depends on the order of elements when they are popped out of the stack. If the elements are monotonically increasing when popped, it's called a monotonically increasing stack. If they are decreasing, it's called a monotonically decreasing stack.

For example：

[1,2,3,4] is a monotonically decreasing stack (because the order of popping is 4,3,2,1. Same below, not elaborated further)
[3,2,1] is a monotonically increasing stack
[1,3,2] is not a valid monotonous stack
So, what is the use of this limitation? What kind of problems can this characteristic (feature) solve?

### Applicable Scenarios:

Monotonous stacks are suitable for problems that involve finding **the next element greater or smaller than xxx**. So whenever you encounter these types of problems, you should think of monotonous stacks.

> Why are monotonous stacks suitable for finding the next element greater or smaller than xxx? The reason is quite simple, and I will explain it through an example here.

Let's take the example of a monotonically decreasing stack:

Suppose we need to push the array [1,3,4,5,2,9,6] into a monotonous stack one by one.

1. First, push 1, the stack is now: [1]
2. Continue to push 3, the stack is now: [1,3]
3. Continue to push 4, the stack is now: [1,3,4]
4. Continue to push 5, the stack is now: [1,3,4,5]
5. If we continue to push 2, the stack is now: [1,3,4,5,2] which doesn't satisfy the characteristics of a monotonically decreasing stack, so it needs to be adjusted. How do we adjust? Since the stack only has a pop operation, we have no choice but to keep popping until it satisfies monotonically decreasing condition.
6. Actually, we didn't push 2 yet, but first popped until pushing 2 still maintains a monotonically decreasing order. Then we push 2, so now the stack is: [1,2]
7. Continue to push 9, the stack is now: [1,2,9]
8. If we continue to push 6, it doesn't satisfy the characteristics of a monotonically decreasing stack, so we repeat the popping process until it does. Now the stack is: [1,2,6]

Note that the stack is still non-empty. If some problems require all array information, it's very possible to fail certain test cases due to not considering the boundary. Here's a technique - the **sentinel method**, often used in monotonous stack algorithms.

For the above example, I can add an item smaller than the smallest item in the original array [1,3,4,5,2,9,6] on the right side, such as -1. Now the array is [1,3,4,5,2,9,6,-1]. This technique can simplify the code logic, so try to master it.

If you understand the above example, it won't be hard to understand why monotonous stacks are suitable for finding the next greater or smaller element. For example, in the above case, we can easily find the first position smaller than itself after each element. Like for 3, its index is 1, the first index smaller than 3 is 4, 2's index is 4, the first index smaller than 2 is 0, but since it's after 2's index 4, it doesn't meet the condition, meaning there is no position smaller than 2 after itself.

In the example above, we started popping at step 6. The first popped out was 5, so the first index smaller than 5 after itself is 4. Similarly, for 3, 4, and 5 all popped out, it's also 4.

If we use ans to represent the position of the first smaller element after itself, ans[i] represents the position of the first smaller element after arr[i], ans[i] being -1 means no such position exists, like the 2 mentioned earlier. Then, at this time, ans would be [-1,4,4,4,-1,-1,-1].

At step 8, we start popping again. This time what's popped out is 9, so the first index smaller than 9 after itself is 6.

This algorithm can be summarized in one sentence: if pushing onto the stack still maintains monotonicity, then directly push. Otherwise, pop elements from the stack until pushing onto it maintains monotonicity. The principle of this algorithm can be summarized in one sentence: **the elements being popped out are all greater than the current element, and because the stack is monotonically increasing, the nearest smaller element after it is the current element**.

Below are a few problems for you to practice while the knowledge is still fresh in your mind, hurry up and try them out~!

### Pseudocode

The above algorithm can be represented by the following pseudo-code. At the same time, this is a general algorithm template. You can directly solve the problem of monotonic stack.

It is recommended that everyone implement it in a programming language that they are familiar with, and you can basically use it by changing the symbols in the future.

```py
class Solution:
    def monostoneStack(self, arr: List[int]) -> List[int]:
        stack = []
        ans = []  # Define an array of the same length as arr, and initialize it to -1
        for i in range(len(arr)):  # Loop i in arr:
            while stack and arr[i] > arr[stack[-1]]:  # while stack and arr[i] > arr[top of the stack]:
                peek = stack.pop()  # pop the top of the stack
                ans[peek] = i - peek
            stack.append(i)
        return ans
```

**Complexity analysis**

-Time complexity: Since the elements of arr will only enter the stack and exit the stack once at most, the time complexity is still $O(N)$, where N is the length of the array. -Spatial complexity: Since the stack is used, and the maximum length of the stack is consistent with the length of arr, the spatial complexity is $O(N)$, where N is the length of the array.

### Code

Here are the monotonic stack templates for the two programming languages for your reference.

Python3：

```py
class Solution:
    def monostoneStack(self, T: List[int]) -> List[int]:
        stack = []
        ans = [0] * len(T)
        for i in range(len(T)):
            while stack and T[i] > T[stack[-1]]:
                peek = stack.pop(-1)
                ans[peek] = i - peek
            stack.append(i)
        return ans
```

JS:

```js
var monostoneStack = function (T) {
  let stack = [];
  let result = [];
  for (let i = 0; i < T.length; i++) {
    result[i] = 0;
    while (stack.length > 0 && T[stack[stack.length - 1]] < T[i]) {
      let peek = stack.pop();
      result[peek] = i - peek;
    }
    stack.push(i);
  }
  return result;
};
```

### Topic recommendation

The following questions will help you understand the monotonic stack and let you understand when you can use the monotonic stack for algorithm optimization.

- [42. Pick up the rain](https://github.com/azl397985856/leetcode/blob/master/problems/42.trapping-rain-water.md "42. Pick up the rain")
- [84. The largest rectangle in the histogram](https://github.com/azl397985856/leetcode/blob/master/problems/84.largest-rectangle-in-histogram.md "84. The largest rectangle in the histogram")
- [739.Daily temperature](https://github.com/azl397985856/leetcode/blob/master/daily/2019-06-06.md "739. Daily temperature")

- 316. Remove duplicate letters
- 402. Remove K digits
- 496. The next larger element I
- 581. Shortest unordered continuous subarray
- 901. Stock price span

## Summary

The essence of a monotonous stack is still a stack, which is a type of limited data structure itself. Its limitation refers to the fact that operations can only be performed at one end. Monotonous stacks further limit the basic stack by requiring that the elements within always maintain monotonicity.


Since the elements in the stack are always monotonic, it is inherently suitable for solving problems like **finding the first position smaller than the element itself that comes after it**. If you encounter problems that require **finding the first position smaller than the element itself that comes after it**, consider using a monotonous stack.

The approach to implementing a monotonous stack is relatively fixed. You can refer to my pseudocode and summarize a template for yourself. Direct application of this template in the future can greatly improve efficiency and reduce errors in problem-solving.
