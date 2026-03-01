# Rotate Array

## Problem Description
#### Given an integer array nums, rotate the array to the right by k steps, where k is non-negative.
<details>
<summary>
<strong>Expand Full Problem</strong>
</summary>

[View problem on leetcode](https://leetcode.com/problems/rotate-array/?envType=study-plan-v2&envId=top-interview-150)

Given an array nums of size n, return the majority element.

The majority element is the element that appears more than ⌊n / 2⌋ times. You may assume that the majority element always exists in the array.

> Example 1:
>
> Input: nums = [1,2,3,4,5,6,7], k = 3\
> Output: [5,6,7,1,2,3,4]\
> Explanation:\
> rotate 1 steps to the right: [7,1,2,3,4,5,6]\
> rotate 2 steps to the right: [6,7,1,2,3,4,5]\
> rotate 3 steps to the right: [5,6,7,1,2,3,4]\

> Example 2:
>
> Input: nums = [-1,-100,3,99], k = 2\
> Output: [3,99,-1,-100]\
> Explanation:\
> rotate 1 steps to the right: [99,-1,-100,3]\
> rotate 2 steps to the right: [3,99,-1,-100]\

> Constraints:\
>\
> 1 <= nums.length <= 105\
> -231 <= nums[i] <= 231 - 1\
> 0 <= k <= 105\

</details>

## Solution
```python
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        nums_copy = nums.copy()
        for i in range(len(nums_copy)):
            nums[(i + k)%len(nums)] = nums_copy[i]
```

## Thought Process

#### Complexity Considerations
- By iterating the list and reassigning elements to a different index, the array can be rotated in O(n) time 
- However, my concern was that by modifying the array in place, I might overwrite values that I still need.
- Therefore, I can copy the array in O(n) time and use the copy as a template.
- Walk the new array, calculate the new index of each element, place the element in that index in the original array

For example, if shifting right by 1:
- I move `nums[0]` to `nums[1]`.
- But then when I move to index 1, its original value has already been overwritten.
- So that approach won’t work.
- Thus, the need to make a copy of the array.

---

Now I need a formula for the new index.

I need to relate:
- The original index `i`
- The shift amount `k`
- The length of the array

Using modulo makes sense because rotation wraps around.

So the new index should be:

```python
(i + k) % len(nums)