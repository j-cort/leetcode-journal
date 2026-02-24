# Remove Duplicates From Sorted Array

## Problem Description
#### Given an integer array nums sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The relative order of the elements should be kept the same.
<details>
<summary>
<strong>Expand Full Problem</strong>
</summary>

[View problem on leetcode](https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/?envType=study-plan-v2&envId=top-interview-150)

Given an integer array nums sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The relative order of the elements should be kept the same.

Consider the number of unique elements in nums to be k​​​​​​​​​​​​​​. After removing duplicates, return the number of unique elements k.

The first k elements of nums should contain the unique numbers in sorted order. The remaining elements beyond index k - 1 can be ignored.

Custom Judge:

The judge will test your solution with the following code:

> int[] nums = [...]; // Input array\
> int[] expectedNums = [...]; // The expected answer with correct length\
>
> int k = removeDuplicates(nums); // Calls your implementation\
>
> assert k == expectedNums.length;\
> for (int i = 0; i < k; i++) {\
>    assert nums[i] == expectedNums[i];\
>}

If all assertions pass, then your solution will be accepted.

> Example 1:
>
> Input: nums = [1,1,2]\
> Output: 2, nums = [1,2,_]\
> Explanation: Your function should return k = 2, with the first two elements of nums being 1 and 2 respectively.\
> It does not matter what you leave beyond the returned k (hence they are underscores).\

> Example 2:
>
> Input: nums = [0,0,1,1,1,2,2,3,3,4]
> Output: 5, nums = [0,1,2,3,4,_,_,_,_,_]
> Explanation: Your function should return k = 5, with the first five elements of nums being 0, 1, 2, 3, and 4 respectively.
> It does not matter what you leave beyond the returned k (hence they are underscores).

> Constraints:
> 
> 1 <= nums.length <= 3 * 104\
> -100 <= nums[i] <= 100\
> nums is sorted in non-decreasing order.
</details>

## Solution
```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        walker = 0
        current_index = 0

        while walker < len(nums):
            if nums[walker] > nums[current_index]:
                current_index += 1
                nums[current_index] = nums[walker] 
            walker +=1    

        return current_index+1
```

## Thought Process
#### Complexity Considerations
- I want an O(n) solution, so I should traverse the array only once.
- Since the array is sorted, duplicates appear in contiguous blocks.
- Removing elements from the middle would require shifting values, which is inefficient.
- Instead, I can overwrite values in-place as I scan (in constant time)

#### Strategy
Use a two-pointer approach:
- walker (read pointer): traverses the array from left to right.
- current_index (write pointer): tracks the position of the last unique element written.
- Both pointers start at index 0.
- Since the array is sorted, if nums[walker] is greater than nums[current_index],
we have reached a new unique value (the previous duplicates are over).
- When that happens:
	1.	Increment current_index.
	2.	Overwrite nums[current_index] with nums[walker].
- Continue scanning until the walker walks the whole array
- The current_index only incremented to mark a new unique value
  - nums[0..current_index]
  - current_index + 1 is the total number of unique elements

---
## Complexity

- Time: O(n)
- Space: O(1)