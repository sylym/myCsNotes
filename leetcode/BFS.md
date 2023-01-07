# BFS
## 应用
- 寻找最短路径，由于水波扩散特点，bfs找到的路径一定是最短路径。
## 例题
- [（难）1036. 逃离大迷宫](https://leetcode.cn/problems/escape-a-large-maze/)

### 多源bfs

- [417. 太平洋大西洋水流问题 ](https://leetcode.cn/problems/pacific-atlantic-water-flow/)

- [286. 墙与门 ](https://leetcode.cn/problems/walls-and-gates/)

  

### 1.（难）与状态压缩结合，多维特殊BFS（如可以重复访问点，那么需要其他（高维）判断方法来防止循环）
- [847.访问所有节点的最短路径](https://leetcode.cn/problems/shortest-path-visiting-all-nodes/)
  - 特殊bfs，可以重复访问节点，防止重复访问不能仅仅记录一个点是否访问过，而应记录的是，在访问过的节点相同时，是否重复去访问一个节点。
   ```c++
  int shortestPathLength(vector<vector<int>>& graph) {
        int n=graph.size();
        queue<tuple<int,int,int>>q;//记录当前位置，点的访问状态，路径长度（这个也可以不记录在这里，可以加一层循环来计算）
        vector<vector<bool>>seen(n,vector<bool>(1<<n));//判断是否到过，用访问状态和当前点共同判断。
        for(int i=0;i<n;i++)//初始化
        {
            q.emplace(i,1<<i,0);
            seen[i][1<<i]=true;
        }
        int ans=0;
        while(!q.empty())
        {
            auto [u,mask,dist]=q.front();
            q.pop();
            if(mask==(1<<n)-1)//结束
            {
                ans=dist;
                break;
            }
            for(int v:graph[u])
            {
                int mask_v=mask|(1<<v);
                if(!seen[v][mask_v])//去重
                {
                    q.emplace(v,mask_v,dist+1);
                    seen[v][mask_v]=true;
                }
            }
        }
        return ans;
  }
   ```
- [864.获得所有钥匙的最短路径](https://leetcode.cn/problems/shortest-path-to-get-all-keys/)
    - 也是可以重复通过一个点，如果持有相同钥匙两次通过一个点那么就是重复的（与第一题这是相似第）。

