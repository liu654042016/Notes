# [动态规划](https://www.hello-algo.com/chapter_dynamic_programming/)

## 14.1 初探动态规划

* 给定一个共有你 n 阶的楼梯，你每步可以上 1 阶或者 2 阶，请问有多少种方案可以爬到楼顶？

```cpp
//回溯法
void backtrack(vector<int> &choices, int state,int n, vector<int>& res){
    if(state ==n )
        res[0]++;
    //遍历所有选择
    for(auto &choice : choices){
        // 剪枝 不允许超过n节
        if(state + choice  > n){
            continue;
        }
        //尝试
        backtrack(choices, state+choice, n, res);
    }
}
int climbingStairsBacktrack(int n){
    vector<int> choices{1,2};
    int state = 0;
    vector<int> res = {0};
    backtrack(choices, state, n, res);
    return res[0];
}
```

### 14.1.1 方法一：暴力搜素

```cpp
int dfs(int i){
    //已知dp[1] 和 dp[2]
    if(i==1 || i==2)
        return i;
    //dp[i] = dp[i-1] + dp[i-2]
    int count = dfs(i-1) + dfs(i-2);
    return count;
}
//爬楼梯
int climbingStairsDFS(int n){
    return dfs(n);
}


```

### 14.1.2 记忆化搜索

```cpp
// 记忆化搜索
int dfs(int i, vector<int> &mem){
    //已知 df[1] 和 dp[2] 返回值
    if(i==1 || i==2)
        return i;
    //若存在 dp[i] ,则直接返回值
    if(mem[i]! = -1)
        return mem[i];
    //dp[i] = dp[i-1] + dp[i-2]
    int count = dfs(i-1, mem) + dfs(i-2, mem);
    //记录 dp[i]
    mem[i] = count;
    return count;
}
//爬楼梯
int climbingStairDFSMem(int n)
{
    vector<int> mem(n+1, -1);
    return dfs(n, mem);
}
```

### 14.1.3 动态规划

```cpp
//爬楼梯 动态规划
int climbingStairsDP(int n ){
    if(n==1 || n==2)
        return n;
    //初始化 dp 表，用于存储子问题的解
    vector<int> dp(n+1);
    //初始状态
    dp[1] = 1;
    dp[2] = 2;
    //状态转移：从较小子问题逐步求解较大子问题
    for(int i=3; i< n; i++){
        dp[i] = dp[i-1] + dp[i-2];
    }
    return dp[n];
}

```

### 14.1.4 空间优化

```cpp
int climbingStairsDPComp(int n){
    if(n==1 || n==2)
        return n;
    int a=1; b= 2;
    for(int i=3; i<=n; i++){
        int tmp = b;
        b =a + b;
        a = tmp;
    }
    return b;
}
```

## 14.2 动态规划问题特性

包含重叠子问题，还包含两大特性：最优子结构 ， 无后效性

### 14.2.1 最优子结构

* 给定一个楼梯，你每步可以上 1 阶或者 2 阶，每一阶楼梯上都贴有一个非负整数，表示你在该台阶所需要付出的代价。给定一个非负整数数组cost，其中 cost[i]表示在第i个台阶需要付出的代价，cost[0]为地面（起始点）。请计算最少需要付出多少代价才能到达顶部？

```cpp
int minCostClimbingStairsDP(vector<int> &cost){
    int n = cost.size() - 1;
    if(i==1 || i==2)
        return cost[n];
    //初始化 dp 表，用于存储子问题的解
    vector<int> dp(n+1);
    //初始状态，预设最小子问题的解
    dp[1] = cost[1];
    dp[2] = cost[2];

    //状态转移，从较小子问题逐步求解较大子问题
    for(int i = 3; i<=n; i++){
        dp[i] = min(dp[i-1], [i-2]) + cost[i];
    }
    return dp[n];
}

//空间优化后的动态规划
int minCostClimbingStairsDPComp(vector<int> &cost){
    int n = cost.size() -1;
    if(n==1 || n==2)
        return cost[n];
    int a= cost[1], b = cost[2];
    for(int i=3; i<=n; i++){
        int tmp = b;
        b = min(a, tmp) + cost[i];
        a = tmp;
    }
    return b;
}

```

### 14.2.2 无后效性、

* 给定一个共有 n 阶的楼梯，你每步可以上 1 阶或者 2 阶，但不能连续两轮跳 1 阶，请问有多少种方案可以爬到楼顶？

