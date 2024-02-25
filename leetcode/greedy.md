# [15.1 贪心算法](https://www.hello-algo.com/chapter_greedy/)

1. 零钱兑换问题

   ```cpp
   //零钱兑换 贪心
   int coinChangeGreedy(vector<int> &coins, int amt){
   	//假设coins 列表有序
   	int i = coins.size()-1;
   	int count = 0;
   	//循环进行贪心选择， 直到无剩余金额
   	while(amt>0){
   		//找到小于且接近剩余金额的硬币
   		while(i>0 && coins[i]>amt){
   			i--;
   		}
   		//选择coins[i]
   		amt-=coins[i];
   		count++;
   	}
   	//若未找到可行方案， 则返回 -1
   	return amt == 0 ? count : -1;
   }


   ```
 # 15.2 分数背包问题
给定 n 个物品，第 i 个物品的重量为  wgt[i-1]、价值为 val[i-1]，和一个容量为 cap 的背包。每个物品只能选择一次，但可以选择物品的一部分，价值根据选择的重量比例计算，问在限定背包容量下背包中物品的最大价值
* 将物品按照单位价值从高到低进行排序
* 遍历所有物品， 每轮贪心地选择单位价值最高得物品
* 若剩余背包容量不足， 则使用当前物品得一部分填充
```cpp
//物品 
class Item{
    public:
        int w;
        int v;
        Item(int w, int v):w(w), v(v){
        }
};
//分数背包 ： 贪心
double fractionalKnapsack(vector<int> &wgt, vector<int> &val, int cap){
    //创建物品列表， 包含两个属性，重量价值
    vector<Item> items;
    for(int i=0; i< wgt.size(); i++){
        items.push_back(Item(wgt[i], val[i]));
    }
    //按照单位价值 item.v / item.w 从高到低进行排序
    sort(items.begin(), items.end(), [](Item &a, Item &b){return (double)a.v/a.w> (double)b.v/b.w });
    //循环贪心选择
    double res = 0;
    for(auto &item:items){
        if(item.w<= cap){
            //若剩余容量充足， 则将当前整个物品
            res += item.v;
            cap -= item.w;
        }else{
            res += (double) item.v/item.w * cap;
            //已无剩余容量， 因此退出循环
            break;
        }
    }
    return res;
}
```
# 15.3 最大容量问题
输入一个数组 ht，其中的每个元素代表一个垂直隔板的高度。数组中的任意两个隔板，以及它们之间的空间可以组成一个容器。容器的容量等于高度和宽度的乘积（面积），其中高度由较短的隔板决定，宽度是两个隔板的数组索引之差。请在数组中选择两个隔板，使得组成的容器的容量最大，返回最大容量
* 代码实现
```cpp
int maxCapacity(vector<int> &ht){
    //初始化 i，j
    int i = 0; j = ht.size()-1;
    //初始化最大值为 0
    int res = 0;
    //循环贪心选择， 直至两版相遇
    while(i<j){
        //更新最大容量
        int cap = min(ht[i], ht[j]) * (j-i);
        res = max(res, cap);
        //向内移动短板
        if(ht[i] < ht[j]>){
            i++;
        }else{
            j--;
        }
    }
    return res;
}
```
# 最大切分乘积问题
给定一个正整数  n ，将其切分为至少两个正整数的和，求切分后所有整数的乘积最大是多少
```cpp
int maxProductCutting(int n){
    //当 n<=3 必须切分出一个
    if(n<= 3){
        return 1*(n-1);
    }/
    //贪心地切分出 3， a 为 3的个数， b为余数
    int a = n/3;
    int b = n%3;
    if(b==1){
        return (int)pow(3, a-1)*2*2;
    }
    if(b==2){
        return (int)pow(3, a)*2;
    }
    //当余数为 0 时 ，不做处理
    return (int) pow (3, a);
}
```