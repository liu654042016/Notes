[参考网站](https://www.hello-algo.com/chapter_stack_and_queue/stack/)

c++基本数据类型

```
byte 1 byte
short 2 byte
int 4 byte
long 8byte
float 4 byte
double 8 byte
char 2 byte
bool 1 byte
```

链表

```cpp
struct ListNode{
	int val;
	ListNode *next;
	ListNode{int x}:val(x), next(nullptr) {}//构造函数
};
```

链表常用操作

```cpp
ListNode* n0 = new ListNode(1);
ListNode* n1 = new ListNode(3);
ListNode* n2 = new ListNode(2);
ListNode* n3 = new ListNode(5);
ListNode* n4 = new ListNode(4);
// 构造节点之间的引用
n0->next = n1;
n1->next = n2;
n2->next = n3;
n3->next = n4;
```

插入节点

```cpp
// 在链表的节点n0之后插入节点p
void insert(LisNode *no, ListNode* p){
	ListNode *n1 = n0->next;
	p->next = n1;
	n0->next = p;
}
```

删除节点

```cpp
void remove(ListNode *n0){
	if(n0->next == nullptr){
		return;
	}
	ListNode *p = n0->next;
	ListNode *n1 = p->next;
	n0->next = n1;
	delete p;
}
```

访问节点

```cpp
//访问链表中索引为index的节点
ListNode *access(ListNode *head, int index){
	for (int i=0; i<index; i++){
		if (head==nullptr)
			return nullptr;
		head = head->next;
	}
	return head;
}
```

查找节点

```cpp
//在链表中查找值为target 的首个节点
int find(ListNode *head , int target){
	int index = 0;
	while(head != nullptr){
		if(head->val ==target )
			return index;
		head = head->next;
		index++;
	}
	return -1;
}
```

## 列表

```cpp


vector<int> nums = {1,3,2,5,4};
num.clear() //清楚列表
nums.push_back(1);
nums.insert(nums.begin()+3, 6);

//遍历
count = 0
for(int num:nums){
	count+=num;
}

//拼接
vector<int> nums1={3,4,5}
nums.insert(nums.end(), nums1.begin(), nums1.end());
//排序
sort(nums.begin(), nums.end());

//列表实现
class MyList{
	private
		int *arr;
		int arrCapacity = 10;
		int arrSize = 0;
		int extendRation = 2;
	public:
		MyList(){
			arr = new int{arrCapacity};
		}
		~MyList(){
			delete[] array;
		}
		int capacity(){
			return arrCapacity;
		}
		int get(int index){
			if(index<0 || index>=size){
				throw out_of_range("索引越界");
			}
			return arr[index];
		}
		int add(int num){
			if(size() == capacity)
				extendCapacity();
			for(int j=size() - 1; j>=index; j--){
				arr[j+1] = arr[j];
			}
			arr[index] = num;
			//更新元素数量
			arrSize++;
		}
		int remove(int index){
			if(index<0 || index >=size())
				throw out_of_range("索引越界")；
			int num = arr[index];
			for(int j = index; j<size()-1; j++){
				arr[j] = arr[j+1];
			}
			arrSize--;
			return num;
		}
		//列表扩容
		voidextendCapacity(){
			int newCapacity = capacity()*extendRatio;
			int *tmp = arr;
			arr = new int[newCapacity];
			for(int i=0;i<size(); i++){
				arr[i] = tmp[i];
			}
			delete [] tmp;
			arrCapacity = newCapacity;
		}

}


```

栈与队列

栈的基本操作

```cpp
stack<int> stack;
stack.push(1);
int top = stack.top();//访问元素
stack.pop()// 元素出栈
stack.size() //栈的长度
bool empty = stack.empty()

//基于链表实现栈
class LinkedListStack{
	private:
		ListNode *stackTop;
		int stkSize;
	public:
		LinkedListStack(){
			stackTop = nullptr;
			stkSize = 0;
		}
		~LinkedListStack(){
			freeMemoryLinkedList(stackTop);
		}
		//获取栈的长度
		int size(){
			return strSize;
		}
		//判断栈是否为空
		bool isEmpty(){
			return size() == 0;
		}
		//入栈
		void push(int num){
			ListNode *node = new ListNode(num);
			node->next = stackTop;
			stackTop = node;
			stkSize++;
		}
		//出栈
		int pop(){
			int num = top();
			ListNode *tmp = stackTop;
			stackTop = stackTop->next;
			delete tmp;
			stkSize--;
			return num;
		}
		// 访问栈顶元素
		int top(){
			if(isEmpty())
				throw out_of_range("栈为空")；
			return stackTop->val;
		}
		// 将list转换为Array
		vector<int> toVector(){
			ListNode *node = stackTop;
			vector<int> res(size());
			for(int i= res.size() -1; i>=0; i--){
				res[i] = node->val;
				node = node->next;
			}
			return res;
		}
};
// 基于数组的实现
class ArrayStack{
	private:
		vector<int> stack;
	public:
		int size(){
			return stack.size() ==0;
		}
		//入栈
		void push (int num){
			stack.push_back(num);
		}
		//出栈
		int pop(){
			int num = top();
			stack.pop_back();
			return num;
		}
		// 访问栈顶元素
		int top(){
			if(isEmpty())
				throw out_of_range("栈为空")；
			return stack.back();
		}
		// 返回vector
		vector<int> toVector(){
			return stack;
		}

}
//基于数组实现队列
class ArrayQueue{
	private:
		int *nums;
		int front;
		int queSize;
		int queCapacity;
	public:
		ArrayQueue(int capactiyt){
			nums = new int[capacity];
			queCapacity = capacity;
			front = queSize=0;
		}
		~ArrayQueue(){
			delete[] nums;
		}
		int capacity(){
			return queCapacity;
		}
		int size(){
			return queSize;
		}
		bool isEmpty(){
			return size() == 0;
		}
		//入队
		void push(int num){
			if(queSize == queCapacity){
				cout<< "队列已满"<<endl;
				return;
			}
			int rear = (front+queSize)%queCapacity;
			nums[rear] = num;
			queSize++;

		}
		int pop(){
			int num = peek();
			front = (front+1)%queCapacity;
			queSize--;
			return num;
		}
		int peek(){
			if(isEmpty())
				throw out_of_range("异常")；
			return nums[front];
		}
		vector<int> toVector(){
			vector<int> arr(queSize);
			for(int i=0 ,j=front; i<queSize; i++, j++){
				arr[i] = nums[j%queCapacity];
			}
			return arr;
		}
}

```

队列

先进先出

```cpp
queue<int> queue;
queue.push(1);
int front = queue.front();
queue.pop();
int size = queue.size();
bool empty = queue.empty();

//链表实现队列
class LinkedListQueue{
	private:
		ListNode *front, *rear; //头节点 front, 尾节点 rear
		int queSize;
	public:
		LinkedListQueue(){
			front = nullptr;
			rear = nullptr;
			queSize = 0;
		}
		~LinkedListQueue(){
			freeMemoryLinkedList(front);
		}
		int size(){
			reutrn queSize;
		}
		bool isEmpty(){
			return queSize==0;
		}
		void push(int num){
			ListNode *node = new ListNode(num);
			if(front == nullptr){
				front = node;
				rear = node;
			}else{
				rear->next = node;
				rear = node;
			}
			queSize++;
		}
		void pop(){
			int num = peek();
			ListNode *tmp = front;
			front = front->next;
			delete tmp;
			queSize--;
			return num;
		}
		int peek(){
			if(size() == 0)
				throw out_of_range("队列为空")；
			return front->val;
		}
		vector<int> toVector(){
			ListNode *node = front;
			vector<int> res(size());
			for (int i=0; i<res.size()l i--){
				res[i] = node->val;
				node = node->next;
			}
			return res;
		}
}
```

双向队列

双向队列 double-ended queue」提供了更高的灵活性，允许在头部和尾部执行元素的添加或删除操作、

```cpp
deque<int> deque;

deque.push_back(2);//添加队尾
deque.push_front(4);//添加队尾

//访问元素
int front = deque.front();
int back = deque.back();

//元素出队
deque.pop_front()
deque.pop_back()

int size = deque.size()

bool empty = deque.empty()



```