```cpp
int climbingStairConstraintDP(int n){
    if(n==1 || n==2){
        return 1;
    }
    // 初始化 dp 表，用于存储子问题的解
    vector<vector<int>> dp(n+1, vector<int>(3, 0));
    //初始状态：预设最小子问题的解
    dp[1][1] = 1;
    dp[1][2] = 0;
    dp[2][1] = 0;
    dp[2][2] = 1;
    //状态转移： 从较小子问题逐步求解较大子问题
    for(int i=3; i<=n ; i++){
        dp[i][1] = dp[i-1][2];
        dp[i][2] = dp[i-2][1] + dp[i-2][2];
    }
    return dp[n][1] + dp[n][2];
}

```

## 14.3 动态规划解题思路

### 14.3.1 问题判断

### 14.3.2 问题求解步骤

1. 暴力搜索

```cpp
int minPathSumDFS(vector<vector<int>> &grid, int i, int j){
    //若为左上单元个，则停止搜索
    if(i==0 && j==0){
        return grid[0][0];
    }
    //若行列索引初阶，则返回+代价
    if(i<0 || j<0){
        return INT_MAX;
    }
    //计算从左上角(i-1, j)和（i, j-1）度最小路径和
    int up = minPathSumDFS(grid, i-1, j);
    int left = minPathSumDFS(grid, i, j-1);
    //返回从左上角(i, j)的最小路径代价
    return min(left, up) !=INT_MAX ? min(left, up) + grid[i][j]:INT_MAX;
}
```

2. 记忆化搜索

```cpp
int minPathSumDFSMem(vector<vector<int>> &grid, vector<vector<int>> &mem, int i, int j){
    // 若为左上角单元格，则终止搜索
    if(i==0 & j==0){
        return grid[0][0];
    }
    //若行列索引越界， 则返回 + 代价
    if(i<0 || j<0)
        return INT_MAX;
    //若有记录，则直接返回
    if(mem[i][j] != -1)
        return mem[i][j];
    //左边和上边单元格最小路径代价
    int up = minPathSumDFSMem(grid, mem, i-1, j);
    int left = minPathSumDFSMem(grid, mem, i, j-1);
    // 记录并返回左上角(i, j)的最小路径代价
    mem[i][j] = min(left, up) != INT_MAX ? min(left, up) + grid[i][j] : INT_MAX;
    return mem[i][j];
}

```

3. 动态规划

```cpp
int minPathSumDP(vector<vector<int>> &grid){
    int n = grid.size(), m = grid[0].size();
    //初始化 dp 表
    vector<vector<int>> dp(n, vector<int>(m));
    dp[0][0] = grid[0][0];
    //状态转移： 首行
    for(int j=1; j<m; j++){
        dp[0][j = dp[0][j-1] + grid[0][j];
    }
    //状态转移首列
    for(int i=1; i<n; i++){
        fp[i][0] = dp[i-1][0] + grid[i][0];
    }
    //状态转移其余行和列
    for(int i=1; i< n; i++){
        for(int j=1; j<m ;j++){
            dp[i][j] = min(dp[i][j-1], dp[i-1][j]) + grid[i][j];
        }
    }
    return dp[n-1][m-1];
}

```

4. 空间优化

```cpp
int minPathSumDPComp(vector<vector<int>>& grid){
    int n = grid.size(), m = grid[0].size();
    //初始化 dp 表
    vector<int> dp(m);
    //状态转移 首行
    for(int j=1; j<m ; j++ ){
        dp[j] = dp[j-1] + grid[0][j];
    }
    //状态转移： 其余行
    for(int i=1; i<n; i++){
        // 状态转移： 首列
        dp[0] = dp[0] + grid[i][0];
        //状态转移: 其余列
        for(int j=1; j<m; j++){
            dp[j] = min(dp[j-1], dp[j]) + grid[i][j];
        }
    }
    return dp[m-1];
}

```

## 14.4 0-1 背包问题

 给定 n 个物品，第 i 个物品的重量为 wgt[i-1]、价值为 val[i-1] ，和一个容量为 cap 的背包。每个物品只能选择一次，问在限定背包容量下能放入物品的最大价值。

1. 暴力搜索

* 递归参数: 状态 [i,c]
* 返回值：子问题的解 dp[i, c]
* 终止条件： 当物品编号越界i=0 或者背包剩余容量为0时，终止递归并返回为 0。
* 剪枝 当前物品重量超出背包剩余容量 则只能选择不放入背包

