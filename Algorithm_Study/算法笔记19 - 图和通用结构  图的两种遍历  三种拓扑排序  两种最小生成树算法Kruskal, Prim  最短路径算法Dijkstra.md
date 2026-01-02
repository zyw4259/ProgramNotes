[TOC]



## 1. 引言

很多图类题目会给出“各式各样”的图表示：邻接表、边集、矩阵、甚至是自定义节点结构。你这部分代码的核心目标很明确：**先把题目给的任意图结构统一转换成一套通用图结构（点/边/图），再基于该结构实现两种遍历（BFS/DFS），最后实现拓扑排序**。其中“通用结构 + 标准算法模板”的组合，能显著降低不同题目之间的切换成本。 

------

​	

## 2. 自定义通用图结构设计

### 2.1 结构定义与设计意图

定义了三个核心对象：

- Node（点）：包含 `value`，并维护 **入度 `in` / 出度 `out`**，以及两套邻接信息：
  - `nexts`：直接邻居点列表，便于遍历（BFS/DFS）快速走边
  - `edges`：从该点出发的边列表，便于需要边权/边对象的算法（例如最小生成树、最短路等扩展方向）
- Edge（边）：包含 `weight` 与 `from/to`，表达“带权有向边/无向边都能映射”的统一模型
- Graph（图）：用 `HashMap<Integer, Node>` 管理点集，用 `HashSet<Edge>` 管理边集

这套结构的优势在于：**题目无论给的是“点 + 邻居”，还是“边列表”，最终都能汇总到 Node/Edge/Graph 上**，遍历与后续算法只依赖这一层抽象。  

```java
import java.util.ArrayList;

public class Node {
    public int value;
    public int in;
    public int out;
    public ArrayList<Node> nexts;
    public ArrayList<Edge> edges;

    public Node(int value) {
        this.value = value;
        in = 0;
        out = 0;
        nexts = new ArrayList<>();
        edges = new ArrayList<>();
    }
}
public class Edge {
    public int weight;
    public Node from;
    public Node to;

    public Edge(int weight, Node from, Node to) {
        this.weight = weight;
        this.from = from;
        this.to = to;
    }
}
import java.util.HashMap;
import java.util.HashSet;

public class Graph {
    public HashMap<Integer, Node> nodes;
    public HashSet<Edge> edges;

    public Graph() {
        nodes = new HashMap<>();
        edges = new HashSet<>();
    }
}
```

------

​	

## 3. 两种图遍历

### 3.1 BFS（宽度优先遍历）

这个 BFS 代码强调了一个非常关键的细节：**“进队时标记”**，也就是节点一旦入队就立刻加入 visited（HashSet）。这样可以从源头避免同一节点被重复入队，尤其是在存在环或多路径指向同一节点时更重要。

执行流程：

1. 起点入队，同时标记 visited
2. 弹出队头并“打印”（遍历时机在出队）
3. 将未访问过的邻居入队，并立刻标记

```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.Queue;

public class GraphBFS {

    public void graphBFS(Node start) {
        if (start == null) return;

        Queue<Node> queue = new LinkedList<>();
        HashSet<Node> hs = new HashSet<>();

        queue.add(start);
        hs.add(start);
        while (!queue.isEmpty()) {
            Node poll = queue.poll();
            System.out.print(poll.value + ", ");

            for (Node curr : poll.nexts) {
                if (!hs.contains(curr)) {
                    queue.add(curr);
                    hs.add(curr);
                }
            }
        }
    }

}
```

### 3.2 DFS（深度优先遍历）

这里的 DFS 写法很有代表性：它不是“纯粹弹栈就打印”的版本，而是采用一种更贴近递归的栈模拟策略：

- 起点入栈后立刻打印（遍历时机在入栈）
- 每次弹出 `curr` 后，找到一个未访问的 `next`：
  - 把 `curr` 再压回去（用于回溯）
  - 再压入 `next`（继续深入）
  - 打印 `next` 并标记
  - `break`：保证“沿着一条路走到底”的深度优先特性

这个“**curr 回压 + break**”是模拟递归调用栈的一种经典技巧。

```java
import java.util.HashSet;
import java.util.Stack;

public class GraphDFS {

    public void graphDFS(Node start) {
        if (start == null) return;

        Stack<Node> stack = new Stack<>();
        HashSet<Node> hs = new HashSet<>();

        stack.add(start);
        System.out.print(start.value + ", ");
        hs.add(start);
        while (!stack.isEmpty()) {
            Node curr = stack.pop();
            for (Node next : curr.nexts) {
                if (!hs.contains(next)) {
                    stack.add(curr);
                    stack.add(next);
                    System.out.print(next.value + ", ");
                    hs.add(next);
                    break;
                }
            }
        }
    }

}
```

