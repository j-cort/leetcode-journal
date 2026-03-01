# Best Time to Buy and Sell Stock

## Problem Description
#### You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.
<details>
<summary>
<strong>Expand Full Problem</strong>
</summary>

[View problem on leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/?envType=study-plan-v2&envId=top-interview-150)

You are given an array prices where prices[i] is the price of a given stock on the ith day.

You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.

> Example 1:
>
> Input: prices = [7,1,5,3,6,4]\
> Output: 5\
> Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.\
> Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.

> Example 2:
>
> Input: prices = [7,6,4,3,1]\
> Output: 0\
> Explanation: In this case, no transactions are done and the max profit = 0.

> Constraints:\
>\
> 1 <= prices.length <= 10^5\
> 0 <= prices[i] <= 10^4

</details>

## Solution
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        highest_point = prices[len(prices)-1]
        biggest_difference = 0

        for i in range(len(prices)-1, -1, -1):
            if  highest_point - prices[i] > biggest_difference:
                biggest_difference = highest_point - prices[i] 
            if prices[i] > highest_point:
                highest_point = prices[i]
        
        return biggest_difference
```

## Thought Process

### First idea – brute-force thinking

Basic idea: **buy low, sell high**.

- If I’m on day 0:
  - I ask: are there any days after day 0 with a higher price?
  - If yes, which one gives the highest profit? I store that as the max profit for day 0.
- Then I do the same for day 1, then day 2, all the way until day `n - 1`.
- If the max profit is ≤ 0, we just return 0.

This is a straightforward nested comparison and ends up being **O(n²)**, which is rubbish for large `n`.

### Trying to cut down operations (recursive approach)

I then tried to reduce the amount of work by trimming the array and using recursion.

Idea:

- From the left:
  - If `n+1 <= n`, we can disregard `n` and start from `n+1`, we keep walking forward until `n+1` is lower (i.e., skip early days when buying is obviously bad - no profit).
- From the right:
  - If `n-2` is higher than `n-1`, it’s better to start from `n-2`, same as above, keep walking and trim down to a viable time frame.

Once I have a trimmed segment:

- I know `n+1` is bigger than `n`, so that pair defines a candidate largest difference.
- Then I consider the next element `n+2`:
  - If `n+2` is bigger than `n+1`, then `n..n+2` become the new “largest” range, and I keep walking forward looking for an even better high.
  - If `n+2` is smaller than `n+1` but still bigger than `n`, then `n+2` can be disregarded and I continue walking.
  - If `n+2` is smaller than `n`, it could be a new low. Something after it might give a greater profit than `n+1 - n`, but there’s no way of knowing without scanning the rest of the list:
    - So I start a **new loop/recursion** from `n+2 → end`, which will return its own “largest” difference to be compared to the current one.
    - That recursive call will again cut from the front and back.
    - Then it will walk forward comparing its own largest to the next elements, and so on.

I wasn’t 100% sure of the asymptotic complexity, but the idea was: “I’m cutting things down as much as I can.”

Some extra observations I made:

- If I have two new lows in a row, we disregard the first and just start from the next new low.
- To trigger a new recursion, we need a pattern like:
  - A “low”, then a “high or same”, then a **new low** that is lower than the first.
- That repeating pattern would be the worst case.
  - That “high/same + down” pattern would roughly divide `n` by 2.
  - And the further we walk along the array, the less there is left to iterate over in each recursion.
- It's still not ideal time complexity, but I thought maybe it behaves like an “optimised quadratic”.

The code for that attempt looked like this:

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        start = 0
        end = len(prices)-1
        largest = 0

        ## adjust start
        while start < len(prices)-1:
            if prices[start+1] > prices[start]:
                break
            start += 1

        # adjust end - only done once but worth if a lot of non-unique values at the end
        while end > 0:
            if prices[end-1] < prices[end]:
                break
            end -= 1
        
        # begin comparisons procedure
        if end - start > 0:
            largest = prices[start+1] - prices[start]
        
        if end - start > 1:
            for i in range(start+2, end+1):
                difference = prices[i] - prices[start]
                if difference > largest:
                    largest = difference
                if difference <= 0:
                    # if we've found a new low after a high, then we have to find it's "largest" margin, from that new low, until the end of the array
                    largest_rec = self.maxProfit(prices[i:])
                    if largest_rec > largest:
                        largest = largest_rec  

        return largest
```
In the end: **Time Limit Exceeded** → this does not work in practice.

At this point I wondered:

- Would it be better just to do a simpler brute-force approach?
- Or add some kind of cache to avoid repeated recursion?
- Or maybe use the python `.max` method
- Or maybe this is one of those cases where I need to walk backwards?

I also thought about another optimisation:

- When I find a new low, I could ask:
  - “What’s the highest point in the future after this low?”
  - If the difference between that high and the low is already better than the current `largest`, maybe I can cancel the rest of the walk for the current base.
- This might cut down some redundant recursions.
- But overall I started to feel like I was overcomplicating things.

---

### Overcomplicating further – thinking about crawling from both sides

I considered a two-sided crawl:

- From the left:
  - If I find a lower price, walk forward and keep going until I find the lowest.
- From the right:
  - If I find a higher price, walk backwards (towards the left) and keep going until I find the highest.

- Discard some left and right parts of the array once I’ve identified clear lows/highs.
- Then walk forward from the left side and compare... I couldn't make a clear algorithm from this and decided to stop and break.

---

### Actual breakthrough – Walking backwards, Note the latest high, Continuously calculate the biggest difference

The realisation:

> It’s not about clever optimisations on top of a complicated structure.  
> It’s about walking from the end, tracking the highest point, and calculating the difference at each step.

My earlier thought about “scoping into the future to see the highest price” lead me to the idea of starting from the back.

Final approach:

- Start from the last element:
  - Let `highest_point` be the price at the last index.
  - Let `biggest_difference = 0`.
- Walk backwards through the array:
  - At index `i`, compute `highest_point - prices[i]`.
  - If this is greater than `biggest_difference`, update it.
  - If `prices[i]` is greater than `highest_point`, update `highest_point`.
- Return `biggest_difference`.

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        highest_point = prices[len(prices)-1]
        biggest_difference = 0

        for i in range(len(prices)-1, -1, -1):
            if highest_point - prices[i] > biggest_difference:
                biggest_difference = highest_point - prices[i]
            if prices[i] > highest_point:
                highest_point = prices[i]
        
        return biggest_difference
```

### Reflection / Lesson

- I really went around the houses with this one before landing on the simple `O(n)` solution.
- The key insight was:
  - I could have done the same thing by tracking the lowest point and walking forwards, instead of tracking the highest and walking backwards.
- The bigger lesson:
  - LeetCode is rarely looking for a quadratic solution when `n` can go up to `10^5`.
  - If I’m drifting into `O(n²)` territory, I probably need to step back and look for a simple linear scan with some running state instead of piling on “optimisations” over a quadratic idea.
  
## Complexity 
Time: O(n)
Space: O(1)