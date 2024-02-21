# Bit Operation

Here are four problems on bit manipulation that I want to share with you: 136 and 137, 260 and 645. All four problems are about bit manipulation, and if you want to practice bit manipulation, don't miss them!

## Introduction

Before we start, let's learn about XOR, which we will use later.

1. Properties of XOR

The result of XORing two numbers "a^b" is to perform the operation on each bit of the binary representations of a and b. The logic of the operation is that if the digits in the same bit are the same, the result is 0, and if they are different, the result is 1.


2. XOR rules

- Any number XORed with itself is `0`.

- Any number XORed with 0 is `itself`

3. XOR operation satisfies the commutative law, i.e：

`a ^ b ^ c = a ^ c ^ b`

OK, let's take a look at these three problems.

## 136. Single Number

The gist of the problem is that all numbers appear twice except for one, and we are asked to find the number that appears once. We can perform a full member XOR to get the result.

```python
class Solution:
  def singleNumber(self, nums: List[int]) -> int:
    single_number = 0
    for num in nums:
      single_number ^= num
    return single_number
```

**_Complexity analysis_**

-Time complexity:$O(N)$, where N is the length of the array. -Space complexity:$O(1)$

## 137. Single Number II

The gist of the problem is that all numbers appear three times except for one, and we are asked to find the number that appears once. Flexible use of bit manipulation is the key to this problem.

Python3:

```python
class Solution:
  def singleNumber(self, nums: List[int]) -> int:
    res = 0
    for i in range(32):
      cnt = 0 # Record how many 1s are in the current bit
      bit = 1 << i # Record the current bit to be operated on
      for num in nums:
        if num & bit != 0:
          cnt += 1
      if cnt % 3 != 0:
        # Not equal to 0 means that the unique number is 1 in this bit
        res |= bit

    return res - 2 ** 32 if res > 2 ** 31 - 1 else res
```

- Why does Python need to check the return value at the end?

If we don't do this, when the test case is [-2,-2,1,1,-3,1,-3,-3,-4,-2], it will output 4294967292. The reason for this is that Python is a dynamically typed language, and in this case it will treat the 1 in the sign position as a value, not as a sign "negative number". This is incorrect. The correct answer should be -4, the binary code of -4 is 1111...100, which becomes 2^32-4=4294967292, and the solution is to subtract 2 ** 32.

> The reason why this doesn't cause problems is because the range of the array specified by the problem does not exceed 2 ** 32

JavaScript:

```js
var singleNumber = function (nums) {
 let res = 0;
 for (let i = 0; i < 32; i++) {
  let cnt = 0;
  let bit = 1 << i;
  for (let j = 0; j < nums.length; j++) {
   if (nums[j] & bit) cnt++;
  }
  if (cnt % 3 != 0) res = res | bit;
 }
 return res;
};
```

**_Complexity analysis_**

-Time complexity:$O(N)$, where N is the length of the array. - Space complexity:$O(1)$

## 645. Set Mismatch

The idea is the same as the above "137. Single Number II". This problem does not limit the space complexity, so it is no problem to directly store it in a hashmap. Without further ado, let's take a look at a solution with a space complexity of $O(1)$.

Since the idea is basically the same as "137. Single Number II", I directly reused the code. The specific idea is to extract all the indexes of nums into an array idx, then the array composed of idx and nums is the input of singleNumbers, and its output is the two unique numbers.

However, we don't know which one is missing and which one is duplicated, so we need to traverse again to find out which one is missing and which one is duplicated.

```python
class Solution:
    def singleNumbers(self, nums: List[int]) -> List[int]:
        ret = 0  # The result of the XOR of all numbers
        a = 0
        b = 0
        for n in nums:
            ret ^= n
        # Find the first non-zero bit
        h = 1
        while(ret & h == 0):
            h <<= 1
        for n in nums:
            # Divide them into two groups based on whether this bit is 0
            if (h & n == 0):
                a ^= n
            else:
                b ^= n

        return [a, b]

    def findErrorNums(self, nums: List[int]) -> List[int]:
        nums = [0] + nums
        idx = []
        for i in range(len(nums)):
            idx.append(i)
        a, b = self.singleNumbers(nums + idx)
        for num in nums:
            if a == num:
                return [a, b]
        return [b, a]
```

**_Complexity analysis_**

-Time complexity:$O(N)$ - Space complexity:$O(1)$

## 260. Single Number III

The gist of the problem is that we are asked to find the two numbers that appear only once, while all others appear twice.

We perform a full XOR operation, and the result is the XOR result of the two unique numbers that appear only once.

