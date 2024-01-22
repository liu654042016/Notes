[13.1 回溯算法](https://www.hello-algo.com/chapter_backtracking/backtracking_algorithm/)

* 给定一棵二叉树，搜索并记录所有值为 7 的节点，请返回节点列表。

```cpp
void preOrder(TreeNode *root){
    if(root == nullptr){
        return;
    }
    if(root->val == 7){
        res.push_back(root);
    }
    preOrder(root->left);
    preOrder(rot->right);
}

```

## 13.1.1 尝试与回退

* 在二叉树中搜索所有值为 7 的节点，请返回根节点到这些节点的路径。

```cpp
void preOrder(TreeNode *root){
    if(root == nullptr){
        return;
    }
    //尝试
    path.push_back(root);
    if(root->val == 7){
        //记录解
        res.push_back(path);
    }
    preOrder(root->left);
    preOrder(root->right);
    //回退
    path.pop_back();
}
```

## 13.1.2 剪枝

* 在二叉树中搜索所有值为 7 的节点，请返回根节点到这些节点的路径，并要求路径中不包含值为 3 的节点。

```cpp
void preOrder(TreeNode *root){
    //剪枝
    if(root == nullptr || root->val ==3){
        return;
    }
    //尝试
    path.push_back(root);
    if(root->val==7)
        res.push_back(path);
    preOrder(root->left);
    preOrder(root->right);
    //回溯
    path.pop_back();
}
```

## 13.1.3 框架代码

```cpp
// state表示问题的当前状态， choices表示当前状态下可以做出的选择
//回溯法算法框架
void backtrack(State *state, vector<Choice *> &choices, vector<State *> &res){
    // 判断是否为解
    if(isSolution(state)){
        //记录解
        recordSolution(state, res);
        return;
    }
    //遍历所有选择
    for(Choice choice : choices){
        // 剪纸判断选择是否合法
        if(isValid(state, choice)){
            // 剪枝: 判断选择是否合法
            makeChoice(state, choice);
            backtrack(state, choices, res);
            //回退：撤销选择，恢复到之前的状态
            undoChoice(state, choice);
        }

    }
}

bool isSolution(vector<TreeNode *> &state){
    return !state.empty() && state.back()->val == 7;
}

//记录解
void recordSolution(vector<TreeNode *> &state, vector<vector<TreeNode *>> &res){
    res.push_back(state);
}
// 判断当前状态下， 选择是否合法
bool isValid(vector<TreeNode *> &state, TreeNode *choice){
    return choice != nullptr && choice->val !=3;
}
// 更新状态
void makeChoice(vector<TreeNode *> &state, TreeNode *choice){
    state.push_back(choice);
}
//恢复状态
void undoChoice(vector<TreeNode *> &state, TreeNode *choice){
    state.pop_back();
}

//回溯算法： 例题三
void backtrack(vector<TreeNode *> &state, vecor<TreeNode *> &choices, vector<vector<TreeNode *>> &res){
    //检查是否为解
    if(isSolution(state)){
        //记录解
        recordSolution(state, res);
    }
    //遍历所有选择
    for(TreeNode *choice : choices){
        // 剪枝
        if(ifValid(state, choice)){
            //尝试
            makeChoice(state, choice);
            vector<TreeNode *> nextChoices{choice->left, choice->right};
            backtrack(state, nextChoices, res);
            //回溯
            undoChoice(state, choice);
        }
    }
}
```

## 13.2 全排列问题

### 13.2.1 无相等元素的情况

* 重复选择剪枝

```cpp
//回溯法 全排列
void backtrack(vector<int> &state, const vector<int> &choice, vector<bool> &selected, vector<vector<int>> &res){
    //当前长度等于元素数据时， 记录解
    if(state.size() == choice.size()){
        res.pus_back(state);
        return;
    }
    //遍历所有选择
    for(int i=0; i<choices.size(); i++){
            int choice = choices[i];
            //剪枝
            if(!selected[i]){
            selected[i] = true;
            state.push_back(choice);
            // 进行下一轮选择
            backtrack(state, choices, selected, res);
            //回退
            selected[i] = false;
            state.pop_back;
            }
    }
}
//全排列
vector<vector<int>> permutationsI(vector<int> nums){
    vector<int> state;
    vector<bool> selected(num.size(), false);
    vector<vector<int>> res;
    backtrack(state, nums, selected, res);
    return res;
}

```

### 13.2.2 考虑相等元素的情况

1. 相等元素的剪枝

```cpp
void backtrack(vector<int> &state, const vector<int> &choice, vector<bool> &selected vector<vector<int>> &res){
    //当状态长度等于元素数量时，记录解
    if(state.size() == choices.size()){
        res.push_back(state);
        return;
    }
    //遍历所有选择
    unordered_set<int> duplicated;
    for(int i= 0 ; i<choices.size() ; i++){
        int choice = choices[i];
        // 剪枝
        if(!selected[i] && duplicated.find(choice) == duplicated.end()){
             duplicated.emplace(choice);
             selected[i] = true;
             state.push_back(choice);
             //进行下一轮选择
             backtrack(state, choices, selected, res);
             //回溯
             selected[i] = false;
             state.pop_back();
        }
    }
}
// 全排列 II
vector<vector<int>> permutationsII(vector<int> nums){
    vector<int> state;
    vector<bool> selected(nums.size(), false);
    vector<vector<int>> res;
    backtrack(state, nums, selcted, res);
    return res;

}

```

## 13.3 子集和问题
### 13.3.1 无重复元素的情况
1. 参考全排列解法
```cpp
//子集和问题


void backtrack(vector<int> &state, int target, int total, vector<int> &choice, vector<vector<int>> &res){
    // 子集和等于 target 时，记录集
    if(total == target){
        res.push_back(state);
        return;
    }
    // 遍历所有选择
    for(size_t i = 0; i< choices.size(); i++){
        //剪枝：若子集和超过 target，则跳过该选择
        if(total + choices[i] > target; i++){
            continue;
        }
        // 尝试：作出选择，更新元素 total
        state.push_back(choices[i]);
        //进行下一轮选择
        backtrack(state, target, total + choices[i], choices, res);
        // 回退：撤销选择，恢复之前的状态
        state.pop_back();
    }
}
//求解子集和
vector<vector<int>> subsetSumINaive(vector<int> &nums, int target){
    vector<int> state; //状态
    int total = 0;     //子集和
    vector<vector<int>> res; //结果列表
    backtrack(state, target, total, nums, res);
    return res;
}

```
### 13.3.2 考虑重复元素的情况
1. 相等元素的剪枝
```cpp
void backtrack(vector<int> &state, int target, vector<int> &choices, int start, vector<vector<int>> &res){
    //子集和等于 target 时，记录解
    if(target ==0 ){
        res.push_back(state);
        return;
    }
    //遍历所有选择
    // 剪枝二: 从start开始遍历，避免生成重复子集
    // 剪枝三： 从start 开始遍历，避免重复统一选择
    for(int i = start, i<choices.size() ;i++){
        // 剪枝一 :，若子集和超过 target, 则直接结束循环
        //只是因为数组已排序，后边元素更大， 子集和一定超过 target
        if(target - choices[i] < 0){
            break;
        }
        // 剪枝四：如果该元素与左边相等， 说明搜索分支重复， 直接跳过
        if(i>start, && choices[i] == choices[i-1]){
            continue;
        }
        //尝试 做出选择 更新 target,start
        state.push_back(choices[i]);
        //进行一轮选择
        backtrack(state, target - choices[i], choices, i+1, res);
        //回退 
        state.pop_back();
    }
}
vector<vector<int>> subsetSunII(vector<int> &nums, int target){
    vector<int> state;
    sort(nums.begin(), nums.end());
    int start = 0;
    vector<vector<int>> res;
    backtrack(state, target, nums, start, res);
    return res;
}
```

## 13.4 n 皇后问题
1. 逐行放置策略
```cpp
// n 皇后
void backtrack(int row, int n, vector<vector<string>> &state, vector<vector<string>> &res, vector<bool> &cols, vector<bool> &diages1, vector<bool> &diags2){
    //放置所有行时， 记录解
    if(row == n){
        res.push_back(state);
        return;
    }
    //遍历所有列
    for(int col=0; col <n ; col++){
        // 计算该格子对应的主对焦线和次对角线
        int diag1 = row -col + n -1;
        int diag2 = row + col;

        // 剪枝 不允许该格子所在列,主对角线，次对角线上存在皇后
        if(!cols[col] && !diags1[diag1] && !diags2[diag2]){
            //尝试：将皇后放置在该格子
            state[row][col] = "Q";
            cols[col] = diags1[diag1] = diags2[diag2] = true;
            //放置下一行
            backtrack(row+1, n, state, res, cols, diags1, diags2);
            // 回溯:将该格子恢复为空位
            state[row][col] = "#";
            cols[col] = diags1[diag1] = diags2[diag2] = false;
        }
    }
}
vector<vector<vector<string>>> nQueens(int n){
    //初始化 n*n 大小的棋盘，其中 "Q"代表皇后， ‘#’代表空位
    vector<vector<string>> state(n, vector<string>(n, "#"));
    vector<bool> cols(n, false);
    vector<bool> diags1(2*n-1, false);
    vector<bool> diags2(2*n-1, false);
    vector<vector<vector<string>>> res;
    backtrack(0, n, state, res, cols, diags1, diags2);
    return res;
}
```
