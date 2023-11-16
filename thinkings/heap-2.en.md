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

-[I have almost finished swiping all the linked topics of Lixu, and I found these things. 。 。 ](https://lucifer.ren/blog/2020/11/08/linked-list/) 
-[After almost brushing all the tree questions of Li Buckle, I found these things. 。 。 ](https://lucifer.ren/blog/2020/11/23/tree/) 
-[After almost brushing all the piles of questions, I found these things. 。 。 (First bullet)](https://lucifer.ren/blog/2020/12/26/heap/)

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

##### Idea

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
##### Idea
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







