This post is a quick, comprehensive **Time Complexity Cheat Sheet** summarizing the **Worst-Case Time Complexity** $O(\cdot)$ of the most important algorithms and data structure operations used in computer science. This is a vital resource for coding interviews, academic study, and software design.

Understanding **Big $\text{O}$ notation** is crucial for predicting an algorithm's performance and scalability as the input size $n$ grows.

## 1. Sorting Algorithms Time Complexity

Efficient sorting is a cornerstone of computer science. Here is the complexity analysis for standard comparison-based and non-comparison-based sorts.

| Algorithm | Worst-Case Time Complexity (Time) | Space Complexity (Auxiliary) | Notes / Keyword Focus |
| :--- | :--- | :--- | :--- |
| **Insertion Sort** | $O(n^2)$ | $O(1)$ | Simple sort, good for small arrays. |
| **Merge Sort** | $O(n \log n)$ | $O(n)$ | **Stable sort**. Guaranteed $O(n \log n)$ performance. |
| **Heapsort** | $O(n \log n)$ | $O(1)$ | **In-place** sort using a **Binary Heap**. |
| **Quicksort** (Worst-Case) | $O(n^2)$ | $O(n)$ or $O(\log n)$ | High performance on **average-case** $O(n \log n)$. |
| **Counting Sort** | $O(n+k)$ | $O(n+k)$ | Linear time. Requires input to be in range $[0, k]$. |
| **Radix Sort** | $O(d(n+k))$ | $O(n+k)$ | Linear time. Sorts by digit (**Non-Comparison Sort**). |


## 2. Data Structure Operations Complexity

The time cost of fundamental operations on core **Data Structures**.

| Data Structure / Operation | Worst-Case Time Complexity | Keyword Focus / Notes |
| :--- | :--- | :--- |
| **Binary Search** (Sorted Array) | $O(\log n)$ | Efficient searching method. |
| **Hash Table** (Insert/Delete/Search) | $O(1)$ (Average) / $O(n)$ (Worst) | Crucial for fast lookups. Worst case due to collisions. |
| **Red-Black Tree** (Insert/Delete/Search) | $O(\log n)$ | **Self-balancing BST**, guarantees logarithmic complexity. |
| **B-Tree** (Insert/Delete/Search) | $O(\log_t n)$ | Optimized for disk I/O (**External Storage**). |
| **Heap** (MAX-HEAPIFY) | $O(\log n)$ | Used for **Priority Queues** and Heapsort. |
| **Disjoint Sets** (FIND-SET / UNION) | $\approx O(\alpha(n))$ (Amortized) | Inverse Ackermann function is nearly constant time. |

## 3. Graph Algorithms Time Complexity

Complexity analysis for common **Graph Algorithms**, where $V$ is vertices and $E$ is edges.

| Algorithm / Problem | Worst-Case Time Complexity | Keyword Focus / Notes |
| :--- | :--- | :--- |
| **Breadth-First Search (BFS)** | $O(V+E)$ | Finds shortest paths in unweighted graphs. |
| **Depth-First Search (DFS)** | $O(V+E)$ | Used for **Topological Sort** and finding cycles. |
| **Prim's Algorithm** (MST) | $O(E + V \log V)$ (Binary Heap) | Finds the **Minimum Spanning Tree** (MST). |
| **Kruskal's Algorithm** (MST) | $O(E \log E)$ or $O(E \log V)$ | MST algorithm using the **Disjoint Set** structure. |
| **Dijkstra's Algorithm** (SSSP) | $O(E + V \log V)$ | **Single-Source Shortest Paths** for non-negative weights. |
| **Bellman-Ford Algorithm** (SSSP) | $O(V E)$ | Handles graphs with **negative edge weights**. |
| **Floyd-Warshall Algorithm** (APSP) | $O(V^3)$ | Finds **All-Pairs Shortest Paths** using **Dynamic Programming**. |


## 4. Dynamic Programming and Other Techniques

| Algorithm / Problem | Worst-Case Time Complexity | Technique Used / Keyword |
| :--- | :--- | :--- |
| **Matrix-Chain Multiplication** | $O(n^3)$ | **Dynamic Programming** optimization. |
| **Longest Common Subsequence (LCS)** | $O(mn)$ | **Dynamic Programming** for sequence comparison. |
| **Activity Selection** | $O(n \log n)$ | **Greedy Approach** (requires initial sort). |
| **Fast Fourier Transform (FFT)** | $O(n \log n)$ | **Divide-and-Conquer** for signal processing/multiplication. |


## Summary of Common Big $\text{O}$ Classes

This table visually summarizes the hierarchy of performance for algorithm analysis.

| Complexity Class | Name | Performance Rank | Example Algorithm |
| :--- | :--- | :--- | :--- |
| $O(1)$ | Constant Time | Best | Accessing an array element by index. |
| $O(\log n)$ | Logarithmic Time | Excellent | **Binary Search**. |
| $O(n)$ | Linear Time | Good | Searching an unsorted array. |
| $O(n \log n)$ | Linearithmic Time | Standard/Optimal | **Merge Sort**, Heapsort. |
| $O(n^2)$ | Quadratic Time | Poor/Inefficient | Bubble Sort (Worst-Case). |
| $O(2^n)$ | Exponential Time | Worst | Naive Traveling Salesperson Problem (TSP). |

## Conclusion

This **Algorithm Complexity Cheat Sheet** provides the foundational **Big O** values essential for any developer or computer science student. Mastering these complexities is the first step toward writing efficient, scalable code.

For full-stack developers, data scientists, and engineers, always prioritize algorithms with logarithmic or quasilinear ($O(n \log n)$) complexity when possible!