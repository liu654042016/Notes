[分治法](https://www.hello-algo.com/chapter_divide_and_conquer/divide_and_conquer/)
## 12.1 分治算法
## 12.2 分治搜素策略
### 1.基于分治实现二分查找
```cpp
int dfs(vector<int> &nums, int target, int i, int j){
    //若区间为空， 代表无目标元素返回-1
    if(i>j)
        return -1;
    int m =(i+j)/2;
    if(nums[m] < target)
        return dfs(nums, target, m+1, j);
    else if(nums[m]>target)
        return dfs(nums, target, i, m-1);
    else
        return m;
}

```
## 12.3 构建树问题
* 代码实现
```cpp
TreeNode *dfs(vector<int> &preorder, unordred_map<int, int> &inorderMap, int i, int l , int r){
    if(r-l <0)
        return NULL;
    //初始化根节点
    TreeNode *root = new TreeNode(preorder[i]);
    //查询m, 从而划分左右子树
    int m =inorderMap[preorder[i]];
    //子问题 构建左子树
    root->left = dfs(preorder, inorderMap, i+1, l, m-1);
    //子问题 构建右子树
    root->right = dfs(preorder, inorderMap, i+1+m=1, m+1, r);
    return root;
}
//构建二叉树
TreeNode *buildTree(vector<int> &preorder, vector<int> &inorder){
    unorder_map<int, int> inorderMap;
    for(int i=0; i< inorder.size();++i){
        inorderMap[inorder[i]] = i;
    }
    TreeNode *root = dfs(preorder, inorderMap, 0, 0, inorder.size()-1);
    return root;
}

```
## 12.4 汉诺塔问题
```cpp
// 移动一个圆盘
void move(vector<int> &src, vector<int> &tar){
    int pan = src.back();
    src.pop_back();
    tar.push_back(pan);
}
void dfs(int i, vector<int> &src, vector<int> &buf, vector<int> &tar){
    if(i==1){
        move(src, tar);
        return;
    }
    dfs(i-1, src, tar, buf);
    move(src, tar);
    dfs(i-1, buf, src, tar);
}
void solveHanota(vector<int> &A, vector<int> &buf, vector<int> &C){
    int n = A.size();
    dfs(n, A, B, C);
}
```