We just talked about one of the properties of XOR, "any number XORed with itself is 0", so our idea is to see if we can divide these two unique numbers into two groups A and B.

The grouping needs to satisfy two conditions.

1. The two unique numbers are divided into different groups

2. The same numbers are divided into the same group

This way, the XOR of the data in each group will give us the two numbers.

The key question is how do we group?

Since the property of XOR is that if the same bit is the same, it is 0, and if it is different, it is 1. The result of XORing all numbers must not be 0, which means that at least one bit is 1.

We can take any one, and the basis for grouping is that if the bit you take is 0, it is divided into group 1, and if the bit is 1, it is divided into group 2.


Sure, here is the translation of the Chinese text you provided:

645. Set Mismatch

The idea is the same as the above 137. Single Number II. This problem does not limit the space complexity, so it is no problem to directly store it in a hashmap. Without further ado, let's take a look at a solution with a space complexity of O(1).

Since the idea is basically the same as 137. Single Number II, I directly reused the code. The specific idea is to extract all the indexes of nums into an array idx, then the array composed of idx and nums is the input of singleNumbers, and its output is the two unique numbers.

However, we don't know which one is missing and which one is duplicated, so we need to traverse again to find out which one is missing and which one is duplicated.

Python
class Solution:
    def singleNumbers(self, nums: List[int]) -> List[int]:
        ret = 0  # The result of the XOR of all numbers
        a = 0
        b = 0
        for n in nums:
            ret ^= n
        # Find the first non-zero bit
        h = 1
        while(ret & h == 0):
            h <<= 1
        for n in nums:
            # Divide them into two groups based on whether this bit is 0
            if (h & n == 0):
                a ^= n
            else:
                b ^= n

        return [a, b]

    def findErrorNums(self, nums: List[int]) -> List[int]:
        nums = [0] + nums
        idx = []
        for i in range(len(nums)):
            idx.append(i)
        a, b = self.singleNumbers(nums + idx)
        for num in nums:
            if a == num:
                return [a, b]
        return [b, a]

Hãy thận trọng khi sử dụng các đoạn mã.
Complexity Analysis

Time complexity: O(N)
Space complexity: O(1)
260. Single Number III

The gist of the problem is that we are asked to find the two numbers that appear only once, while all others appear twice.

We perform a full XOR operation, and the result is the XOR result of the two unique numbers that appear only once.

We just talked about one of the properties of XOR, any number XORed with itself is 0, so our idea is to see if we can divide these two unique numbers into two groups A and B.

The grouping needs to satisfy two conditions.

The two unique numbers are divided into different groups

The same numbers are divided into the same group

This way, the XOR of the data in each group will give us the two numbers.

The key question is how do we group?

Since the property of XOR is that if the same bit is the same, it is 0, and if it is different, it is 1. The result of XORing all numbers must not be 0, which means that at least one bit is 1.

We can take any one, and the basis for grouping is that if the bit you take is 0, it is divided into group 1, and if the bit is 1, it is divided into group 2.

This will definitely guarantee "2. The same numbers are divided into the same group", and will the different numbers be divided into different groups? Obviously yes, so we choose 1, that is to say "the two unique numbers" must be different in that bit, so the two unique elements will definitely be divided into different groups.

```python
class Solution:
    def singleNumbers(self, nums: List[int]) -> List[int]:
        ret = 0  # The result of the XOR of all numbers
        a = 0
        b = 0
        for n in nums:
            ret ^= n
        # Find the first non-zero bit
        h = 1
        while(ret & h == 0):
            h <<= 1
        for n in nums:
            # Divide them into two groups based on whether this bit is 0
            if (h & n == 0):
                a ^= n
            else:
                b ^= n

        return [a, b]
```

**_Complexity analysis_**

-Time complexity:$O(N)$, where N is the length of the array. - Space complexity:$O(1)$

## Related topics

- [190. Reverse binary bits](https://leetcode-cn.com/problems/reverse-bits /) (simple)
- [191. The number of digits 1](https://leetcode-cn.com/problems/number-of-1-bits /) (simple)
- [338. Bit count](https://leetcode-cn.com/problems/counting-bits /) (medium)
- [1072. Flip by column to get the maximum value and other rows](https://leetcode-cn.com/problems/flip-columns-for-maximum-number-of-equal-rows /) (medium)

For more questions, please visit my LeetCode questions warehouse:https://github.com/azl397985856/leetcode . There are already 38K stars.

Pay attention to the official account, work hard to restore the problem-solving ideas in clear and straightforward language, and there are a large number of diagrams to teach you how to recognize routines and brush questions efficiently.
