# [搜索](https://www.hello-algo.com/chapter_searching/)
## 10.1 二分查找
```cpp

int binarySearch(vector<int> &nums, int target){
    int i=0, j=num.size() -1;
    while(i<=j){
        int m = i+(j-i)/2;
        if(num[m]<target)
            i = m+1;
        else if(num[m]>target)
            i = m-1;
        else
            return m;
    }
    return -1;
}
```
### 10.1.1 区间表示方法
```cpp
//二分查找，
int binarySearch(vector<int> &nums, int target){
    int i=0, j= nums.size();
    while(i<j){
        int m= i+(j-1)/2;
        if(nums[m]<target)
            i = m+1;
        else if (nums[m]>target)
            j = m;
        else 
            return m;
    }
    return -1;

}

```
## 10.2 二分查找插入点

### 10.2.1 无重复元素的情况
```cpp
int binarySearchInsertionSimple(vector<int> &nums, int target){
    int i=0, j = nums.size()-1;
    while(i<=j){
        int m = i + (j-1)/2;
        if(nums[m]<target>)
            i = m+1;
        else if(nums[m]>target)
            j = m-1;
        else
            return m;//找到target 返回插入点m
    }
    return i;
}
```
### 10.2.2 存在重复元素的情况
```cpp
int binarySearchInsertion(vector<int> &mums, int target){
    int i=0; j = nums.size()-1;
    while(i<=j){
        int m = i+(j-i)/2;
        if(nums[m] < target){
            i = m+1;
        }else if(nums[m] > target){
            j = m-1;
        }else{
            j = m-1;
        }
    }
    return i;
}
```
## 10.3 二分查找边界
### 10.3.1 查找左边界
```cpp
int binarySearchLetfEdge(vector<int> &nums, int target){
    int i = binarySearchInsertion(nums, target);
    if(i==nums.size() || nums[i]!=target)
        return -1;
    return i;
}
```
### 10.3.2 查找有边界
1. 复用查找左边界
```cpp
int binarySearchRightEdgr(vector<int> &nums, int target){
    int i = binarySearchInsertion(nums, target+1);
    int j = i-1;
    if(j==-1 || nums[j]!=target)
        return -1;
    return j;
}
```

## 10.4 哈希优化策略 

### 10.4.1 线性查找：以时间换空间
```cpp
vector<int> twoSumBruteFore(vector<int>, int target){
    int size = nums.size();
    for(int i=0; i<size-1;i++){
        for(int j=i+1; j<size; j++)
            if(nums[i] + nums[j] == target)
                return{i, j};

    }
    return {};
}

```
### 10.4.2 哈希查找：以空间换时间

```cpp
vector<int> twoSumHashTable(vector<int>, int target){
    int size = nums.size();
    unordered_map<int, int> dic;
    for(int i =0 ; i <size; i++ ){
        if(dict.find(target-nums[i]) !=dic.end())
            return dic{target-nums[i], i};
        return {};
    } 
}
```
## 10.5 重识搜索算法