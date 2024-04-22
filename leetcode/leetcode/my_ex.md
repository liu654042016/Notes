## 101

### 贪心

* 分配问题

455. Assign Cookies

```cpp
int findContentChildren(vector<int>& children, vector<int>& cookies){
    sort(children.begin(), children.end());
    sort(cookies.begin(), cookies.end());
    int child = 0, cookie = 0;
    while(child<child.size() && cookie<cookies.size()){
        if(children[child]<= cookies[cookie]) ++child;
        ++cookie;
    }
    return cookie;
}
```

135. Candy

```cpp
int findMinCandy(vector<int>& score){
    vector<int> Candy(score.size() , 1);
    for(int i=1; i<Candy.size(); i++){
        if(score[i]>score[i-1]) Candy[i] = Candy[i-1]+1;
    }
    for(int i=Candy.size()-2; i>=0; i--){
        if(score[i]>score[i=1]) num[i] = max(num[i], num[i-1]+1);
    }
    return accumulate(Candy.begin(), Candy.end(), 0);
}
```

* 区间问题

435. Non-overlapping Intervals

```cpp
int eraseOverlapIntervals(vector<vector<int>>& intervals){
    if(intervals.empty()){
        return 0;
    }
    int n = intervals.size();
    sort(intervals.begin(), intervals.end(),[](vector<int>& a,vector<int>& b){return a[1]<b[1]});
    int removed = 0; prev = intervals[0][1];
    for(int i=1; i<n; ++i){
        if(intervals[i][0] <prev){
            ++removed;
        }else{
            prev = intervals[i][1];
        }
    }
    return removed;
}
```

### 双指针

#### Two Sum

167. Two Sum II - Input array is sorted

```cpp
vector<int> twoSum(vector<int>& numbers, int target){
    int l =0; r = numbers.size()-1;
    while(l<r){
        sum = numbers[l] + numbers[r];
        if(sum==target) break;
        if(sum<target) l++;
        else --r;
    }
    return vector<int>{l+1, r+1};
}
```

#### 归并两个默认数组

88. Merge Sorted Array

```cpp
Merge(vector<int>& nums1,int m, vector<int>& nums2, int n){
    int num_1 = m-1, num_2 = l-1;
    while( num_1>=0 && num_2>=0){
        if(nums2[num_2]>nums1[num_1]){
            nums1[num_2+num_1+1] = nums2[num_2];
            num_2--;
        }else{
            nums1[num_2+num_1+1] = nums1[num_1];
            num_1--;
        }
    }
    while(n>=0){
        nums1[num_1--] = nums2[num_2--];
    }
}
```

#### 快慢指针

142. Linked List Cycle II

```cpp
ListNode *detectCycle(ListNode *head){
    ListNode *slow = head, *fast = head;
    do{
        if(!fast || !fast->next) return nullptr;
        fast = fast->next->next;
        slow = slow->next;
    }while(fast!=slow)

    fast = head;
    while(fast!=head){
        slow = slow->next;
        fast = fast->next;
    }
    return fast;
}
```

#### 滑动窗口

76 Minimum Window Substring

```cpp
string minWindow(string s, string T){
    vector<int> chars(128, 0);
    vector<bool> flag(128, false);
    //先统计 T 中字符情况
    for(int i=0; i<T.size(); i++){
        flag[T[i]] = true;
        ++chars[T[i]];
    }
    // 移动滑动窗口， 不断统计数据
    int cnt =0; l=0, min_l =0, min_size = S.size() + 1;
    for(int r=0; r<S.size(); ++r){
        if(flag[S[r]]){
            if(--chars[S[r]]>=0){
                ++cnt;
            }
            //若目前滑动窗口已经包含全部字符串
            //尝试将1右移，在不影响结果的情况下获取最短字符串
            while(cnt == T.size()){
                if(r-l+1<min_size){
                    min_l = l;
                    min_size = r-l+1;
                }
                if(flag[S[l]] && ++chars[S[l]]>0){
                    --cnt;
                }
                ++l;
            }
        }
    }
    return min_size > S.size()? "":S.substr(min_l, min_size);
}
```

### 二分查找

* 求开方

