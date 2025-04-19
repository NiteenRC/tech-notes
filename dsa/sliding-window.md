## 🚪 Sliding Window LeetCode Questions with Hints

<details>
<summary>🌬️ <strong>Sliding Window Problems</strong></summary>

| Topic | Problem Link | Hint |
|-------|--------------|------|
| Maximum Sum of a Subarray of Size K | [Link](https://leetcode.com/problems/maximum-average-subarray-i/) | Fixed-size window: keep sum of `k` elements, slide window forward |
| Find All Anagrams in a String | [Link](https://leetcode.com/problems/find-all-anagrams-in-a-string/) | Compare frequency maps of target and current window |
| Longest Repeating Character Replacement | [Link](https://leetcode.com/problems/longest-repeating-character-replacement/) | Max window where (window size - max freq char count) ≤ k |
| Longest Substring Without Repeating Characters | [Link](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | Use set or map to track characters; shrink left pointer on repeat |
| Longest Substring with At Most K Distinct Characters | [Link](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) | Use hashmap to count distinct chars; shrink window when count > k |
| Minimum Window Substring | [Link](https://leetcode.com/problems/minimum-window-substring/) | Use two maps: target and current; shrink window when all target chars matched |
| Sliding Window Maximum | [Link](https://leetcode.com/problems/sliding-window-maximum/) | Use deque to store useful indices; front always has the max |
| Subarray Sum Equals K | [Link](https://leetcode.com/problems/subarray-sum-equals-k/) | Prefix sum + hashmap to store cumulative sum frequencies |
| Fruit Into Baskets | [Link](https://leetcode.com/problems/fruit-into-baskets/) | Longest subarray with at most 2 distinct elements (same as K distinct) |
| Max Consecutive Ones III | [Link](https://leetcode.com/problems/max-consecutive-ones-iii/) | Allow at most `k` zeroes in window; shrink when exceeded |

</details>