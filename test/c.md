```cpp
void quicksort(vector<int> &num){
    if (num.size()<=1)
        return;
    int l,i=0, j,r=num.size(), temp = num[0];
    while(l<r){
        while(l<r && num[l]<=temp){
            l++;
        }
        swap(temp, num[l]);
        while(l<r && num[r]>=temp){
            r--;
        }
        swap(num[l], num[r])
    } 
    swap()
}


int partition(vector<int> &nums, int left, int right){
    int i=left, j=right;
    while(i<j){
        while(i<j && nums[j]>=nums[left]){
            j--;
        }
        while(i<j && nums[i]<=nums[left]){
            i++;
        }
        swap(nums[i], nums[j]);
    }
    swap(i, left);
    return i;
}
void quick_sort(vector<int> &nums, int left, int right){
    if(left>=right)
        return;
    int pivot = partition(nums, left, right);
    quick_sort(nums, left, pivot-1);
    quick_sort(nums, pivot+1, right);
}

```