------

​	

## 4. 拓扑排序

> 这里给了三种拓扑排序思路：**入度法（Kahn）**、**DFS 深度法**、**DFS 点次法（不推荐）**。它们针对的“题目给定结构”是 `DirectedGraphNode(label + neighbors)`，属于常见的题目输入模型。

### 4.1 方法一：入度法（Kahn / BFS 思想）

核心点是用哈希表记录入度，并维护一个“入度为 0 的队列 zero”。

实现步骤很清晰：

1. 第一次遍历把所有点入 `inDegree`，初始化入度为 0
2. 第二次遍历沿邻接关系累加入度
3. 将所有入度为 0 的点入队
4. 不断弹出队头加入结果，并把它指向的邻居入度减 1，减到 0 则入队

这本质上就是“每次选一个当前没有前置依赖的点输出”。

```java
import java.lang.reflect.Array;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.LinkedList;
import java.util.Queue;

public class TopologicalOrder1 {

    public static class DirectedGraphNode {
        public int label;
        public ArrayList<DirectedGraphNode> neighbors;

        public DirectedGraphNode(int x) {
            label = x;
            neighbors = new ArrayList<DirectedGraphNode>();
        }
    }

    public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
        HashMap<DirectedGraphNode, Integer> inDegree = new HashMap<>();
        Queue<DirectedGraphNode> zero = new LinkedList<>();
        ArrayList<DirectedGraphNode> rst = new ArrayList<>();

        for (DirectedGraphNode curr : graph) {
            inDegree.put(curr, 0);
        }

        for (DirectedGraphNode curr : graph) {
            for (DirectedGraphNode next : curr.neighbors) {
                inDegree.put(next, inDegree.get(next) + 1);
            }
        }

        for (DirectedGraphNode curr : inDegree.keySet()) {
            if (inDegree.get(curr) == 0) {
                zero.add(curr);
            }
        }

        while (!zero.isEmpty()) {
            DirectedGraphNode curr = zero.poll();
            rst.add(curr);

            for (DirectedGraphNode next : curr.neighbors) {
                inDegree.put(next, inDegree.get(next) - 1);
                if (inDegree.get(next) == 0) zero.add(next);
            }
        }

        return rst;
    }

}
```

​	

### 4.2 方法二：DFS 深度法

这套写法的关键抽象是 `Record(node, depth)`：一个点的 depth 定义为“从该点出发能走到的最大深度 + 1”。然后：

1. DFS 计算每个点的 depth（用 `record` 做记忆化，避免重复计算）
2. 将所有点的 Record 收集起来，按 depth 降序排序
3. 排完后的节点顺序作为拓扑序输出

直觉上：**越“靠前”的点，通常能到达越多后续层级，因此 depth 越大，应更先输出**。 

```java
import java.util.*;

public class TopologicalOrder2 {

    public static class DirectedGraphNode {
        public int label;
        public ArrayList<DirectedGraphNode> neighbors;

        public DirectedGraphNode(int x) {
            label = x;
            neighbors = new ArrayList<DirectedGraphNode>();
        }
    }

    public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
        if (graph == null) return null;

        HashMap<DirectedGraphNode, Record> record = new HashMap<>();

        for (DirectedGraphNode curr : graph) {
            if (!record.containsKey(curr)) {
                record.put(curr, getDepth(curr, record));
            }
        }

        ArrayList<Record> order = new ArrayList<>();
        for (DirectedGraphNode curr : graph) {
            order.add(record.get(curr));
        }
        order.sort(new NodeDepthComparator());

        ArrayList<DirectedGraphNode> rst = new ArrayList<>();
        for (Record curr : order) {
            rst.add(curr.node);
        }

        return rst;
    }

    public Record getDepth(DirectedGraphNode curr, HashMap<DirectedGraphNode, Record> record) {
        if (curr == null) return new Record(null, 0);

        int nextMathDepth = 0;
        for (DirectedGraphNode next : curr.neighbors) {
            if (record.containsKey(next)) {
                nextMathDepth = Math.max(nextMathDepth, record.get(next).depth);
            } else {
                Record nextRecord = getDepth(next, record);
                nextMathDepth = Math.max(nextMathDepth, nextRecord.depth);
                record.put(next, nextRecord);
            }
        }

        return new Record(curr, nextMathDepth + 1);
    }

    public class Record {
        public DirectedGraphNode node;
        public int depth;

        public Record(DirectedGraphNode node, int depth) {
            this.node = node;
            this.depth = depth;
        }
    }

    public class NodeDepthComparator implements Comparator<Record> {

        @Override
        public int compare(Record o1, Record o2) {
            return o2.depth - o1.depth;
        }
    }

}
```

