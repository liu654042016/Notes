#  第6章  一切皆可搜索
## 6.1 算法解释
* 深度优先搜索
* 广度优先搜索
## 6.2 深度优先搜索
695 Max Arrea of island
```cpp
vector<int> direction{-1, 0, 1, 0, -1};
int maxAreaOfIsland(vector<vector<int>>& grid){
    int m = grid.size(), n = m ? grid[0].size() : 0, local_area, area = 0, x,y;
    for(int i=0; i<m; ++i){
        for(int j=0; j<n; j++){
            if(grid[i][j]){
                local_area = 1;
                grid[i][j]=0;
                stack<pair<int, int>> island;
                while(!island.empty()){
                    auto [r,c] =island.top();
                    island.pop();
                    for(int k=0; k<4; ++k){
                        x = r + direction[k], y = c + direction[k+1];
                        if(x>=0 && x< m && y>=0 && y< n && grid[x][y]==1){
                            grid[x][y] = 0;
                            ++local_area;
                            island.push({x, y});
                        }
                    }
                }
                area = max(area, local_area);
            }
        }
    }
    return area;
}

// 递归方法
int maxAreaOfIsland(vector<vector<int>>& grid){
    if(grid.empty() || grid[0].empty()) return 0;
    int max_area = 0;
    for(int i=0; i<grid.size(); ++i){
        for(int j=0; j<grid[0].size(); j++){
            if(grid[i][j] == 1){
                max_area = max(max_area, dfs(grid, i, j));
            }
        }
    }
    return max_area;
}

int dfs(vector<vector<int>>& grid, int r, int c){
    if(grid[r][c]==0) return 0;
    grid[r][c] = 0;
    int x,y,area = 1;
    for(int i=0; i<4; ++i){
        x = r + direction[i], y = c + direction[i+1];
        if(x>=0 && x<grid.size() && y>=0 && y<grid[0].size()){
            area + = dfs(grid, x, y);
        }
    }
    return area;
}

```
547 Friend Circles
```cpp
//主函数
int findCircleNum(vector<vector<int>>& friends){
    int n = friends.sie(), count = 0;
    vector<bool> visited(n, false);
    for(int i=0; i<n ;++i){
        if(!visited[i]){
            dfs(friend, i, visited);
            ++count;
        }
    }
    return count;
}
//辅函数
void dfs(vector<vector<int>>& friends, int i, vector<bool>& visited){
    visited[i] = true;
    for(int k=0; k<friend.size(); ++k){
        if(friend[i][k] == 1 && !visited[k]){
            dfs(friends, k, visited);
        }
    }
}
```
* 417 Pacific Atlantic Water Flow
```cpp
vector<int> direction{-1,0, 1, 0, -1};
//主函数
vector<vector<int>> pacificAtlantic(vector<vector<int>>& matrix){
    if(matrix.empty() || matrix[0].empty())
        return 0;
    vector<vector<int>> ans;
    int m = matrix.size(), n = matrix[0].size();
    vector<vector<bool>> can_reach_p(m, vector<bool>(n, false));
    vector<vector<bool>> can_reach_a(m, vector<bool>(n, false));
    for(int i=0; i<m ;i++){
        dfs(matrix, can_reach_p, i, 0);
        dfs(matrix, can_reach_a, i, n-1;)
    }
    for(int i=0; i<n; ++i){
        dfs(matrix, can_reach_p, 0, i);
        dfs(matrix, can_reach_a, m-1,i);
    }
    for(int i=0; i<m; ++i){
        for(int j=0; j<n ; ++j){
            if(can_reach_p[i][j] && can_reach_a[i][j])
                ans.push_back(vector<int>{i, j});
        }
    }
    return ans;
}

//辅函数
vod dfs(const vector<vector<it>>& matrix, vector<vector<int>>& can_reach, int r, int c){
    if(can_reach[r][c])
        return;
    can_reach[r][c] = true;
    int x,y;
    for(int i=0; i<4 ; ++i){
        x = r + direction[i], y = c + direction[i+1];
        if(x>=0 && x<matrix.size() && y >=0 && y<matrix[0].size() && matrix[r][c] <= matrix[x][y]){
            dfs(matrix, can_reach, x, y);
        }
    }
}
```
## 6.3 回溯法
* 46 Permutations(Medium)
```cpp
//主函数
vector<vector<int> permute(vector<int>& nums)>{
    vector<vector<int>> ans;
    backtracking(nums, 0, ans);
    return ans;
}
//辅函数
void backtracking(vector<int> &nums, int level, vector<vector<int>> &ans){
    if(level == num.size() - 1){
        ans.push_back(nums);
        return;
    }
    for(int i =level; i<nums.size(); ++i ){
        swap(nums[i], nums[level]);//修改当前节点状态
        backtracking(nums, level+1, ans);//递归子节点
        swap(nums[i], nums[level]);// 回改当前节点状态
    }
}

```