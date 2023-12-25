哈希表常用操作

```cpp
unordered_map<int, string>

map[1] = "dog";
string name = map[1];
map.erase(1);
```

哈希表简单的实现

```cpp
struct Pair{
	public:
		int key;
		string val;
		Pair(int key, string val){
			this->key = key;
			this->val = val;
		}
}
基于数组实现的哈希表
class ArrayHashMap{
	private:
		vector<Pair*> buckets;
	public:
		ArrayHashMap(){
			buckets = vector<Pair *>(100);
		}
		~ArrayHashMap(){
			for(const auto &bucket:buckets){
				delete bucket;
			}
			buckets.clear();
		}
		//哈希函数
		int hashFunc(int key){
			int index = key%100;
			return index;
		}
		//查询操作
		string get(int key){
			int index = hashFunc(key);
			Pair *pair = buckets[index];
			if(pair == nullptr){
				return " ";
			}
			return pair->val;
		}
		//添加操作
		void put(int key, string val){
			Pair *pair = new Pair(key, val);
			int index = hashFunc(key);
			buckets[index] = pair;
		}
		//删除操作
		void remove(int key){
			int index = hashFunc(key);
			delete buckets[index];
			buckets[index]=nullptr;
		}
		// 获取所有键值对
		vector<Pair *> psirSet(){
			vector<Pair *> pairSet;
			for(Pair *pair : buckets){
				if(pair != nullptr){
					pairSet.push_back(pair);
				}
			}
			return pairSet;
		}
		//获取所有键
		vector<int> keySet(){
			vector<int> keySet;
			for(Pair *pair:buckets){
				if(pair != nullptr){
					keySet.push_back(pair->key);
				}
			}
			return keySet;
		}
		//获取所有植
		vector<string> valueSet(){
			vector<string> valueSet;
			for(Pair *pair: buckets){
				if(pair!=nullptr)
					valueSet.push_back(pair->val);
			}
			return valueSet;
		}
		void print(){
			for(Pair *kv:pairSrt()){
				cout << kv->key << "->" <<kv->val<<endl;
			}
		}
};
```
