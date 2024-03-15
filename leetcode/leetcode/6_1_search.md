# 第6章  一切皆可搜索

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
* 77. combinations
```cpp
vector<vector<int>> combine(int n, int k){
    vector<vector<int>> ans;
    vector<int> comb(k, 0);
    int count = 0;
    backtracking(ans, comb, count, 1, n, k);
    return ans;
}
// 辅函数
void backtracking(vector<vector<int>>& ans, vector<int>& comb, int& count, int pos, int n, int k){
    if(count == k){
        ans.push_back(count);
        return;
    }
    for(int i=pos; i<=n; ++i){
        comb[count++] =i; //修改当前节点状态
        backtracking(ans, comb, count, i+1, n, k);//递归子节点
        --count;

    }
}
```
* 79. world search
```cpp
bool exit(vector<vector<char>>& board, string word){
    i(board.empty()) return false;
    int m = board.size(), n=board[0].size();
    vector<vector<bool>> visited(m, vector<bool>)(n, false);
    bool find = false;
    for(int i=0; i<m ; ++i){
        for(int j=0; j<n ;++j){
            backtracking(i, j, board, word, find, visited, 0);
        }
    }
    return find;
}
void backtracking(int i, int j, vector<vector<char>>& board, string& word, bool& find, vector<vector<bool>>& visited, int pos){
    if(i<0 || i>=board.size() || j<0 || j>=board[0].size()){
        return;
    }
    if(visited[i][j] || find || board[i][j] !=word[pos]){
        return;
    }
    if(pos == word.size() - 1){
        find = true;
        return;
    }
    visited[i][j] = true;
    //递归子节点
    backtracking(i+1, j, board, word, find, visited, pos + 1);
    backtracking(i-1, j, board, word, find, visited, pos + 1);
    backtracking(i, j+1, board, word, find, visited, pos + 1);
    backtracking(i, j-1, board, word, find, visited, pos + 1);
    visited[i][j] = false;

}
```
51. N-Queens(hard)
```cpp
//主函数
vector<vector<string>> solveNQueens(int n){
    vector<vector<string>> ans;
    if(n==0){
        return ans;
    }
    vector<string> board(n, string(n, '.'));
    vector<bool> column(n, false), ldiag(2*n-1, false), rdiag(2*n-1, false);
    backtracking(ans, board, column, ldiag, rdiag, 0, n);
    return ans;
}
// 辅函数
void backtracking(vector<vector<strig>> &ans, vector<string>&board, vector<bool> &column, vector<bool> &ldiag, vector<bool> &rdiag, int row, int n){
    if(row == n){
        ans.push_back(board);
        return;
    }
    for(int i=0; i<n; ++i){
        if(column[i] || ldiag[n-row+i-1] || rdiag[row+i]){
            continue;
        }
        //修改当前节点状态
        board[row][i] = 'Q';
        column[i] = ldiag[n-row+i-1] = rdiag[row+i] = true;
        //递归子节点
        backtracking(ans, board, column, ldiag, rdiag, row+1, n);
        //回改当前节点状态
        board[row][i] = ',';
        column[i] = ldiag[n-row+i-1] = rdiag[row+i] = false;
    }
}

```
## 6.4 广度优先搜索


广度优先遍历图
```cpp
void bfs(vector<vector<int>>& graph, int start){
    vector<bool> visited(graph.size(), false);
    queue<int> q;
    
    visited[start] = true;
    q.push(start);

    while(!q.empty()){
        int current = q.front();
        q.pop();
        cout << current <<" ";
        
        for(int neighbor : graph[current]){
            if(!visited[neighbor]){
                visited[neighbor] = true;
                q.push[neighbor];
            }

        }
    }
}
```
广度优先遍历树
```cpp
void bfs(TreeNode* root){
    if(root == nullptr){
        return;
    }
    queue<TreeNode*> q;
    q.push(root);

    while(!q.empty()){
        TreeNode* current = q.front();
        q.pop();
        cout << current->val << " ";

        if(current->left! = nullptr){
            q.push(current->left);
        }
        if(current->right!= nullptr){
            q.push(current->right);
        }
    }
}

```

934. shortest bridge
```cpp
vector<int> direction{-1, 0, 1,0, -1};
//主函数
int shortestBridge(vector<vector<int>>& grid){
    int m  grid.size(), n = grid[0].size();
    queue<pair<int, int>> points;
    // dfs 寻找第一个岛屿， 并把1全部赋值为2
    bool flipped = false;
    for(int i=0; i<m ;++i){
        if(flipped) break;
        for(int j=0; j<n; ++j){
            if(grid[i][j] == 1){
                dfs(points, grid, m, n, i, j);
                flipped = true;
                break;
            }
        }
    }
    //bfs 寻找第二个岛屿，并把过程经过的0赋值为2
    int x, y;
    int level = 0;
    while(!points.empty()){
        ++level;
        int n_points = points.size();
        while(n_points--){
            auto[r, c] = points.front();
            grid[r][c] = 2;
            points.pop();
            for(int k=0; k<4; ++k){
                x = r + direction[k], y = c+ direction[k+1];
                if(x>=0 && y>=0 && x<m && y<n){
                    if(grid[x][y] == 2){
                        continue;
                    }
                    if(grid[x][y] == 1){
                        return level;
                    }
                    points.push({x, y});
                    grid[x][y] = 2;
                }
            }
        }

    }
    return 0;
}

//辅函数
void dfs(queue<pair<int, int>>& points, vector<vector<int>>& grid, int m, int n, int i, int j){
    if(i<0 || j<0 || i==m || j==n || grid[i][j] = 2){
        return;
    }
    i(grid[i][j] == 0){
        points.push({i, j});
        return;
    }
    grid[i][j] = 2;
    dfs(points, grid, m, n, i-1, j);
    dfs(points, grid, m, n, i+1, j);
    dfs(points, grid, m, n, i, j-1);
    dfs(points, grid, m, n , i, j+1);
}

```
126. word Ladder II
