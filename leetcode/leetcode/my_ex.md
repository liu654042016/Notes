# 101

## 第2章 贪心

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

## 双指针

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

## 二分查找

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

## 排序算法

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

## 一切皆可搜索

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
    for(int i=n-1, i>=0; --i){
        for(int j=m-1, j>=0; --j){
            if(matrix[i][j] !=0){
                if(j<m-1){
                    dp[i][j] = min(dp[i][i], dp[i][j+1]+1);
                }
                if(n<n-1){
                    dp[i][j] = min(dp[i][j], dp[i+1][j] + 1);
                }
            }
        }
    }
    return dp;
}
```

221. Maximal Square

```cpp
int maximalSquare(vector<vector<char>>& matrix){
    if(matrix.empty() || matrix[0].empty()){
        return 0;
    }
    int m = matrix.size(), n=matrix[0].size(), max_size = 0;
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for(int i=0; i<=m ; ++i){
        for(int j=0; j<=n; ++j){
            if(matrix[i-1][j-1] == '1'){
                dp[i][j] = min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1]) + 1;
            }
        }
    }
    return max_side*max_side;

}
```

* 分割类型题

279. Perfect Squares

```cpp
int numSquares(int n){
    vector<int> dp(n+1, INT_MAX);
    dp[0] = 0;
    for(int i=1; i<=n; ++i){
        for(int j=1; j*j<=i; ++j){
            dp[i] = min(dp[i], dp[i-j*j]+1);
        }
    }
    return dp[n];
}

