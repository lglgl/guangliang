---
title: Hello World
weight: 10
---
## 高级算法目录


### 1.1. BFS (Breadth-First Search)

广度优先搜索（BFS）是一种图搜索算法，从起始节点开始，逐层向外扩展，访问所有相邻节点。BFS 使用队列来跟踪要访问的节点顺序，适用于寻找最短路径（在无权图中）和检测图的连通性。

**实现步骤：**
1. 创建一个空队列 `queue` 和一个空集合 `visited`。
2. 将起始节点放入队列和访问集合中。
3. 当队列不为空时，重复以下步骤：
   - 弹出队列中的节点，将其标记为已访问。
   - 将所有未访问过的邻居节点加入队列和访问集合。


### 1.2. DFS (Depth-First Search)

深度优先搜索（DFS）是一种图搜索算法，从起始节点开始，沿着一个分支深入，直到不能继续为止，然后回溯并继续搜索未访问的节点。DFS 使用栈来实现递归，适用于检测环、拓扑排序等。

**实现步骤：**
1. 创建一个空集合 `visited` 来记录访问过的节点。
2. 从起始节点开始调用递归函数 `dfs`：
   - 将当前节点标记为已访问。
   - 对于当前节点的每个未访问的邻居节点，递归调用 `dfs` 函数。

## 2. Shortest Path Algorithms


### 2.1. Dijkstra

Dijkstra 算法用于计算单源最短路径，适用于边权重非负的图。它使用优先队列来选择当前最短路径的节点，逐步更新其邻居的最短路径。

**实现步骤：**
1. 创建一个优先队列 `pq` 和距离字典 `distances`，初始化起始节点的距离为 0，其余节点为无穷大。
2. 将起始节点和距离 0 放入队列。
3. 当队列不为空时，重复以下步骤：
   - 弹出队列中距离最小的节点。
   - 对于该节点的每个邻居节点，如果通过当前节点到达该邻居节点的路径距离更短，则更新距离并将其加入队列。

---

### 2.2. Bellman-Ford

Bellman-Ford 算法用于计算单源最短路径，适用于包含负权边的图。它通过反复松弛所有边，更新最短路径，最多进行 |V|-1 次迭代。它还能检测负权环。

**实现步骤：**
1. 创建距离字典 `distance`，初始化起始节点的距离为 0，其余节点为无穷大。
2. 进行 |V|-1 次迭代，每次迭代遍历所有边：
   - 对于每条边 (u, v) 权重为 w，如果通过节点 u 到节点 v 的路径距离更短，则更新距离。
3. 再次遍历所有边，检查是否存在负权环。

## 3. Pattern Matching Algorithms


### 3.1. KMP (Knuth-Morris-Pratt)

KMP 算法用于字符串模式匹配，通过预处理模式串构建部分匹配表，从而在匹配过程中避免重复扫描文本。

**实现步骤：**
1. 构建部分匹配表 `lps`，用于指示在匹配失败时模式串应移动的位置。
2. 使用两个指针 i 和 j 分别遍历文本和模式串：
   - 如果匹配成功（text[i] == pattern[j]），则同时向前移动 i 和 j。
   - 如果 j 达到模式串末尾，说明找到了匹配，记录匹配的起始位置。
   - 如果匹配失败（text[i] != pattern[j]），根据部分匹配表移动模式串的位置 j。



### 3.2. Boyer-Moore

Boyer-Moore 算法是另一种字符串模式匹配算法，通过从右向左匹配和使用跳跃表显著减少比较次数，提高匹配效率。

**实现步骤：**
1. 构建字符到最后出现位置的映射表 `last`。
2. 使用两个指针 s 和 j 分别遍历文本和模式串：
   - 从右向左比较模式串和文本，当匹配失败时根据 `last` 表跳过一定的字符。
   - 如果完全匹配，记录匹配的起始位置并移动 s。
   - 如果部分匹配但未完全匹配，根据 `last` 表调整 j 的位置。

这些算法在图论、路径查找和字符串模式匹配领域中具有重要应用，能够有效解决各种复杂问题。