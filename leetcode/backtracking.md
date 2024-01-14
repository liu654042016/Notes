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
## 13.3 