```

91. Decode Ways

```cpp
int numDecodings(string s){
    int n =s.length();
    if(n==0) return 0;
    int prev = s[0] - '0';
    if(!prev) return 0;
    if(n==1) return 1;
    vector<int> dp(n+1, 1);
    for(int i=2; i<=n; ++i){
        int cur = s[i-1] - '0';
        if((prev==0 || prev>2) && cur == 0){
            if(cur){
                dp[i] = dp[i-2] + dp[i-1];
            }else{
                dp[i] = dp[i-2];
            }else{
                dp[i] = dp[i-1];
            }
            dp[i] =dp[i-1];
        }
        prev = cur;
    }
    return dp[n];
}
```

139. Word Break

```cpp
bool wordBreak(string s, vector<string>& wordDict){
    int n = s.length();
    vector<bool> dp(n+1, false);
    dp[0] = true;
    for(int i =1; i<=n; ++i){
        for(const string & word : wordDict){
            int len = word.length();
            if(i>= len && s.substr(i-len, len) == word){
                dp[i] = dp[i] || dp[i-len];
            }
        }
    }
    return dp[n];
}
```

7.5 子序列问题
300. Longest Increasing Subsequence

```cpp
int lengthOfLIS(vector<int>& nums){
    int max_length =0, n=num.size();
    if(n<=1) return n;
    for(int i=0; i<n ++i){
        for(int j=0; j<i; ++j){
            if(nums[i] > nums[j]){
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
        max_length = max(max_length, dp[i]);
    }
    return max_length;
}

int lengthOfLIS(vector<int>& nums){
    int n = nums.size();
    i(n<=1) return n;
    vector<int> dp;
    dp.push_back(nums[0]);
    for(int i=1; i<n; ++i){
        if(dp.back()<nums[i]){
            dp.push_back(nums[i]);
        }else{
            auto itr  = lower_bound(dp.begin(), dp.end(), nums[i]);
            *itr = nums[i];
        }
    }
    return dp.size();
}
```

1143. Longest Commom Subsequence

```cpp
int longestCommonSubsequence(string text1, string text2){
    int m = text1.length(), n = text2.length();
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for(int i=0; i<=m; ++i){
        for(int j=0; j<=n; ++j){
            if(text1[i-1] == text2[j-1]){
                dp[i][j] = dp[i-1][j-1] + 1;
            }else{
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
    }
    return dp[m][n];
}


```

7.6 背包问题
0-1背包问题

```cpp
int knapsack(vector<int> weights, vector<int> values, int N, int M){
    vector<vector<int>> dp(N+!, vector<int>(W+1, 0));
    for(int i=1; i<=N; i++){
        int w = weights[i-1], v = values[i-1];
        for(int j=1; j<=M; j++){
            if(j>=w){
                dp[i][j] = max(dp[i-1][j], dp[i-1][j-w]+v);
            }else{
                dp[i][j] = dp[i-1][j];
            }
        }
    }
    retrun dp[N][W];
}

int knapsack(vector<int> weights, vector<int> values, int N, int W){
    vector<int> dp(W+1, 0);
    for(int i=1; i<=N; ++i){
        int w = weights[i-1], v=values[i-1];
        for(int j=W; j>=w; --j){
            dp[j] = max(dp[j], dp[j-w]+v);
        }
    }
    return dp[W];
}

```

* 完全背包问题

```cpp
int knapsack(vector<int> &weights, vector<int> &values, int N, int W){
    vector<vector<int>> dp(N+1, vector<M+1, 0>);
    for(int i=1; i<=N; i++){
        w = weights[i-1], v = value[i-1];
        for(int j=1; j<=M;j++){
            if(j>=w){
                dp[i][j] = max(dp[i-1][j], dp[i][j-w]+v)；
            }else{
                dp[i][j] = dp[i-1][j];
            }
        }
    }
    return dp[N][M];
}

int knapsack(vector<int> weights, vector<int> values, int N, int W){
    vector<int> dp(W+1, 0);
    for(int i=1; i<=N; i++){
        int w = weights[i-1], v = value[i-1];
        for(int j=2; j<=W;++j){
            dp[j] = max(dp[j], dp[j-w]+v);
        }
    }
    return dp[W];
}

```

416. Partition Equal Subset Sum

```cpp
bool canPartition(vector<int> &nums){
    int sum = accumulate(nums.begin(), nums.end(), 0);
    if(sum%2) return false;
    int target = sum /2, n=num.size();
    vector<vector<int>> dp(n+1, vector<bool>(target=1， false));
    dp[0][0] = true;
    for(int i=1; i<=N; ++i){
        for(int j=0; j<= target; ++j){
            if(j<nums[i-1]){
                dp[i][j] = dp[i-1][j];
            }else{
                dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i-1]];
            }
        }
    }
    return dp[n][target];
}

bool canPartition(vector<int> &nums){
    int sum = accumulate(nums.begin(), nums.end(), 0);
    if(sum % 2) return false;
    int target = su /2, num.size();
    vector<bool> dp(target+1, false);
    dp[0] = true;
    for(int i=1; i<=n; i+=){
        for(int j=target; j>= nums[i-1]){
            dp[j] = dp[j] || dp[j-nums[i-1]];
        }
    }
    return dp[target];
}
```

474. Ones and Zeros

```cpp
int findMaxForm(vector<string>& strs, int m, int n){
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for(const string &str:strs){
        auto [count0, count1] = count(str);
        for( int i=m; i>= count0; --i){
            for(j =n; j>=count1; --j){
                dp[i][j] = max(dp[i][j], 1+ [i-count0][j-count1]);
            }
        }
    }
    return dp[m][n];
}
//辅函数
pair<int, int> count(const string& s){
    int count0 = s.length(), count1 = 0;
    for(const char & c : s){
        if(c== '1'){
            ++count1;
            --count0;
        }
        return make_pair(count0, count1);
    }
}
```

322. Coin Changes

```cpp
dp[i][j] = min(dp[i-1][j], dp[i][j-v] + 1)
dp[j] = min(dp[j], dp[j-coin] + 1)
int coinChange(vector<vector<int>> &coins, int amount){
    if(coins.empty()) return -1;
    vector<int> dp(amount + 1, amount +1);
    dp[0]=[0];
    for(int i=1; i<= amount; ++i){
        for(const int & coin : coins){
            dp[i] min(dp[i], dp[i-coin] + 1);
        }
    }
    return dp[amount] == amount + 1 ? -1 dp[amount];
}
```

* 7.7 字符串编辑
  7.2 Edit Distance

```cpp
//
int minDistance(string word1, string word2){
    int m = word1.length(), n=word2.length();
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for(int i=0; i<=m ;i++){
        for(int j=0; j<=n; j++){
            if(i==0){
                dp[i][j] = j;
            }else if(j==0){
                dp[i][j] = i;
            }else{
                dp[i][j] = min(dp[i-1][j-1]+((word[i-1]==word2[j-1])?0:1, min(dp[i-1][j]+1, dp[i][j-1]+1));
            }
        }
    }
    return dp[m][n];
}
```

650. 2 Keys Keyboard

```cpp
dp[i] = dp[j] + dp[i/j]
int minSteps(int n){
    vector<int> dp(n+1);
    for(int i=2; i<=n; ++i){
        dp[i] = i;
        for(int j=2 ; j*j<=i; ++j){
            if(i%j==0){
                dp[i] = dp[j] + dp[i/j];
                brak;
            }
        }
    }
    return dp[n];

}
```

10. Regular Expression Matching

```cpp
bool isMatch(string s, string p){
    int m = s.size(), n = p.size();
    vector<vector<bool>> dp(m+1, vector<bool>(n+1, false));
    dp[0][0] = true;
    for(int i=1; i<=n; ++i){
        if(p[i-1] == '*'){
            dp[0][i] = dp[0][i-2];
        }
    }
    for(int i=1; i<=m ; ++i){
        for(int j=1; j<=n; ++j){
            if(p[j-1] == '.'){
                dp[i][j] = dp[i-1][j-1];
            }else if(p[j-1] != '*'){
                dp[i][j] = dp[i-1][j-1] && p[j-1] = s[i-1];
            }else if(p[j-2] != s[i-1] && p[j-2] != ','){
                dp[i][j] = dp[i][j-2];
            }else{
                dp[i][j] = dp[i][j-1] || dp[i-1][j] || dp[i][j-2];
            }
        }
    }
    return dp[m][n];
}
```

* 7.8 股票交易

121. Best Time to Buy and Sell Stock

```cpp
int maxProfit(vector<int>& prices){
    int sell =0; buy = INT_MIN;
    for(int i=0; i<prices.size(); ++i){
        buy = max(buy, -prices[i]);
        sell = max(sell, buy+prices[i]);
    }
    return sell;
}

```

## 化繁为简的分治法

* 表达式问题

241. Different Ways to Add Parentheses

```cpp
vector<int> diffWaysToCompute(string input){
    vector<int> ways;
    for(int i=0; i<input.length(); i++){
        char c = input[i];
        if(c=='+' || c=='-' || c=='*'){
            vector<int> left = diffWaysToCompute(input.substr(0, i));
            vector<int> right = diffWaysToCompute(input.substr(i+1));
            for(const int & l:left){
                for(const int &r : right){
                    switch(c){
                        case '+' : ways.push_back(l+r);break;
                        case '-' : ways.push_back(l-r);break;
                        case '*' : ways.push_back(l*r);break;
                    }
                }
            }
        }
        if(ways.empty()) ways.push_back(stoi(inputs));
        return ways;
    }
}
// dp//不会
vector<int> diffWaysToCompute(string input){
    vector<int> data;
    vector<int> ops;
    int num = 0;
    char op = ' ';
    istringstream ss(input + "+");
    while(ss>>num && ss>>op){
        data.push_back(num);
        ops.push_back(op);
    }
    int n = data.size();
}
```

## 11 妙用数据结构

* 11.2 数组

448. Find All Numbers Disappeared in an Array

```cpp
vector<int> findDisappearedNumbers(vector<int>& nums){
    vector<int> ans;
    for(const int & num:nums){
        int pos = abs(num) - 1;
        if(nums[pos]>0){
            nums[pos] = -nums[pos];
        }
    }
    for(int i=0; i<nums.size();++i){
        if(nums[i]>0){
            ans.push_back(i+1);
        }
    }
    return ans;
}
```

48. Rotate Image

```cpp
void rotate(vector<vector<int>>& matrix){
    int temp = 0, n = matrix.size() - 1;
    for(int i=0; i<= n/2; ;++i){
        for(int j=i; j<n-i;++j){
            temp = matrix[j][n-i];
            matrix[j][n-i] = matrix[i][j];
            matrix[i][j] = matrix[n-j][i];
            matrix[n-j][i] = matrix[n-i][n-j];
            matrix[n-i][n-j] = temp;
        }
    }
}

```

240. Search a 2D Matrix II

```cpp
bool searchMatrix(vector<vector<int>>& matrix, int target){
    int m = matrix.size();
    if(m==0)
        return false;
    int n = matrix[0].size();
    int i=0; j=n-1;
    while(i<m && j>=0){
        if(matrix[i][j] == target){
            return true;
        }else(if matrix[i][j] > target){
            --j
        }else{
            ++i
        }
    }
    return false;
}
```

769. Max Chunks To Make Sorted

```cpp
int maxChunksToSorted(vector<int>& arr){
    int chunks = 0; cur_max = 0;
    for(int i=0; i<arr.size(); i++){
        cur_max = max(cur_max, arr[i]);
        if(cur_max == i){
            ++chunks;
        }
    }
    return chunks;
}
```

* 11.3 栈和队列

232. Implement Queue using Stacks

```cpp
ex
MyQueue queue = new MyQueue();
queue.push(1);
queue.push(2);
queue.peek();
queue.pop();
queue.empty();

class MyQueue{
    stack<int> in, out;
public:
    MyQueue() {}
    void push(int x){
        in.push(x);
    }
    int pop(){
        in2out();
        int x = out.top();
        out.pop();
        return x;
    }
    int peek(){
        in2out();
        return out.top();
    }
    void in2out(){
        if(out.empty()){
            while(!in.empty()){
                int x = in.top();
                in.pop();
                out.push(x);
            }
        }
    }
    bool empty(){
        return in.empty() && out.empty();
    }
};  
```

155. Min Stack

```cpp
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();// return -3
minStack.pop();
minStack.top();//
minStack.getMin();//

class MinStack(){
    stack<int> s, min_s;
    public:
    MinStack() {}
    void push(int x){
        s.push(x);
        if(min_s.empty() || min_s.top()>=0){
            min_s.push(x);
        }
    }
    void pop(){
        if(!min_s.empty() && min_s.top() == s.top()){
            min_s.pop();
        }
        s.pop();
    }

    int top(){
        return s.top();
    }
    int getMin(){
        return min_s.top();
    }
}
```

20. Valid Parentheses

```cpp
bool isValid(string s){
    stack<char> parsed;
    for(int i=0; i<s.length(); ++i){
        if(s[i] == '{' || s[i] == '[' || s[i]=='(' ){
            parsed.push(s[i]);
        }else{
            if(parsed.empty()){
                return false;
            }
            char c = parsed.top();
            if(s[i] == '}' && c == '{' ||
               s[i] == ']' && c == '[' ||
               c[i] == ')' && c == '('){
                parsed.pop();
               }else{
                return false;
               }
        }
    }
    return parsed.empty();
}
```

* 11.4 单调栈

739. Daily Temperatures

```cpp
vector<int> dailyTemperatures(vector<int> & temperatures){
    int n = temperate.size();
    vector<int> ans(n);
    stack<int> indices;
    for(int i=0; i<n;++i){
        while(!indices.empty()){
            int pre_index = indices.top();
            if(temperatures[i]<= temperatures[pre_index]){
                break;
            }
            indices.pop();
            ans[pre_index] = i - pre_index;
        }
        indices.push(i);
    }
    return ans;
}
```

* 11.5 优先队列
  完全二叉树用堆来实现，每个节点的值总是大于等于子节点的值，具体实现存储是个数组

```cpp
vector<int> heap;

//上浮
void swim(int pos){
    while(pos>0 && heap[(pos-1)/2]<heap[pos]){
        swap(heap[(pos-1)/2], heap[pos]);
        pos = (pos-1)/2;
    }
}

//下沉
void sink(int pos){
    while(2*pos + 1 <= N){
        int i = 2 * pos + 1;
        if(i<N && heap[i] <heap[i+1]) ++i;
        if(heap[pos] >= heap[i] ) break;
        swap(heap[pos], heap[i]);
        pso = i;
    }
}

// 插入任意值， 把数字放到最后一位， 然后上浮
void push(int k){
    heap.push_back(k);
    swim(heap.size() - 1);
}
// 删除最大值：把最后一个数字挪到开头， 然后下沉
void pop(){
    heap[0] = heap.back();
    heap.pop_back();
    sink(0);
}

//获得最大值
int top(){
    return heap[0];
}
```

23. Merge k Sorted Lists

```cpp
struct Comp{
    bool operator()(ListNode* l1, ListNode* l2){
        return l1->val > l2->val;
    }
};
ListNode* mergeKLists(vector<ListNode*>& lists){
    if(lists.empty()) return nullptr;
    priority_queue<ListNode* , vector<ListNode*>, Comp> q;
    for(ListNode* list:lists){
        if(list){
            q.push(list);
        }
    }
    ListNode* dummy = new ListNode(0), *cur = dummy;
    while(!q.empty()){
        cur->next = q.top();
        q.pop();
        cur = cur->next;
        if(cur->next){
            q.push(cur->next);
        }
    }
    return dummy->next;
}
```

218. The Skyline Problem

```cpp
vector<vector<int>> getSkyline(vector<vector<int>>& buildings){
    vector<vector<int>> ans;
    priority_queue<pair<int>> max_heap;<//高度，右端>
    int i=0; len = buildings.size();
    int cur_x, cur_h;
    while(i<len || !max_heap.empty()){
        if(max_heap.empty() || i<len && buildings[i][0] <= max_heap.top().second){
            cur_x = buildings[i][0];
            while(i < len && cur_x == buildings[i][0]){
                max_heap.emplace(buildings[i][2], buildings[i][1]);
                ++i;
            }
        }else{
            cur_x = max_heap.top().second;
            while(!max_heap.empty() && cur_x >= max_heap.top().second){
                max_heap.pop();
            }
        }
        cur_h =(max_heap.empty()) ? 0 : max_heap.top().first;
        if(ans.empty() || cur_h != ans.back()[1]){
            ans.push_back(cur_x, cur_h);
        }
    }
    return ans;
}
```

* 11.6 双端队列

```cpp
vector<int> maxSlidingWindows(vector<int>& nums, int k){
    deque<int> dq;
    vector<int> ans;
    for(int i=0; i<nums.size(); ++i){
        if(!dq.empty() && dq.front() == i-k){
            dq.pop_front();
        }
        while(!dq.empty() && nums[dp.back()]<nums[i]){
            dq.pop_back();
        }
        dp.push_back();
        if(i>=k-1){
            ans.push_back(nums[dq.front()]);
        }
    }
    return ans;
}

```

* 11.7 哈希表

```cpp
int MyHash(const int& key, const int& tableSize){
    return ky % tableSize;
}

```

1. Two Sum
2. Longest Consecutive Sequence
3. Max Points on a Line

## 令人头大的字符串

* 12.1 引言
* 12.2 字符串比较

242. Valid Anagram

```cpp
bool isAnagram(string s, string t){
    if(s.length() != t.length())
        return false;
    vector<int i=0; i<s.length();++i>{
        ++counts[s[i] - 'a'];
        --counts[t[i] - 'a'];
    }
    for(int i=0; i<26; i++){
        if(counts[i])
            return false;
    
    }
    return true;
}
```

205. Isomorphic Strings

```cpp

```

647. Palindromic Substrings

```cpp

```

696. Cont Binary Substrings

```cpp

```

* 12.3 字符串理解

227. Basic Calculator

```cpp
int calculate(string s){
    int i=0;
    return parseExpr(s, i);
}

//辅助函数
int parseExpr(const string& s, int& i){
    char op = '+';
    long left = 0, right = 0;
    while(i<s.length()){
        if(s[i] != ''){
            long n = parseNum = (s, i);
            switch(op){
                case '+' : left += right;right=n;break;
                case '-' : left += right;right = -n;break;
                case '*' : right *= n; break;
                case '/' : right /= n ; break;
            }
            if(i<s.length()){
                op = s[i];
            }
        }
        ++i;
    }
    return left+right;
}

long parseNum(const string& s, int& i){
    long n =0;
    while(i<s.length() && isdigit(s[i])){
        n = 10 * n + (s[i++] - '0');
    }
    return n;
}
```

* 12.4 字符串匹配

28. Implement strStr

```cpp
int strStr(string haystack, string needle){
    int k =-1, n = haystack.length(), p = needle.length();
    if(p==0) return 0;
    vector<int> next(p, -1);
    callNext(needle, next);
    for(int i=0; i<n; ++i){
        while(k>-1&& needle[k+1] != haystack[i]){
            k = next[k];
        }
        if(needle[k+1] == haystack[i]){
            ++k;
        }
        if(k==p-1){
            return i-p + 1;
        }
    }
    return -1;
}

void calNext(const string &needle, vector<int> &next){
    for(int j=1, p=-1; j<needle.length(); ++j){
        while(p>-1 && needle[p+1] != needle[j]){
            p = next[p];
        }
        if(needle[p+1]==needle[j]){
            ++p;
        }
        next[j] = p;
    }
}

```

## 第 13 章  指针三剑客之一 ： 链表

* 13.1 数据结构介绍

```cpp
struct ListNode{
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(nullptr){}
};
```

* 13.2 链表的基本操作

206. Revers Linked List

```cpp
ListNode* reverseList(ListNode* head, ListNode prev=nullptr){
    if(!head){
        return prev;
    }
    ListNode* next = head->next;
    head->next = prev;
     return reverseList(next, head);
  
}
ListNode* reverseList(ListNode* head){
    ListNode *prev = nullptr, *next;
    while(head){
        next = head->next;
        head->next = prev;
        prev = head;
        head = next;
    }
    return prev;
}
```

21. Merge Two Sorted

```cpp
ListNode* mergedList(ListNode* l1, ListNode* l2){
    if(!l2){
        return l1;
    }
    if(!l1){
        return l2;
    }
    if(l1->val > l2->val){
        l2->next = mergeTwoLists(l1, l2->next);
        return l2;
    }
    l1->next = mergeTwoList(l1->next, l2);
    return l1;
}
ListNode* mergeTwoLists(ListNode * l1, ListNode* l2){
    ListNode *dummy = new ListNode(0), *node = dummy;
    while(l1  && l2){
        if(l1->val >= l2->val){
            node->next = l1;
            l1 = l1->next;
        }else{
            node->next = l2;
            l2 = l2->next;
        }
        node = node - >next;
    }
    node->next = l1 ? l1:l2;
    return dummy->next;
}
```

24. Swap Nodes in Pairs

```cpp
ListNode* swapPairs(ListNode* head){
    ListNode *p = head, *s;
    if(p && p->next){
        s = p->next;
        p->next = s->next;
        s->next = p;
        head = s;
        while(p->next && p->next->next){
            s = p->next->next;
            p->next->next = s->next;
            s->next = p->next;
            p->next = s;
            p = s->next;
        }
    }
    return head;
}
```

* 13.3 其他链表技巧

160. Intersection Of Two Linked Lists

```cpp
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB){
    ListNode *l1=headA, *l2=headB;
    while(l1!=l2){
        l1 = l1?l1->next : HeadB;
        l2 = l2?l2->next : headA;
    }
    return l1;
}
```

234. Palindrome Linked List

```cpp
bool isPalindrome(ListNode* head){
    if(!head || !head->next){
        return true;
    }
    ListNode *slow = head, *fast = head;
    while(fast->next && fast->next->next){
        slow = slow->next;
        fast = fast->next->next;
    }
    slow->next = reverseList(slow->next);
    slow = slow->next;
    while(slow){
        if(head->val != slow->val){
            return false;
        }
        head = head->next;
        slow = slow->next;
    }
    return true;
}
ListNode* reverseList(ListNode* head){
    ListNode *prev = nullptr *next;
    while(head){
        next = head->next;
        head->next = prev;
        prev = head;
        head = next;
    }
    return prev;
}

```

## 第 14 章 指针三剑客之二： 树

* 14.1 数据结构介绍

```cpp
struct TreeNode{
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x):val(x), left(NULL), right(NULL) {}
}
```

* 14.2 树的递归

104. Maximum Depth of Binary Tree

```cpp
int maxDepth(TreeNode* root){
    return root? 1+max(maxDepth(root->left), maxDepth(root->right)) : 0;
}
```

110. Balanced Binary Tree

```cpp
bool isBalanced(TreeNode* root){
    return helper(root) != -1;
}
//辅函数
int helper(TreeNode* root){
    if(!root){
        return 0;
    }
    int left = helper(root->left),right = helper(root->right);
    if(left == -1 || right ==-1 || abs(left-right)>1){
        return -1;
    }
    return 1+max(left,right);
}

```

543. Diameter of Binary Tree

```cpp
int diameterOfBinaryTree(TreeNode* root){
    int diameter = 0;
    helper(root, diameter);
    return diameter;
}

int helper(TreeNode* node, int& diameter){
    if(!node)
        return 0;
    int l = helper(node->left, diameter);
    int r = helper(node->right, diameter);
    diameter = max(l+r, diameter);
    return max(l,r)+1;
}
```

437. Path Sum III

```cpp
int pathSum(TreeNode* root, int sum){
    return root ? pathSumStartWithRoot(root, sum)+pathSum(root->left, sum) + pathSum(root->right, sum) : 0;
}

//辅函数 
long long pathSumStartWithRoot(TreeNode* root, long long sum){
    if(!root)
        return 0;
    long long count =root->val == sum ? 1 : 0;
    count += pathSumStartWithRoot(root->left, sum-root->val);
    count +=pathSumStartWithRoot(root->right, sum-root->val);
    return count;
}

```

101. Symmetric Tree

```cpp
//主函数
bool isSymmetric(TreeNode* root){
    return root ? isSymmetric(root->left, root->right) : true;
}
//辅函数
bool isSymmetric(TreeNode* left, TreeNode* right){
    if(!left && !right){
        return true;
    }
    if(!left || !right){
        return false;
    }
    if(left->val != right->val){
        return false;
    }
    return isSymmetric(left->left, right->right) && isSymmetric(left->right, right->left);
}
```

1110. Delete Node And Return Froest

```cpp
vector<TreeNode*> delNodes(TreeNode* root, vector<int>& to_delete){
    vector<TreeNode*> forest;
    unordered_set<int> dict(to_delete.begin(), to_delete.end());
    root = helper(root, dict, forest);
    if(root){
        forest.push_back(root);
    }
    return forest;
}
//辅函数
TreeNode* helper(TreeNode* root, unordered_set<int>& dict, vector<TreeNode*> & forest){
    if(!root){
        return root;
    }
    root->left = helper(root->left, dict, forest);
    root->right = helper(root->right, dict, forest);
    if(dict.count(root->val)){
        if(root->left){
            forest.push_back(root->left);
        }
        if(root->right){
            forest.push_back(root->right);
        }
        root = NULL;
    }
    return root;
}
```

* 14.3 层次遍历

637. Average of Levels in Binary

```cpp
vector<double> averageOfLevels(TreeNode* root){
    vector<double> ans;
    if(!root){
        return ans;
    }
    queue<TreeNode*> q;
    q.push(root);
    while(!q.empty()){
        int count = q.size();
        double sum = 0;
        for(int i=0; i<count; i++){
            TreeNode* node = q.front();
            q.pop();
            sum+=node->val;
            if(node->left){
                q.push(node->left);
            }
            if(node->right){
                q.push(node->right);
            }
        }
        ans.push_back(sum / count);
    }
    return ans;
}
```

### 14.4 前中后序遍历

```cpp
void preorder(TreeNode* root){
    visit(root);
    preorder(root->left);
    preorder(root->right);
}
void inorder(TreeNode* root){
    inorder(root->left);
    visit(root);
    inorder(root->right);
}
void postorder(TreeNode* root){
    postorder(root->left);
    postorder(root->right);
    visit(root);
}
```

105. Construct Binary Tree from Preorder and Inorder Travel

```cpp
TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder){
    if(preorder.empty()){
        return nullptr;
    }
    unordered_map<int, int> hash;
    for(int i=0; i<preorder.size();++i){
        hash[inorder[i]] = i;
    }
    return buildTreeHelper(hash, preorder, 0, preorder.size()-1, 0);
}
//辅函数
TreeNode* buildTreeHelper(unordered_map<int, int> &hash, vector<int>& preorder, int s0, int e0, int s1){
    if(s0 > e0){
        return nullptr;
    }
    int mid = preorder[s1], index = hash[mid], leftLen = index -s0 - 1;
    TreeNode* node = new TreeNode(mid);
    node->left = buildTreeHelper(hash, preorder, s0, index-1, s1+1);
    node->right = buildTreeHelper(hash, preorder, index+1, e0, s1+2+leftLen);
    return node;
}

