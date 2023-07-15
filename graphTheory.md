# 目录
- [目录](#目录)
- [802. 找到最终的安全状态](#802-找到最终的安全状态)
- [1192. 查找集群内的关键连接](#1192-查找集群内的关键连接)
- [127. 单词接龙](#127-单词接龙)
- [864. 获取所有钥匙的最短路径](#864-获取所有钥匙的最短路径)
- [1557. 可以到达所有点的最少点数目](#1557-可以到达所有点的最少点数目)
- [1857. 有向图中最大颜色值](#1857-有向图中最大颜色值)

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

# 127. 单词接龙
字典 wordList 中从单词 beginWord 和 endWord 的 转换序列 是一个按下述规格形成的序列 beginWord -> s1 -> s2 -> ... -> sk：

每一对相邻的单词只差一个字母。
 对于 1 <= i <= k 时，每个 si 都在 wordList 中。注意， beginWord 不需要在 wordList 中。
sk == endWord
给你两个单词 beginWord 和 endWord 和一个字典 wordList ，返回 从 beginWord 到 endWord 的 最短转换序列 中的 单词数目 。如果不存在这样的转换序列，返回 0 。

**要点：** 这道题由于数据量较大，在判断两个词是否只差一个字母时，使用暴力的方法会超时。所以我们需要使用无向图来进行优化。

<details>
<summary>单词接龙</summary>

```c++
class Solution {
public:
    unordered_map<string, vector<string>> graph;
    unordered_set<string> visited;
    queue<string> q;
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        wordList.push_back(beginWord);
        for (string word : wordList) {
            for (int i = 0; i < word.size(); i++) {
                string newWord = word.substr(0, i) + '*' + word.substr(i+1);
                graph[newWord].push_back(word);
            }
        }
        q.push(beginWord);
        visited.insert(beginWord);
        int step = 0;
        while (!q.empty()) {
            int size = q.size();
            step++;
            for (int i = 0; i < size; i++) {
                string word = q.front();
                q.pop();
                if (word == endWord) {
                    return step;
                }
                for (int i = 0; i < word.size(); i++) {
                    string newWord = word.substr(0, i) + '*' + word.substr(i+1);
                    for (string neighbor : graph[newWord]) {
                        if (visited.find(neighbor) == visited.end()) {
                            visited.insert(neighbor);
                            q.push(neighbor);
                        }
                    }
                }
            }
        }
        return 0;
    }
};
```
</details>

# 864. 获取所有钥匙的最短路径
给定一个二维网格 grid ，其中：

'.' 代表一个空房间
'#' 代表一堵墙
'@' 是起点
小写字母代表钥匙
大写字母代表锁
我们从起点开始出发，一次移动是指向四个基本方向之一行走一个单位空间。我们不能在网格外面行走，也无法穿过一堵墙。如果途经一个钥匙，我们就把它捡起来。除非我们手里有对应的钥匙，否则无法通过锁。

假设 k 为 钥匙/锁 的个数，且满足 1 <= k <= 6，字母表中的前 k 个字母在网格中都有自己对应的一个小写和一个大写字母。换言之，每个锁有唯一对应的钥匙，每个钥匙也有唯一对应的锁。另外，代表钥匙和锁的字母互为大小写并按字母顺序排列。

返回获取所有钥匙所需要的移动的最少次数。如果无法获取所有钥匙，返回 -1 。

**要点：** 这道题的钥匙数量即使在同一单元格也有可能不一样，所以要随着路径记录实时的钥匙数量，所以需要使用状态压缩，用6位的0和1来记录钥匙的状态。

<details>
<summary>获取所有钥匙的最短路径</summary>

```c++
class Solution {
public:
    int shortestPathAllKeys(vector<string>& grid) {
        const int MAX = 1e9;
        vector<vector<int>> d = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        int start_x = -1, start_y = -1, keys = 0;
        int m = grid.size(), n = grid[0].size();
        for(int i = 0; i < m; i++)
            for(int j = 0; j < n; j++)
                if(grid[i][j] == '@')
                    start_x = i, start_y = j;
                else if(islower(grid[i][j]))
                    keys = max(keys, grid[i][j] - 'a' + 1);
        vector<vector<vector<int>>> dp(m, vector<vector<int>>(n, vector<int>(1 << keys, MAX)));
        queue<vector<int>> q;
        dp[start_x][start_y][0] = 0;
        q.push({start_x, start_y, 0});
        while(!q.empty()){
            auto cur = q.front(); q.pop();
            int x = cur[0], y = cur[1], key = cur[2];
            if(key == (1 << keys) - 1) return dp[x][y][key];
            for(auto& dir : d){
                int nx = x + dir[0], ny = y + dir[1];
                int nkey = key;
                if(nx < 0 || nx >= m || ny < 0 || ny >= n || grid[nx][ny] == '#') continue;
                if(islower(grid[nx][ny])) nkey |= 1 << (grid[nx][ny] - 'a');
                else if(isupper(grid[nx][ny]) && !(key & (1 << (grid[nx][ny] - 'A')))) continue;
                if(dp[nx][ny][nkey] > dp[x][y][key] + 1){
                    dp[nx][ny][nkey] = dp[x][y][key] + 1;
                    q.push({nx, ny, nkey});
                }
            }
        }
        return -1;
    }
};
```
</details>

# 1557. 可以到达所有点的最少点数目
给你一个 有向无环图 ， n 个节点编号为 0 到 n-1 ，以及一个边数组 edges ，其中 edges[i] = [fromi, toi] 表示一条从点  fromi 到点 toi 的有向边。

找到最小的点集使得从这些点出发能到达图中所有点。题目保证解存在且唯一。

你可以以任意顺序返回这些节点编号。

**要点：** 所有入度为0的结点的数目即可以到达所有点的最少点数目
首先，所有入度为0的结点是一定要选的，因为不可能从其它任何结点通过任何一条路径到达入度为0的结点。接下来，如果能证明总存在一个入度为0的结点通过某一条路径到达一个入度不为0的结点，那么我们的思路就是正确的。

<details>
<summary>可以到达所有点的最少点数目</summary>

```c++
class Solution {
public:
    vector<int> findSmallestSetOfVertices(int n, vector<vector<int>>& edges) {
        vector<int> indegree(n);
        for (auto &i : edges) {
            ++indegree[i[1]];
        }
        vector<int> ret;
        for (int i = 0; i < n; ++i) {
            if (indegree[i] == 0) {
                ret.push_back(i);
            }
        }
        return ret;
    }
};
```
</details>

# 1857. 有向图中最大颜色值
给你一个 有向图 ，它含有 n 个节点和 m 条边。节点编号从 0 到 n - 1 。

给你一个字符串 colors ，其中 colors[i] 是小写英文字母，表示图中第 i 个节点的 颜色 （下标从 0 开始）。同时给你一个二维数组 edges ，其中 edges[j] = [aj, bj] 表示从节点 aj 到节点 bj 有一条 有向边 。

图中一条有效 路径 是一个点序列 x1 -> x2 -> x3 -> ... -> xk ，对于所有 1 <= i < k ，从 xi 到 xi+1 在图中有一条有向边。路径的 颜色值 是路径中 出现次数最多 颜色的节点数目。

请你返回给定图中有效路径里面的 最大颜色值 。如果图中含有环，请返回 -1 。

**要点：** 这道题是拓扑排序的变形，需要记录每个结点的入度，以及每个结点的颜色值。在拓扑排序的过程中，每次将入度为0的结点加入队列，然后更新其邻接结点的颜色值，最后判断是否有环。

<details>
<summary>有向图中最大颜色值</summary>

```c++
class Solution {
public:
    int largestPathValue(string colors, vector<vector<int>>& edges) {
        int n = colors.size();
        vector<vector<int>> g(n);
        vector<int> in(n), colorCount(n), dp(n);
        vector<vector<int>> colorFreq(n, vector<int>(26));//从0到n-1的颜色出现频率
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            ++in[e[1]];
        }
        queue<int> q;
        for (int i = 0; i < n; ++i)
            if (in[i] == 0)
                q.push(i);
        while (!q.empty()) {
            auto u = q.front(); q.pop();
            colorFreq[u][colors[u] - 'a']++;
            dp[u] = *max_element(colorFreq[u].begin(), colorFreq[u].end());
            for (auto v : g[u]) {
                if (--in[v] == 0)
                    q.push(v);
                for (int i = 0; i < 26; ++i)
                    colorFreq[v][i] = max(colorFreq[v][i], colorFreq[u][i]);//动态规划更新颜色值
            }
        }
        if (count(in.begin(), in.end(), 0) < n)
            return -1;
        return *max_element(dp.begin(), dp.end());
    }
};
```
</details>