​	

### 4.3 方法三：DFS 点次法

标注“不推荐”的原因很典型：所谓“点次”这里是从当前点出发可到达节点数的累加（含自身），然后按点次降序排。

这类指标在一些图结构上能产生“看起来合理”的序，但它**不是拓扑排序的充分条件**：可到达点多并不必然代表应该更靠前（可能存在结构使得点次相近或误导排序），因此更适合作为思路对比而非主方案。

```java
import java.util.ArrayList;
import java.util.HashMap;

public class TopologicalOrder3 {

    public static class DirectedGraphNode {
        public int label;
        public ArrayList<DirectedGraphNode> neighbors;

        public DirectedGraphNode(int x) {
            label = x;
            neighbors = new ArrayList<DirectedGraphNode>();
        }
    }

    public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
        if (graph == null) return null;

        HashMap<DirectedGraphNode, Long> record = new HashMap<>();

        for (DirectedGraphNode curr : graph) {
            if (!record.containsKey(curr)) {
                record.put(curr, getNodeCount(curr, record));
            }
        }

        graph.sort((o1, o2) -> Math.toIntExact(record.get(o2) - record.get(o1)));

        return graph;
    }

    public long getNodeCount(DirectedGraphNode curr, HashMap<DirectedGraphNode, Long> record) {
        if (curr == null) return 0;

        long count = 1L;
        for (DirectedGraphNode next : curr.neighbors) {
            if (record.containsKey(next)) {
                count += record.get(next);
            } else {
                long nextCount = getNodeCount(next, record);
                count += nextCount;
                record.put(next, nextCount);
            }
        }

        return count;
    }

}
```

------

​	

## 5. 最小生成树一：Kruskal

### 5.1 核心思路

Kruskal 解决的是**无向图最小生成树**问题：从所有边中“由小到大”尝试加入，**只要不成环就保留**，否则丢弃。实现上分成两步：
1）把所有边按权重丢进小根堆；2）用并查集维护“两个端点是否已在同一集合”，从而判环。

该实现的关键在并查集 DSU：

- `findFather` 使用栈记录路径并做路径压缩；
- `union` 采用按 size 合并（小集合挂到大集合上）；

​	

### 5.2 关键实现细节

- 边排序：`PriorityQueue<Edge>` + `WeightComparator`（权重升序）。
- 节点集合初始化：将 `graph.nodes` 中所有节点收集后构造 DSU。
- 选边循环：每弹出一条边，若两端点不在同一集合则加入答案并合并集合。

```java
import java.util.*;

public class Kruskal {

    public static Set<Edge> kruskal(Graph graph) {
        if (graph == null) return null;

        Set<Edge> rst = new HashSet<>();
        PriorityQueue<Edge> pq = new PriorityQueue<>(new WeightComparator());

        pq.addAll(graph.edges);
        ArrayList<Node> nodes = new ArrayList<>();
        for (int i : graph.nodes.keySet()) {
            nodes.add(graph.nodes.get(i));
        }

        DSU dsu = new DSU(nodes);
        while (!pq.isEmpty()) {
            Edge curr = pq.poll();
            if (!dsu.isSameSet(curr.from, curr.to)) {
                rst.add(curr);
                dsu.union(curr.from, curr.to);
            }
        }

        return rst;
    }

    public static class WeightComparator implements Comparator<Edge> {

        @Override
        public int compare(Edge o1, Edge o2) {
            return o1.weight - o2.weight;
        }
    }

    public static class DSU {
        public HashMap<Node, Node> father;
        public HashMap<Node, Integer> size;

        public DSU(List<Node> nodes) {
            father = new HashMap<>();
            size = new HashMap<>();
            for (Node curr : nodes) {
                father.put(curr, curr);
                size.put(curr, 1);
            }
        }

        public Node findFather(Node curr) {
            Stack<Node> path = new Stack<>();
            while (curr != father.get(curr)) {
                path.add(curr);
                curr = father.get(curr);
            }
            while (!path.isEmpty()) {
                father.put(path.pop(), curr);
            }

            return curr;
        }

        public boolean isSameSet(Node a, Node b) {
            return findFather(a) == findFather(b);
        }

        public void union(Node a, Node b) {
            Node aFather = findFather(a);
            Node bFather = findFather(b);
            int aSize = size.get(aFather);
            int bSize = size.get(bFather);

            if (aFather != bFather) {
                if (aSize <= bSize) {
                    father.put(aFather, bFather);
                    size.remove(aFather);
                    size.put(bFather, aSize + bSize);
                } else {
                    father.put(bFather, aFather);
                    size.remove(bFather);
                    size.put(aFather, aSize + bSize);
                }
            }
        }
    }

}
```

