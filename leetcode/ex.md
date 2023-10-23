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


215 kth largest element in an array
```cpp
int findKthLargest(vector<int>& nums, int k){
    int l =0; r=nums.size()-1, target = nums.size()-k;
    while(l<r){
        int mid = quickSelection(nums, l, r);
        if(mid == target){
            return nums[mid];
        }
        if (mid < target){
            l = mid + 1;
        }else{
            r = mid -1;
        }
    }
    return nums[l];
}
// 
int quickSelection(vector<int>& nums, int l, int r){
    int i = l + 1, j =r ;
    while(true){
        while (i<r && nums[i] <= nums[l]){
            ++i;
        }
        while (l < j && nums[j]> nums[l]){
            --j;
        }
        if(i >= j){
            break;
        }
        swap(nums[i], nums[j]);
        return j;
    }
}

```

```cpp
vector<int> topKFrequent(vector<int>& nums, int k){
    unordered_map<int, int> counts;
    int max_count = 0;
    for (const int & num : nums){
        max_count = max(max_count, ++counts[num]);
    }

    vector<vector<int>> buckets(max_count + 1);
    for (const auto & p : counts){
        buckets[p.second].push_back(p.first);
    }

    vector<int> ans;
    for (int i = max_count; i > = 0 && ans.size() < k ; --i){
        for (const int & num : buckets[i]){
            ans.push_back(num);
            if (ans.size() == k){
                break;
            }
        }
    }
    return ans;
}
```




```cpp


```



