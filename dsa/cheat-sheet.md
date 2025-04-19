## 🔍 **General Strategy**
- **Clarify the problem** — ask for constraints, input size, and edge cases.
- **Categorize input** — Array, String, Tree, Graph, Linked List, etc.
- **Identify what’s being asked** — search, count, construct, optimize, validate, etc.
- **Choose the right technique or data structure.**

---

## ♦️ **If the Input is an Array or String**

### ✅ Is the array sorted?
- **Yes**:
    - Use **Binary Search** for element lookup.
    - Use **Two Pointers** or **Sliding Window** for pairs, subarrays, etc.
- **No**: Consider hashing, prefix sums, or sorting first.

### ❓ What is being asked?
- **Number of ways / combinations / sequences?**
    - Use **Dynamic Programming** (especially if overlapping subproblems exist).
- **Get max/min or make optimal choices?**
    - Use **Greedy** if the local optimum leads to the global one.
- **Is it about checking feasibility or existence?**
    - Use **Backtracking** or **DFS with pruning**.

### 🧠 Is it about string manipulation?
- **Prefix/suffix matching?** → Use **Trie**.
- **Reversals, balancing, or distances?** → Use **Stack** or **Monotonic Stack**.
- **Is the pattern repetitive or cyclic?** → Use **KMP Algorithm** or **Rabin-Karp**.

### 🧩 Is it about subarrays/substrings?
- **Fixed/variable window?** → Use **Sliding Window**.
- **Need to track frequency/counts?** → Use **Hash Map / Counting Array**.

### 🔎 Is it about finding or tracking elements?
- **First/Last occurrence?** → Use **Binary Search** or **Hash Map**.
- **Duplicates?** → Use **Set** or **Two Pointers after sorting**.
- **Top K elements?** → Use **Heap / Priority Queue**.

---

## ♦️ **If the Input is a Graph**

### 🌐 What kind of graph?
- **Directed / Undirected? Weighted / Unweighted? Cyclic?**

### 🛣️ Shortest Path / Fewest Steps?
- Use **BFS** (for unweighted) or **Dijkstra** (for weighted).

### 🔄 All paths, components, or cycles?
- Use **DFS**, **Union-Find**, or **Tarjan’s algorithm** (for SCCs).
- **Topological Sorting** if it's a **DAG**.

### 📌 Other ideas:
- **Grid-based graph**? → Model it as a graph and use **BFS/DFS**.
- **Minimum Spanning Tree?** → Use **Kruskal’s or Prim’s Algorithm**.

---

## ♦️ **If the Input is a Tree (usually binary)**

### 🌲 Depth-based traversal?
- **Level order / depth level info?** → Use **BFS (Queue)**.
- **Preorder / Inorder / Postorder?** → Use **DFS (Recursion or Stack)**.

### 💡 Common Tree Patterns:
- **Lowest Common Ancestor (LCA)?** → Use **Binary Lifting** or **Recursive DFS**.
- **Balanced tree check?** → DFS with height checks.
- **BST operations?** → Leverage **in-order traversal** properties.
- **Diameter / Path Sum?** → Use **DFS with backtracking or postorder**.

---

## ♦️ **If the Input is a Linked List**

### 🔄 Is it about cycles or meeting points?
- Use **Fast and Slow Pointers (Floyd's Cycle Detection)**.

### ↩️ Reversal or Modification?
- Use **Iterative Reversal with Prev Node**.
- Use **Dummy Node** to simplify edge cases (especially with head changes).

### 🧵 Merging or Sorting?
- Use **Two Pointers** for merging.
- Use **Merge Sort** for sorting.

### 🧠 More advanced?
- **K-group reversal?** → Use recursion + pointer manipulation.
- **Palindrome check?** → Use slow/fast pointer + stack or reverse second half.

---

## 🧠 Bonus Tips

- **Cache repeated work** → Use **Memoization (Top-Down DP)** or **Tabulation (Bottom-Up DP)**.
- **Heavy recursion?** → Watch out for stack overflows; optimize with **tail recursion** or **iterative approach**.
- **Can’t decide between DP and Backtracking?** → Start with backtracking; optimize with memoization.

---

> 📌 **Pro Tip**: Most problems boil down to identifying the pattern and applying the right technique. The more you recognize patterns, the faster you solve problems.