```cpp
int knapsackDFS(vector<int> &wgt, vector<int> &val, in i, int c){
    //已选完物品 或者 背包无剩余容量
    if(i== 0  || c ==0){
        return 0;
    }
    //若超过背包容量， 则只能选择不放入背包
    if(wgt[i-1] > c){
        return knapsackDFS(wgt, val, i-1, c);
    }
    // 计算不放入和放入物品 i 的最大价值
    int no = knapsackDFS(wgt, val, i-1, c);
    int yes = knapsackDFS(wgt, val, i-1, c-wgt[i-1]+val[i-1]);
    //返回两种方案中价值更大的那一个
    return max(no, yes);
}
```

2. 记忆化搜索

```cpp
int knapsackDFSMem(vector<int> &wgt, vector<int> &val, vector<int> &Mem, int i, int c){
    //若已选完所有物品 或者 容量为0
    if(i==0 || c==0){
        return 0;
    }
    //若已有记录，则直接返回
    if(mem[i][c] != -1){
        return mem[i][c];
    }
    //  若超过背包容量， 则只能选择不放入背包
    if(wgt[i-1] > c)
        return knapsackDESMem(wgt, val, mem, i-1, c);
    //计算不放入和放入物品 i 的最大价值
    int no = knapsackDFSMem(wgt, val, mem, i-1, c);
    int yes = knapsackDFSMem(wgt, val, mem, i-1, c-wgt[i-1] + val[i-1]);
    //记录并返回两种方案中价值更大的那一个
    mem[i][c] = max(no, yes);
    return mem[i][c];
}

```

3. 动态规划

```cpp
int knapsackDP(vector<int> &wgt, vector<int> &val, int cap){
    int n = wgt.size();
    //初始化dp表
    vector<vector<int>> dp(n+1, vector<int>(cap=1， 0))；
    //状态转移
    for(int i=1; i<=n; i++){
        for (int c = 1; c <= cap; c++){
            if(wg[i-1] > c){
                //若超过背包容量，则不选物品 i
                dp[i][c] = dp [i-1][c];
            }else{
                dp[i][c] = max(dp[i-1][c], dp[i-1][c-wgt[i-1]+val[i-1]]);
            }
        }

    }
    return dp[n][cap];

}
```

## 14.5 完全背包问题

### 14.5.1 完全背包问题

给定 n 个物品，第 i 个物品的重量为 wgt[i-1]、价值为 val[i-1]，和一个容量为 cap 的背包。每个物品可以重复选取，问在限定背包容量下能放入物品的最大价值

1. 动态规划思路
   状态 [i, c] 的变化分为两种情况

* 不放入物品 i : 转移至 [i-1, c]
* 放入物品 i : 与 0-1 背包问题不同，转移至 [i, c-wgt[i-1]]
  状态转移方程：
  dp[i, c] = max(dp[i-1, c], dp[i, c-wgt[i-1]]+ val[i-1])

```cpp
int unboundedKnapsackDP(vector<int> &wgt, vector<int> &val, int cap){
    int n = wgt.size();
    // 初始化 dp 表
    vector<vector<int>> dp(n+1, vector<int>(cpa + 1, 0));
    // 状态转移
    for(int i=1; i< = n ; i ++){
        for (int c= 1; c<=cap; c++){
            if(wgt[i-1] > c){
                // 若超过背包容量，则不选物品
                dp[i][c] = dp [i-1][c];
            }else{
                // 不选和物品 i 这两种的较大值
                dp[i][c] = max(dp[i-1][c], dp[i][c-wgt[i-1]]+val[i-1]);
            }
        }
    }
    return dp[n][cap];
}
```


空间优化

```cpp
/*完全背包 空间优化后的动态规划*/
int unboundedKnapsackDPCom (vector<int> &wgt, vector<int> &val, int cap){
	int n = wgt.size();
	//初始化dp表
	vector<int> dp(cap+1, 0);
	//状态转移
	for(int i=1; i<=n; i++){
		for(int c = 1; c<=cap; c++){
			if(wgt[i-1] > c){
			// 若超过背包容量，则不选物品 i
				dp[c] = dp[c];
			}else{
			//不选和选物品 i 这两种方式较大值
				dp[c] = max(dp[c], dp[c-wgt[i-1]] + val[i-1]);
			}
		}
	}
	return dp[cap];
}
```

## 14.5.2 零钱兑换问题

```cpp
/*动态规划*/
int coinChangeDP(vector<int> &coins, int amt){
	int n = coins.size();
	int MAX = amt + 1;
	//初始化 dp 表
	vector<vector<int>> dp(n+1, vector<int>(amt+1, 0));
	//状态转移 首行首列
	for(int a =1; a<= amt; a++){
		dp[0][a] = MAX;
	}
	//状态转移， 其余行和列
	for(int i=1; i<=n; i++){
		for(int a =1; a<=amt; a++){
			if(coins[i-1] > a){
				//若超过目标金额， 则不选硬币 i
				dp[i][a] = dp[i-1][a];
			}else{
				//不选和选硬币 i 这两种方法的较小值
				dp[i][a] = min(dp[i-1][a], dp[i][a-coins[i-1]] + 1);
			}
		}
	}
	return dp[n][amt] != MAX ? dp[n][amt] : -1;
}

```

