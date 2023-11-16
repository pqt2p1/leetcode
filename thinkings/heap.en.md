# Heap Topic

![](https://p.ipic.vip/dns3hz.jpg)

Hello everyone, I am lucifer. Today I bring you the topic of "Heap". First, let's look at the outline of this article, which I've created using a mindmap. I will continue to refine it and gradually complete other topics.

> You can also use the vscode blink-mind extension to open the source file and view some notes. The source file can be obtained by replying to my public account "力扣加加" (LeetCode Plus Plus) for the mindmap, and I will continue to update it with more content. The vscode extension link is: https://marketplace.visualstudio.com/items?itemName=awehook.vscode-blink-mind

This series includes the following topics:

- [After nearly completing all the linked list questions on LeetCode, I discovered these things...](https://lucifer.ren/blog/2020/11/08/linked-list/)
- [After nearly completing all the tree questions on LeetCode, I discovered these things...](https://lucifer.ren/blog/2020/11/23/tree/)
- After nearly completing all the heap questions on LeetCode, I discovered these things... (which is this article)
- 
<!-- more -->

## A Little Ramble

There are 42 questions under the [Heap tag](https://leetcode.cn/tag/tree/problemset/) on LeetCode. To prepare for this topic, I went through almost all of the heap questions on LeetCode.

![](https://p.ipic.vip/qx5cws.jpg)

As you can see, except for 3 locked ones, I have tried all of them. By focusing on these questions, I found some interesting information, which I will share with you today.

It's important to note that this article does not distinguish between heaps and priority queues. Therefore, you can consider the heaps and priority queues mentioned in this article as the same thing. If you are interested in the academic differences between the two, you can refer to relevant materials.

> Unless specified otherwise, the heaps mentioned in this article refer to min-heaps.

## How Difficult Are Heap Problems?

Heap is indeed a challenging topic. According to the official difficulty tags, there are only 42 heap problems, with nearly 50% being of hard difficulty. To put this into perspective, less than 10% of tree-related topics are marked as hard.

In terms of pass rate, **more than half** of the heap problems have an average pass rate below 50%. By comparison, less than one-third of tree problems have a pass rate below 50%.

However, don't feel too pressured. I, lucifer, bring you a mnemonic: **"One core, two implementations, three techniques, four applications"**. We will discuss not only implementations and principles but also the **background, patterns, and templates** of problems.

> [The templates mentioned in the article can be accessed anytime from my LeetCode Cheatsheet Plugin.](https://chrome.google.com/webstore/detail/leetcode-cheatsheet/fniccleejlofifaakbgppmbbcdfjonle/related?hl=zh-CN&authuser=0 "力扣刷题插件 leetcode-cheatsheet")

## Analysis of Heap Use Cases

A heap is essentially a data structure, and data structures serve algorithms. So, what kind of algorithms does the heap data structure serve, and what are its use cases? This is the **first** question anyone learning about heaps needs to solve.
When do we use heaps? What are the principles behind heaps? How is a heap implemented? Don't worry, this article will unveil these secrets.

Before getting into the main content, here's a learning tip - **don't get too caught up in how a heap is implemented; first, understand what problem it solves.** Once you understand the background and the problem it addresses, act like a "package manager", directly using existing heap APIs to solve problems. Once you have a good understanding, then delve into the principles and implementation of heaps. This is how I learned about heaps, so I'm sharing this experience with you.

To illustrate the use scenarios of heaps, let's consider a hypothetical scenario.

**The following example is important; it will be referenced repeatedly later in the discussion.**

### A Registration System

#### Problem Description

Imagine you are the technical lead of a queuing system for registration. This system needs to issue a queue number to each person in line (enqueue), and call numbers based on a first-come, first-served principle (dequeue).

Additionally, we have several types of customers: ordinary customers, VIP customers, and supreme VIP customers.

- If different types of customers use different windows, how should I design and implement my system? (They receive different services, like VIP customers get expert doctors, while ordinary customers get general doctors)

- If different types of customers use the same window, how should I design and implement my system? (They receive the same service, but with different priorities. For instance, under the same conditions (like arriving at the same time), VIP customers have higher priority than ordinary customers)

How should I design my system to meet these requirements and achieve good scalability?

#### Preliminary Solution

If different types of customers use different windows, we can design three separate queues for the three types of people waiting in line. This design satisfies the requirements of the problem and is sufficiently simple.

![](https://p.ipic.vip/noqe15.jpg)

However, if **we have only one window** where all patients use the same queue, and customers of the same type follow the first-come, first-served principle mentioned above, customers of different types might cut in line.

To simplify, I introduce the concept of **virtual time**. Specifically:

- The virtual time for ordinary customers is the same as the real time.
- The virtual time for VIP customers is their actual arrival time minus one hour. For example, if a VIP customer arrives at 14:00, we consider them as having arrived at 13:00.
- The virtual time for supreme VIP customers is their actual arrival time minus two hours. For instance, if a supreme VIP customer arrives at 14:00, we consider them as having arrived at 12:00.

With this approach, we only need to serve customers based on their **"virtual arrival time"**.

Therefore, we can continue using the three-queue approach from before, except the queues store virtual times instead of real times. Each time we start calling numbers, we compare virtual times and serve those with the earlier virtual times first.

![](https://p.ipic.vip/clq91x.jpg)

> It's not hard to see that the times within each queue are ordered.

**In this scenario, the virtual time essentially represents the priority weight in a priority queue,** where a smaller virtual time implies a higher priority.

#### Dealing with Queue Jumping

This algorithm effectively meets our requirements with a good level of complexity. However, the situation is not yet fully resolved; we have encountered new product requirements:

- If a patient from another clinic is transferred to our clinic, they will be placed in the queue based on their previous waiting time. For example, if someone was registered at 12:00 in another clinic, they are still considered to have registered at 12:00 in our clinic.
- If a patient does not respond within three minutes of being called, their spot is forfeited. However, if they return later, they are requeued with a virtual time of one hour before the current time. For example, if they were called at 13:00, did not respond, and came back at 13:30, they are considered to have joined the queue at 12:30 and are re-enqueued.

This introduces a situation of "queue jumping." How should this be handled? A simple method is to insert them into the correct position and **readjust the positions of everyone else in line.**


The following images illustrate the insertion of a regular customer who starts queuing at 1:30.

![](https://p.ipic.vip/q22wm2.jpg)
(Finding the insertion point)

![](https://p.ipic.vip/uhftpi.jpg)
(Inserting them into the queue)

If the queue is implemented as an array, the time complexity of the queue-jumping process is $O(N)$ where $N$ is the length of the queue being jumped. If the queue is long, the number of adjustments increases significantly.

However, we realize that what we are essentially maintaining is an **ordered list**. While using an array allows for random access, this feature is not necessary for our requirements. If we use a linked list, the theoretical time complexity is $O(1)$， but how do we locate the insertion point? A naive approach is to search by traversal, but this degrades the time complexity back to $O(N)$. Is there a better approach? The answer lies in our main topic: **priority queues**.

As mentioned, even with a linked list, searching requires $O(N)$，time. Can we optimize this process? In fact, this is a priority queue implemented as a linked list. Since it is ordered, we can use a skip list to accelerate the search, reducing the time complexity to $O(logN)$。

![](https://p.ipic.vip/t6l1jp.jpg)

Many similar problems exist in the world of algorithms. For example, in building database indexes, if we add an index to an ordered column, we can't adjust all the data every time we insert a record (as with the array implementation). Therefore, we can use a balanced tree, where each insertion adjusts at most $(O(logN))$ data. Another implementation of the priority queue, the binary heap, also optimizes the time complexity to $O(logN)$.

![](https://p.ipic.vip/0au6fq.jpg)

This article only explains the common binary heap implementation and does not cover skip lists and red-black trees. We will introduce the binary heap implementation of priority queues in detail later. For now, it's important to understand what problem the priority queue solves.

#### Solving Problems with Heaps

The two core APIs of a heap are `push` and `pop`.

Don't worry about how they are implemented for now; you can think of it as a black box that provides two APIs:

- `push`: Inserts data without concern for the internal organization. This corresponds to **queueing** and **queue jumping** in the scenario described above.
- `pop`： Removes and returns the smallest data element, again without needing to know the internal mechanics. This corresponds to **calling numbers** in the scenario above.

> The examples here actually refer to a min-heap. In a max-heap, the `pop` function would return the largest element.

Using these two APIs, we can implement the requirements mentioned above.

```py
# 12:00 - A regular customer arrives (push)
heapq.heappush(normal_pq, '12:00')
# 12:30 - Another regular customer arrives (push)
heapq.heappush(normal_pq, '12:30')
# 13:00 - Another regular customer arrives (push)
heapq.heappush(normal_pq, '13:00')
# Queue jumping (push). The time complexity can reach O(logN). Let's not worry about the specifics for now; we'll cover that later.
heapq.heappush(normal_pq, '12:20')
# Calling numbers (pop). The customer from 12:00 is called first. Note that the popping time complexity has also become O(logN), which might be considered the price of convenience.
heapq.heappop(normal_pq)
```

### Summary

In the scenario above, using either arrays or linked lists can meet the requirements, but other data structures perform better in handling "queue jumping."

Specifically：

- Maintaining a sorted array at all times makes it easy to find extreme values, but queue jumping is problematic.

- Maintaining a sorted linked list also makes it easy to find extreme values. However, to find the insertion point quickly and not just through linear scanning, we need to use indexes. This implementation corresponds to the **skip list implementation** of a priority queue.

- Maintaining a tree at all times can also achieve this, where the root node is the extreme value, allowing $O(1)$ access to it, but the adjustment process requires $O(logN)$. This implementation corresponds to the **binary heap implementation** of a priority queue.

To summarize, **a heap dynamically helps you find extreme values.** Use it when you need to dynamically find the maximum or minimum value. The specifics of how it's implemented and the complexity analysis will be discussed later. For now, just remember the usage scenarios, how a heap solves these problems, and the heap APIs.

## Queue vs. Priority Queue

Through an example, we've just explored the concept of a priority queue. Before delving into the specific implementations, I think it's necessary to address a common question: Is a priority queue a queue?

Many people believe that queues and priority queues are entirely different, similar to how Java and JavaScript are different. This is a common assertion in many articles.

However, I have a different view. In fact, a regular queue can be considered a special kind of **priority queue**. This differs from the majority opinion that **priority queues and queues have little in common**. I believe that **a queue is simply a priority queue where time is the variable used as the priority**: the earlier the time, the higher the priority, and the higher the priority, the earlier it leaves the queue.

When writing BFS (Breadth-First Search) algorithms, we usually use queues to manage the order of node visits. But can a priority queue be used instead? Of course! Let me give an example:

### Example Problem - 513. Find Bottom Left Tree Value

#### Problem Description

```
Given a binary tree, find the leftmost value in the last row of the tree.

Example 1:

Input:

    2
   / \
  1   3

Output:
1
 

Example 2:

Input:

        1
       / \
      2   3
     /   / \
    4   5   6
       /
      7

Output:
7
 

Note: You may assume the tree (i.e., the given root node) is not NULL.
```

#### Approach

We can use BFS (Breadth-First Search) to perform a level-order traversal of the tree. For each level, we traverse from right to left. This way, the last node encountered in the level-order traversal will be the **leftmost node in the bottom row** of the tree.

The conventional method uses a deque (double-ended queue), which easily implements the level-order traversal due to the first-in-first-out principle of queues.

#### Code

For those who might not understand the code right away, don't worry. It might be easier to revisit this section after reading through the entire article. The same goes for subsequent sections; further explanation will not be repeated.

Python Code:

```py
class Solution:
    def findBottomLeftValue(self, root: TreeNode) -> int:
        if root is None:
            return None
        queue = collections.deque([root])
        ans = None
        while queue:
            size = len(queue)
            for _ in range(size):
                ans = node = queue.popleft()
                if node.right:
                    queue.append(node.right)
                if node.left:
                    queue.append(node.left)
        return ans.val

```

In fact, we can also use a priority queue in a similar way, with the thought process and code almost identical to the above.

```py
class Solution:
    def findBottomLeftValue(self, root: TreeNode) -> int:
        if root is None:
            return None
        queue = []
        # The heap stores a tuple (a, b, c), where a is the level, b is the node number (numbered as in a complete binary tree, including null nodes), and c is the node itself
        heapq.heappush(queue, (1, 1, root))
        ans = None
        while queue:
            size = len(queue)
            for _ in range(size):
                level, i, node = heapq.heappop(queue)
                ans = node
                if node.right:
                    heapq.heappush(queue, (level + 1, 2 * i + 1, node.right))
                if node.left:
                    heapq.heappush(queue, (level + 1, 2 * i + 2, node.left))
        return ans.val
```

### Summary

**Every place where a queue is used, a priority queue can be used as well, but the reverse is not always true.**

Since priority queues are so powerful, why don't we use them all the time instead of regular queues? The main reason is that they have a worse time complexity.

For example, in the above case, enqueuing and dequeuing could easily be done in $O(1)$  time. But now? Both enqueuing and dequeuing have a complexity of $O(logN)$，where N is the current size of the queue. Therefore, using a heap where it's not necessary greatly increases the algorithm's time complexity, which is of course not appropriate. Crudely put, it's like 'taking off your pants to fart' - an unnecessarily complicated way to do a simple task.

But are priority queues really never used for BFS? Of course not! For instance, in shortest path problems in weighted graphs, priority queues are necessary instead of regular queues for BFS, because the paths have **differences in weights**. This is exactly what priority queues were designed for. **A typical BFS implementation using a priority queue is the Dijkstra's algorithm.**

This reinforces my point that **a queue is just a special type of priority queue.** It's special in that everyone's priority is determined by their order of arrival - the earlier someone arrives, the higher their priority. In such cases, there's no need to maintain a heap, thus achieving better time complexity.

## The Central Concept

The core concept of heap problems is **dynamically finding** extreme values. Both 'dynamic' and 'extreme value' are essential components.

Finding extreme values is relatively straightforward, usually meaning to find the maximum or minimum value. However, 'dynamic' is not as straightforward. For example, if you're asked to find the k-th smallest number in an array, is that dynamic? This actually depends on your interpretation. In our context, this situation is considered dynamic.

How to understand that the above example is dynamic?

Think of it this way: since a heap can only find extreme values (e.g., the smallest value), it cannot directly find the k-th smallest value.

So, could we first find the smallest value, then remove it from the heap (corresponding to 'calling a number' in the previous example)? Then continue to find the smallest value again, which would now be the second smallest. To find the k-th smallest value, you would repeat this process k times.

During this process, you'll notice that the data is **dynamically changing**, corresponding to changes in the size of the heap.

Next, let's illustrate this with a few examples.

### Example 1 - 1046. Last Stone Weight

#### Problem Description

```
There is a pile of stones, each stone has a positive integer weight.

Each round, choose the two heaviest stones and smash them together. Assume the weights of the stones are x and y, with x <= y. The possible outcomes of the smash are:

If x == y, both stones are completely smashed;
If x != y, the stone weighing x is completely smashed, and the stone weighing y will have a new weight of y-x.
In the end, there will be at most one stone left. Return the weight of this stone. Return 0 if there are no stones left.

 

Example:

Input: [2,7,4,1,8,1]
Output: 1
Explanation:
First, choose 7 and 8, resulting in 1, so the array becomes [2,4,1,1,1],
Then choose 2 and 4, resulting in 2, so the array becomes [2,1,1,1],
Next, choose 2 and 1, resulting in 1, so the array becomes [1,1,1],
Finally, choose 1 and 1, resulting in 0, leaving the array as [1], which is the weight of the last remaining stone.
 

Constraints:

1 <= stones.length <= 30
1 <= stones[i] <= 1000
```

#### Approach

The problem is relatively straightforward and can be solved by direct simulation. Note that after each round of smashing the two heaviest stones, the weight of the heaviest stone changes. This will **affect the choice of the heaviest stone in the next round**. Simply put, the weight of the heaviest stone is **dynamically changing** during the simulation.

This scenario of **dynamically finding extreme values** is very suitable for using a heap.

> Of course, looking at the data range `1 <= stones.length <= 30 and 1 <= stones[i] <= 1000`, it should also be possible to use a counting method.

#### Code

Java Code:

```java
import java.util.PriorityQueue;

public class Solution {

    public int lastStoneWeight(int[] stones) {
        int n = stones.length;
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(n, (a, b) -> b - a);
        for (int stone : stones) {
            maxHeap.add(stone);
        }

        while (maxHeap.size() >= 2) {
            Integer head1 = maxHeap.poll();
            Integer head2 = maxHeap.poll();
            if (head1.equals(head2)) {
                continue;
            }
            maxHeap.offer(head1 - head2);
        }

        if (maxHeap.isEmpty()) {
            return 0;
        }
        return maxHeap.poll();
    }
}
```

### Example 2 - 313. Super Ugly Number

#### Problem Description

```
Write a program to find the nth super ugly number.

A super ugly number is a positive integer whose prime factors are all from a given list of prime numbers of length k, called primes.

Example:

Input: n = 12, primes = [2,7,13,19]
Output: 32
Explanation: Given a list of 4 prime numbers primes = [2,7,13,19], the sequence of the first 12 super ugly numbers is: [1,2,4,7,8,13,14,16,19,26,28,32].
Note:

1 is a super ugly number for any given primes.
The numbers in the given primes are in ascending order.
0 < k ≤ 100, 0 < n ≤ 10^6, 0 < primes[i] < 1000.
The nth super ugly number is guaranteed to fit within a 32-bit signed integer range.
```

#### Approach

At first glance, this problem might not seem related to dynamically finding extreme values. However, let's analyze it more closely.

One approach might be to generate a very large number of ugly numbers, such as creating the first N ugly numbers in ascending order and then directly taking the Nth one.

Considering the constraints of this problem, 0 < n ≤ 10^6, should we pre-generate an array of super ugly numbers of size $10^6$? This way, we could retrieve the Nth super ugly number in $O(1)$ time.

However, there are two main issues with this approach:

1. **Time and Space Waste**: It's unnecessary to calculate all super ugly numbers every time. Such pre-processing would be inefficient in terms of both time and space.

2. **Generating Super Ugly Numbers**: How do we generate $10^6$ super ugly numbers?

By understanding the definition of ugly numbers, we know that a super ugly number can definitely be written in the following form.

```
if primes = [a, b, c, ...]
then f(ugly) = a * x1 * b * x2 * c * x3 ...
where x1, x2, x3 are positive integers.
```

Let's simplify the problem for a moment, considering the given example: [2,7,13,19].

We can handle this with four pointers. Let's look at the code:

```java
public class Solution {
    public int solve(int n) {
        int ans[]=new int[n+5];
        ans[0]=1;
        int p1=0,p2=0,p3=0,p4=0;
        for(int i=1;i<n;i++){
            ans[i]=Math.min(ans[p1]*2,Math.min(ans[p2]*7,Math.min(ans[p3]*13,ans[p4]*19)));
            if(ans[i]==ans[p1]*2) p1++;
            if(ans[i]==ans[p2]*7) p2++;
            if(ans[i]==ans[p3]*13) p3++;
            if(ans[i]==ans[p3]*19) p4++;
        }
        return ans[n-1];
    }
}
```

I personally refer to this technique as **multi-way merge** (I can't think of a better name), and I will also optimize this method using a heap in the later section "Three Techniques."

Since the pointers are dynamic and their number corresponds to the length of the primes array given in the problem, we can actually use memoized recursion to solve this. Maintain an iteration variable in the recursion body and the recursion stack. This problem can be seen as a state machine, so using dynamic programming to solve it aligns with intuition. However, here I introduce a heap-based solution, which I personally find simpler and more intuitive.

> Regarding state machines, I have an article [Can State Machines Be Used to Solve LeetCode Problems?](https://lucifer.ren/blog/2020/01/12/1262.greatest-sum-divisible-by-three/) that you might find useful.

In fact, we can **dynamically** maintain the current smallest super ugly number. After finding the first one, we remove it and then find the **next current smallest super ugly number** (which is the globally second smallest super ugly number). After n rounds, we get the nth smallest super ugly number. This scenario of dynamically maintaining extreme values is exactly what heaps are for.


> Does this remind you of the stone problem mentioned earlier?

Taking the given example [2,7,13,19]:

1. Put [2,7,13,19] into the heap.
2. Remove one number from the heap, which is 2. This gives us the **first** super ugly number.
3. Then, put the products of 2 and [2,7,13,19], i.e., [4,14,26,38], into the heap.
4. Repeat this process until we get the nth super ugly number.

The correctness of the above approach is unquestionable. Since the heap always provides the smallest number and we remove the smallest number each time, naturally, after n iterations, we get the nth largest super ugly number.

The heap solution is not very difficult; the only thing to be mindful of is eliminating duplicates. For instance, 2 * 13 = 26 and 13 * 2 is also 26. We cannot put 26 into the heap twice. There are simple solutions to this:

- Either use a hash table to record all numbers that have been removed and avoid repeating them.
- Another method is to record the last number removed. Since the numbers removed from the heap are in non-strictly increasing order, you can simply compare the last number removed with the current number to determine if it's a duplicate.

Which method to use should be clear, right?

#### Code

Java Code:

```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        PriorityQueue<Long> queue=new PriorityQueue<>();
        int count = 0;
        long ans = 1;
        queue.add(ans);
        while (count < n) {
            ans=queue.poll();
            while (!queue.isEmpty() && ans == queue.peek()) {
                queue.poll();
            }
            count++;
            for (int i = 0; i < primes.length ; i++) {
                queue.offer(ans * primes[i]);
            }
        }
        return (int)ans;
    }
}
```

> The initialization of `ans` to 1 acts like a virtual head, solely for simplifying operations.

### Summary

The central concept of heaps is **dynamically finding extreme** values.

Extreme values are essentially the maximum or minimum values, which is straightforward. If we need the maximum value, we can use a max heap, and for the minimum value, a min heap is suitable.

In fact, without the dynamic aspect, heaps are often unnecessary. For example, you can simply traverse once to find the maximum value. The dynamic aspect is not always obvious, and that's where the difficulty of the problem lies. You need to analyze the problem to discern that it is indeed about **dynamically finding extreme values**, and then think of using a heap for optimization. There are many examples like this, and I will explain more in later sections.

## Two Implementations

Earlier, we briefly mentioned a few implementations of heaps. Here, I'll introduce two common implementations: one based on linked lists - Skip Lists, and the other based on arrays - Binary Heaps.

Using Skip Lists, if your algorithm isn't finely tuned, the performance can be unstable, and the memory usage can increase significantly with large data volumes. Therefore, we will detail the implementation of Binary Heaps, and for Skip Lists, we'll only discuss the basic principles. Detailed code implementation and other specifics will not be covered here as they are more niche.

### Skip Lists

Skip Lists are also a type of data structure, thus they serve some algorithms as well.

Although Skip Lists are not frequently encountered in interviews, they are often used in industry. LeetCode has only one problem about Skip Lists. However, the design principles of Skip Lists are worth learning and contemplating. They contain many algorithmic and data structural techniques worth learning, such as the idea of trading space for time, efficiency trade-offs, and more.

We mentioned earlier that handling queue-jumping is a primary consideration when designing **heaps**. How does the implementation of Skip Lists in heaps solve this problem?

We know that without additional space, searching for a value in a linked list requires checking each element in sequence, with a time complexity of $O(N)$，where N  is the length of the list.

![](https://p.ipic.vip/7k87vh.jpg)

(Singly Linked List)

When the length of the list is large, this kind of search time is unacceptable. A common optimization is to create a hash table with all nodes in it, trading space for time to reduce the time complexity. This method has a time complexity of $O(1)$， but a space complexity of $O(N)$。

![](https://p.ipic.vip/e3aci5.jpg)

(Singly Linked List + Hash Table)

To prevent issues with duplicate nodes in the linked list, we need to serialize the nodes before building the hash table. This approach uses even more space, and although the increase is only a factor, the overhead is not negligible. More importantly, a hash table cannot solve the problem of finding extreme values; it is only suitable for retrieving content based on a key.

To address these issues, Skip Lists were introduced.

As shown in the following image, we extract every second element from the linked list and add a level of indexing. The first-level index points to the original linked list; for example, through the down pointer of index 7, we can find the original 7 in the linked list. But how do we find 10?

> Note that this algorithm requires the linked list to be ordered.

![](https://p.ipic.vip/oziotf.jpg)

(Establishing a First-Level Index)

We can:

- Search in the current first-level skip list and find 7. We see that the next number, 18, is greater than 10, which means the 10 we are looking for is between these two numbers.
- Use the down pointer to go back to the original linked list and find 10 through the next pointer.

The performance improvement is not apparent in this example. However, if the number of elements continues to increase, and we continue to add more levels of indexing (second level, third level, etc.), the linked list can achieve binary search efficiency. But correspondingly, we pay the cost of additional space.

![](https://p.ipic.vip/5av4uh.jpg)

(Adding More Levels of Indexing)

Understanding this, you can metaphorically think of Skip Lists as **save points** in a game.

Imagine a game with 10 levels. If you want to play a specific part of level 5, you can start directly from level 5, which is faster than starting from level 1. We can even set many save points in each level. If you want to play a specific part of level 5, you can start not from the beginning of level 5 but directly from a save point closer to where you want to play, similar to a second-level index in a Skip List.

The time and space complexity of Skip Lists are not straightforward to analyze. Since the time complexity = height of the index * the average number of elements traversed per index level, and the height is approximately $logn$，with each level traversing a constant number of elements, the time complexity is $O(logn)$，the same as binary search.。

The space complexity is equivalent to the number of index nodes. For example, if we establish an index for every two nodes, it's roughly n/2 + n/4 + n/8 + ... + 8 + 4 + 2, so the space complexity is $O(n)$. Of course, if you create an index node for every three nodes, it saves more space, but the complexity remains the same.

With this understanding, implementing heaps with Skip Lists is straightforward:

- For heap insertions, insert into the linked list based on the index and update the index (optional).
- For heap deletions, remove from the head (or tail) and update the index (optional).

If you want to test your implementation, you can try LeetCode's [1206. Design Skiplist.](https://leetcode-cn.com/problems/design-skiplist/) 

Next, let's look at another more common implementation - Binary Heaps.

### Binary Heap

For the implementation of a binary heap, we will only explain the two core operations: `heappop` (removing from the heap) and `heappush` (adding to the heap). Other operations will not be covered, but I believe once you understand these two core operations, the rest should not be difficult.

The usage after implementation looks something like this:

```py
h = min_heap()
h.build_heap([5, 6, 2, 3])

h.heappush(1)
h.heappop() # 1
h.heappop() # 2
h.heappush(1)
h.heappop() # 1
h.heappop() # 3
```

#### Basic Principle

Essentially, a binary heap is a special kind of complete binary tree. Its uniqueness lies in one aspect: the value of the parent node is not greater than that of its children (**in a min-heap**).

![](https://p.ipic.vip/v32zmq.jpg)
(A Min-Heap)

Remember this point, as everything about binary heaps stems from it.

Since **the value of the parent node is not greater than that of its children (in a min-heap)**, we can naturally deduce that the root of the tree is the minimum value. This fulfills the heap's function of **finding extreme values**.

But what about the dynamic aspect? How does a binary heap achieve this?

##### Removing from the Heap (heappop)

Suppose we remove the root node from the heap. This leaves an empty spot at the root. The second smallest item should replace it. But how? It all comes back to the principle: **the value of the parent node is not greater than that of its children (in a min-heap).**

If we only remove the root node, then one heap would split into two, complicating the problem.

![](https://p.ipic.vip/ypzpn9.jpg)
(After removing the root node, two new heaps are formed)

A common operation is to swap the root node with the last node. However, the new root node may not satisfy the heap property **where the parent node's value is not greater than its children's values (min-heap).**

As shown in the following image, after swapping the root node's value (2) with the value at the tail, the heap property is no longer satisfied.

![](https://p.ipic.vip/gi2ofs.jpg)

At this point, we only need to sink the new root node down to the correct position. The **correct position** still aligns with the principle that **the parent node's value is not greater than its children's values (min-heap)**. If this condition is not met, we continue to sink the node down until it is satisfied.

When sinking the root node, there are two possible directions: sink to the left child or the right child? For a min-heap, the answer should be to sink to the smaller child, otherwise, the correct heap structure might be missed. For instance, in the heap above, if we sink to the right child (4), the correct heap top (3) cannot be achieved. Therefore, we need to sink to the left child.

![](https://p.ipic.vip/gqm5d9.jpg)

Even after sinking to the position shown in the image, the heap property is not satisfied, so we continue with the same operation.

![](https://p.ipic.vip/ar3ty6.jpg)

Some might wonder if, after popping the root node and performing the sinking operations described above, the heap still satisfies its properties?

The answer is yes. This is not hard to understand. Since the last leaf node has been moved to the root, its eventual position is uncertain, but from the operations above, we can see:

- The nodes along its sinking path certainly satisfy the heap property.
- Nodes not on the sinking path retain their relative positions from before the root was removed, so they also satisfy the heap property.

Therefore, **after popping the root node and performing the sinking operations, the heap still satisfies its properties.**

In terms of time complexity, it can be shown that sinking is directly proportional to the tree's height, so the time complexity is $O(h)$， where ℎ is the tree height. Since a binary heap is a complete binary tree, the tree height is approximately $logN$，where $N$ is the number of nodes in the tree.
##### Adding to the Heap (heappush)

Adding to the heap is similar to removing from it. We can directly insert a new node at the end of the tree. Similar to before, this operation may also disrupt the heap property.

> One reason for doing this is that it has a lower time complexity, as we are simulating with an array and adding elements to the end of an array has a time complexity of $O(1)$。

![](https://p.ipic.vip/usffec.jpg)

In this case, the node that does not satisfy the heap property is the recently inserted tail node, so we cannot perform a sinking operation. Instead, we need to do an **upward floating (swim) operation.**

> Leaf nodes can only float upwards (the root node can only sink down, while other nodes can either sink or float).

Similar to the sinking process, if the heap property is not satisfied, we swap it with its parent node (floating upwards), and continue this process until the heap property is satisfied.

![](https://p.ipic.vip/r0vogx.jpg)
(First float upwards, still not satisfying heap property, continue floating)

![](https://p.ipic.vip/arunjx.jpg)
(Satisfies the heap property, floating process completed)

After such operations, it still forms a heap that satisfies the heap property. The proof process is similar to above and will not be repeated.

Note that since floating **only requires comparing the current node with its parent**, and omits the process of determining which child node is smaller, it is simpler.

#### Implementation

For complete binary trees, using an array for implementation is very convenient because:

- If a node's index in the array is i，then its left child's index is $2 \times i$，and the right child's index is $2 \times i$+1。
- If a node's index in the array is i, then its parent's index is i//2（floor division）。

This requires that **the array starts storing data from index 1**. If not, the formulas can be slightly adjusted to achieve the same effect. However, this is an industry convention, and it's better to stay consistent with the industry. Another advantage of starting from 1 is that we can leave the index 0 position empty to store information like **the size of the heap**, which is a practice in some university textbooks.

The following image shows a complete binary tree and its array representation.

![](https://p.ipic.vip/npka2q.jpg)
(Note the correspondence between array indices)

To visualize it, we can draw the following correspondence diagram:

![](https://p.ipic.vip/an63qu.jpg)

With this, doesn't it look quite similar to the tree above? Is it easier to understand?

Having explained the floating and sinking processes, and the relationship between parent and child node indices, the code implementation becomes quite clear. Let's look at the core **floating** and **sinking** code implementation.

Pseudocode：

```java
// x is the element to float up, starting from the bottom of the tree
private void shift_up(int x) {
  while (x > 1 && h[x] > h[x / 2]) {
    // swap exchanges the values of two positions in the array
    swap(h[x], h[x / 2]);
    x /= 2;
  }
}
// x is the element to sink down, starting from the top of the tree
private void shift_down(int x) {
  while (x * 2 <= n) {
    // minChild returns the index of the smaller child node
    int mc = minChild(x);
    if (h[mc] >= h[x]) break;
    swap(h[x], h[mc]);
    x = mc;
  }
}
```

Here is an example using Java to discuss the code writing. Implementations of binary heaps in other languages can be found in my **LeetCode Cheatsheet plugin**. You can obtain the plugin by responding to the **LeetCode Plus Plus** public account and asking for the plugin.

```java
import java.util.Arrays;
import java.util.Comparator;

/**
 * Constructing a heap using a complete binary tree
 * Prerequisite: starting from 1
 * Then, the child nodes are i <<1 and i<<1 + 1
 * Core methods are:
 * shiftdown - swapping and sinking
 * shiftup - swapping and floating
 * 
 * build - building the heap
 */

public class Heap {

    int size = 0;
    int queue[];

    public Heap(int initialCapacity) {
        if (initialCapacity < 1)
            throw new IllegalArgumentException();
        this.queue = new int[initialCapacity];
    }

    public Heap(int[] arr) {
        size = arr.length;
        queue = new int[arr.length + 1];
        int i = 1;
        for (int val : arr) {
            queue[i++] = val;
        }
    }

    public void shiftDown(int i) {

        int temp = queue[i];

        while ((i << 1) <= size) {
            int child = i << 1;
            // child!=size checks if the current element has a right node
            if (child != size && queue[child + 1] < queue[child]) {
                child++;
            }
            if (temp > queue[child]) {
                queue[i] = queue[child];
                i = child;
            } else {
                break;
            }
        }
        queue[i] = temp;
    }


    public void shiftUp(int i) {
        int temp = queue[i];
        while ((i >> 1) > 0) {
            if (temp < queue[i >> 1]) {
                queue[i] = queue[i >> 1];
                i >>= 1;
            } else {
                break;
            }
        }
        queue[i] = temp;
    }

    public int peek() {

        int res = queue[1];
        return res;
    }

    public int pop() {

        int res = queue[1];

        queue[1] = queue[size--];
        shiftDown(1);
        return res;
    }

    public void push(int val) {
        if (size == queue.length - 1) {
            queue = Arrays.copyOf(queue, size << 1+1);
        }
        queue[++size] = val;
        shiftUp(size);
    }

    public void buildHeap() {
        for (int i = size >> 1; i > 0; i--) {
            shiftDown(i);
        }
    }

    public static void main(String[] args) {

        int arr[] = new int[]{2,7,4,1,8,1};
        Heap heap = new Heap(arr);
        heap.buildHeap();
        System.out.println(heap.peek());
        heap.push(5);
        while (heap.size > 0) {
            int num = heap.pop();
            System.out.printf(num + "");
        }
    }
}

```

#### Summary

There are many implementations of heaps, such as Skip Lists based on linked lists, Binary Heaps based on arrays, and implementations based on Red-Black Trees, among others. Here, we have detailed the implementation of Binary Heaps, not only because of their simplicity but also because they perform well in many situations, making them highly recommended for thorough understanding.

The core principle of implementing a Binary Heap is to always maintain the heap's properties, which are **the parent node's value is not greater than its children's values (in a min-heap)**. To achieve this, we use floating and sinking operations during heap insertion and removal, appropriately swapping elements. Specifically, during the floating process, we swap with the larger parent node, and during the sinking process, we swap with the smaller of the two child nodes, assuming they exist and are smaller than the node being sunk.

Heapification was not analyzed in detail here. However, if you understand the heap insertion operation discussed in this article, heapification is quite straightforward. Essentially, heapification is a process of continuously adding to the heap, but **turning discrete operations over time into a one-time operation.**

## Preview

This article is planned to be published in two parts. This is the first part. The content to follow is even more substantial, covering** Three Techniques** and **Four Major Applications**.

- Three Techniques

1. Fixed Heap
2. Multi-way Merge
3. Post-facto Zhuge Liang (strategy based on hindsight)

- Four Major Applications

1. Top K
2. Weighted Shortest Distance
3. Factor Decomposition
4. Heap Sort

These topics are specifically designed to teach you how to solve problems. Mastering them will make most of the heap-related problems on LeetCode a piece of cake (of course, I'm only referring to the parts of the problems that involve heaps).

I welcome your opinions and feedback, which I will check and respond to when I have time. For more algorithmic patterns and solutions, you can visit my LeetCode solution repository: https://github.com/azl397985856/leetcode. It currently has over 37K stars. You can also follow my public account "LeetCode Plus Plus" to tackle the tough nut of algorithms.

https://p.ipic.vip/e910pi.jpg