------

​	

## 6. 最小生成树二：Prim

### 6.1 核心思路

Prim 的视角是“从点出发长出一棵树”：

- 从某个起点开始，把该点视为已访问（代码中叫 **unlocked / 解锁**）；
- 将解锁点的所有边扔进小根堆；
- 每次取堆顶最小边，看它指向的 `to` 点是否已解锁：
  - 未解锁：保留该边、解锁 `to`，并把 `to` 的所有边继续入堆；
  - 已解锁：跳过该边。

该实现还处理了**非连通图**：对 `graph.nodes.values()` 逐个尝试作为新分量的起点，因此得到的是最小生成森林。

### 6.2 关键实现细节

一个很有辨识度的细节是“**延迟判断去重**”：入堆时不做 `contains` 检查，允许重复边进入堆，等出堆时再用 `unlocked` 判断是否应该丢弃。这样代码更简洁，且利用堆的性质把去重压力延后到“最关键的一次判定”。

```java
import java.util.*;

public class Prim {

    public static Set<Edge> prim(Graph graph) {
        PriorityQueue<Edge> leastEdges = new PriorityQueue<>(new EdgeComparator());
        Set<Edge> rst = new HashSet<>();
        HashSet<Node> unlocked = new HashSet<>();

        for (Node curr : graph.nodes.values()) {
            if (!unlocked.contains(curr)) {
                unlocked.add(curr);

                for (Edge edge : curr.edges) {
                    leastEdges.add(edge);
                }

                while (!leastEdges.isEmpty()) {
                    Edge edge = leastEdges.poll();
                    Node toNode = edge.to;

                    if (unlocked.contains(toNode)) {
                        continue;
                    }
                    rst.add(edge);
                    unlocked.add(toNode);
                    for (Edge nextEdge : toNode.edges) {
                        leastEdges.add(nextEdge);
                    }
                }
            }
        }

        return rst;
    }

    public static class EdgeComparator implements Comparator<Edge> {
        @Override
        public int compare(Edge o1, Edge o2) {
            return o1.weight - o2.weight;
        }
    }

}
```

------

​	

## 7. 最短路径：Dijkstra（朴素实现）

### 7.1 核心思路

Dijkstra 用于**非负权**图的单源最短路。该实现的整体框架是典型的“distance 表 + selected 集合”：

- `distance`：记录 start 到各节点的当前最短距离；
- `selected`：记录哪些节点已经被选为“跳板”（确定最短路，不再更新）；
- 循环过程：每次从 `distance` 表里选出一个“未 selected 且距离最小”的节点 `curr`，用它去松弛（relax）所有出边。

“朴素”体现在选点步骤：`getCurrMinDistance` 每轮通过遍历 `distance.keySet()` 找最小值，因此时间复杂度为 (O(V^2 + E)) 量级（相对堆优化版）。

​	

### 7.2 关键实现细节

- 未出现在 `distance` 的节点代表“不可达”（等价于正无穷）。
- 松弛逻辑：`curDistance = distance[curr] + edge.weight`，若 `to` 不在表中则写入，否则取更小值覆盖。

```java
import java.lang.classfile.attribute.InnerClassesAttribute;
import java.util.HashMap;
import java.util.HashSet;

public class Dijkstra {

    public static HashMap<Node, Integer> dijkastra(Node start) {
        HashMap<Node, Integer> distance = new HashMap<>();
        HashSet<Node> selected = new HashSet<>();

        distance.put(start, 0);
        Node curr = getCurrMinDistance(distance, selected);

        while (curr != null) {
            for (Edge edge : curr.edges) {
                int curDistance = distance.get(curr) + edge.weight;
                if (!distance.containsKey(edge.to)) {
                    distance.put(edge.to, curDistance);
                } else {
                    distance.put(edge.to, Math.min(distance.get(edge.to), curDistance));
                }
            }
            selected.add(curr);
            curr = getCurrMinDistance(distance, selected);
        }

        return distance;
    }

    private static Node getCurrMinDistance(HashMap<Node, Integer> distance, HashSet<Node> selected) {
        int min = Integer.MAX_VALUE;
        Node rst = null;
        for (Node node : distance.keySet()) {
            if (!selected.contains(node) && distance.get(node) < min) {
                rst = node;
                min = distance.get(node);
            }
        }
        return rst;
    }

}
```

