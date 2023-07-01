# 目录
- [目录](#目录)
- [802. 找到最终的安全状态](#802-找到最终的安全状态)
- [1192. 查找集群内的关键连接](#1192-查找集群内的关键连接)

# 802. 找到最终的安全状态
有一个有 n 个节点的有向图，节点按 0 到 n - 1 编号。图由一个 索引从 0 开始 的 2D 整数数组 graph表示， graph[i]是与节点 i 相邻的节点的整数数组，这意味着从节点 i 到 graph[i]中的每个节点都有一条边。

如果一个节点没有连出的有向边，则该节点是 终端节点 。如果从该节点开始的所有可能路径都通向 终端节点 ，则该节点为 安全节点 。

返回一个由图中所有 安全节点 组成的数组作为答案。答案数组中的元素应当按 升序 排列。

**要点：** 通过这道题主要复习邻接表的结构和dfs的写法。

<details>
<summary>找到最终的安全状态</summary>

```c++
class Solution {
public:
    struct Node{
        int id;
        Node* next;
    };
    void addEdge(vector<Node*>& edgeList, int start, int end){
        Node* temp = new Node;
        temp->id = end;
        temp->next = edgeList[start];
        edgeList[start] = temp;
        return;
    }
    int safeNode[10001]={0};
    vector<int> eventualSafeNodes(vector<vector<int>>& graph) {
        vector<Node*> edgeList(graph.size());  
        for(int i=0;i<graph.size();i++){
            if(graph[i].size() == 0){
                safeNode[i] = 2;
                continue;
            } 
            for(int j=0;j<graph[i].size();j++){
                addEdge(edgeList, i, graph[i][j]);
            }           
        }
        vector<int> ans;
        for(int i=0;i<graph.size();i++){
            if(dfs(edgeList, i))
                ans.push_back(i);
        }
        //删除动态内存
        for(int i=0;i<graph.size();i++) {
            Node* temp = edgeList[i];
            while(temp) {
                Node* nextNode = temp->next;
                delete temp;
                temp = nextNode;
            }
        }
        return ans;
    }
    bool dfs(vector<Node*>& edgeList, int n){
        if(safeNode[n] > 0){
            return safeNode[n] == 2;
        }
        safeNode[n] = 1;
        Node* temp = edgeList[n];
        while(temp){
            if(!dfs(edgeList, temp->id)){
                safeNode[temp->id] = 0;
                return false;
            }
            temp = temp->next;
        }
        safeNode[n] = 2;
        return true;
    }
};
```
</details>

# 1192. 查找集群内的关键连接
力扣数据中心有 n 台服务器，分别按从 0 到 n-1 的方式进行了编号。它们之间以 服务器到服务器 的形式相互连接组成了一个内部集群，连接是无向的。用  connections 表示集群网络，connections[i] = [a, b] 表示服务器 a 和 b 之间形成连接。任何服务器都可以直接或者间接地通过网络到达任何其他服务器。

关键连接 是在该集群中的重要连接，假如我们将它移除，便会导致某些服务器无法访问其他服务器。

请你以任意顺序返回该集群内的所有 关键连接 。

**要点：** 这道题是强连通图的tarjan算法。关于tarjan算法，可以具体参考[这篇博客](https://blog.csdn.net/qaqwqaqwq/article/details/119884138)。

<details>
<summary>查找集群内的关键连接</summary>

```c++
class Solution {
public:
    struct Node{
        int id;
        Node* next;
    };
    vector<vector<int>> bridges;
    vector<int> ids, low;
    int id;
    void addEdge(vector<Node*>& edgeList, int start, int end){
        Node* temp = new Node;
        temp->id = end;
        temp->next = edgeList[start];
        edgeList[start] = temp;
        return;
    }
    vector<vector<int>> criticalConnections(int n, vector<vector<int>>& connections) {
        vector<Node*> edgeList(n);
        for(int i=0;i<connections.size();i++){
            addEdge(edgeList, connections[i][0], connections[i][1]);
            addEdge(edgeList, connections[i][1], connections[i][0]);
        }
        id = 0;
        ids.resize(n, -1);
        low.resize(n, -1);
        for (int i = 0; i < n; i++) {
            if (ids[i] == -1) {
                dfs(edgeList, i, -1);
            }
        }
        return bridges;
    }
    void dfs(vector<Node*>& edgeList, int at, int parent){
        ids[at] = low[at] = id++;
        for (Node* node = edgeList[at]; node != NULL; node = node->next) {
            if (node->id == parent) continue;//跳过自环
            if (ids[node->id] == -1) {
                dfs(edgeList, node->id, at);
                low[at] = min(low[at], low[node->id]);
                if (ids[at] < low[node->id]) {
                    bridges.push_back({at, node->id});
                }
            } else {
                low[at] = min(low[at], ids[node->id]);//反向边u节点得到v节点的dfs时间戳
            }
        }
    }
};
```
</details>
在上面这份代码中，id代表了时间戳，ids是dfs搜索的时间，low代表了当前节点能够访问到的最早的节点的时间戳。如果当前节点的ids小于low，则说明当前节点是关键连接。