69. Sqrt(x)
    [开闭区间](https://www.cnblogs.com/spacerunnerZ/p/17216926.html)

```cpp
//左闭右闭
int sqrt(int a){
    if(a==0) return a;
    int l=1, r=a, mid, sqrt;
    while(l<=r){
        mid = l+(r-1)/2;
        sqrt = a/mid;
        if(sqrt==mid){
            return mid;
        }else if(mid > sqrt){
            r = mid -1;
        }else{
            l = mid + 1;
        }
    }
    return r;
}
//左开右闭
int sqrt(int a){

}
```

* 查找区间

34. Find First and Last Position of Element in Sorted Array

```cpp
//主函数
vector<int> searchRange(vector<int>& nums, int target){
    if(nums.empty()) return vector<int> {-1, -1};
    int lower = lower_bound(nums, target);
    int upper = upper_bound(nums, target) - 1;
    if(lower == nums.size() || nums[lower] != target){
        return vector<int>{-1, -1};
    }
    return vector<int> {lower, upper};
}
//辅函数
int lower_bound(vector<int> &nums, int target){
    int l=0; r = nums.size(),mid;
    while(l<r){
        mid = l + (r-1)/2;
        if(nums[mid] > = target){
            r = mid;
        }else{
            l = mid + 1;
        }
    }
    return l;
}

int upper_bound(vector<int> &nums, int target){
    int l=0; r=nums.size(), mid;
    while(l<r){
        mid = 1+(r-1)/2;
        if(nums[mid] > target){
            r = mid;
        }else{
            l = mid + 1;
        }
    }
    return l;
}

```

* 旋转数组查找数字

81. Search in Rotated Sorted Array II

```cpp
bool search(vector<int>& nums, int target){
    int l=0, r=nums.size()-1;
    while(l<=r){
        int mid = l + (r-1)/2;
        if(nums[mid] == target){
            return true;
        }
        if(nums[l] == nums[mid]){
            ++l;
        }else if(nums[r] == nums[mid]){
            --r;
        }else if(nums[mid] < nums[r]){
            //右区间是增序的
            if(target > nums[mid] && target <= nums[r]){
                l = mid + 1;
            }else{
                r = mid -1;
            }
        }else{
            // 左区间是增序的
            if(target >= nums[l] && target<nums[mid]){
                r = mid -1;
            }else{
                l = mid + 1;
            }
        }
    }
    return false;
}
```

### 排序算法

* 常用排序算法

1. 快速排序

```cpp
void quick_sort(vector<int> &nums, int l, int r){
    if(l+1>=r){
        return;
    }
    int first = l, last = r-1, key = nums[first];
    while(first<last){
        while(first<last && key<=nums[last]){
            last--;
        }
        swap(key, nums[last]);
        while(first<last && key>=nums[first]){
            first ++;
        }
        swap(nums[last], nums[first]);
    }
    nums[first] = key;
    quick_sort(nums, l, first);
    quick_sort(nums, first+1, r);
}
```

2. 归并排序

```cpp
void merge_sort(vector<int> &nums, int l, int r, vector<int> &temp){
    if(l+1 >=r) return;
    //divide
    int m = l + (r-1)/2;
    merge_sort(nums, l, m, temp);
    merge_sort(nums, m, r, temp);
    //conquer
    int p=l, q=m, i==1;
    while(p<m || q<r){
        if(q>=r || (p<m && nums[p]<=nums[q])){
            temp[i++] = nums[p++];
        }else{
            temp[i++] = nums[q++];
        }
    }
    for(i=l; i<>r ; ++i){
        nums[i] = temp[i];
    }
}
```

3. 插入排序

```cpp
voi insertion_sort(vector<int> &nums, int n){
    for(int i=0; i<n; i++){
        for(int j=i; j<n;j++){
            if(int j=i; j>0 && nums[j]<nums[j-1]; j--){
                swap(nums[j], nums[j-1]);
            }
        }
    }
}

```

4. 冒泡排序

```cpp
void bubble_sort(vector<int> &nums, int n){
    bool swapped;
    for(int i=1; i<n; i++){
        swapped = false;
        for(int j=1; j<n-i+1; ++j){
            if(nums[j] < nums[j-1]){
                swap(nums[j], nums[j-1]);
                swapped = true;
            }
        }
        if(!swapped) break;
    }
}
```

5. 选择排序

```cpp
void selection_sort(vector<int> &nums, int n){
    int mid;
    for(int i=0; i<n-1; ++i){
        mid = i;
        for(int j= i+1, j<n; ++j){
            if(nums[j]<nums[mid]){
                mid = j;
            }
        }
        swap(nums[mid], nums[i]);
    }
}

```

* 快速选择

215. Kth Largest Element in an Array

```cpp
int findKthLargest(vector<int> nums, int k){
    int l=0; r =nums.size()-1; target = nums.size() - k;
    while(l<r){
        int mid = quickSelection(nums, l, r);
        if(mid == target){
            return nums[mid];
        }
        if(mid < target){
            l = mid + 1;
        }else{
            r = mid - 1;
        }
    }
    return nums[l];
}
//
int quickSelection(vector<int>& nums, int l, int r){
    int i = l+1, j = r;
    while(true){
        while(i<r && nums[i]<=nums[l]){
            ++i;
        }
        while(l<j && nums[j]>=nums[l]){
            --j;
        }
        if(i>=j){
            break;
        }
        swap(nums[i], nums[j]);
    }
    swap(nums[l], nums[j]);
    return j;
}

```

* 桶排序

347. Top K Frequent Elements

```cpp
vector<int> topKFrequent(vector<int>& nums, int k){
    unordered_map<int, int> counts;
    int max_count = 0;
    for(const int & num : nums){
        max_count = max(max_count, ++counts[num]);
    }
    vector<vector<int>> buckers(max_count+1);
    for(const auto &p : counts){
        buckets[p.second].push_back(p.first);
    }

    vector<int> ans;
    for(int i=max_count; i>=0 && ans.size()<k; --i){
        for(const int & num : buckers[i]){
            ans.push_back(num);
            if(ans.size()==k){
                break;
            }
        }
    }
    return ans;

}
```

### 一切皆可搜索

* 算法解释
* 深度优先搜索

695. Max Area of Island

```cpp
vector<int> direction{-1, 0, 1, 0, -1};
int maxAreaOfIsland(vector<vector<int>>& grid){
    int m = grid.size(), n=m?grid[0].size():0, local_area, area=0, x, y;
    for(int i=0; i<m; ++i){
        for(int j=0; j<n; ++j){
            if(grid[i][j]){
                local_area = 1;
                grid[i][j] = 0;
                stack<pair<int, int>> island;
                island.push({i, j});
                while(!island.empty()){
                    auto [r,c] = island.top();
                    island.pop();
                    for(int k=0; k<4; ++k){
                        x = r + direction[k], y = c + direction[k+1];
                        if(x>=0 && x<m && y>=0 && y<n & grid[x][y] == 1){
                            grid[x][y] = 0;
                            ++local_area;
                            island.push({x,y});
                        }
                    }
                }
                area = max(area, local_area);
            }
        }
    }
    return area;
}


//
vector<int> direction{-1, 0, 1, 0, -1};
int maxAreaOfIsland(vector<vector<int>>& grid){
    if(grid.empty() || grid[0].empty()) return 0;
    int max_area = 0;
    for(int i=0; i<grid.size(); ++i){
        for(int j=0; j<grid[0].size(); ++j){
            if(grid[i][j] == 1){
                max_area = max(max_area, dfs(grid, i, j));
            }
        }
    }
    return max_area;
}
//辅函数
int dfs(vector<vector<int>>& grid, int r, int c){
    if(grid[r][c] == 0) return 0;
    grid[r][c] = 0;
    int x, y, area = 1;
    for(int i=0; i<4; ++i ){
        x = r + direction[i], y = c+direction[i+1];
        if(x>=0 && x<grid.size() && y>=0 && y<grid[0].size()){
            area += dfs(grid, x, y);
        }
    }
    return area;
}

```

547. Friend Circle

```cpp
int findCircleNum(vector<vector<int>>& friend){
    int n = friends.size(), count = 0;
    vector<bool> visited(n, false);
    for(int i=0; i<n; ++i){
        if(!visited[i]){
            dfs(friends, i, visited);
            ++count;
        }
    }
    return count;
}
// 辅函数
void dfs(vector<vector<int>>& friends, int i, vector<bool>& visited){
    visited[i] = true;
    for(int k=0; k<friend.size(); ++k){
        if(friends[i][k] == 1 && !visited[k]){
            dfs(friends, k, visited);
        }
    }
}
```

417. Pacific Atlantic Water Flow

```cpp
vector<int> direction{-1, 0, 1, 0, -1};
//主函数
vector<vector<int>> pacificAtlantic(vector<vector<int>>& matrix){
    if(matrix.empty() || matrix[0].empty())
        return {};
    vector<vector<int>> ans;
    int m = matrix.size(), n=matrix[0].size();
    vector<vector<bool>> can_reach_p(m, vector<bool>(n, false));
    vector<vector<bool>> can_reach_a(m, vector<bool>(n, false));
    for(int i=0; i<m; ++i){
        dfs(matrix, can_reach_p, i, 0);
        dfs(matrix, can_reach_a, i, n-1);
    }
    for(int i=0; i<n; ++i){
        dfs(matrix, can_reach_p, 0, i);
        dfs(matrix, can_reach_a, m-1, i);
    }
    for(int i=0; i<m ; i++){
        for(int j=0; j<n; j++){
            if(can_reach_p[i][j] && can_reach_a[i][j])
                ans.push_back(vector<int>{i, j});
        }
    }
    return ans;
}

//辅函数
void dfs(const vector<vector<int>>& matrix, vector<vector<bool>>& can_reach, int r, int c){
    if(can_reach[r][c])
        return;
    can_reach[r][c] = true;
    int x,y;
    for(int i=0; i<4; ++i){
        x = r + direction[i], y = c+ direction[i+1];
        if(x>=0 && x<matrix.size() && y>=0 && y<matrix[0].size() && matrix[r][c] <= matrix[x][y]){
            dfs(matrix, can_reach, x, y);
        }
    }
}
```

* 回溯法
  排列，组合，选择类

46. Permutations

```cpp
vector<vector<int>> permute(vector<int>& nums){
    vector<vector<int>> ans;
    backtracking(nums, 0, ans);
    return ans;
}
//辅函数
void backtracking(vector<int> &nums, int level, vector<vector<int>> &ans){
    if(level == nums.size()-1){
        ans.push_back(nums);
        return;
    }
    for(int =level; i<nums.size(); i++){
        swap(nums[i], nums[level]);// 修改当前节点状态
        backtracking(nums, level+1, ans);//递归子节点
        swap(nums[i], nums[level]);//回改当前节点状态
    }
}
```

77. Combinations

```cpp
vector<vector<int>> combine(int n, int k){
    vector<vector<int>> ans;
    vector<int> comb(k, 0);
    int count = 0;
    backtracking(ans,comb, count, 1, n, k);
    return ans;
}
//辅函数
void backtracking(vector<vector<int>>& nums, vector<int>& comb, int& count, int pos, int n, int k){
    if(count == k){
        ans.push_back(comb);
        return;
    }
    for(int i=pos; i<=n; ++i){
        comb[count++] = i;// 修改当前节点状态
        backtracing(ans, comb, count, i+1, n, k);//递归子节点
        --count;//回改当前节点状态
    }
}
```

79. Word Search

```cpp
bool exit(vector<vector<char>>& board, string word){
    if(board.empty()) return false;
    int m = board.size(), n = board[0].size();
    vector<vector<bool>> visited(m, vector<bool>(n, false));
    bool find = false;
    for(int i=0; i<m; ++i){
        for(int j=0; j<n; ++j){
            backtracking(i, j, board, word, find, visited, 0);
        }
    }
    return find;
}

void backtracking(int i, int j, vector<vector<char>>& board, string& word, bool& find, vector<vector<bool>>& visited, int pos){
    if(i<0 || i>= board.size() || j<0 || j>=board[0].size())
        return;
    if(visited[i][j] || find || board[i][j]!=word[pos]){
        return;
    }
    if(pos == word.size() - 1){
        find = true;
        return;
    }
    visited[i][j] = true;
    // 递归子节点
    backtracking(i+1, j, board, word, find, visited, pos+1);
    backtracking(i-1, j, board, word, find, visited, pos+1);
    backtracking(i, j+1, board, word, find, visited, pos+1);
    backtracing(i, j-1, board, word, find , visited, pos + 1);
    visited[i][j] = false;
}

```

51. N-Queens

```cpp
//主函数
vector<vector<string>> solveNQueens(int n){
    vector<vector<string>> ans;
    if(n==0){
        return ans;
    }
    vector<string> board(n, string(n,'.'));
    vector<bool> column(n, false), ldiag(2*n-1, false), rdiag(2*n-1, false);
    backtracking(and, board, column, ldiag, rdiag, 0, n);
    return ans;
}
//辅函数
void backtracking(vector<vector<string>> &ans, vector<string> &board, vector<bool> &column, vector<bool> &ldiag, vector<bool> &rdiag, int row, int n){
    if(row==n){
        ans.push_back(board);
        return;
    }
    for(int i=0; i<n; ++i){
        if(column[i] || ldiag[n-row+i-1] || rdiag[row+i]){
            continue;
        }
        //修改当前节点状态
        board[row][i] = 'Q';
        column[i] = ldiag[n-row + i -1] = rdiag[row+i] = true;
        //递归子节点
        backtracking(ans, board, column, ldiag, rdiag, row=l, n);
        //回改当前节点状态
        board[row][i] = ',';
        column[i] = ldiag[n-row+i-1] = rdiag[row=i] = false;
    }
}

```

* 广度优先搜索
  bfs

```cpp
void bfs(TreeNode* root){
    if(root==NULL){
        return;
    }
    queue<TreeNode*> q;
    q.push(root);
    while(!q.empty()){
        TreeNode* current = q.front();
        q.pop();
        cout<< current->val<<end;
        if(current->left !=NULL){
            q.push_back(current->left);
        }
        if(current->right!=NULL){
            q.push_back(current->right);
        }
    }
}
```

934. Shortest Bridge

```cpp
vector<int> direction{-1, 0, 1, 0, 1};
int shortestBridge(vector<vector<int>>& grid){
    int m = grid.sie(), n = grid[0].size();
    queue<pair<int, int>> points;
    //dfs 寻找一个岛屿，并把 1 全部设置为 2 
    bool flipped = false;
    for(int i=0; i<m; ++i){
        if(flipped) break;
        for(int j=0; j<n; j++){
            if(grid[i][j] == 1){
                dfs(points, grid, m, n, i, j)
                flipped = true;
                break;
            }
        }
    }
    //bfs 寻找第二个岛屿， 并把过程中经过的 0 赋值为 2
    int x,y;
    int level = 0;
    while(!points.empty()){
        ++level;
        int n_points = points.size();
        while(n_points--){
            auto[r, c] = points.front();
            grid[r][c] = 2;
            points.pop();
            for(int k = 0; k<4; ++k){
                x = r + direction[k], y=c+direction[k+1];
                if(x>=0 && y>=0 && x<m && y<n){
                    if(grid[x][y] == 2){
                        continue;
                    }
                    if(grid[x][y] == 1){
                        return level;
                    }
                    points.push({x, y};
                    grid[x][y] = 2;
                }
            }
        }
    }
    return 0;
}
// 辅函数
void dfs(queue<pair<int, int>>& points, vector<vector<int>>& grid, int m, int n, int i, int j){
    if(i<0 || j<0 || i==m || j==n || grid[i][j] ==2 ){
        return;
    }
    if(grid[i][j] == 0){
        points.push({i, j});
        return;
    }
    grid[i][j] = 2;
    dfs(points, grid, m, n, i-1, j);
    dfs(points, grid, m, n, i+1, j);
    dfs(points, grid, m, n, i, j-1);
    dfs(points, grid, m, n, i, j+1);
}

```

126. Word Ladder II

```cpp
vector<vector<string>> findLadders(string, beginWord, string endWord, vector<string>& wordList){
    vector<vector<string>> ans;
    unordered_set<string> dict;
    for(const auto &w : wordList){
        dict.insert(w);
    }
    if(!dict.count(endWord)){
        return ans;
    }
    dict.erase(beginWord);
    dict.erase(endWord);
    unordered_set<string> q1{beginWord}, q2{endWord};
    unordered_map<string, vector<string>> next;
    bool reversed = false, found = false;
    while(!q1.empty()){
        unordered_set<string> q;
        for(const auto &w : q1){
            string s = w;
            for(size_t i=0; i<s.size(); i++){
                char ch = s[i];
                for(int j=0; j<26; j++){
                    s[i] = j + 'a';
                    if(q2.count(s)){
                        reversed? next[s].push_back(w):next[w].push_back(s);
                        found = true;
                    }
                    if(dict.count(s)){
                        reversed?next[s].push_back(w):next[w].push_back(s);
                        q.insert(s);
                    }
                }
                s[i] = ch;
            }
        }
        if(found){
            break;
        }
        for(const auto &w:q){
            dict.erase(w);
        }
        if(q.size()<=q2.size()){
            q1 = q;
        }else{
            reversed = !reversed;
            q1 = q2;
            q2 = q;
        }
    }
    if(found){ 
        vector<string> path = {beginWord};
        backtracking(beginWord, endWord, next, path, ans);
    }
    return ans;
}   
//辅函数
void backtracking(const string &src, const string &dst, unordered_map<string, vector<string>> &next, vector<string>& path,vector<vector<string>> &ans)
{
    if(src == dst){
        ans.push_back(path);
        return;
    }
    for(const auto &s : next[src]){
        path.push_back(s);
        backtracking(s, dst, next, path, ans);
        path.pop_back();
    }
}

```

7. 深入浅出动态规划

* 算法解释
* 基本动态规划： 一维

70. Climbing Stairs

```cpp
int climbStairs(int n){
    if(n<=2) return n;
    vector<int> dp(n+1, 1);
    for(int i=2; i<=n; ++i){
        dp[i] = dp[i-1] + dp[i-2];
    }
    return dp[n];
}

int climbStairs(int n){
    if(n<=2) return n;
    for(int i=2; i<n; ++i){
        cur = pre1 + pre2;
        pre2 = pre1;
        pre1 = cur;
    }
    return cur;
}
```

198. House Robber

```cpp
int rob(vector<int>& nums){
    if(nums.empty()) return 0;
    int n = nums.size();
    vector<int> dp(n+1, 0);
    dp[1] = nums[0];
    for(int i=2; i<=n ; ++i){
        dp[i] = max(dp[i-1], nums[i-1] + dp[i-2]);
    }
    return dp[n];
}
```

413. Arithmetic Slices

```cpp
int numberOfArithmeticSlices(vector<int>& nums){
    int n= nums.size();
    int(n<3) return 0;
    vector<int> dp(n, 0);
    for(int i=0; i<n; ++i){
        if(nums[i] - nums[i-1] == nums[i-1] -nums[i-2]){
            dp[i] = dp[i-1] + 1;
        }
    }
    return accumulate(dp.begin(), dp.end(), 0);
}
```

* 基本动态规划： 二维

64. Minimum Path Sum

```cpp
int minPathSum(vector<vector<int>>& grid){
    int m = grid.size(), n=grid[0].size();
    vector<vector<int>> dp(m, vector<int>(n, 0));
    for(int i=0; i<m ;i++){
        for(int j=0; j<n; j++){
            if(i==0 && j==0){
                dp[i][j] = grid[i][j];
            }else if(i==0){
                dp[i][j] = dp[i][j-1] + grid[i][j];
            }else if(j==0){
                dp[i][j] = dp[i-1][j] + grid[i][j];
            }else{
                dp[i][j] = min(dp[i-1][j], dp[i][j-1]+grid[i][j]);
            }
        }
    }
    return do[m-1][n-1];
}

int minPathSum(vector<vector<int>>& grid){
    int m= grid.size(), n=grid[0].size();
    vector<int> dp(n, 0);
    for(int i=0; i<m; ++i){
        for(int j=0; j<n; ++j){
            if(i==0 && j==0){
                dp[j] = grid[i][j];
            }else if(i==0){
                dp[j] = dp[j-1] + grid[i][j];
            }else if(j==0){
                dp[j] = dp[j] + grid[i][j];
            }else{
                dp[j] = min(dp[j], dp[j-1] + grid[i][j]);
            }
        }
    }
    return dp[n-1];
}

```

542. 01 Matrix(Medium)

```cpp
vector<vector<int>> updateMatrix(vector<vector<int>>& matrix){
    if(matrix.empty()) return {};
    int n=matrix.size(), m=matrix[0].size();
    vector<vector<int>> dp(n, vector<int>(m, INT_MAX-1));
    for(int i=0; i<n ; ++i){
        for(int j=0; j<m; ++j){
            if(matrix[i][j]==0){
                dp[i][j] = 0;
            }else{
                if(j>0){
                    dp[i][j] = min(dp[i][j], dp[i][j-1] + 1);
                  
                }
                if(i>0){
                    dp[i][j] = min(dp[i][j], dp[i-1][j] + 1);
                }
            }
        }
    }
}
```
