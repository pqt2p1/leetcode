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

这个技巧我自己称之为**多路归并**（实现想不到什么好的名字），我也会在后面的**三个技巧**也会对此方法使用堆来优化。

由于这里的指针是动态的，指针的数量其实和题目给的 primes 数组长度一致。因此实际上，我们可以使用记忆化递归的形式来完成，**递归体和递归栈分别维护一个迭代变量即可**。而这道题其实可以看出是一个状态机，因此使用动态规划来解决是符合直觉的。而这里，介绍一种堆的解法，相比于动态规划，个人认为更简单和符合直觉。

> 关于状态机，我这里有一篇文章[原来状态机也可以用来刷 LeetCode？](https://lucifer.ren/blog/2020/01/12/1262.greatest-sum-divisible-by-three/ "原来状态机也可以用来刷 LeetCode？")，大家可以参考一下哦。

实际上，我们可以**动态**维护一个当前最小的超级丑数。找到第一个， 我们将其移除，再找**下一个当前最小的超级丑数**（也就是全局第二小的超级丑数）。这样经过 n 轮，我们就得到了第 n 小的超级丑数。这种动态维护极值的场景正是堆的用武之地。

> 有没有觉得和上面石头的题目很像？

以题目给的例子 [2,7,13,19] 来说。

1. 将 [2,7,13,19] 依次入堆。
2. 出堆一个数字，也就是 2。这时取到了**第一个**超级丑数。
3. 接着将 2 和 [2,7,13,19] 的乘积，也就是 [4,14,26,38] 依次入堆。
4. 如此反复直到取到第 n 个超级丑数。

上面的正确性是毋庸置疑的，由于每次堆都可以取到最小的，每次我们也会将最小的从堆中移除。因此取 n 次自然就是第 n 大的超级丑数了。

堆的解法没有太大难度，唯一需要注意的是去重。比如 2 \* 13 = 26，而 13 \* 2 也是 26。我们不能将 26 入两次堆。解决的方法也很简单：

- 要么使用哈希表记录全部已经取出的数，对于已经取出的数字不再取即可。
- 另一种方法是记录上一次取出的数，由于取出的数字是按照**数字大小不严格递增**的，这样只需要拿上次取出的数和本次取出的数比较一下就知道了。

用哪种方法不用多说了吧？

#### 代码

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

> ans 初始化为 1 的作用相当于虚拟头，仅仅起到了简化操作的作用

### 小结

堆的中心就一个，那就是**动态求极值**。

而求极值无非就是最大值或者最小值，这不难看出。如果求最大值，我们可以使用大顶堆，如果求最小值，可以用最小堆。

而实际上，如果没有动态两个字，很多情况下没有必要使用堆。比如可以直接一次遍历找出最大的即可。而动态这个点不容易看出来，这正是题目的难点。这需要你先对问题进行分析， 分析出这道题**其实就是动态求极值**，那么使用堆来优化就应该被想到。类似的例子有很多，我也会在后面的小节给大家做更多的讲解。

## 两种实现

上面简单提到了堆的几种实现。这里介绍两种常见的实现，一种是基于链表的实现- 跳表，另一种是基于数组的实现 - 二叉堆。

使用跳表的实现，如果你的算法没有经过精雕细琢，性能会比较不稳定，且在数据量大的情况下内存占用会明显增加。 因此我们仅详细讲述二叉堆的实现，而对于跳表的实现，仅讲述它的基本原理，对于代码实现等更详细的内容由于比较偏就不在这里讲了。

### 跳表

跳表也是一种数据结构，因此 ta 其实也是服务于某种算法的。

跳表虽然在面试中出现的频率不大，但是在工业中，跳表会经常被用到。力扣中关于跳表的题目只有一个。但是跳表的设计思路值得我们去学习和思考。 其中有很多算法和数据结构技巧值得我们学习。比如空间换时间的思想，比如效率的取舍问题等。

上面提到了应付插队问题是设计**堆**应该考虑的首要问题。堆的跳表实现是如何解决这个问题的呢？

我们知道，不借助额外空间的情况下，在链表中查找一个值，需要按照顺序一个个查找，时间复杂度为 $O(N)$，其中 N 为链表长度。

![](https://p.ipic.vip/7k87vh.jpg)

（单链表）

当链表长度很大的时候， 这种时间是很难接受的。 一种常见的的优化方式是**建立哈希表，将所有节点都放到哈希表中，以空间换时间的方式减少时间复杂度**，这种做法时间复杂度为 $O(1)$，但是空间复杂度为 $O(N)$。

![](https://p.ipic.vip/e3aci5.jpg)

（单链表 + 哈希表）

为了防止链表中出现重复节点带来的问题，我们需要序列化节点，再建立哈希表，这种空间占用会更高，虽然只是系数级别的增加，但是这种开销也是不小的 。更重要的是，哈希表不能解决查找极值的问题，其仅适合根据 key 来获取内容。

为了解决上面的问题，跳表应运而生。

如下图所示，我们从链表中每两个元素抽出来，加一级索引，一级索引指向了原始链表，即：通过一级索引 7 的 down 指针可以找到原始链表的 7 。那怎么查找 10 呢？

> 注意这个算法要求链表是有序的。

![](https://p.ipic.vip/oziotf.jpg)

（建立一级索引）

我们可以：

- 通过现在一级跳表中搜索到 7，发现下一个 18 大于 10 ，也就是说我们要找的 10 在这两者之间。
- 通过 down 指针回到原始链表，通过原始链表的 next 指针我们找到了 10。

这个例子看不出性能提升。但是如果元素继续增大， 继续增加索引的层数，建立二级，三级。。。索引，使得链表能够实现二分查找，从而获得更好的效率。但是相应地，我们需要付出额外空间的代价。

![](https://p.ipic.vip/5av4uh.jpg)

（增加索引层数）

理解了上面的点，你可以形象地将跳表想象为玩游戏的**存档**。

一个游戏有 10 关。如果我想要玩第 5 关的某一个地方，那么我可以直接从第五关开始，这样要比从第一关开始快。我们甚至可以在每一关同时设置很多的存档。这样我如果想玩第 5 关的某一个地方，也可以不用从第 5 关的开头开始，而是直接选择**离你想玩的地方更近的存档**，这就相当于跳表的二级索引。

跳表的时间复杂度和空间复杂度不是很好分析。由于时间复杂度 = 索引的高度 \* 平均每层索引遍历元素的个数，而高度大概为 $logn$，并且每层遍历的元素是常数，因此时间复杂度为 $logn$，和二分查找的空间复杂度是一样的。

空间复杂度就等同于索引节点的个数，以每两个节点建立一个索引为例，大概是 n/2 + n/4 + n/8 + … + 8 + 4 + 2 ，因此空间复杂度是 $O(n)$。当然你如果每三个建立一个索引节点的话，空间会更省，但是复杂度不变。

理解了上面的内容，使用跳表实现堆就不难了。

- 入堆操作，只需要根据索引插到链表中，并更新索引（可选）。
- 出堆操作，只需要删除头部（或者尾部），并更新索引（可选）。

大家如果想检测自己的实现是否有问题，可以去力扣的[1206. 设计跳表](https://leetcode-cn.com/problems/design-skiplist/) 检测。

接下来，我们看下一种更加常见的实现 - 二叉堆。

### 二叉堆

二叉堆的实现，我们仅讲解最核心的两个操作： heappop（出堆） 和 heappush（入堆）。对于其他操作不再讲解，不过我相信你会了这两个核心操作，其他的应该不是难事。

实现之后的使用效果大概是这样的：

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

#### 基本原理

本质上来说，二叉堆就是一颗特殊的完全二叉树。它的特殊性只体现在一点，那就是**父节点的权值不大于儿子的权值（小顶堆）**。

![](https://p.ipic.vip/v32zmq.jpg)
（一个小顶堆）

上面这句话需要大家记住，一切的一切都源于上面这句话。

由于**父节点的权值不大于儿子的权值（小顶堆）**，那么很自然能推导出树的根节点就是最小值。这就起到了堆的**取极值**的作用了。

那动态性呢？二叉堆是怎么做到的呢？

##### 出堆

假如，我将树的根节点出堆，那么根节点不就空缺了么？我应该将第二小的顶替上去。怎么顶替上去呢？一切的一切还是那句话**父节点的权值不大于儿子的权值（小顶堆）**。

如果仅仅是删除，那么一个堆就会变成两个堆了，问题变复杂了。

![](https://p.ipic.vip/ypzpn9.jpg)
（上图出堆之后会生成两个新的堆）

一个常见的操作是，把根结点和最后一个结点交换。但是新的根结点可能不满足 **父节点的权值不大于儿子的权值（小顶堆）**。

如下图，我们将根节点的 2 和尾部的数字进行交换后，这个时候是不满足堆性质的。

![](https://p.ipic.vip/gi2ofs.jpg)

这个时候，其实只需要将新的根节点下沉到正确位置即可。这里的**正确位置**，指的还是那句话**父节点的权值不大于儿子的权值（小顶堆）**。如果不满足这一点，我们就继续下沉，直到满足。

我们知道根节点往下下沉的过程，其实有两个方向可供选择，是下沉到左子节点？还是下沉到右子节点？以小顶堆来说，答案应该是下沉到较小的子节点处，否则会错失正确答案。以上面的堆为例，如果下沉到右子节点 4，那么就无法得到正确的堆顶 3。因此我们需要下沉到左子节点。

![](https://p.ipic.vip/gqm5d9.jpg)

下沉到如图位置，还是不满足 **父节点的权值不大于儿子的权值（小顶堆）**，于是我们继续执行同样的操作。

![](https://p.ipic.vip/ar3ty6.jpg)

有的同学可能有疑问。弹出根节点前堆满足堆的性质，但是弹出之后经过你上面讲的下沉操作，一定还满足么？

答案是肯定的。这个也不难理解。由于最后的叶子节点被提到了根节点，它其实最终在哪是不确定的，但是经过上面的操作，我们可以看出：

- 其下沉路径上的节点一定都满足堆的性质。
- 不在下沉路径上的节点都保持了堆之前的相对关系，因此也满足堆的性质。

因此**弹出根节点后，经过上面的下沉操作一定仍然满足堆的性质**。

时间复杂度方面可以证明，下沉和树的高度成正相关，因此时间复杂度为 $O(h)$，其中 h 为树高。而由于二叉堆是一颗完全二叉树，因此树高大约是 $logN$，其中 N 为树中的节点个数。

##### 入堆

入堆和出堆类似。我们可以直接往树的最后插入一个节点。和上面类似，这样的操作同样可能会破坏堆的性质。

> 之所以这么做的其中一个原因是时间复杂度更低，因为我们是用数组进行模拟的，而在数组尾部添加元素的时间复杂度为 $O(1)$。

![](https://p.ipic.vip/usffec.jpg)

这次我们发现，不满足堆的节点目前是刚刚被插入节点的尾部节点，因此不能进行下沉操作了。这一次我们需要执行**上浮操作**。

> 叶子节点是只能上浮的（根节点只能下沉，其他节点既可以下沉，又可以上浮）

和上面基本类似，如果不满足堆的性质，我们将其和父节点交换（上浮），继续这个过程，直到满足堆的性质。

![](https://p.ipic.vip/r0vogx.jpg)
(第一次上浮，仍然不满足堆特性，继续上浮)

![](https://p.ipic.vip/arunjx.jpg)
（满足了堆特性，上浮过程完毕）

经过这样的操作，其还是一个满足堆性质的堆。证明过程和上面类似，不再赘述。

需要注意的是，由于上浮**只需要拿当前节点和父节点进行比对就可以了，** 由于省去了判断左右子节点哪个更小的过程，因此更加简单。

#### 实现

对于完全二叉树来说使用数组实现非常方便。因为：

- 如果节点在数组中的下标为 i，那么其左子节点下标为 $2 \times i$，右节点为 $2 \times i$+1。
- 如果节点在数组中的下标为 i，那么父节点下标为 i//2（地板除）。

当然这要求你的**数组从 1 开始存储数据**。如果不是，上面的公式其实微调一下也可以达到同样的效果。不过这是一种业界习惯，我们还是和业界保持一致比较好。从 1 开始存储的另外一个好处是，我们可以将索引为 0 的位置空出来存储诸如**堆大小**的信息，这是一些大学教材里的做法，大家作为了解即可。

如图所示是一个完全二叉树和树的数组表示法。

![](https://p.ipic.vip/npka2q.jpg)
（注意数组索引的对应关系）

形象点来看，我们可以可以画出如下的对应关系图：

![](https://p.ipic.vip/an63qu.jpg)

这样一来，是不是和上面的树差不多一致了？有没有容易理解一点呢？

上面已经讲了上浮和下沉的过程。刚才也讲了父子节点坐标的关系。那么代码就呼之欲出了。我们来下最核心的**上浮**和**下沉**的代码实现吧。

伪代码：

```java
// x 是要上浮的元素，从树的底部开始上浮
private void shift_up(int x) {
  while (x > 1 && h[x] > h[x / 2]) {
    //  swqp 就是交换数组两个位置的值
    swap(h[x], h[x / 2]);
    x /= 2;
  }
}
// x 是要下沉的元素，从树的顶部开始下沉
private void shift_down(int x) {
  while (x * 2 <= n) {
    // minChild 是获取更小的子节点的索引并返回
    mc = minChild(x);
    if (h[mc] <= h[x]) break;
    swap(h[x], h[mc]);
    x = mc;
  }
}
```

这里 Java 语言为例，讲述一下代码的编写。其他语言的二叉堆实现可以去我的**刷题插件 leetcode-cheatsheet** 中获取。插件的获取方式在公众号**力扣加加**里，回复插件即可。

```java
import java.util.Arrays;
import java.util.Comparator;

/**
 * 用完全二叉树来构建 堆
 * 前置条件 起点为 1
 * 那么 子节点为  i <<1 和 i<<1 + 1
 * 核心方法为
 * shiftdown 交换下沉
 * shiftup 交换上浮
 * <p>
 * build 构建堆
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
            // child!=size 判断当前元素是否包含右节点
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

#### 小结

堆的实现有很多。比如基于链表的跳表，基于数组的二叉堆和基于红黑树的实现等。这里我们详细地讲述了二叉堆的实现，不仅是其实现简单，而且其在很多情况下表现都不错，推荐大家重点掌握二叉堆实现。

对于二叉堆的实现，核心点就一点，那就是始终维护堆的性质不变，具体是什么性质呢？那就是 **父节点的权值不大于儿子的权值（小顶堆）**。为了达到这个目的，我们需要在入堆和出堆的时候，使用上浮和下沉操作，并恰当地完成元素交换。具体来说就是上浮过程和比它大的父节点进行交换，下沉过程和两个子节点中较小的进行交换，当然前提是它有子节点且子节点比它小。

关于堆化我们并没有做详细分析。不过如果你理解了本文的入堆操作，这其实很容易。因此堆化本身就是一个不断入堆的过程，只不过**将时间上的离散的操作变成了一次性操作**而已。

## 预告

本文预计分两个部分发布。这是第一部分，后面的内容更加干货，分别是**三个技巧**和**四大应用**。

- 三个技巧

1. 多路归并
2. 固定堆
3. 事后小诸葛

- 四大应用

1. topK
2. 带权最短距离
3. 因子分解
4. 堆排序

这两个主题是专门教你怎么解题的。掌握了它，力扣中的大多数堆的题目都不在话下（当然我指的仅仅是题目中涉及到堆的部分）。

大家对此有何看法，欢迎给我留言，我有时间都会一一查看回答。更多算法套路可以访问我的 LeetCode 题解仓库：https://github.com/azl397985856/leetcode 。目前已经 37K star 啦。大家也可以关注我的公众号《力扣加加》带你啃下算法这块硬骨头。

![二维码](https://p.ipic.vip/e910pi.jpg)
