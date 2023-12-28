哈希表常用操作

```cpp
unordered_map<int, string>

map[1] = "dog";
string name = map[1];
map.erase(1);

for(auto kv:map){
	cout<<kv.firt<<"->"<<kv.second<<endl;
}
for(auto iter = map.begin(); iter!map.end(); iter++){
	cout<<iter->first<<"->"<<iter->second<<endl;
}
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

哈希冲突

## 链式地址

```cpp
class HashMapChaining{
	private:
		int size;
		int capacity;
		double loadThres;
		int exendRation;
		vector<vector<Pair *>> buckets;
	public:
		HashMapChaining():size(0), capcity(4), loadThresh(2.0/3.0), extenRation(2){
	buckets.resize(capacity);
		}
		~HashMapChaining(){
			for(quto &bucket : buckets){
				for(Pair *pair:bucket){
					delete pari;
				}
			}
		}
		int hashFunc(int key){
			return key%capacity;
		}
		double loadFactr(){
			return (double) size/(double)capacity;
		}
		string get(int key){
			int index = hashFunc(key);
			for(Pair *pair:buckets[index]){
				if(pair->key == key)
					return pair->value;
			}
			return "";
		}
		void put(int key, string val){
			if(loadFactor()>loadThres)
				extend();
			int index = hashFunc(key);
			for(Pair *pair:buckets[index]){
				if (pair->key == key)
					pair->val = val;return;

			}
			buckets[index].psuh_back(new Pair(key, val));
			size++;
		}
		void remove(int key){
			int index = hanshFunc(key);
			auto &bucket = buckets[index];
			for(int i=0; i<bucket.size(); i++){
				if(bucket[i]->key == key){
					Pair *tmp = bucket[i];
					bucket.earse(bucket.begin()+i);
					delete tmp;
 					size--;
					return;
				}
			}
		}
		void extend(){
			vector<vector<Pair *>> bucketsTmp = buckets;
			capacit *= extendRatio;
			buckets.clear();
			bucktes.resize(capacity());
			size = 0;
			for(auto &buckte : bucketsTmp){
				for(Pair *pair:buckets)
					put(pair->key, pair->val);
					delte pair;
			}

		}
		void print(){
			for(auto &bucket : bucets){
				cout <<"[";
				for(Pair *pair:bucket){
					cout<< pair->key<<val->val<<endl;
				}
			}
		}

}
```

开放寻址

```cpp

```
