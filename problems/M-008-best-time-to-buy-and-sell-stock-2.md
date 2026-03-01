# Best Time to Buy and Sell Stock 2

## Problem Description
#### You are given an integer array prices where prices[i] is the price of a given stock on the ith day.
<details>
<summary>
<strong>Expand Full Problem</strong>
</summary>

[View problem on leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/?envType=study-plan-v2&envId=top-interview-150)

On each day, you may decide to buy and/or sell the stock. You can only hold at most one share of the stock at any time. However, you can sell and buy the stock multiple times on the same day, ensuring you never hold more than one share of the stock.

Find and return the maximum profit you can achieve.



> Example 1:
>
> Input: prices = [7,1,5,3,6,4]\
> Output: 7\
> Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.\
> Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.\
> Total profit is 4 + 3 = 7.

> Example 2:
>
> Input: prices = [1,2,3,4,5]\
> Output: 4\
> Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.\
> Total profit is 4.

> Example 3:
>
> Input: prices = [7,6,4,3,1]\
> Output: 0\
> Explanation: There is no way to make a positive profit, so we never buy the stock to achieve the maximum profit of 0.

> Constraints:\
>\
> 1 <= prices.length <= 3 * 10^4\
> 0 <= prices[i] <= 10^4

</details>

## Solution
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        profit = 0
        for i in range(len(prices)-1):
            if prices[i + 1] > prices[i]:
                profit += prices[i+1] - prices[i]
        
        return profit
```

## Thought Process

### First idea – brute-force thinking

From the last exercise (Stock I), I learned I should probably aim for an `O(n)` solution — scan once and collect the necessary data.

Here, the difference is that I’m allowed to:

- Buy and sell multiple times.
- Only hold at most one share at a time.

So the goal becomes:

> Capture every profitable upward movement.

---

### Intuition

I theorised that I should make maximum profit by:

- Buying at every new low,
- Selling at every last high in an upward cycle.

Let’s imagine: [7, 5, 6, 10, 2, 12, 14, 5, 4, 7]

- First new low at 5.
- The last high in that upward run is 10.
  - Profit = 10 − 5 = 5.

But note:

- We would get the same profit by:
  - Buy 5, sell 6 → profit 1.
  - Buy 6, sell 10 → profit 4.
  - Total = 5.

So instead of explicitly tracking lows and highs, we can just accumulate every positive increase.

---

### Important Realisation

If we don’t sell at 10 and instead hold through a dip:

- Holding from 5 → 12 gives profit 7.
- But if we:
  - Sell at 10 → profit 5,
  - Buy at 2 and sell at 12 → profit 5,
- Total profit ends up better -> 5+5 = 10

There is **no advantage to holding through a dip**.

If tomorrow’s price is lower than today’s, you lose nothing by selling today and re-buying later.

So:

> Any time `prices[i+1] > prices[i]`, that difference is guaranteed profit.

---

### Final Strategy

- Walk the array once.
- For every consecutive pair:
  - If `prices[i+1] > prices[i]`,
    - Add `prices[i+1] - prices[i]` to total profit.

No need to explicitly track valleys and peaks.

Just accumulate all positive differences.

---

## Complexity

- **Time:** `O(n)` — single pass.
- **Space:** `O(1)` — only a running profit counter.

---

### Reflection

This actually felt easier than Stock I.

In Part I, I had to track the largest future difference across dips.

Here, I’m allowed to exploit every upward move, so the problem collapses into summing positive gradients.

Once that insight clicked, the implementation was super simple.

## Complexity 
Time: O(n)\
Space: O(1)