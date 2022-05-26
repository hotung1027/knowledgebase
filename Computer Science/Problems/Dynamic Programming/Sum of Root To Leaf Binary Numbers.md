---
tags : 
	- Leet-Code
	- Binary-Tree
date : 11-01-2022
---
# Sum of Root To Leaf Binary Numbers

# Problem 
You are given the `root` of a binary tree where each node has a value `0` or `1`. Each root-to-leaf path represents a binary number starting with the most significant bit.

-   For example, if the path is `0 -> 1 -> 1 -> 0 -> 1`, then this could represent `01101` in binary, which is `13`.

For all leaves in the tree, consider the numbers represented by the path from the root to that leaf. Return _the sum of these numbers_.

The test cases are generated so that the answer fits in a **32-bits** integer.

**Exmple 1:**
![](https://assets.leetcode.com/uploads/2019/04/04/sum-of-root-to-leaf-binary-numbers.png)


```
**Input:** root = [1,0,1,0,1,0,1]
**Output:** 22
**Explanation:** (100) + (101) + (110) + (111) = 4 + 5 + 6 + 7 = 22

```

**Example 2:**
```
**Input:** root = [0]
**Output:** 0
```
 