# I've Nearly Completed All the Heap Problems on LeetCode, and Here's What I Discovered... (Part Two)

## A Little Aside

Last time, I conducted a small survey on my WeChat public account, "Vote for the Programming Language You Want in Solutions". Here are the results:

![Voting results](https://p.ipic.vip/j4yrg2.jpg)

Regarding other languages, most votes were for Go.

![What did the other people who voted for write?](https://p.ipic.vip/fe8utj.jpg)

Since Java and Python accounted for over 60% of the preference, this time I'll try writing in both Java and Python, thanks to @CaptainZ for the Java code. To avoid making the article too long and tedious, I've placed all the code (both Java and Python) on the official LeetCode Plus website, available at: https://leetcode-solution.cn/solution-code

> If you're not using a VPN, the website might open slowly.

## Main Content

![](https://p.ipic.vip/4r5oeh.jpg)

Hello everyone, I am Lucifer. Today, I bring you a special topic on "Heaps". First, let's look at the outline of this article, which I created using a mindmap. I will continue to refine it, gradually completing other topics.

> You can also open the source file in vscode blink-mind to view it, which includes some notes. The source file can be obtained from my WeChat public account "LeetCode Plus" by replying with "mindmap". I'll keep updating the mindmap with more content. vscode plugin link: https://marketplace.visualstudio.com/items?itemName=awehook.vscode-blink-mind

This series includes the following topics:

- [I have almost finished swiping all the linked topics of Lixu, and I found these things. 。 。 ](https://lucifer.ren/blog/2020/11/08/linked-list/)

- [After almost brushing all the tree questions of Li Buckle, I found these things. 。 。 ](https://lucifer.ren/blog/2020/11/23/tree/) 

- [After almost brushing all the piles of questions, I found these things. 。 。 (First bullet)](https://lucifer.ren/blog/2020/12/26/heap/)

<! -- more -->

This is the second part. If you haven't read the first part, I strongly recommend reading it. [After almost brushing all the piles of questions, I found these things. 。 。 (First bullet)](https://lucifer.ren/blog/2020/12/26/heap/)

This second part contains even more practical content, specifically Three Techniques and Four Major Applications. These topics are specifically designed to teach you how to solve problems. Mastering them means most heap-related problems on LeetCode will be within your grasp (of course, I'm only referring to the heap aspect of the problems).

Warning: The problems in this chapter are mostly marked as hard on LeetCode, which is common for heap problems, as I've mentioned before.

## A Brief Explanation

Before we dive into the main course, let's have an appetizer.

I'd like to introduce two concepts here: **Tuples** and **Simulating a Max Heap**. These are to ensure you won't be lost later on.

### Tuple

Using heaps isn't limited to storing single values, like the 1, 2, 3, 4 in [1,2,3,4]. In addition to single values, you can also store composite values, like objects or tuples.

Here, we'll talk about storing tuples, a technique that will be widely used later on. Please make sure to understand it. For example, [(1,2,3), (4,5,6), (2,1,3),(4,2,8)].

```py
h = [(1,2,3), (4,5,6), (2,1,3),(4,2,8)]
heapq.heappify(h) # Turn into a min-heap

heapq.heappop() # Pops out (1,2,3)
heapq.heappop() # Pops out (2,1,3)
heapq.heappop() # Pops out (4,2,8)
heapq.heappop() # Pops out (4,5,6)
```

The heap structure with tuples would look like this:

![Use a small top heap of tuples](https://p.ipic.vip/wioiow.jpg)

A simple explanation of the above code:

Using tuples, the first element of the tuple is taken as the key for comparison. If the first elements are the same, the second ones are compared. For example, (4,5,6) and (4,2,8) have the same first element, so we compare the second elements. Since 5 is greater than 2, (4,2,8) comes out of the heap first.

This technique has two uses:

1. To carry additional information. For example, if you want to find the k-th smallest number in a two-dimensional matrix, you would use the value as the key. But if you need row and column information during the process, tuples like (val, row, col) are suitable.

2. To sort based on two keys, a primary and a secondary key. There are typically two ways to do this:

    2.1. Both keys are sorted in the same order, either ascending or descending.

    2.2. The two keys are sorted in different orders, one in ascending and the other in descending.

Due to the length of the question, the details will not be discussed here. Pay attention to these techniques while practicing, and I might write a separate article explaining them in detail.

> If your programming language does not support heaps or tuples, you can still enable this by customizing the comparison logic.

### Simulating a Max Heap

Since Python doesn't have a built-in max heap, I've used a min heap for simulation. That is, taking the opposite of the original numbers. For example, if the original number is 5, then push -5 into the heap. With this approach, a min heap can be used as a max heap. However, remember to reverse the number when you pop it out.

Code example：

```py
h = []
A = [1,2,3,4,5]
for a in A:
heapq. heappush(h, -a)
-1 * heapq. heappop(h) # 5
-1 * heapq. heappop(h) # 4
-1 * heapq. heappop(h) # 3
-1 * heapq. heappop(h) # 2
-1 * heapq. heappop(h) # 1
```

The heap represented graphically would look like this:

![Small top pile simulates big top pile](https://p.ipic.vip/226haf.jpg)

That's all for the introduction, now let's get to the main content.

## Three Techniques

### Technique One - Fixed Heap

This technique refers to keeping the size of the heap, k, constant. In code, this can be achieved by **pushing a new element every time you pop one out**. Since the initial heap might be 0, we need to push elements into the heap to reach a size of k, so it's more about **keeping the heap's size at most k**.

A typical application of a fixed heap is to find the k-th smallest number. The simplest way to do this is to create a min heap, push all the numbers into the heap, and then pop them out one by one, a total of k times. The last number popped out is the k-th smallest.

However, we can also achieve this without pushing all the numbers in first. Instead, we create a max heap (note, not the min heap mentioned above) and keep the size of the heap to k. If a new number is pushed into the heap and the size exceeds k, the larger number between the top of the heap and the new number should be removed. This ensures that the numbers in the heap are the smallest k numbers of the entire set. And the largest among these smallest k numbers (i.e., the top of the heap) is exactly the k-th smallest, which is why we choose to build a max heap instead of a min heap.

![Fix the 5th smallest number on the big top stack](https://p.ipic.vip/okcn10.jpg)

In short, **a fixed-size k max heap can quickly find the k-th smallest number, and vice versa, a fixed-size k min heap can quickly find the k-th largest number**. For instance, the third problem of the LeetCode weekly contest on 2020-02-24 [5663. Find the kth largest XOR coordinate value](https://leetcode-cn.com/problems/find-kth-largest-xor-coordinate-value) can be solved using this fixed min heap technique (as this problem asks for the k-th largest number).

It might not be very clear just yet, so let's look at two examples to help deepen your understanding.

#### 295. The median of the data stream

##### Title description

```
The median is the middle number in an ordered list. If the length of the list is even, the median is the average of the two middle numbers.

For example,

The median of [2,3,4] is 3.

The median of [2,3] is (2 + 3) / 2 = 2.5.

Design a data structure that supports the following two operations:

Void addNum (int num)-add an integer from the data stream to the data structure.
Double findMedian()-returns the median of all current elements.
example：

addNum(1)
addNum(2)
findMedian() -> 1.5
addNum(3)
findMedian() -> 2
Advanced:

If all integers in the data stream are in the range of 0 to 100, how would you optimize your algorithm?
If 99% of the integers in the data stream are in the range of 0 to 100, how would you optimize your algorithm?
```

##### Thought Process

This problem can actually be seen as a special case of finding the k-th smallest number.

- If the list length is odd, then k is (n + 1) / 2, and the median is the k-th number. For example, if n is 5, k is (5 + 1)/ 2 = 3.

- If the list length is even, then k is (n + 1) / 2 and (n + 1) / 2 + 1, and the median is the average of these two numbers. For example, if n is 6, k is (6 + 1)/ 2 = 3 and (6 + 1) / 2 + 1 = 4.

Therefore, we can maintain two fixed heaps, where the size of each heap is $(n + 1) \div 2$ and $n - (n + 1)\div2$, meaning the sizes of the two heaps differ by at most 1. More specifically, $ 0 <= (n + 1) \div 2 - (n - (n + 1) \div 2) <= 1$。

Based on the above knowledge, we can:

- Create a max heap to store the smallest $(n + 1) \div 2$ numbers, so the top of the heap is the $(n + 1) \div 2$-th smallest number, which is the median in the case of an odd number of elements.
- Create a min heap to store the largest n - $(n + 1) \div 2$ numbers, so the top of the heap is the n - $(n + 1) \div 2$-th largest number. Combined with the max heap, this can be used to find the median in the case of an even number of elements.

With this knowledge, what remains is just how to maintain the sizes of the two heaps.

- If the max heap has fewer elements than the min heap, then transfer the smallest element from the min heap to the max heap. Since the min heap maintains the largest k numbers and the max heap maintains the smallest k numbers, the top of the min heap is guaranteed to be greater than or equal to the top of the max heap, and these two tops are the median at that time.
- If the max heap has 2 more elements than the min heap, then transfer the largest element from the max heap to the min heap, for the same reason.

By now, you might have understood why two separate heaps are established, and why one is a max heap and the other is a min heap. The reasons are as described above.

The application of fixed heaps is not limited to this; let's continue with another problem.

##### Code

```py
class MedianFinder:
    def __init__(self):
        self.min_heap = []
        self.max_heap = []
    def addNum(self, num: int) -> None:
        if not self.max_heap or num < -self.max_heap[0]:
            heapq.heappush(self.max_heap, -num)
        else:
            heapq.heappush(self.min_heap, num)
        if len(self.max_heap) > len(self.min_heap) + 1:
            heappush(self.min_heap, -heappop(self.max_heap))
        elif len(self.min_heap) > len(self.max_heap):
            heappush(self.max_heap, -heappop(self.min_heap))
    def findMedian(self) -> float:
        if len(self.min_heap) == len(self.max_heap): return (self.min_heap[0] - self.max_heap[0]) / 2
        return -self.max_heap[0]
```

(Code 1.3.1)

#### 857. Minimum Cost to Hire K Workers

##### Problem Description

```py
There are N workers. The quality of work of the i-th worker is quality[i], and their minimum expected wage is wage[i].

Now, we want to hire K workers to form a wage group. When hiring a group of K workers, we must pay them according to the following rules:

For each worker in the wage group, they should be paid according to the ratio of their work quality to the work quality of other workers in the group.
Each worker in the wage group should at least receive their minimum expected wage.
Return the minimum amount of money required to form a wage group that meets the above conditions.



Example 1:

Input: quality = [10,20,5], wage = [70,50,30], K = 2
Output: 105.00000
Explanation: We pay 70 to 0th worker and 35 to 2nd worker.
Example 2：

Input: quality = [3,1,10,10,1], wage = [4,8,2,2,7], K = 3
Output: 30.66667
Explanation: We pay 4 to 0th worker, 13.33333 to 2nd and 3rd workers separately.


Constraints:

1 <= K <=N<=10000, where N=quality. length = wage. length
1 <= quality[i] <= 10000
1 <= wage[i] <= 10000
Answers with an error within 10^-5 of the correct answer will be considered correct.
```

##### Thought Process

The problem requires us to select k people, pay wages based on the ratio of their work quality to the work quality of other workers in the group, and ensure that each worker in the wage group receives at least their minimum expected wage.

In other words, for the same group of k people, the ratio of their work quality to wage (q/w) must be a fixed value in order to minimize the total wages paid. Please understand this point first, as the rest of the content is based on this premise.

Let's define an index called **work efficiency**, which is the value of **q/w**. As mentioned earlier, for the wages to be minimal, this **q/w** must be the same for these k people and must be the lowest among them (the weakest link), otherwise someone will not receive their minimum expected wage.


Thus, we can write the following code:

```py
class Solution:
    def mincostToHireWorkers(self, quality: List[int], wage: List[int], K: int) -> float:
        eff = [(q / w, q, w) for q, w in zip(quality, wage)]
        eff.sort(key=lambda a: -a[0])
        ans = float('inf')
        for i in range(K-1, len(eff)):
            h = []
            k = K - 1
            rate, _, total = eff[i]
            # Find k people with higher work efficiency, and their wages should be as low as possible.
            # Since we have sorted by work efficiency in descending order, those before are all higher, and then using a heap can give us the k people with the lowest wages.
            for j in range(i):
                heapq.heappush(h, eff[j][1] / rate)
            while k > 0:
                total += heapq.heappop(h)
                k -= 1
            ans = min(ans, total)
        return ans
```

(Code 1.3.2)

This approach involves pushing many numbers and popping k times, which does not effectively utilize the **dynamic** nature of heaps, but only their ability to **find extreme values**.

A better method is to use the **fixed heap technique**.

This problem can be considered from a different angle. Isn't it essentially about selecting k people, comparing their work efficiency to the lowest among them, and calculating the total wage based on this lowest work efficiency to find the minimum total wage? Therefore, this problem can be approached by fixing a max heap of size k and ensuring through certain operations that the top of the heap is the k-th smallest (similar to the operations in the previous problems).

Moreover, the previous solution used a tuple of (q / w, q, w), which is actually unnecessary. Since two of these values are known, the third can be deduced, so storing two is sufficient. As we need to use the ratio of work efficiency as the key for the heap, we can choose either q or w. Here, I have chosen q, i.e., storing a tuple of (q/2, q).

Specifically, the total wages for k people with rate as the lowest work efficiency ratio equals $\displaystyle \sum_{n=1}^{k}{q}_{n}/rate$, where this rate is the current **q/w** and also the minimum **q/w** among the k people.

##### Code

```py
class Solution:
    def mincostToHireWorkers(self, quality: List[int], wage: List[int], K: int) -> float:
        effs = [(q / w, q) for q, w in zip(quality, wage)]
        effs.sort(key=lambda a: -a[0])
        ans = float('inf')
        h = []
        total = 0
        for rate, q in effs:
            heapq.heappush(h, -q)
            total += q
            if len(h) > K:
                total += heapq.heappop(h)
            if len(h) == K:
                ans = min(ans, total / rate)
        return ans
```

(Code 1.3.3)

### Technique Two - Multi-way Merge

This technique was actually mentioned before when discussing **Super Ugly Number**, but it wasn't given a **specific name**.

Actually, calling it multi-pointer optimization might be more appropriate, but this name is too plain and easily confused with two-pointer techniques. Therefore, I gave it a unique name - **Multi-way Merge**.

- "Multi-way" is reflected in having multiple candidate paths. In the code, we can represent these with multiple pointers.
- "Merge" is reflected in the fact that the result could be the longest or shortest among several candidates, or perhaps the k-th one, etc. Therefore, we need to compare the results of multiple paths and, based on the problem statement, discard or select one or more paths.

This description is quite abstract, so let's deepen the understanding with a few examples.

Here, I have carefully prepared four hard-level problems for you. Mastering this technique will allow you to joyfully solve these four problems.

#### 1439. Find the Kth Smallest Sum of a Matrix With Sorted Rows

##### Problem Description

```py
You are given an m * n matrix mat, and an integer k. Each row of the matrix is sorted in non-decreasing order.

You can select 1 element from each row to form an array. Return the k-th smallest array sum among all possible arrays.

Example 1:

Input: mat = [[1,3,11],[2,4,6]], k = 5
Output: 7
Explanation: Selecting one element from each row, the first k smallest sums of arrays are:
[1,2], [1,4], [3,2], [3,4], [1,6]. The 5th smallest sum among them is 7.
Example 2:

Input: mat = [[1,3,11],[2,4,6]], k = 9
Output: 17
Example 3:

Input: mat = [[1,10,10],[1,4,5],[2,3,6]], k = 7
Output: 9
Explanation: Selecting one element from each row, the first k smallest sums of arrays are:
[1,1,2], [1,1,3], [1,4,2], [1,4,3], [1,1,6], [1,5,2], [1,5,3]. The 7th smallest sum among them is 9.
Example 4:

Input: mat = [[1,1,10],[2,2,9]], k = 7
Output: 12

Constraints:

m == mat.length
n == mat.length[i]
1 <= m, n <= 40
1 <= k <= min(200, n ^ m)
1 <= mat[i][j] <= 5000
mat[i] is a non-decreasing array
```

#### Idea

This problem essentially gives you m one-dimensional arrays of equal length, and asks you to select one number from each of these m arrays, i.e., a total of m numbers, to find the sum that is the k-th smallest among **all possible selections**.

![](https://p.ipic.vip/xi03t7.jpg)

A naive idea is to use multiple pointers for the solution. For this problem, it would mean using m pointers, each pointing to one of the m one-dimensional arrays, where the position of the pointer indicates the current selection from that array.

Take the example from the problem `mat = [[1,3,11],[2,4,6]], k = 5.`

- First, initialize two pointers p1 and p2, each pointing to the beginning of the two one-dimensional arrays, which means initializing them to 0 in the code.
- At this point, the sum of the numbers pointed to by the two pointers is 1 + 2 = 3, which is the 1st smallest sum.
- Next, we move one of the pointers. Now, we can move either p1 or p2.
- The 2nd smallest sum will be the smaller of the two sums obtained by moving p1 and moving p2. Here, moving either p1 or p2 both result in 5, which means the 2nd and 3rd smallest sums are both 5.

We now have two scenarios (note the bold position, which represents the position of the pointer):

1. [1,**3**,11],[**2**,4,6] with a sum of 5
2. [**1**,3,11],[2,**4**,6] with a sum of 5

Next, these two scenarios should **proceed together.**

For scenario 1, moving next yields two possibilities.

1. [1,3,**11**],[**2**,4,6] Sum to 13
2. [1,**3**,11],[2,**4**,6] Sum to 7

For scenario 2, moving next also yields two possibilities.

1. [1,**3**,11],[2,**4**,6] Sum to 7
2. [**1**,3,11],[2,4,**6**] Sum to 7

By comparing these four cases, we conclude: the 4th, 5th, and 6th smallest sums are all 7. However, the 7th smallest sum might not necessarily be 13. The reason is similar to above; the 7th smallest might be hidden in the new scenarios after the split at 7. In fact, it is indeed the case. Therefore, we need to continue with the above logic.

Furthermore, we can extend the above approach to the general case.

As mentioned, the problem requires finding the k-th smallest sum, and the smallest sum is easy to determine - it's the sum of the first items of all the one-dimensional arrays. We also found that from the smallest sum, we can deduce the 2nd smallest by moving one of the pointers, which creates n scenarios in total, where n is the length of the one-dimensional arrays. The 2nd smallest sum is among these n scenarios, and the selection is made by choosing the smallest sum among them. The subsequent scenarios are similar. It's not hard to see that each split changes the extreme values, indicating a dynamic search for extreme values, which makes using a heap a good choice.

So how should the code be written?

As mentioned above, we first need to initialize m pointers and set them to 0. Corresponding pseudocode:

```py
# Initialize the heap
h = []
# sum(vec[0] for vec in mat) is the sum of the first items in m one-dimensional arrays
# [0] * m initializes an array of length m filled entirely with 0s.
# We assemble the above two pieces of information into a tuple cur for convenience
cur = (sum(vec[0] for vec in mat), [0] * m)
# Push it onto the heap
heapq.heappush(h, cur)
```
Next, we move one pointer each time, thereby branching out into a new direction. Each time we pop the smallest element from the heap, and after k pops, we get the k-th smallest element. Pseudocode:

```py
for 1 to K:
    # acc is the current sum, pointers represents the state of the pointers.
    acc, pointers = heapq.heappop(h)
    # Each time, we crudely move one pointer in the pointer array. Each movement of a pointer results in one branch, with a total of n possible movements, where n is the length of the one-dimensional array.
    for i, pointer in enumerate(pointers):
        # If pointer == len(mat[0]) - 1, it means it has reached the end and can't be moved
        if pointer != len(mat[0]) - 1:
            # The next two lines mean to update the pointer at pointers[i] to pointers[i] + 1
            new_pointers = pointers.copy()
            new_pointers[i] += 1
            # Push the updated acc and pointer array back into the heap
            heapq.heappush(h, (acc + mat[i][pointer + 1] - mat[i][pointer], new_pointers))
```

This is the core code for a **Multi-way Merge** problem, and it's important to remember it.

> The code may seem lengthy, but it's actually only seven lines if you remove the comments.

The above pseudocode has a potential issue. For example, consider two one-dimensional arrays with pointers both initialized to 0. If we move the pointer of the first array first and then the pointer of the second array second, the pointers array becomes [1, 1], meaning all pointers are pointing to the elements at index 1. Conversely, if we move the pointer of the second array first and then the pointer of the first array, the pointers array is still [1, 1]. This is actually the same scenario, and if not controlled, it will be calculated twice, leading to errors.

One possible solution is to use a hashset to record all pointer states, thus avoiding the same pointer configuration being calculated multiple times. To do this, we need to make a slight adjustment in how we use the pointers array, namely, using tuples instead of arrays. The reason is that arrays cannot be hashed directly. For more details, please refer to the code section.

The problems involving **Multi-way Merge** typically have similar thought processes and codes. To better understand the subsequent problems, it's crucial to grasp this problem thoroughly, as we will not analyze subsequent problems in such detail.

```py
class Solution:
    def kthSmallest(self, mat, k: int) -> int:
        h = []
        cur = (sum(vec[0] for vec in mat), tuple([0] * len(mat)))
        heapq.heappush(h, cur)
        seen = set(cur)

        for _ in range(k):
            acc, pointers = heapq.heappop(h)
            for i, pointer in enumerate(pointers):
                if pointer != len(mat[0]) - 1:
                    t = list(pointers)
                    t[i] = pointer + 1
                    tt = tuple(t)
                    if tt not in seen:
                        seen.add(tt)
                        heapq.heappush(h, (acc + mat[i][pointer + 1] - mat[i][pointer], tt))
        return acc
```
(Section 1.3.4)

#### 719. Find the K-th Smallest Pair Distance

##### Problem Description
```py
Given an integer array, return the k-th smallest distance among all the pairs. The distance between a pair (A, B) is defined as the absolute difference between A and B.

Example 1:

Input:
nums = [1,3,1]
k = 1
Output: 0
Explanation:
All the pairs are as follows:
(1,3) -> 2
(1,1) -> 0
(3,1) -> 2
Therefore, the 1st smallest pair distance is (1,1), with a distance of 0.
Hints:

2 <= len(nums) <= 10000.
0 <= nums[i] < 1000000.
1 <= k <= len(nums) * (len(nums) - 1) / 2.
```
#### Idea
It's evident that there could be a total of $C_n^2$, or $n\times(n-1)\div2$, possible pairs.

Hence, we could use two loops to find all pairs and sort them in ascending order, then take the k-th element.

In fact, we can use the fixed heap technique, maintaining a max heap of size k, so that the top element of the heap is the k-th smallest. This has already been discussed in the section about fixed heaps and won't be elaborated on here.

```py
class Solution:
    def smallestDistancePair(self, nums: List[int], k: int) -> int:
        h = []
        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                a, b = nums[i], nums[j]
                # Keep the heap size no larger than k
                if len(h) == k and -abs(a - b) > h[0]:
                    heapq.heappop(h)
                if len(h) < k:
                    heapq.heappush(h, -abs(a - b))

        return -h[0]
```
(Section 1.3.5)

However, this optimization is not very significant because the bottleneck of the algorithm lies in the $N^2$ part of the enumeration. We should try to optimize this aspect.

If we sort the pairs, the smallest pair distance will definitely be among nums[i] - nums[i - 1], where i is an integer from 1 to n, depending on which one is smaller. We can then use the multi-way merge approach mentioned above to solve it.

If the difference nums[i] - nums[i - 1] is the smallest, then the 2nd smallest will definitely be among the n - 1 remaining situations and the new situation resulting from the split of nums[i] - nums[i - 1]. The method of splitting is similar to the above; we only need to move the pointer at i to i + 1. Here, the length of the pointers array is fixed at 2, unlike the m in the previous problem. Here, I named the two pointers as fr and to, representing from and to, respectively.

##### Code

```py
class Solution(object):
    def smallestDistancePair(self, nums, k):
        nums.sort()
        # n candidate answers
        h = [(nums[i+1] - nums[i], i, i+1) for i in range(len(nums) - 1)]
        heapq.heapify(h)

        for _ in range(k):
            diff, fr, to = heapq.heappop(h)
            if to + 1 < len(nums):
                heapq.heappush((nums[to + 1] - nums[fr], fr, to + 1))

        return diff

```
(Section 1.3.6)

Since the time complexity is related to k, and k could potentially reach the magnitude of $N^2$, this method might also time out. **However, this proves the correctness of this approach, and it might be useful if the problem is slightly modified.**

This problem can be solved through binary search, which deviates a bit from the heap topic, so I'll only briefly mention it here.

When looking for the k-th smallest number, heaps and binary search often come to mind. Binary search is considered because finding the k-th smallest essentially means finding a number that has k - 1 numbers not greater than itself. This problem often satisfies monotonicity, therefore binary search can be used to solve it.

For this problem, the largest pair difference is the maximum value of the array minus the minimum value, let's call it max_diff. We can ask questions like:

- How many pairs have a difference less than max_diff?
- How many pairs have a difference less than max_diff - 1?
- How many pairs have a difference less than max_diff - 2?
- How many pairs have a difference less than max_diff - 3?
- How many pairs have a difference less than max_diff - 4?
...

We know that the answers to these questions are not strictly decreasing, so binary search should be considered. We keep asking questions until we find that there are **k - 1 pairs with a difference less than x**. However, this method has issues. The reasons are two-fold:

1. There might be more than one number that has k - 1 pairs less than it.
2. We can't be sure that a number with k - 1 pairs less than it definitely exists. For example, if the pair differences are [1,1,1,1,2], and you are asked to find the 3rd largest, then a number with exactly two pairs less than it does not exist.

Our approach can be adjusted to find less than or equal to x with k pairs, and then we can use the leftmost template of binary search to solve it. For more about the leftmost template, you can refer to my [binary search topic](https://github.com/azl397985856/leetcode/blob/master/91/binary-search.md)

##### Code:
```py
class Solution:
    def smallestDistancePair(self, A: List[int], K: int) -> int:
        A.sort()
        l, r = 0, A[-1] - A[0]

        def count_ngt(mid):
            slow = 0
            ans = 0
            for fast in range(len(A)):
                while A[fast] - A[slow] > mid:
                    slow += 1
                ans += fast - slow
            return ans

        while l <= r:
            mid = (l + r) // 2
            if count_ngt(mid) >= K:
                r = mid - 1
            else:
                l = mid + 1
        return l
```

(Code 1.3.7)

#### 632. Smallest Range Covering Elements from K Lists

##### Problen Description

```
You have k non-decreasing sorted lists of integers. Find the smallest interval such that each of the k lists contains at least one number in this interval.

We define an interval [a,b] as smaller than [c,d] if b-a < d-c or when b-a == d-c if a < c.

Example 1:

Input: nums = [[4,10,15,24,26], [0,9,12,20], [5,18,22,30]]
Output: [20,24]
Explanation:
List 1: [4, 10, 15, 24, 26], 24 is in the interval [20,24].
List 2: [0, 9, 12, 20], 20 is in the interval [20,24].
List 3: [5, 18, 22, 30], 22 is in the interval [20,24].

Example 2:

Input: nums = [[1,2,3],[1,2,3],[1,2,3]]
Output: [1,1]

Example 3:

Input: nums = [[10,10],[11,11]]
Output: [10,11]

Example 4:

Input: nums = [[10],[11]]
Output: [10,11]

Example 5:

Input: nums = [[1],[2],[3],[4],[5],[6],[7]]
Output: [1,7]

Constraints:

nums.length == k
1 <= k <= 3500
1 <= nums[i].length <= 50
-105 <= nums[i][j] <= 105
nums[i] are sorted in non-decreasing order

```

##### Thought Process

This problem essentially involves **selecting one number from each of m one-dimensional arrays to form a new array A, aiming to minimize the difference (diff) between the maximum and minimum values in A.**

This problem is somewhat similar to the previous one, with a slight difference. Here, we deal with a matrix, whereas the previous problem was about a one-dimensional array. However, we can treat the two-dimensional matrix as a one-dimensional array, allowing us to apply the same approach as before.

The key idea from the previous problems is that the smallest diff will always occur between adjacent elements after sorting. In this case, we cannot sort the two-dimensional array directly, and even if we did, it would be challenging to determine the sorting principle.

We can still use the approach from the previous two problems, which specifically involves using **a min-heap to get the smallest value in the heap and using a variable to record the maximum value in the heap.** This way, we know the diff, and each time we update a pointer, we generate a new diff. We repeat this process and maintain the global minimum diff.

The prerequisite for this algorithm is that all k lists are sorted in ascending order. The reasoning for requiring ascending order is the same as in the previous problem. With sorted arrays, we can maintain a pointer for each list, which allows us to use the aforementioned approach to solve the problem.

Taking nums = [[1,2,3],[1,2,3],[1,2,3]] from the problem as an example:

- [1,2,3]
- [1,2,3]
- [1,2,3]

First, we select the minimum value from all rows, which is [1,1,1]. Here, the diff is 0, the global maximum is 1, and the minimum is also 1. Next, we continue to look for a "backup", to see if there's a better option available.

The next backup could result from situation 1:

- [**1**,2,3]
- [**1**,2,3]
- [1,**2**,3] moving the pointer of this row from 0 to 1.

Or situation 2:

- [**1**,2,3]
- [1,**2**,3] moving the pointer of this row from 0 to 1.
- [**1**,2,3]

。。。

These situations lead to even more scenarios, similar to the previous problem, and need not be repeated here.

##### Code

```py
class Solution:
    def smallestRange(self, matrix: List[List[int]]) -> List[int]:
        l, r = -10**9, 10**9
        # Put the smallest element of each row into the heap, along with its row and column number, a total of n elements advancing together
        h = [(row[0], i, 0) for i, row in enumerate(matrix)]
        heapq.heapify(h)
        # Maintain the maximum value
        max_v = max(row[0] for row in matrix)

        while True:
            min_v, row, col = heapq.heappop(h)
            # max_v - min_v is the current maximum-minimum difference, r - l is the global maximum-minimum difference. We update the global result if the current is smaller
            if max_v - min_v < r - l:
                l, r = min_v, max_v
            if col == len(matrix[row]) - 1: return [l, r]
            # Update the pointer, move one position forward
            heapq.heappush(h, (matrix[row][col + 1], row, col + 1))
            max_v = max(max_v, matrix[row][col + 1])
```

(Code 1.3.8)

#### 1675: Minimum Deviation in Array

##### Problem Description

```
You are given an array nums consisting of n positive integers. You can perform any number of two types of operations on any element of the array:

你可以对数组的任意元素执行任意次数的两类操作：

1. If the element is even, divide it by 2.
    For example, if the array is [1,2,3,4], you can perform this operation on the last element to make it [1,2,3,2].
2. If the element is odd, multiply it by 2.
    For example, if the array is [1,2,3,4], you can perform this operation on the first element to make it [2,2,3,4].

The deviation of the array is the maximum difference between any two elements in the array.

**Goal**: Return the minimum deviation the array can have after performing some operations.

Examples:

Input: nums = [1,2,3,4]
Output: 1
Explanation: You can transform the array to [1,2,3,2], then to [2,2,3,2]. The deviation is 3 - 2 = 1.

Input: nums = [4,1,5,20,3]
Output: 3
Explanation: After two operations, you can transform the array to [4,2,5,5,3]. The deviation is 5 - 2 = 3.

Input: nums = [2,10,8]
Output: 3

Constraints:

n == nums.length
2 <= n <= 105
1 <= nums[i] <= 109

提示：

n == nums.length
2 <= n <= 105
1 <= nums[i] <= 109
```

##### Idea

The problem allows arbitrary numbers of operations on each element of the array, but in reality, the operations are limited:

- You can only perform the multiplication by 2 on odd numbers once, as it becomes even after the operation.
- You can divide even numbers by 2 several times until it becomes an odd number. This is also a finite number of operations.

Take [1,2,3,4] as an example. You can:

- Change 1 to 2 (or leave it as is)
- Change 2 to 1 (or leave it as is)
- Change 3 to 6 (or leave it as is)
- Change 4 to 2 or 1 (or leave it as is)

Represented graphically, this is:

[Convert one-dimensional array to a two-dimensional array](https://p.ipic.vip/9pcj1q.jpg)

Isn't this the same as selecting a number from each row of a two-dimensional array like [[1,2], [1,2], [3,6], [1,2,4]] and minimizing their difference? Isn't this exactly like the previous problem?

Here, I have encapsulated the solution of the previous problem into an API call, as detailed in the code.

##### Code

```py
class Solution:
    def smallestRange(self, matrix: List[List[int]]) -> List[int]:
        l, r = -10**9, 10**9
        # Put the smallest element of each row into the heap, along with its row and column number, a total of n elements advancing together
        h = [(row[0], i, 0) for i, row in enumerate(matrix)]
        heapq.heapify(h)
        # Maintain the maximum value
        max_v = max(row[0] for row in matrix)

        while True:
            min_v, row, col = heapq.heappop(h)
            # max_v - min_v is the current maximum-minimum difference, r - l is the global maximum-minimum difference. We update the global result if the current is smaller
            if max_v - min_v < r - l:
                l, r = min_v, max_v
            if col == len(matrix[row]) - 1: return [l, r]
            # Update the pointer, move one position forward
            heapq.heappush(h, (matrix[row][col + 1], row, col + 1))
            max_v = max(max_v, matrix[row][col + 1])

    def minimumDeviation(self, nums: List[int]) -> int:
        matrix = [[] for _ in range(len(nums))]
        for i, num in enumerate(nums):
            if num & 1 == 1:
                matrix[i] += [num, num * 2]
            else:
                temp = []
                while num and num & 1 == 0:
                    temp += [num]
                    num //= 2
                temp += [num]
                matrix[i] += temp[::-1]
        a, b = self.smallestRange(matrix)
        return b - a
```

(Code 1.3.9)

### Technique Three - Hindsight is 20/20

![](https://p.ipic.vip/aqqg1v.jpg)

This technique refers to the fact that when we traverse from left to right, we do not know what is on the right until we get there.

If you want to know what's on the right, a simple way is to traverse twice: record the data during the first traversal, and use the recorded data in the second traversal. This is the most commonly used method. However, sometimes we can backtrack after reaching a certain element, allowing us to collect data while traversing and complete the process in a single traversal. Specifically, it means collecting all data from left to right, and when needed, picking one from the collection. If we always need to take the maximum or minimum value and these extreme values change, **using a heap can speed up the process**. Intuitively, it's like using a time machine to go back to the past, achieving the effect of being wise after the event.

You might not understand what this means right now. No worries, let's clarify this through a few examples. After you have seen these examples, look back at this statement.

#### 871. Minimum Number of Refueling Stops

##### Problem Description

```
A car travels from a starting position to a destination which is target miles east of the starting position.

There are gas stations along the way. Each station[i] represents a gas station; it is located station[i][0] miles east of the starting position and has station[i][1] gallons of gas.

Assume the car's fuel tank is unlimited, and initially, it has startFuel gallons of fuel. It uses up 1 gallon of fuel per mile.

When the car reaches a gas station, it may stop to refuel, transferring all the gas from the station to the car.

What is the minimum number of refueling stops the car must make to reach its destination? If it is impossible to reach the destination, return -1.

Note: If the car reaches a gas station with 0 remaining fuel, it can still refuel there. If the car reaches the destination with 0 remaining fuel, it is still considered to have reached the destination.

 

Example 1:

Input: target = 1, startFuel = 1, stations = []
Output: 0
Explanation: We can reach the destination without refueling.

Example 2:

Input: target = 100, startFuel = 1, stations = [[10,100]]
Output: -1
Explanation: We cannot reach the destination, not even the first gas station.

Example 3:

Input: target = 100, startFuel = 10, stations = [[10,60],[20,30],[30,30],[60,40]]
Output: 2
Explanation:
We start with 10 gallons of fuel.
We drive to the gas station at 10 miles, using up 10 gallons of fuel. Refuel from 0 to 60 gallons.
Then, we drive from the gas station at 10 miles to the one at 60 miles (using up 50 gallons),
and refuel from 10 to 50 gallons. Then we drive to the destination.
We stop at two gas stations along the way, so we return 2.
 

Constraints:

1 <= target, startFuel, stations[i][1] <= 10^9
0 <= stations.length <= 500
0 < stations[0][0] < stations[1][0] < ... < stations[stations.length-1][0] < target

```

##### Idea

To achieve the **minimum number of refueling stops**, we naturally want to avoid refueling unless absolutely necessary. So, **when is it essential to refuel? The answer should be when you can't reach the next destination without refueling.**

The pseudocode description is as follows:

```py
cur = startFuel # Starting with startFuel gallons of fuel
last = 0 # The position of the last station
for i, fuel in stations:
    cur -= i - last # The fuel consumption between two stations is their distance, i.e., i - last
    if cur < 0:
        # We must have refueled before this point, otherwise we can't reach here
        # But at which station should we refuel?
        # Intuition tells us to greedily choose the station i that offers the most fuel, and if adding fuel from i still makes cur < 0, continue adding from the second-largest station j until no more fuel can be added or cur > 0
```

As mentioned above, we should choose the station i that offers the most fuel, and if that's not enough, continue with the second-largest station. This dynamic extreme value scenario is very suitable for using a heap.

Specifically, the process is:

- Add the fuel amount of each station to the heap as you pass by.
- Drive as far as possible, continuing as long as the fuel is not below 0.
- If the fuel level falls below 0, take the largest amount of fuel from the heap and add it to the fuel tank. Repeat this process if the fuel level is still below 0.
- If, after refueling, the fuel level is above 0, continue driving and repeat the steps above. Otherwise, return -1, indicating that it is impossible to reach the destination.

How does this algorithm embody the concept of "Hindsight is 20/20"? Imagine yourself in the scenario of the problem. Think of yourself as driving a car, with your goal being the requirement of the problem: **minimum number of refueling stops**. When you arrive at a station, you don't know if your fuel will be enough to reach the next station, and even if it's not enough, maybe it would have been better to refuel at the previous station. So **in reality** you can never know **whether you should refuel at the current station or not** because there's too little information.

![](https://p.ipic.vip/tygyyh.jpg)

What would I do in this situation? If I were driving, I would have to refuel every time. If I still can't reach the destination, then it's definitely impossible to reach it. But if this allows me to reach the destination, I can then say, "If we had refueled at that station, and chose not to refuel at this one, we could have reached the destination with the minimum number of refuels." Why didn't you say that earlier? Isn't this exactly hindsight?

This "hindsight" is reflected in **the fact that we only think about refueling at a previous station when we run out of fuel.**

So, the essence of this "Hindsight is 20/20" is solving the problem where you cannot get the best solution with the current information, and you must have all the information to backtrack. For this problem, we can first traverse through the stations, then record each station's fuel amount in an array. Every time we "foresee" that we can't reach the next station, we take the largest amount from this array... Based on this, we can consider using a heap to optimize the process of finding the extreme value, instead of using an array.

##### Code

```py
class Solution:
    def minRefuelStops(self, target: int, startFuel: int, stations: List[List[int]]) -> int:
        stations += [(target, 0)]
        cur = startFuel
        ans = 0

        h = []
        last = 0
        for i, fuel in stations:
            cur -= i - last
            while cur < 0 and h:
                cur -= heapq.heappop(h)
                ans += 1
            if cur < 0:
                return -1
            heapq.heappush(h, -fuel)

            last = i
        return ans
```

(Code 1.3.10)

#### 1488. Avoid Flood in The City

##### Problem Description

```
Your country has an infinite number of lakes, all initially empty. When it rains over the n-th lake, if the n-th lake is empty, it will fill up with water; otherwise, the lake will flood. Your goal is to avoid flooding in any of the lakes.

Given an integer array rains where:
- rains[i] > 0 indicates that on day i, it will rain over the rains[i]-th lake.
- rains[i] == 0 indicates that on day i, no lakes will receive rain, and you can choose to drain one lake of water.

Return an array ans that satisfies:
- ans.length == rains.length
- If rains[i] > 0, then ans[i] == -1.
- If rains[i] == 0, ans[i] is the lake you choose to drain on day i.
- If there are multiple valid solutions, return any of them. If it’s impossible to avoid flood, return an empty array.

Note that if you choose to drain a full lake, it becomes empty. But if you choose to drain an empty lake, nothing happens (see example 4).

 

Example 1:

Input: rains = [1,2,3,4]
Output: [-1,-1,-1,-1]
Explanation: After the first day, the full lakes include [1]
After the second day, the full lakes include [1,2]
After the third day, the full lakes include [1,2,3]
After the fourth day, the full lakes include [1,2,3,4]
There’s no day when you can drain any lake, and no lakes will flood.
Example 2:

Input: rains = [1,2,0,0,2,1]
Output: [-1,-1,2,1,-1,-1]
Explanation: After the first day, the full lakes include [1]
After the second day, the full lakes include [1,2]
On the third day, we drain lake 2. So the remaining full lakes include [1]
On the fourth day, we drain lake 1. So temporarily there are no full lakes.
After the fifth day, the full lakes include [2].
After the sixth day, the full lakes include [1,2].
It can be seen that in this scheme, no flood will occur. [-1,-1,1,2,-1,-1] is also another feasible scheme with no flood.
Example 3:

Input: rains = [1,2,0,1,2]
Output: []
Explanation: After the second day, the full lakes include [1,2]. We can drain a lake on the third day.
But after the third day, lakes 1 and 2 will both receive rain again, so no matter which lake we drain on the third day, the other lake will flood.
Example 4:

Input: rains = [69,0,0,0,69]
Output: [-1,69,1,1,-1]
Explanation: Any form like [-1,69,x,y,-1], [-1,x,69,y,-1] or [-1,x,y,69,-1] where 1 <= x,y <= 10^9 is a feasible solution.
Example 5:

Input: rains = [10,20,20]
Output: []
Explanation: Since lake 20 will receive rain for 2 consecutive days, there’s no way to prevent the flood.
 

Constraints:

1 <= rains.length <= 10^5
0 <= rains[i] <= 10^9

```

##### Idea

If using **"Hindsight is 20/20**" to describe the above problem seems a bit far-fetched, then the next two problems are very suitable.

The problem states we can drain a lake when it's not raining. If multiple lakes are full, which one should we drain? Obviously, it should be the lake that is closest to being flooded. However, in reality, we can never know in advance which lake will receive rain on which day, not even with weather forecasts, as they are not 100% reliable.

But code can do this. We can first traverse the rain array to know which lake receives rain on which day. With this information, we can then use hindsight.

“Today is a good day, I have opened my third eye, and tomorrow Lake 2 will be flooded, so let's drain it today, otherwise, it will overflow”

![](https://p.ipic.vip/ztgz23.jpg)

Like the previous problem, we can also simulate each day's changes without first traversing the rain array. Even if the current day is sunny, we do not drain any lakes. During the simulation, we record sunny days and when a flood is imminent, we consider which sunny day should be used to drain which lake. Thus, this "Hindsight is 20/20" is manifested in waiting until a flood is imminent to consider what measures should have been taken on a previous day.

Algorithm：

- Traverse `rain`, simulating each day's changes.
- If the current `rain` is 0, it means it's a sunny day, and we don't drain any lakes. But we record the current day in the `sunny` array.
- If `rain` is greater than 0, it indicates a lake is receiving rain. We check if this lake is about to flood. Essentially, we see if the lake had water before the rain. This suggests using a data structure lakes to record the status of each lake, where we can use 0 to indicate no water and 1 for water. So when `lake` i receives rain and `lakes[i] = 1`, a flood will occur.
- If the current lake is about to flood, we find a sunny day from the `sunny` array to drain it. This prevents it from flooding, and we just need to maintain `lakes[i] = 1` thereafter.

This problem does not use a heap intentionally. The reason for this is to make it clear that "Hindsight is 20/20" is not exclusive to heaps. In fact, it's a common algorithmic thought process, similar to traversing from back to front. However, in many "Hindsight is 20/20" scenarios, we need to dynamically find the maximum and minimum values, and this is when we should consider using a heap. This goes back to the "central idea" at the beginning of the article, so it's important to use these techniques flexibly and not rigidly.

The next problem is a true "Hindsight is 20/20" + "Heap Optimization" scenario.

##### 代码

```py
class Solution:
    def avoidFlood(self, rains: List[int]) -> List[int]:
        ans = [1] * len(rains)
        lakes = collections.defaultdict(int)
        sunny = []

        for i, rain in enumerate(rains):
            if rain > 0:
                ans[i] = -1
                if lakes[rain - 1] == 1:
                    if 0 == len(sunny):
                        return []
                    ans[sunny.pop()] = rain
                lakes[rain - 1] = 1
            else:
                sunny.append(i)
        return ans
```

(Code 1.3.11)

The code above has an issue. The problem lies in the implementation of the algorithm's part where **if the current lake is about to flood, then find a sunny day from the sunny array to drain it, preventing the flood**. The fundamental premise for choosing a sunny day to drain the lake from the sunny array is that **there must be water in the lake on the sunny day to drain**. If the lake is empty on a sunny day, draining it is not feasible. This suggests that our lakes array should not store 0 and 1, but rather the day on which a flood occurs. This turns the problem into **finding an item in sunny with a date greater than lakes[rain-1]** and then removing it from the sunny array. Since the sunny array is ordered, we can use binary search for this purpose.

> However, since we need to delete items from the `sunny` array, the time complexity will not be reduced by using binary search.


The correct code should be:

```py
class Solution:
    def avoidFlood(self, rains: List[int]) -> List[int]:
        ans = [1] * len(rains)
        lakes = {}
        sunny = []

        for i, rain in enumerate(rains):
            if rain > 0:
                ans[i] = -1
                if rain - 1 in lakes:
                    j = bisect.bisect_left(sunny, lakes[rain - 1])
                    if j == len(sunny):
                        return []
                    ans[sunny.pop(j)] = rain
                lakes[rain - 1] = i
            else:
                sunny.append(i)
        return ans
```

#### 1642. 可以到达的最远建筑

##### 题目描述

```
给你一个整数数组 heights ，表示建筑物的高度。另有一些砖块 bricks 和梯子 ladders 。

你从建筑物 0 开始旅程，不断向后面的建筑物移动，期间可能会用到砖块或梯子。

当从建筑物 i 移动到建筑物 i+1（下标 从 0 开始 ）时：

如果当前建筑物的高度 大于或等于 下一建筑物的高度，则不需要梯子或砖块
如果当前建筑的高度 小于 下一个建筑的高度，您可以使用 一架梯子 或 (h[i+1] - h[i]) 个砖块
如果以最佳方式使用给定的梯子和砖块，返回你可以到达的最远建筑物的下标（下标 从 0 开始 ）。
```

![](https://p.ipic.vip/r12e0t.gif)

```

示例 1：


输入：heights = [4,2,7,6,9,14,12], bricks = 5, ladders = 1
输出：4
解释：从建筑物 0 出发，你可以按此方案完成旅程：
- 不使用砖块或梯子到达建筑物 1 ，因为 4 >= 2
- 使用 5 个砖块到达建筑物 2 。你必须使用砖块或梯子，因为 2 < 7
- 不使用砖块或梯子到达建筑物 3 ，因为 7 >= 6
- 使用唯一的梯子到达建筑物 4 。你必须使用砖块或梯子，因为 6 < 9
无法越过建筑物 4 ，因为没有更多砖块或梯子。
示例 2：

输入：heights = [4,12,2,7,3,18,20,3,19], bricks = 10, ladders = 2
输出：7
示例 3：

输入：heights = [14,3,19,3], bricks = 17, ladders = 0
输出：3
 

提示：

1 <= heights.length <= 105
1 <= heights[i] <= 106
0 <= bricks <= 109
0 <= ladders <= heights.length

```

##### 思路

我们可以将梯子看出是无限的砖块，只不过只能使用一次，我们当然希望能将好梯用在刀刃上。和上面一样，如果是现实生活，我们是无法知道啥时候用梯子好，啥时候用砖头好的。

没关系，我们继续使用事后诸葛亮法，一次遍历就可完成。和前面的思路类似，那就是我无脑用梯子，等梯子不够用了，我们就要开始事后诸葛亮了，**要是前面用砖头就好了**。那什么时候用砖头就好了呢？很明显就是当初用梯子的时候高度差，比现在的高度差小。

直白点就是当初我用梯子爬了个 5 米的墙，现在这里有个十米的墙，我没梯子了，只能用 10 个砖头了。要是之前用 5 个砖头，现在不就可以用一个梯子，从而省下 5 个砖头了吗？

这提示我们将用前面用梯子跨越的建筑物高度差存起来，等到后面梯子用完了，我们将前面被用的梯子“兑换”成砖头继续用。以上面的例子来说，我们就可以先兑换 10 个砖头，然后将 5 个砖头用掉，也就是相当于增加了 5 个砖头。

如果前面多次使用了梯子，我们优先“兑换”哪次呢？显然是优先兑换**高度差**大的，这样兑换的砖头才最多。这提示每次都从之前存储的高度差中选最大的，并在“兑换”之后将其移除。这种**动态求极值**的场景用什么数据结构合适？当然是堆啦。

##### 代码

```py
class Solution:
    def furthestBuilding(self, heights: List[int], bricks: int, ladders: int) -> int:
        h = []
        for i in range(1, len(heights)):
            diff = heights[i] - heights[i - 1]
            if diff <= 0:
                continue
            if bricks < diff and ladders > 0:
                ladders -= 1
                if h and -h[0] > diff:
                    bricks -= heapq.heappop(h)
                else:
                    continue
            bricks -= diff
            if bricks < 0:
                return i - 1
            heapq.heappush(h, -diff)
        return len(heights) - 1
```

(代码 1.3.12)

## 四大应用

接下来是本文的最后一个部分《四大应用》，目的是通过这几个例子来帮助大家巩固前面的知识。

### 1. topK

求解 topK 是堆的一个很重要的功能。这个其实已经在前面的**固定堆**部分给大家介绍过了。

这里直接引用前面的话：

“其实求第 k 小的数最简单的思路是建立小顶堆，将所有的数先全部入堆，然后逐个出堆，一共出堆 k 次。最后一次出堆的就是第 k 小的数。然而，我们也可不先全部入堆，而是建立大顶堆（注意不是上面的小顶堆），并维持堆的大小为 k 个。如果新的数入堆之后堆的大小大于 k，则需要将堆顶的数和新的数进行比较，并将较大的移除。这样可以保证堆中的数是全体数字中最小的 k 个，而这最小的 k 个中最大的（即堆顶）不就是第 k 小的么？这也就是选择建立大顶堆，而不是小顶堆的原因。”

其实除了第 k 小的数，我们也可以将中间的数全部收集起来，这就可以求出最小的 **k 个数**。和上面第 k 小的数唯一不同的点在于需要收集 popp 出来的所有的数。

需要注意的是，有时候权重并不是原本数组值本身的大小，也可以是距离，出现频率等。

相关题目：

- [面试题 17.14. 最小 K 个数](https://leetcode-cn.com/problems/smallest-k-lcci/ "面试题 17.14. 最小K个数")
- [347. 前 K 个高频元素](https://leetcode-cn.com/problems/top-k-frequent-elements/ "347. 前 K 个高频元素")
- [973. 最接近原点的 K 个点](https://leetcode-cn.com/problems/k-closest-points-to-origin/ "973. 最接近原点的 K 个点")

力扣中有关第 k 的题目很多都是堆。除了堆之外，第 k 的题目其实还会有一些**找规律**的题目，对于这种题目则可以通过**分治+递归**的方式来解决，具体就不再这里展开了，感兴趣的可以和我留言讨论。

### 2. 带权最短距离

关于这点，其实我在前面部分也提到过了，只不过当时只是一带而过。原话是“不过 BFS 真的就没人用优先队列实现么？当然不是！比如带权图的最短路径问题，如果用队列做 BFS 那就需要优先队列才可以，因为路径之间是有**权重的差异**的，这不就是优先队列的设计初衷么。**使用优先队列的 BFS 实现典型的就是 dijkstra 算法**。”

DIJKSTRA 算法主要解决的是图中任意两点的最短距离。

算法的基本思想是贪心，每次都遍历所有邻居，并从中找到距离最小的，本质上是一种广度优先遍历。这里我们借助堆这种数据结构，使得可以在 $logN$ 的时间内找到 cost 最小的点，其中 N 为 堆的大小。

代码模板：

```py
def dijkstra(graph, start, end):
    # 堆里的数据都是 (cost, i) 的二元祖，其含义是“从 start 走到 i 的距离是 cost”。
    heap = [(0, start)]
    visited = set()
    while heap:
        (cost, u) = heapq.heappop(heap)
        if u in visited:
            continue
        visited.add(u)
        if u == end:
            return cost
        for v, c in graph[u]:
            if v in visited:
                continue
            next = cost + c
            heapq.heappush(heap, (next, v))
    return -1
```

(代码 1.4.1)

> 可以看出代码模板和 BFS 基本是类似的。如果你自己将堆的 key 设定为 steps 也可模拟实现 BFS，这个在前面已经讲过了，这里不再赘述。

比如一个图是这样的：

```
E -- 1 --> B -- 1 --> C -- 1 --> D -- 1 --> F
 \                                         /\
  \                                        ||
    -------- 2 ---------> G ------- 1 ------
```

我们使用邻接矩阵来构造：

```py
G = {
    "B": [["C", 1]],
    "C": [["D", 1]],
    "D": [["F", 1]],
    "E": [["B", 1], ["G", 2]],
    "F": [],
    "G": [["F", 1]],
}

shortDistance = dijkstra(G, "E", "C")
print(shortDistance)  # E -- 3 --> F -- 3 --> C == 6
```

会了这个算法模板， 你就可以去 AC [743. 网络延迟时间](https://leetcode-cn.com/problems/network-delay-time/ "743. 网络延迟时间") 了。

完整代码：

```py
class Solution:
    def dijkstra(self, graph, start, end):
        heap = [(0, start)]
        visited = set()
        while heap:
            (cost, u) = heapq.heappop(heap)
            if u in visited:
                continue
            visited.add(u)
            if u == end:
                return cost
            for v, c in graph[u]:
                if v in visited:
                    continue
                next = cost + c
                heapq.heappush(heap, (next, v))
        return -1
    def networkDelayTime(self, times: List[List[int]], N: int, K: int) -> int:
        graph = collections.defaultdict(list)
        for fr, to, w in times:
            graph[fr - 1].append((to - 1, w))
        ans = -1
        for to in range(N):
            # 调用封装好的 dijkstra 方法
            dist = self.dijkstra(graph, K - 1, to)
            if dist == -1: return -1
            ans = max(ans, dist)
        return ans
```

(代码 1.4.2)

你学会了么？

上面的算法并不是最优解，我只是为了体现**将 dijkstra 封装为 api 调用** 的思想。一个更好的做法是一次遍历记录所有的距离信息，而不是每次都重复计算。时间复杂度会大大降低。这在计算一个点到图中所有点的距离时有很大的意义。 为了实现这个目的，我们的算法会有什么样的调整？

> 提示：你可以使用一个 dist 哈希表记录开始点到每个点的最短距离来完成。想出来的话，可以用力扣 882 题去验证一下哦~

其实只需要做一个小的调整就可以了，由于调整很小，直接看代码会比较好。

代码：

```py
class Solution:
    def dijkstra(self, graph, start, end):
        heap = [(0, start)]  # cost from start node,end node
        dist = {}
        while heap:
            (cost, u) = heapq.heappop(heap)
            if u in dist:
                continue
            dist[u] = cost
            for v, c in graph[u]:
                if v in dist:
                    continue
                next = cost + c
                heapq.heappush(heap, (next, v))
        return dist
    def networkDelayTime(self, times: List[List[int]], N: int, K: int) -> int:
        graph = collections.defaultdict(list)
        for fr, to, w in times:
            graph[fr - 1].append((to - 1, w))
        ans = -1
        dist = self.dijkstra(graph, K - 1, to)
        return -1 if len(dist) != N else max(dist.values())
```

(代码 1.4.3)

可以看出我们只是将 visitd 替换成了 dist，其他不变。另外 dist 其实只是带了 key 的 visited，它这里也起到了 visitd 的作用。

如果你需要计算一个节点到其他所有节点的最短路径，可以使用一个 dist （一个 hashmap）来记录出发点到所有点的最短路径信息，而不是使用 visited （一个 hashset）。

类似的题目也不少， 我再举一个给大家 [787. K 站中转内最便宜的航班](https://leetcode-cn.com/problems/cheapest-flights-within-k-stops/ "787. K 站中转内最便宜的航班")。题目描述：

```
有 n 个城市通过 m 个航班连接。每个航班都从城市 u 开始，以价格 w 抵达 v。

现在给定所有的城市和航班，以及出发城市 src 和目的地 dst，你的任务是找到从 src 到 dst 最多经过 k 站中转的最便宜的价格。 如果没有这样的路线，则输出 -1。

 

示例 1：

输入:
n = 3, edges = [[0,1,100],[1,2,100],[0,2,500]]
src = 0, dst = 2, k = 1
输出: 200
解释:
城市航班图如下
```

![](https://p.ipic.vip/li3v94.jpg)

```


从城市 0 到城市 2 在 1 站中转以内的最便宜价格是 200，如图中红色所示。
示例 2：

输入:
n = 3, edges = [[0,1,100],[1,2,100],[0,2,500]]
src = 0, dst = 2, k = 0
输出: 500
解释:
城市航班图如下
```

![](https://p.ipic.vip/6nsi3i.jpg)

```

从城市 0 到城市 2 在 0 站中转以内的最便宜价格是 500，如图中蓝色所示。
 

提示：

n 范围是 [1, 100]，城市标签从 0 到 n - 1
航班数量范围是 [0, n * (n - 1) / 2]
每个航班的格式 (src, dst, price)
每个航班的价格范围是 [1, 10000]
k 范围是 [0, n - 1]
航班没有重复，且不存在自环

```

这道题和上面的没有本质不同， 我仍然将其封装成 API 来使用，具体看代码就行。

这道题唯一特别的点在于如果中转次数大于 k，也认为无法到达。这个其实很容易，我们只需要在堆中用元组来**多携带一个 steps**即可，这个 steps 就是 不带权 BFS 中的距离。如果 pop 出来 steps 大于 K，则认为非法，我们跳过继续处理即可。

```py
class Solution:
    # 改造一下，增加参数 K，堆多携带一个 steps 即可
    def dijkstra(self, graph, start, end, K):
        heap = [(0, start, 0)]
        visited = set()
        while heap:
            (cost, u, steps) = heapq.heappop(heap)
            if u in visited:
                continue
            visited.add((u, steps))
            if steps > K: continue
            if u == end:
                return cost
            for v, c in graph[u]:
                if (v, steps) in visited:
                    continue
                next = cost + c
                heapq.heappush(heap, (next, v, steps + 1))
        return -1
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, K: int) -> int:
        graph = collections.defaultdict(list)
        for fr, to, price in flights:
            graph[fr].append((to, price))
         # 调用封装好的 dijkstra 方法
        return self.dijkstra(graph, src, dst, K + 1)
```

(代码 1.4.4)

### 3. 因子分解

和上面两个应用一下，这个我在前面 《313. 超级丑数》部分也提到了。

回顾一下丑数的定义： **丑数就是质因数只包含 2, 3, 5 的正整数。** 因此丑数本质就是一个数经过**因子分解**之后只剩下 2，3，5 的整数，而不携带别的因子了。

关于丑数的题目有很多，大多数也可以从堆的角度考虑来解。只不过有时候因子个数有限，不使用堆也容易解决。比如：[264. 丑数 II](https://leetcode-cn.com/problems/ugly-number-ii/ "264. 丑数 II") 就可以使用三个指针来记录即可，这个技巧在前面也讲过了，不再赘述。

一些题目并不是丑数，但是却明确提到了类似**因子**的信息，并让你求第 k 大的 xx，这个时候优先考虑使用堆来解决。如果题目中夹杂一些其他信息，比如**有序**，则也可考虑二分法。具体使用哪种方法，要具体问题具体分析，不过在此之前大家要对这两种方法都足够熟悉才行。

### 4. 堆排序

前面的三种应用或多或少在前面都提到过。而**堆排序**却未曾在前面提到。

直接考察堆排序的题目几乎没有。但是面试却有可能会考察，另外学习堆排序对你理解分治等重要算法思维都有重要意义。个人感觉，堆排序，构造二叉树，构造线段树等算法都有很大的相似性，掌握一种，其他都可以触类旁通。

实际上，经过前面的堆的学习，我们可以封装一个堆排序，方法非常简单。

这里我放一个使用堆的 api 实现堆排序的简单的示例代码：

```py
h = [9,5,2,7]
heapq.heapify(h)
ans = []

while h:
    ans.append(heapq.heappop(h))
print(ans) # 2,5,7,9
```

明白了示例， 那封装成**通用堆排序**就不难了。

```py
def heap_sort(h):
    heapq.heapify(h)
    ans = []
    while h:
        ans.append(heapq.heappop(h))
    return ans

```

这个方法足够简单，如果你明白了前面堆的原理，让你手撸一个堆排序也不难。可是这种方法有个弊端，它不是**原位算法**，也就是说你必须使用额外的空间承接结果，空间复杂度为 $O(N)$。但是其实调用完堆排序的方法后，原有的数组内存可以被释放了，因此理论上来说空间也没浪费，只不过我们计算空间复杂度的时候取的是使用内存最多的时刻，因此使用原地算法毫无疑问更优秀。如果你实在觉得不爽这个实现，也可以采用原地的修改的方式。这倒也不难，只不过稍微改造一下前面的堆的实现即可，由于篇幅的限制，这里不多讲了。

## 总结

堆和队列有千丝万缕的联系。 很多题目我都是先思考使用堆来完成。然后发现每次入堆都是 + 1，而不会跳着更新，比如下一个是 + 2，+3 等等，因此使用队列来完成性能更好。 比如 [649. Dota2 参议院](https://leetcode-cn.com/problems/dota2-senate/) 和 [1654. 到家的最少跳跃次数](https://leetcode-cn.com/problems/minimum-jumps-to-reach-home/) 等。

堆的中心就一个，那就是**动态求极值**。

而求极值无非就是最大值或者最小值，这不难看出。如果求最大值，我们可以使用大顶堆，如果求最小值，可以用最小堆。而实际上，如果没有动态两个字，很多情况下没有必要使用堆。比如可以直接一次遍历找出最大的即可。而动态这个点不容易看出来，这正是题目的难点。这需要你先对问题进行分析， 分析出这道题**其实就是动态求极值**，那么使用堆来优化就应该被想到。

堆的实现有很多。比如基于链表的跳表，基于数组的二叉堆和基于红黑树的实现等。这里我们介绍了**两种主要实现** 并详细地讲述了二叉堆的实现，不仅是其实现简单，而且其在很多情况下表现都不错，推荐大家重点掌握二叉堆实现。

对于二叉堆的实现，**核心点就一点**，那就是始终维护堆的性质不变，具体是什么性质呢？那就是 **父节点的权值不大于儿子的权值（小顶堆）**。为了达到这个目的，我们需要在入堆和出堆的时候，使用上浮和下沉操作，并恰当地完成元素交换。具体来说就是上浮过程和比它大的父节点进行交换，下沉过程和两个子节点中较小的进行交换，当然前提是它有子节点且子节点比它小。

关于堆化我们并没有做详细分析。不过如果你理解了本文的入堆操作，这其实很容易。因此堆化本身就是一个不断入堆的过程，只不过**将时间上的离散的操作变成了一次性操作**而已。

另外我给大家介绍了三个堆的做题技巧，分别是：

- 固定堆，不仅可以解决第 k 问题，还可有效利用已经计算的结果，避免重复计算。
- 多路归并，本质就是一个暴力解法，和暴力递归没有本质区别。如果你将其转化为递归，也是一种不能记忆化的递归。因此更像是**回溯算法**。
- 事后小诸葛。有些信息，我们在当前没有办法获取，就可用一种数据结构存起来，方便之后”东窗事发“的时候查。这种数据解决可以是很多，常见的有哈希表和堆。你也可以将这个技巧看成是**事后后悔**，有的人比较能接受这种叫法，不过不管叫法如何，指的都是这个含义。

最后给大家介绍了四种应用，这四种应用除了堆排序，其他在前面或多或少都讲过，它们分别是：

- topK
- 带权最短路径
- 因子分解
- 堆排序

这四种应用实际上还是围绕了堆的一个中心**动态取极值**，这四种应用只不过是灵活使用了这个特点罢了。因此大家在做题的时候只要死记**动态求极值**即可。如果你能够分析出这道题和动态取极值有关，那么请务必考虑堆。接下来我们就要在脑子中过一下复杂度，对照一下题目数据范围就大概可以估算出是否可行啦。

大家对此有何看法，欢迎给我留言，我有时间都会一一查看回答。更多算法套路可以访问我的 LeetCode 题解仓库：https://github.com/azl397985856/leetcode 。目前已经 39K star 啦。大家也可以关注我的公众号《力扣加加》带你啃下算法这块硬骨头。

![二维码](https://p.ipic.vip/nj3yjo.jpg)