```

114. Binary Tree Preorder Traversal

```cpp
vector<int> preorderTravelsal(TreeNode* root){
    vector<int> ret;
    if(!root){
        return ret;
    }
    stack<TreNode*> s;
    s.push(root);
    while(!s.empty()){
        TreeNode* node = s.top();
        s.pop();
        ret.push_back(node->val);
        if(node->right){
            s.push(node->right);
        }
        if(node->left){
            s.push(root->left);
        }
    }
    return ret;
}
```

### 14.5 二叉查找树

```cpp
template <class T>
class BST{
    struct Node{
        T data;
        Node* left;
        Node* right;
    };

    Node* root;

    Node* makeEmpty(Node* t){
        if(t==NULL) return NULL;
        makeEmpty(t->left);
        makeEmpty(t->right);
        delete t;
        return NULL;
    }

    Node* insert(Node* t, T x){
        if(t==NULL){
            t = new Node;
            t->data = x;
            t->left = t->right = NULL;
        }else if(x<t->data){
            t->left = insert(t>left, x);
        }else if(x > t->data){
            t->right = insert(t->right, x);
        }
        return t;
    }

    Node* find(Node* t, T x){
        if(t==NULL) return NULL;
        if(x<t->data) return find(t->left, x);
        if(x>t->data) return find(t->right, x);
        return t;
    }

