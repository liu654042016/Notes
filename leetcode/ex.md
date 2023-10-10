快速排序
    void quick_sort(vector `<int>` &nums, int l, int r){
        if(l+1>=r){
            return;
        }
        int first = l, last = r-1, key=nums[first];
        while(first < last){
            while(first < last && nums[last] >= key){
                --last;
            }
            nums[first] = nums[last];
            while(first < last && nums[first] <=key){
                ++first;
            }
            nums[last]=nums[first];
        }
        nums[first] = key;
        quick_sort(nums, l, first);
        quick_sort(nums, first+1, r);
    }

归并排序

void merge_sort(vector `<int> &nums, int l, int r, vector<int> &temp`){

    if (l+1>=r){

    return ;

    }

    //divide

    int m = l + (r-1)/2;

    merge_sort(nums, l , m, temp);

    merge_sort(nums, m, r, temp);

    int p =l, q = m, i=1;

    while (p<m || q < r){

    if(q >= r || (p < m && nums[p])){
            temp[i++] = nums[p++];
        }else{
            temp[i++] = nums[q++];
        }
        for (i=l ; i<r ;++i){
            nums[i] = temp[i];
        }

}

插入排序

```cpp
void insertion_sort(vector<int> &nums, int n){
    for (int i=0; i<n ; ++i){
        for(int j=i; j > 0 && nums[j] < nums[j-1] ; --j){
            swap(nums[j], nums[j-1]);
        }
    }
}

```
冒泡排序
```cpp
    void bubble_sort(vector<int> &nums, int n){
        bool swapped;
        for (int i=l; i<n ; ++i){
            swapped = false;
            for (int j=l; j< n-i+1; ++j){
                if (nums[j] < nums[j-1]){
                    swap(nums[j], nums[j-1]);
                    swapped = true;
                }
            }
            if (!swapped){
                break;
            }
        }
    }
```
选择排序
```cpp
void selection_sort(vector<int> &num, int n){
    int mid;
    for(int i=0; i<n-1; ++i){
        mid = i;
        for (int j = i+1; j<n; ++j){
            if(nums[j] < nums[mid]){
                mid = j;
            }
            swap(nums[mid], nums[i]);
        }
    }
}
```
