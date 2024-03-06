# Backtracking

Backtracking is a technique in DFS. It uses the [trial and error](https://zh.wikipedia.org/wiki/%E8%AF%95%E9%94%99) approach to solve a problem step by step. During the process of solving the problem step by step, when it finds that the existing step-by-step answer cannot get an effective and correct answer, it will `cancel the calculation of the previous step or even several steps, and then try to find the answer to the problem again through other possible step-by-step answers.`

In popular terms, backtracking is an algorithm that turns back when it hits a dead end.

Backtracking can be abstracted into a tree structure, and it is a highly finite tree (N-ary tree). Backtracking solves the problem of finding subsets in a set, the size of the set is the number of forks of the tree, and the depth of the recursion constitutes the height of the tree.

Take the subset of array [1,2,3] as an example:

![](https://p.ipic.vip/g9vawf.jpg)

> The for loop is used to enumerate the division points. In fact, the interval dp division interval is a similar approach.

In the above figure, we will add to the result set at each node.

![](https://p.ipic.vip/1flyhe.jpg)

For the gray nodes above, adding to the result set results in [1].

![](https://p.ipic.vip/mj1skc.jpg)

This addition to the result set results in [1,2].

![](https://p.ipic.vip/y9t2mb.jpg)

Adding to the result set here results in [2,3], and so on. There are a total of six subsets: [1], [1,2], [1,2,3], [2], [2,3], and [3].

For permutation problems, the elements are added to the result set at leaf nodes, but this is a detail. Once you grasp the idea, learning the details will be much more efficient.

Now let's take a look at how to write specific code.

## Algorithm Flow
1. Construct a spatial tree.
2. Traverse the tree.
3. When encountering boundary conditions where it's not worth continuing the search, switch to another branch.
4. Upon reaching the target condition, output the result.

## Algorithm Template

Pseudo code：

```js
const visited = {}
function dfs(i) {
	if (specific conditions are met) {
		// Return the result or exit the search space
	}

	visited[i] = true // Mark the current state as searched
	dosomething(i) // Perform some operation on i
	for (next state j reachable from i) {
		if (!visited[j]) { // If state j has not been searched
			dfs(j)
		}
	}
	undo(i) // Undo i
}
```


```Python 
def dfs(i, visited):
    if specific_conditions_are_met:
        # Return the result or exit the search space
        return

    visited[i] = True  # Mark the current state as searched
    do_something(i)  # Perform some operation on i
    for j in next_states_reachable_from_i:
        if j not in visited:  # If state j has not been searched
            dfs(j, visited)
    undo(i)  # Undo i
```

## Pruning

Another aspect of backtracking problems is pruning. By appropriately pruning, we can effectively reduce time complexity. For example, I optimized the time complexity of Stone Game V from over 900 ms to just over 500 ms through pruning.

Pruning techniques vary for each problem, but a simple principle is to `avoid recursions that are fundamentally not the answer.`

For example: [842. Split Array into Fibonacci Sequence](https://leetcode.com/problems/split-array-into-fibonacci-sequence/)

Problem description：

```
Given a digit string S, such as S = "123456579", we can split it into a Fibonacci-like sequence [123, 456, 579].

Formally, a Fibonacci-like sequence is a list F of non-negative integers such that:

0 <= F[i] <= 2^31 - 1, (i.e., each integer fits in a 32-bit signed integer type.)
F.length >= 3;
and for all0 <= i < F.length - 2, F[i] + F[i+1] = F[i+2].
Additionally, for every block in the split string, each block's digits (except for possibly the first block) may not begin with 0, and each block must not contain leading zeroes. Except for the first block, each subsequent block's digits must not begin with 0.

Return any Fibonacci-like sequence split from S, or return [] if it cannot be done.



Example 1:

Input: "123456579"
Output: [123,456,579]
Example 2:

Input: "11235813"
Output: [1,1,2,3,5,8,13]
Example 3:

Input: "112358130"
Output: []
Explanation: The task is impossible.
Example 4:

Input: "0123"
Output: []
Explanation: Each block must not contain leading zeroes, so "01", "2", "3" is not valid.
Example 5:

Input: "1101111"
Output: [110, 1, 111]
Explanation: Output [11,0,11,11] would also be accepted.
```

You can still use the backtracking template directly to solve this problem. However, if appropriate pruning is not done, it's easy to exceed the time limit. Here, I perform four pruning operations, as seen in the code:

```py
class Solution:
    def splitIntoFibonacci(self, S: str) -> List[int]:
        def backtrack(start, path):
            # Pruning 1
            if len(path) > 2 and path[-1] != path[-2] + path[-3]:
                return []
            if start >= len(S):
                if len(path) > 2:
                    return path
                return []

            cur = 0
            ans = []
            # Enumerate splitting points
            for i in range(start, len(S)):
                # Pruning 2
                if i > start and S[start] == '0':
                    return []
                cur = cur * 10 + int(S[i])
                # Pruning 3
                if cur > 2**31 - 1:
                    return []
                path.append(cur)
                ans = backtrack(i + 1, path)
                # Pruning 4
                if len(ans) > 2:
                    return ans
                path.pop()
            return ans

        return backtrack(0, [])
```

The pruning process can be represented graphically as follows:

![](https://p.ipic.vip/bjh1zs.jpg)

**Pruning algorithms are a major focus of backtracking problems, which you should master.**

## Cartesian Product

For some backtracking problems, we can still use the Cartesian product method to store the results in the return value instead of the path. This avoids backtracking state, and since the results are in the return value, we can use memoization recursion to optimize it into a dynamic programming form.

Refer to problems like:

- [140. Word Break II](https://leetcode.com/problems/word-break-ii/)
- [401. Binary Watch](../problems/401.binary-watch.md)
- [816. Ambiguous Coordinates](https://github.com/azl397985856/leetcode/blob/master/problems/816.ambiguous-coordinates.md)

Unlike subset and permutation problems, the combinations in this type of problem follow a pattern, and we can use the Cartesian product formula to combine two or more subsets.

## Classic title

- [39. Combination Sum)(../problems/39.combination-sum.md)
- [40. Combination Sum II](../problems/40.combination-sum-ii.md)
- [46. Permutations](../problems/46.permutations.md)
- [47. Permutations II](../problems/47.permutations-ii.md)
- [52. N Queen II](../problems/52.N-Queens-II.md)
- [78. Subsets)(../problems/78.subsets.md)
- [90. Subsets II](../problems/90.subsets-ii.md)
- [113. Path sum II)(../problems/113.path-sum-ii.md)
- [131. Palindrome Partitioning](../problems/131.palindrome-partitioning.md)
- [1255. Maximum Score Words Formed by Letters](../problems/1255.maximum-score-words-formed-by-letters.md)

## Summary

The essence of backtracking is to enumerate all possibilities. It's important to note that since backtracking usually records the result set on the path of the backtracking tree, if the state is not undone after backtracking, the result may differ due to incorrect state after backtracking. Therefore, it's necessary to undo the state when bubbling up recursively from the bottom.

If you make a copy of the data each time you recurse, you don't need to undo the state, but the space complexity will increase relatively.
