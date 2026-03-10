# Jump Game

## Problem Description
#### You are given an integer array nums. Return true if you can reach the last index, or false otherwise.
<details>
<summary>
<strong>Expand Full Problem</strong>
</summary>

[View problem on leetcode](https://leetcode.com/problems/jump-game/description/?envType=study-plan-v2&envId=top-interview-150)

You are given an integer array nums. You are initially positioned at the array's first index, and each element in the array represents your maximum jump length at that position.

Return true if you can reach the last index, or false otherwise.



> Example 1:
>
> Input: nums = [2,3,1,1,4]\
> Output: true\
> Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.

> Example 2:
>
> Input: nums = [3,2,1,0,4]\
> Output: false\
> Explanation: You will always arrive at index 3 no matter what. Its maximum jump length is 0, which makes it impossible to reach the last index.

> Constraints:\
>
> 1 <= nums.length <= 10^4\
> 0 <= nums[i] <= 10^5

</details>

## Solution
```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        nums_length = len(nums)
        current_chain_end = nums_length-1
        valid_chain = False

        if nums_length == 1:
            return True

        for i in range(nums_length-2, -1, -1):
            if nums[i] >= current_chain_end - i:
                current_chain_end = i
                valid_chain = True
            else:
                valid_chain = False
        
        return valid_chain
```

## Thought Process

My first idea was to think about the problem as a series of **stepping stones across a river**.

Each index is a stone, and the value at that index tells me how far I can jump from it.

Instead of trying to explore all forward paths (which could explode combinatorially), I thought it might be easier to reason **backwards from the end**.

### Backwards Chain Idea

Start at the **second-to-last index**.

From that index, ask:

> Do we have enough jump power to reach the last index?

If yes, that index becomes a **valid stepping stone** that can reach the end.

So we mark that position as the **current end of a valid chain**.

Then we move backwards through the array.

For each position `i`, we ask:

> Can this position reach the current end of the chain?

Mathematically: `nums[i] >= current_chain_end - i`

If this is true:

- This index can connect to the chain.
- So it becomes the **new chain end**.

If false:

- This position cannot connect to the chain.
- We ignore it and continue moving backwards.

### Example: `[2,3,1,1,4]`

Start with the chain end at the final index.

Working backwards:

- At index `3` (`1`):  
  Can jump `1` → reaches index `4` → valid chain.

- Move chain end to `3`.

- At index `2` (`1`):  
  Distance to chain = `1` → valid.

- Move chain end to `2`.

- At index `1` (`3`):  
  Distance to chain = `1` → valid.

- Move chain end to `1`.

- At index `0` (`2`):  
  Distance to chain = `1` → valid.

Since index `0` connects to the chain, we can reach the end.

---

### Important Realisation

It is **not about total jump power**.

For example: `[3,1,0,0,4]`

fails even though early numbers are large.

What matters is whether each position can **connect to a valid chain that reaches the end**.

---

### Final Strategy

1. Initialise the **chain end** at the last index.
2. Iterate backwards through the array.
3. If `nums[i]` can reach the current chain end:
   - Update the chain end to `i`.
4. At the end, check whether index `0` connects to the chain.

If yes → return `True`.  
Otherwise → return `False`.

---

## Complexity

- **Time:** `O(n)` — single backward pass.
- **Space:** `O(1)` — constant extra state.
