# Remove Duplicates From Sorted Array II

## Problem Description
#### Given an integer array nums sorted in non-decreasing order, remove some duplicates in-place such that each unique element appears at most twice. The relative order of the elements should be kept the same.
<details>
<summary>
<strong>Expand Full Problem</strong>
</summary>

[View problem on leetcode](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/description/?envType=study-plan-v2&envId=top-interview-150)

Given an integer array nums sorted in non-decreasing order, remove some duplicates in-place such that each unique element appears at most twice. The relative order of the elements should be kept the same.

Since it is impossible to change the length of the array in some languages, you must instead have the result be placed in the first part of the array nums. More formally, if there are k elements after removing the duplicates, then the first k elements of nums should hold the final result. It does not matter what you leave beyond the first k elements.

Return k after placing the final result in the first k slots of nums.

Do not allocate extra space for another array. You must do this by modifying the input array in-place with O(1) extra memory.

Custom Judge:

The judge will test your solution with the following code:

> int[] nums = [...]; // Input array\
int[] expectedNums = [...]; // The expected answer with correct length\
>
> int k = removeDuplicates(nums); // Calls your implementation\
>
> assert k == expectedNums.length;\
> for (int i = 0; i < k; i++) {\
>    assert nums[i] == expectedNums[i];\
>}
>
> If all assertions pass, then your solution will be accepted.

> Example 1:
>
> Input: nums = [1,1,1,2,2,3]\
> Output: 5, nums = [1,1,2,2,3,_]\
> Explanation: Your function should return k = 5, with the first five elements of nums being 1, 1, 2, 2 and 3 respectively.\
> It does not matter what you leave beyond the returned k (hence they are underscores).

> Example 2:
>
> Input: nums = [0,0,1,1,1,1,2,3,3]\
> Output: 7, nums = [0,0,1,1,2,3,3,_,_]\
> Explanation: Your function should return k = 7, with the first seven elements of nums being 0, 0, 1, 1, 2, 3 and 3 respectively.\
> It does not matter what you leave beyond the returned k (hence they are underscores).

> Constraints:
> 
> 1 <= nums.length <= 3 * 104\
> -10^4 <= nums[i] <= 104\
> nums is sorted in non-decreasing order.
</details>

## Solution
```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        walker = 1
        current_index = 0
        current_count = 1

        while walker < len(nums):
            if nums[walker] > nums[current_index]:
                current_index += 1
                nums[current_index] = nums[walker]
                current_count = 1
            else:
                if current_count < 2:
                    current_index += 1
                    nums[current_index] = nums[walker]
                    current_count += 1

            walker += 1
        
        return current_index+1
```

## Thought Process

#### Complexity Considerations

- I want an **O(n)** solution, so I should traverse the array only once.
- The array is sorted in non-decreasing order, so duplicates appear in contiguous blocks.
- I must modify the array **in-place** with **O(1)** extra space.
- Unlike the simpler version of this problem, each unique element may appear **at most twice**, not once.

Since removing elements from the middle would require shifting values, the best approach is to **overwrite values in-place while scanning**.

#### Strategy

Use a two-pointer approach with an additional counter:

- `walker` (read pointer): traverses the array from left to right.
- `current_index` (write pointer): tracks the position of the last kept element.
- `current_count`: tracks how many times the current value has been written into the valid prefix.

Because the array is sorted:

- `nums[walker]` can only be equal to or greater than `nums[current_index]`.

#### Case 1 – New Unique Value

If `nums[walker] > nums[current_index]`, we have reached a new distinct value.

- Increment `current_index`.
- Overwrite `nums[current_index]` with `nums[walker]`.
- Reset `current_count = 1`, since this is the first occurrence of the new value in the kept prefix.

#### Case 2 – Duplicate of Current Value

If `nums[walker] == nums[current_index]`:

- We are still within the same block of duplicates.
- We are allowed to keep at most **two** occurrences.
- If `current_count < 2`:
  - Increment `current_index`.
  - Overwrite `nums[current_index]` with `nums[walker]`.
  - Increment `current_count`.
- If `current_count >= 2`:
  - Skip this element by simply advancing `walker`.

#### Initial Conditions

- The first element always exists (given constraints).
- It should always be kept.
- Therefore:
  - `current_index = 0`
  - `walker = 1`
  - `current_count = 1`

This correctly accounts for the first occurrence before scanning the rest of the array.

After the scan completes:

- `current_index + 1` is the total number of valid elements `k`.

## Complexity

- **Time:** `O(n)` — single pass through the array.
- **Space:** `O(1)` — in-place modification with constant additional state.