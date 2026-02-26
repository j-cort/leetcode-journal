# Majority Element

## Problem Description
#### Given an array nums of size n, return the majority element.
<details>
<summary>
<strong>Expand Full Problem</strong>
</summary>

[View problem on leetcode](https://leetcode.com/problems/majority-element/description/?envType=study-plan-v2&envId=top-interview-150)

Given an array nums of size n, return the majority element.

The majority element is the element that appears more than ⌊n / 2⌋ times. You may assume that the majority element always exists in the array.

> Example 1:
>
> Input: nums = [3,2,3]\
> Output: 3

> Example 2:
>
> Input: nums = [2,2,1,1,1,2,2]\
> Output: 2

> Constraints:
> 
> n == nums.length\
> 1 <= n <= 5 * 10^4\
> -10^9 <= nums[i] <= 10^9

</details>

## Solution
```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        counted = set()
        half_nums = len(nums) // 2
        for num in nums:
            if num not in counted:
                counted.add(num)
                if nums.count(num) > half_nums:
                    return num 
```

## Thought Process

#### Complexity Considerations

- The majority element appears more than ⌊n / 2⌋ times.
- Since a majority element is guaranteed to exist, I only need to find the element whose frequency exceeds half the length of the array.
- A brute-force approach would be to count occurrences for each element using `nums.count(x)`.
- However, `list.count()` runs in `O(n)` time, so doing it for every element would lead to `O(n²)` time complexity.



#### Initial Idea

I first considered using Python’s built-in `count()` method:

- Iterate through the array.
- For each element, count how many times it appears.
- Return the element if its count is greater than `len(nums) // 2`.

This is simple and readable, but repeatedly calling `count()` for the same element would be inefficient.



#### Refinement

To avoid redundant counting:

- Maintain a `set` called `counted` to track which elements have already been counted.
- Only call `nums.count(num)` the first time we encounter a number.
- If the count exceeds `half_nums`, return that number immediately.

Because the majority element appears more than half the time:

- We are guaranteed to find it.
- We will not need to count every unique element in practice.
- At most, we count each distinct element once.



#### Alternative Considered

Another option was to:

- Sort the array (`O(n log n)` due to Timsort).
- Walk through the sorted array and count consecutive occurrences.

However:
- This approach may be asymptotically favourable although less elegant



#### Final Approach

- Compute `half_nums = len(nums) // 2`.
- Iterate through `nums`.
- For each uncounted number:
  - Count its occurrences.
  - If its count exceeds `half_nums`, return it.

This solution is simple and readable.

In practice time complexity is mitigated by:
- avoiding calling count for equivalent values
- returning early after finding the majority element

---

## Complexity

- **Time:** Worst-case `O(n²)`  
  - In the worst case (many unique elements before reaching the majority), `count()` may be called multiple times.
- **Space:** `O(n)`  
  - In the worst case, the `counted` set may store many distinct elements.