    Node* findMin(Node* t){
        if(t==NULL || t->left == NULL) return t;
        return findMin(t->left);
    }

    Node** remove(Node* t, T x){
        Node* temp;
        if(t==NULL) return NULL;
        else if(x<t->data) t->left = remove(t->left, x);
        else if(x>t->data) t->right = remove(t->right,x);
        else if(t->left && t->right){
            temp = findMin(t->right);
            t->data = temp->data;
            t->right = remove(t->right, t->data);
        }else{
            temp = t;
            if(t->left == NULL) t=t->right;
            else if(t->right == NULL) t = t->left;
            return temp;
        }
        return t;
    }
public:
    BST():root(NULL){}
    ~BST(){
        root = makeEmpty(root);
    }
    void insert(T x){
        insert(root, x);
    }
    void remove(T, x){
        remove(root, x);
    }

}
```

99. Recover Binary Search Tree

```cpp
void recoverTree(TreeNode* root){
    TreeNode *mistake1 = nullpte, mistake2 = nullptr,*prev = nullptr;
    inorder(root, mistake1, mistake2, prev);
    if(mistake1 && mistake2){
        int temp = mistak1->val;
        mistake1->val = mistake2->val;
        mistake2->val = temp;
    }
}

void inorder(TreeNode* root, TreeNode*& mistake1, TreeNode*& mistake2, TreeNode*& prev){
    if(!root){
        return;
    }
    if(root->left){
        inorder(root->left, mistake1,mistake2, prev);
    }
    if(prev && root->val < prev->val){
        if(!mistake1){
            mistake1 = prev;
            mistake2 = root;
        }else{
            mistake2 = root;
        }
        cout << mistake1->val;
        cout << mistake2->val;
    }
    prev = root;
    if(root->right){
        inorder(root->right, mistake1, mistake2, prev);
    }
}

```

## 第 15 章图

### 15.1 数据结构介绍

邻接矩阵
邻接链表
