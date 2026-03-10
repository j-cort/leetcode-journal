# Jump Game II

## Problem Description
#### Return the minimum number of jumps to reach index n - 1.
<details>
<summary>
<strong>Expand Full Problem</strong>
</summary>

[View problem on leetcode](https://leetcode.com/problems/jump-game-ii/?envType=study-plan-v2&envId=top-interview-150)

You are given a 0-indexed array of integers nums of length n. You are initially positioned at index 0.

Each element nums[i] represents the maximum length of a forward jump from index i. In other words, if you are at index i, you can jump to any index (i + j) where:

```
0 <= j <= nums[i] and\
i + j < n
```

Return the minimum number of jumps to reach index n - 1. The test cases are generated such that you can reach index n - 1.



> Example 1:
>
> Input: nums = [2,3,1,1,4]\
> Output: 2\
> Explanation: The minimum number of jumps to reach the last index is 2. Jump 1 step from index 0 to 1, then 3 steps to the last index.

> Example 2:
>
> Input: nums = [2,3,0,1,4]\
> Output: 2

> Constraints:\
>
> 1 <= nums.length <= 10^4\
> 0 <= nums[i] <= 1000\
> It's guaranteed that you can reach nums[n - 1].

</details>

## Solution
```python
class Solution:
    def jump(self, nums: List[int]) -> int:
        if len(nums) == 1:
            return 0
        if nums[0] >= len(nums)-1:
            return 1
        
        i = 0
        high = 0
        limit = high + nums[0]
        jumps = 0

        while i < len(nums):
            if i + nums[i] >= len(nums)-1 and (i <= limit):
                jumps = jumps + 2
                break
            if i + nums[i] >= len(nums)-1:
                jumps = jumps + 1
                break
            if i + nums[i] >= high + nums[high]:
                high = i
            if i == limit: 
                jumps = jumps + 1
                limit = high + nums[high]
            i += 1
            
        return jumps
```
## Thought Process

This problem initially looked similar to **Jump Game I**, so my first instinct was to reuse the same idea: walk backwards and build a **valid chain** of indices that can reach the end.

However, Jump Game I only asks *whether* the end is reachable, while this problem asks for the **minimum number of jumps**, which makes the state more complicated.

---

### Attempt 1 — Backwards Chain

I started by working backwards from `n-2`, asking whether each index could connect to a valid chain leading to the end.

The condition is: `nums[i] >= end_of_chain - i`

If true, the index becomes part of the chain.

But this quickly got messy. If an earlier index can connect to **multiple points in the chain**, the number of jumps changes depending on where it connects.

Example:

- If `n-4` connects to `n-3`, it might require **3 jumps** to reach the end.
- If it can jump directly to `n-2`, it might require **2 jumps**.

Now I had two problems:

- How do I store **how many jumps** are needed from each index?
- How do I decide **which chain link is best to connect to**?

Trying to track chain links and jump counts started feeling overly complex and likely quadratic.

---

### Attempt 2 — “Graffiti” / Overwriting the Array

Next I tried modifying the array itself to store the **minimum jumps to the end**.

Idea:

- Set the last index to `0` jumps.
- Work backwards.
- If index `i` can reach `i + j`, then: `nums[i] = 1 + nums[i + j]`
So each index stores how many jumps it takes from there.

Example reasoning:

- If `n-2` can reach `n-1` → `1` jump.
- If `n-3` reaches `n-2` → `2` jumps.
- If `n-4` reaches `n-1` → `1` jump.

But this still required checking multiple landing points and quickly started looking **quadratic**.

At this point I was clearly overcomplicating things.

---

### Breakthrough — Think in Reachable Regions

Eventually I stepped back and realised I didn’t actually need to track exact paths.

Instead, I can think in terms of **reachable regions**:

- With the current number of jumps, there is a **range of indices I can reach**.
- While scanning that range, I track the position that gives the **furthest possible reach** for the next jump.
- Once I reach the boundary of the current range, I must take a jump and extend the boundary.

This avoids tracking paths entirely and reduces the problem to growing a frontier greedily.

---

## Complexity

- **Time:** `O(n)`
- **Space:** `O(1)`

---

### Reflection

This was easily the most frustrating problem so far. My thinking went in circles for a long time and I tried several overly complicated approaches before stepping back.

The key lesson was that I kept trying to **explicitly track paths or jump counts**, when the real solution only needs to track the **furthest reachable frontier**.

Once that shift happened, the solution became much simpler.