空间优化

```cpp
/* 零钱兑换: 空间优化后的动态规划*/
int coinChangeDPComp(vecotr<int> &coins, int amt){
	int n = coins.size();
	int MAX = amt + 1;
	//初始化 dp 表
	vector<int> dp(amt+1, MAX);
	dp[0] = 0;
	//状态转移
	for(int i=1; i<=n; i++){
		for(int a=1; a<=amt; a++){
			if(coins[i-1] > a){
				//若超过目标金额， 则不选硬币 i 
				dp[a] = dp[a];
			}else{
				//不选和选硬币 i 这两种方案的较小值
				dp[a] = min(dp[a], dp[a-coins[i-1]] + 1);
			}
		}
	}
	return dp[amt] !=MAX ? dp[amt] : -1; 
}
```

## 14.5.3 零钱兑换问题 II

给定 n 钟硬币，第 i 种硬币为 coins[i-1], 目标金额为 amt, 每种硬币可以重复选择， 问凑出目标金额的硬币组合数量

```cpp
int coinChangeIIDP(vector<int> &coins, int amt){
	int n = coin.size();
	//初始化dp表
	vector<vector<int>> dp(n+1, vector<int>(amt+1, 0));
	//初始化首列
	for(int i=0;i<=n; i++){
		dp[i][0] = 1;
	}
	//状态转移
	for(int i=1; i<=n; i++){
		for(int a=1; a<=amt; a++){
			if(conins[i-1] > a){
				//若超过目标金额， 则不选硬币 i
				dp[i][a] = dp[i-1][a];
			}else{
				//不选和选硬币 i 这两种方案之和
				dp[i][a] = dp[i-1][a] + dp[i][a-coins[i-1]];
			}
		}
	}
	return dp[n][amt];
}
```

空间优化

```cpp
int coinChangeIIDPComp(vector<int> &coins, int amt){
	int n = coins.size();
	//初始化 dp 表
	vector<int> dp(amt+1, 0);
	dp[0] = 1;
	//状态转移
	for(int i=1; i<=n; i++){
		for(int a=1; a<=amt; a++){
			if(coins[i-1] > a){
				//若超过且目标金额， 则不选择硬币 i
				dp[a] = dp[a];
			}else{
				//不选和选择硬币i两种方案之和
				dp[a] = dp[a]+dp[a-coins[i-1]];
			}
		}
	}
	return dp[amt];
}
```

## 14.6 编辑距离问题

输入两个字符串 s 和 t ，返回将 s 转换为 t 所需的最少编辑步数。你可以在一个字符串中进行三种编辑操作：插入一个字符、删除一个字符、将字符替换为任意一个字符。

1， 动态规划思路

    dp[i, j] = min(dp[i, j-1], dp[i-1, j], dp[i-1][j-1]) + 1;

```cpp
int editDistachDP(string s, string t){
	int n = s.length(), m = t.length();
	vector<vector<int>> dp(n+1, vector<int>(m+1, 0));
	//状态转移 首行首列
	for(int i=1; i<=n; i++){
		dp[i][0] = i;
	}
	for(int j=1; j<=m ; j++){
		dp[0][j] = j;
	}
	//状态转移
	for(int i=1; i<=n; i++){
		for(int j=1; j<=m; j++){
			if(s[i-1] == t[j-1]){
				dp[i][j] = dp[i-1][j-1];
			}else{
				dp[i][j] = min(min(dp[i-1][j], dp[i][j-1]), dp[i-1][j-1]) +1;
			}
		}
	}
	return dp[n][m];
}
```


空间优化

```cpp
int editDistanceDPComp(string s, string t){
	int n = s.length(), m=t.length();
	vector<int> dp(m+1, 0);
	//状态转移 首行
	for(int j=1; j<=m; j++){
		dp[j] = j;
	}
	//状态转移 其余行
	for(int i=1; i<=n; i++){
		// 状态转移 首列
		int leftup = dp[0];
		dp[0] = i;
		for(int j=1; j<= m; j++){
			int temp = dp[j];
			if(s[i-1] == t[j-1]){
				dp[j] = leftup;
			}else{
				dp[j] = min(min(dp[j-1], dp[j]), leftup) + 1;
			}
			leftup = temp;
		}
	}
	return dp[m];
}
```
