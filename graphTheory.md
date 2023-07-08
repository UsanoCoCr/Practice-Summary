# 目录
- [目录](#目录)
- [802. 找到最终的安全状态](#802-找到最终的安全状态)
- [1192. 查找集群内的关键连接](#1192-查找集群内的关键连接)
- [127. 单词接龙](#127-单词接龙)
- [864. 获取所有钥匙的最短路径](#864-获取所有钥匙的最短路径)

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