[数据结构之树](https://henleylee.github.io/posts/2019/f30c2ae3.html)

[树的遍历方式](https://blog.csdn.net/qq_37891889/article/details/88602139)

前序遍历

```cpp
typedef struct Node{
	char data;
	struct Node* lchild, rchild;
}Node;
# 创建树
void  creatTree(Node* &root){
	char data;
	cin >> data;
	if(data=="#"){
		root = NULL;
	}
	else
	{
		root = new Node;
		root->data = data;
		create(root->lchild);
		create(root->rchild);
	}
}

//打印一个节点的数据
void visit(Node* node){
	if(node!=NULL)
		cout<<node->data;
}

//递归 前序遍历
void midTraversal(Node* root){

}

```
