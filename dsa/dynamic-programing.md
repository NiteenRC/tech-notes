# Dynamic Programming LeetCode Questions with Hints

<details>
<summary>🧮 <strong>Basic 1D DP (Easy & Fundamental)</strong></summary>

| Topic | Problem Link | Hint |
|-------|--------------|------|
| Fibonacci Number | [Link](https://leetcode.com/problems/fibonacci-number/) | DP with base cases for n = 0 and 1; use bottom-up or memoization |
| Climbing Stairs | [Link](https://leetcode.com/problems/climbing-stairs/) | Similar to Fibonacci; dp[i] = dp[i-1] + dp[i-2] |
| Min Cost Climbing Stairs | [Link](https://leetcode.com/problems/min-cost-climbing-stairs/) | Start from step 0 or 1; pick min(dp[i-1], dp[i-2]) + cost[i] |
| House Robber | [Link](https://leetcode.com/problems/house-robber/) | Decide: rob this house and skip prev, or skip this house |
| House Robber II | [Link](https://leetcode.com/problems/house-robber-ii/) | Same as above, but circular array — try two runs: [0, n-2] and [1, n-1] |
| Decode Ways | [Link](https://leetcode.com/problems/decode-ways/) | Check one-digit and two-digit validity at each step |

</details>

---

<details>
<summary>🧠 <strong>Advanced 1D DP (Knapsack, Subsets)</strong></summary>

| Topic | Problem Link | Hint |
|-------|--------------|------|
| Jump Game | [Link](https://leetcode.com/problems/jump-game/) | Greedy or DP: track farthest reachable index |
| Jump Game II | [Link](https://leetcode.com/problems/jump-game-ii/) | Greedy: update range of next jump |
| Longest Increasing Subsequence | [Link](https://leetcode.com/problems/longest-increasing-subsequence/) | DP: dp[i] = LIS ending at i; or use binary search with patience sorting |
| Partition Equal Subset Sum | [Link](https://leetcode.com/problems/partition-equal-subset-sum/) | 0/1 knapsack: Can we sum to total/2? |
| Target Sum | [Link](https://leetcode.com/problems/target-sum/) | Transform to subset sum with offset |
| Coin Change | [Link](https://leetcode.com/problems/coin-change/) | Unbounded knapsack: dp[i] = min(dp[i - coin] + 1) |
| Coin Change II | [Link](https://leetcode.com/problems/coin-change-ii/) | Count combinations (not permutations); outer loop on coins |

</details>

---

<details>
<summary>🗺️ <strong>Basic 2D DP (Grid-Based)</strong></summary>

| Topic | Problem Link | Hint |
|-------|--------------|------|
| Unique Paths | [Link](https://leetcode.com/problems/unique-paths/) | DP[i][j] = paths from top and left |
| Unique Paths II | [Link](https://leetcode.com/problems/unique-paths-ii/) | Same as above, but watch for obstacles (0s) |
| Minimum Path Sum | [Link](https://leetcode.com/problems/minimum-path-sum/) | DP[i][j] = grid[i][j] + min(from top, from left) |
| Triangle | [Link](https://leetcode.com/problems/triangle/) | Bottom-up DP: start from second-last row upward |

</details>

---

<details>
<summary>🧬 <strong>Advanced 2D DP (String & Matrix-Based)</strong></summary>

| Topic | Problem Link | Hint |
|-------|--------------|------|
| Edit Distance | [Link](https://leetcode.com/problems/edit-distance/) | Classic 2D DP: insert, delete, replace |
| Word Break | [Link](https://leetcode.com/problems/word-break/) | DP[i] = true if s[0..i] can be broken |
| Word Break II | [Link](https://leetcode.com/problems/word-break-ii/) | Backtracking + memoization to avoid TLE |
| Longest Common Subsequence | [Link](https://leetcode.com/problems/longest-common-subsequence/) | 2D DP: match = 1 + dp[i-1][j-1]; else max(dp[i-1][j], dp[i][j-1]) |
| Longest Palindromic Substring | [Link](https://leetcode.com/problems/longest-palindromic-substring/) | Expand around center or DP on substrings |
| Longest Palindromic Subsequence | [Link](https://leetcode.com/problems/longest-palindromic-subsequence/) | Reverse string and apply LCS |
| Wildcard Matching | [Link](https://leetcode.com/problems/wildcard-matching/) | '?' = single char, '*' = any string; use DP with base conditions |
| Regular Expression Matching | [Link](https://leetcode.com/problems/regular-expression-matching/) | Handle '.' and '*' carefully; 2D DP with boolean states |

</details>