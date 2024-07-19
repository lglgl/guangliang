---
title: BFS (Breadth-First Search)
weight: 20
---
# BFS (Breadth-First Search)


## 1. 用途

广度优先搜索（BFS）在现实中有许多实际用途，主要用于解决以下问题：
1. 最短路径搜索：BFS 可以用于无权图或者所有边权重相同的图中寻找最短路径。这在网络路由、社交网络中的最短友谊链、广告投放中的最短路径传播等方面有广泛应用。
1. 图的连通性检测：BFS 可以用来检测图是否是连通的，即从一个节点能否到达图中的所有其他节点。这在计算机网络中的连通性检测、社交网络中的社群发现等方面很有用。
1. 状态空间搜索：BFS 可以应用于状态空间搜索问题，如在迷宫游戏中找到从起点到终点的最短路径，或者在解决八皇后问题等排列组合问题时寻找最优解。
1. Web爬虫：在网络爬虫中，BFS 用来抓取网页，并且可以限制爬取深度，确保优先爬取距离种子页面更近的链接。
1. 层次遍历：BFS 的特性使其适合进行层次遍历，例如`树的层次遍历`、多级菜单的展开等。
1. 游戏开发：在游戏开发中，BFS 可以用于寻找玩家或NPC的移动路径，或者在地图中寻找最近的敌人或资源。



## 2. 代码

```java
import java.util.*;

public class BFSGraphTraversal {
    // 使用邻接列表表示图
    private Map<Character, List<Character>> graph;

    public BFSGraphTraversal() {
        graph = new HashMap<>();
    }

    // 添加边的方法
    public void addEdge(char from, char to) {
        graph.computeIfAbsent(from, k -> new ArrayList<>()).add(to);
        graph.computeIfAbsent(to, k -> new ArrayList<>()).add(from); // 如果是有向图，删除此行
    }

    // BFS 遍历方法
    public List<Character> bfs(char start) {
        List<Character> traversalOrder = new ArrayList<>();
        if (!graph.containsKey(start)) {
            return traversalOrder;
        }

        Queue<Character> queue = new LinkedList<>();
        Set<Character> visited = new HashSet<>();

        queue.offer(start);
        visited.add(start);

        while (!queue.isEmpty()) {
            char current = queue.poll();
            traversalOrder.add(current);

            for (char neighbor : graph.getOrDefault(current, new ArrayList<>())) {
                if (!visited.contains(neighbor)) {
                    queue.offer(neighbor);
                    visited.add(neighbor);
                }
            }
        }

        return traversalOrder;
    }

    public static void main(String[] args) {
        BFSGraphTraversal graph = new BFSGraphTraversal();

        // 添加图的边
        graph.addEdge('A', 'B');
        graph.addEdge('A', 'C');
        graph.addEdge('B', 'D');
        graph.addEdge('B', 'E');
        graph.addEdge('C', 'F');
        graph.addEdge('E', 'F');

        // 从节点 'A' 开始进行 BFS 遍历
        List<Character> traversalOrder = graph.bfs('A');
        System.out.println("BFS traversal order starting from node A: " + traversalOrder);
    }
}


```