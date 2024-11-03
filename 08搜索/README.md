# 搜索

|                                         |        |                                                              |
| --------------------------------------- | ------ | ------------------------------------------------------------ |
| 岛屿数量（深搜、宽搜）                  | medium | https://leetcode.cn/problems/number-of-islands/description/  |
| 词语阶梯（宽搜、图、哈希表）            | medium | https://leetcode.cn/problems/word-ladder/description/        |
| 词语阶梯2（记录路径的宽搜、图、哈希表） | hard   | https://leetcode.cn/problems/world-ladder-ii/description/    |
| 火柴棍摆正方形（回溯深搜、位运算）      | medium | https://leetcode.cn/problems/matchsticks-to-square/description/ |
| 收集雨水2（带优先级的宽度优先搜索、堆） | hard   | https://leetcode.cn/problems/trapping-rain-water-ii/description/ |

# 岛屿数量（深搜、宽搜）

## DFS

```c++
void DFS(std::vector<std::vector<int>>&mark,
                std::vector<std::vector<char>>&grid,
                int x, int y){
    mark[x][y]=1;//标记已搜寻的位置
    static const int dx[]={-1,1,0,0};//方向数组
    static const int dy[]={0,0,-1,1};
    for(int i=0;i<4;i++){
        int newx = dx[i] +x;
        int newy = dy[i] +y;
        if(newx<0||newx>=mark.size()||
            newy<0||newy>=mark[newx].size()){//超过数组边界时
                continue;
            }
        if(mark[newx][newy]==0&&grid[newx][newy]=='1'){
            DFS(mark,grid,newx,newy);
        }
    }
}
```

## BFS

```c++
void BFS(std::vector<std::vector<int>>&mark,
                std::vector<std::vector<char>>&grid,
                int x, int y){
    static const int dx[]={-1,1,0,0};//方向数组
    static const int dy[]={0,0,-1,1};
	std::queue<std::pair<int,int>>Q;//宽度优先搜索队列
    Q.push(std::make_pair(x,y));
    mark[x][y]=1;//将(x,y)push进入队列,并作标记
	while(!Q.empty()){
        x=Q.front().first;//取队列头部元素
        y=Q.front().second;
        Q.pop();//弹出队头元素
        for(int i=0;i<4;i++){
            int newx = dx[i] +x;//拓展四个方向
            int newy = dy[i] +y;
            if(newx<0||newx>=mark.size()||//忽略越过地图边界的位置
                newy<0||newy>=mark[newx].size()){//超过数组边界时
                    continue;
                }
            if(mark[newx][newy]==0&&grid[newx][newy]=='1'){//如果当前位置未搜索，且为陆地
                Q.push(std::make_pair(newx,newy));//将新位置push进入队列
                mark[newx][newy]=1;//并作标记
            }
		}
    }           
}
```

---

```c++
class Solution {
public:
    void DFS(std::vector<std::vector<int>>&mark,
                std::vector<std::vector<char>>&grid,
                int x, int y){
        mark[x][y]=1;//标记已搜寻的位置
        static const int dx[]={-1,1,0,0};//方向数组
        static const int dy[]={0,0,-1,1};
        for(int i=0;i<4;i++){
            int newx = dx[i] +x;
            int newy = dy[i] +y;
            if(newx<0||newx>=mark.size()||
                newy<0||newy>=mark[newx].size()){//超过数组边界时
                    continue;
                }
                if(mark[newx][newy]==0&&grid[newx][newy]=='1'){
                    DFS(mark,grid,newx,newy);
                }
        }
    }
    int numIslands(vector<vector<char>>& grid) {
        int island_num=0;
        std::vector<std::vector<int>> mark;
        for(int i=0;i<grid.size();i++){
            mark.push_back(std::vector<int>());
            for(int j=0;j<grid[i].size();j++)
            {
                mark[i].push_back(0);
                
            }
        }
        for(int i=0;i<grid.size();i++){
            for(int j=0;j<grid[i].size();j++)
            {
                if(mark[i][j]==0&&grid[i][j]=='1'){
                    DFS(mark,grid,i,j);//或BFS
                    island_num++;
                }
            }
        }  
        return island_num;
    }
};
```

# 收集雨水2（带优先级的宽度优先搜索、堆）

```c++
class Solution {
public:
    int trapRainWater(std::vector<std::vector<int>>& heightMap) {
        int m = heightMap.size();
        if (m == 0) return 0;
        int n = heightMap[0].size();
        if (n == 0) return 0;

        std::vector<std::vector<bool>> visited(m, std::vector<bool>(n, false));//是否被搜索过
        std::priority_queue<std::pair<int, std::pair<int, int>>, std::vector<std::pair<int, std::pair<int, int>>>, std::greater<std::pair<int, std::pair<int, int>>>> pq;//最小堆

        // 将边界加入优先队列
        for (int i = 0; i < m; i++) {//左右边界
            pq.push({heightMap[i][0], {i, 0}});//pq.push({int,pair})
            visited[i][0] = true;
            pq.push({heightMap[i][n - 1], {i, n - 1}});
            visited[i][n - 1] = true;
        }
        for (int j = 1; j < n - 1; j++) {//上下边界
            pq.push({heightMap[0][j], {0, j}});
            visited[0][j] = true;
            pq.push({heightMap[m - 1][j], {m - 1, j}});
            visited[m - 1][j] = true;
        }

        int ans = 0;
        int dirs[4][2] = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};//方向矩阵
        while (!pq.empty()) {
            auto top = pq.top();
            pq.pop();
            for (auto dir : dirs) {
                int newX = top.second.first + dir[0];
                int newY = top.second.second + dir[1];
                if (newX >= 0 && newX < m && newY >= 0 && newY < n &&!visited[newX][newY]) {
                    visited[newX][newY] = true;
                    ans += std::max(0, top.first - heightMap[newX][newY]);//当前点的高度高于拓展点时
                    pq.push({std::max(top.first, heightMap[newX][newY]), {newX, newY}});//积水后水平面升高
                }
            }
        }
        return ans;
    }
};
```

