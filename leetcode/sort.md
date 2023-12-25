排序算法

1.选择排序

```cpp
void selectionSort(vector<int> &nums){
	int n = num.size();
	for(int i=0; i<n-1; i++){
		int k=i;
		for(int j=i+1; j<n; j++){
			if(nums[j] < nums[k])
				k=j;
		}
		swap(nums[i], nums[k]);
	}
}
```

2.插入排序

```cpp
void insertionSort(vector<int> &nums){
	for(int i=1; i<nums.size(); i++){
		int base=nums[i], j=i-1;
		while(j>=0 && nums[j]>base){
			nums[j+1] = nums[j];
			j--;
		}
		nums[j+1]=base;
	}
}
```

3.冒泡排序

```cpp
void bubbleSortWithFlag(vector<int> &nums){
	for (int i=nums.size()-1; i>0; i--){
		for(int j=0; j<i; j++){
			if(mums[j] > nums[j+1])
				swap(nums[j], nums[j+1]);
		}
	}
}
```

4.快速排序

```cpp
void swap(vector<int> &nums, int i, int j){
	int tmp = nums[i];
	nums[i] = nums[j];
	nums[j] = tmp;
}
int partition(vector<int> &nums, int left, int right){
	int i=left, j=right;
	while (i<j){
		while(i<j &&nums[j]>=nums[left] ){
			j--;
		}
		while(i<j && num[i]<=nums[left]){
			i++;
		}
		swap(nums, i, j);
	}
	swap(nums, i, left);
	return i;
}
void quick_sort(vector<int> &nums, int left, int right){
	if(left >=right)
		return;
	int pivot = partition(nums, left, right);
	qucik_sort(nums, left, pivot-1);
	quick_sort(nums, pivot+1, rirht);
}
```

5.归并排序

```cpp
void merge(vector<int> &nums, int left, int mid, int right){
	vector<int> tmp(right-left+1);
	int i = left, j=mid +1; k=0;
	while(i<mid && j<=right){
		if(nums[i] <= nums[j])
			tmp[k++] = nums[i++];
		else
			tmp[k++] = nms[j++];
	}
	while(i<=mid){
		tmp[k++] = nums[i++];
	}
	while(j<=right){
		tmp[k++]=nums[j++];
	}
	for(k =0; k<tmp.size(); k++){
		tmp[k++] = nums[j++];
	}
}
void mergeSort(vector<int> &sums, int left, int right){
	if(left>=right)
		return;
	int mid = (lefg+right)/2;
	mergeSort(nums, left, mid);
	meregeSort(nums,mid+1, right);
	merge(nums, left, mid, right);
}
```

6.堆排序
