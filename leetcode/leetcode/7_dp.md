# 深入浅出动态规划

## 7.2 基本动态规划:  一维
70. Climbing Stairs
```cpp
int climbStairs(int n){
    if(n<=2) return n;
    vector<int> dp(n+1, 1);
    for(int i=2; i<=n; i++){
        dp[i] =dp[i-1] + dp[i-2];
    }
    return dp[n];
}
//优化空间
int climbStairs(int n){
    if(n<=2) return;
    int pre2 = 1, pre1 = 2, cur;
    for(int i=2; i<n; ++i){
        cur = pre1 + pre2;
        pre2 = pre1;
        pre1 = cur;
    }
    return cur;
}
```
198 House Robber
dp[i] = max(dp[i-1], nums[i-1] + dp[i-2])
```cpp
int rob(vector<int> &nums){
    if(nums.empty()) return 0;
    int n = nums.size();
    vector<int>dp(n+1, 0);
    dp[1] = nums[0];
    for(int i=2; i<=n; ++i){
        dp[i] = max(dp[i-1], num[i-1] + dp[i-2]);
    }
    return dp[n];
}

int rob(vector<int>& nums){
    if(nums.empty()) return 0;
    int n = nums.size();
    if(n==1) return nums[0];
    int pre2 =0, pre1 = 0; cur;
    for(int i=0; i<n ; ++i){
        cur = max(pre2+nums[i], pre1);
        pre2 = pre1;
        pre1 = cur;
    }
    return cur;
}

```
413. Arithmetic Slices
等差数列 num[i] - num[i-1] = num[i-1] - num[i-2]
```cpp
int numberOfArithmeticSlices(vector<int>& nums){
    int n = nums.size();
    if(n<3) return 0;
    vector<int> dp(n, 0);
    for(int i=2; i<n ;++i){
        if(nums[i]-nums[i-1] == nums[i-1] - nums[i-2]){
            dp[i] = dp[i-1] + 1;
        }
    }
    return accumulate(dp.begin(), dp.end(), 0);
}
```
##  7.3 基本动态规划: 二维

64. Minimum Path Sum
dp[i][j] = min(dp[i-1][,], [j-1][i]) + grid[i][j]
```cpp
int minPathSum(vector<vector<int>>& grid){
    int m = grid.size(), n=grid[0].size();
    vector<vector<int>> dp(m, vector<n, 0>);
    for(int i=0; i< m; ++i){
        for(int j=0; j<n; ++j){
            if(i==0 && j==0){
                dp[i][j] = grid[i][j];
            }else if(i==0){
                dp[i][j] = dp[i][j-1] + grid[i][j];
            }else if(j==0){
                dp[i][j] = dp[i-1][j] + grid[i][j];
            }else{
                dp[i][j] = min(dp[i-1][j], dp[i][j-1])+grid[i][j];
            }
        }
    }
    return dp[m-1][n-1];
}

int minPathSum(vector<vector<int>>& grid){
    int m = grid.size(), n=grid[0].size();
    vector<int> dp(n, 0);
    for(int i=0; i<m; ++i){
        for(int j=0; j<n; ++j){
            if(i==0 && j==0){
                dp[j] = grid[i][j];
            }else if(i==0){
                dp[j] = dp[j-1] + grid[i][j];
            }else if(j==0){
                dp[j] = dp[j] + grid[i][j];
            }else{
                dp[j] = min(dp[j], dp[j-1]) + min[i][j];
            }
        }
    }
    return dp[n-1];
}

```
542. 01 Matrix(Medium)
```cpp
vector<vector<int>> updateMatrix(vector<vector<int>>& matrix){
    if(matrix.empty()) return {};
    int n=matrix.size(), m = matrix[0].size();
    vector<vector<int>> dp(n, vector<int>(m, INT_MAX-1));
    for(int i=0; i<n; ++i){
        for(int j=0; j<m; ++j){
            if(matrix[i][j] == 0){
                dp[i][j] = 0;
            }else{
                if(j>0){
                    dp[i][j] = min(dp[i][j], dp[i][j-1] + 1);
                }
                if(i>0){
                    dp[i][j] = min(dp[i][j], dp[i-1][j]+1);
                }
            }
        }
    }
    for(int i=n-1 ; i>=0, --i){
        for(int j =m-1; j>=0; --j){
            if(matrix[i][j] !=0){
                if( j<m-1){
                    dp[i][j] = min(dp[i][j], dp[i][j+1] + 1)；
                }
                if(i <n-1){
                    dp[i][j] = min(dp[i][j], dp[i+1][j] + 1);
                }
            }
        }
    }
    return dp;
}
```
221. Maximal Square(Medium)
```cpp
int maximalSquare(vector<vector<char>>& matrix){
    if(matrix.empty() || matrix[0].empty()){
        return 0;
    }
    int m = matrix.size(), n=matrix[0].size(), max_side = 0;
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for(int i=1; i<=m; ++i){
        for(int j=1; j<=n;++j){
            if(matrix[i-1][j-1]=='1'){
                dp[i][j] = min(dp[i-1][j-1], min(dp[i][j-1], dp[i-1][j])) + 1;
            }
            max_side = max(max_side, dp[i][j]);
        }
    }
    return max_side * max_side;
}
```
## 7.4 分割类型题
279. Perfect Squares
```cpp
int numSquares(int n){
    vector<int> dp(n+1, INT_MAX);
    dp[0] = 0;
    for(int i=1; i<= n; ++i){
        for(int j=1; j*j<=i; ++j){
            dp[i] = min(dp[i], dp[i-j*j] + 1);
        }
    }
    return dp[n];
}

```
91. Decode Ways
```cpp
int numDecodings(string s){
    int n = s.length();
    if(n==0) return 0;
    int prev = s[0] - '0';
    if(!prev) return 0;
    if(n==1) return 1;
    vector<int> dp(n+1, 1);
    for( int i=2; i<=n; ++i){
        int cur = s[i-1] - '0';
        if((prev ==0 || prev > 2) && cur ==0)
            return 0;
        if((prev<2 && prev >0) || prev ==2 && cur <7){
            if(cur){
                dp[i] = dp[i-2] + dp[i-1];
            }else{
                dp[i] = dp[i-2];
            }
        }else{
            dp[i] = dp[i-1];
        }
        prev = cur;
    }
    return dp[n];
}

```
139. word Break
```cpp
bool wordBreak(string s, vector<string>& wordDict){
    int n = s.length();
    vector<bool> dp(n+1, false);
    dp[0] = true;
    for(int i=1; i<=n; ++i){
        for(const string & word : wordDict){
            int len = word.length();
            if(i>=len && s.substr(i-len, len) == word){
                dp[i] = dp[i] || dp[i-len];
            }
        }
    }
    return dp[n];
}
```

## 7.5 子序列问题
300. Longest Increasing Subsequence
子序列不必连续，子数组与字符串必须连续
```cpp
int lengthOfLIS(vector<int>& nums){
    it max_length = 0, n=nums.size();
    if(n<=1) return n;
    vector<int> dp(n, 1);
    for(int i=0; i<n; ++i){
        for(int j=0; j<i; ++j){
            if(nums[i] > nums[j]){
                dp[i] =max(dp[i], dp[j] + 1);
            }
        }
        max_length = max(max_length, dp[i]);
    }
    return max_length;
}

```
1143. Longest Common Subsequence 
```cpp
int longestCommonSubsequence(string text1, string text2){
    int m = text1.length(), n = text2.length();
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for(int i=1; i<=m; ++i){
        for(int j=1; j<=n; ++j){
            if(text[i-1]==text[j-1]){
                dp[i][j] = dp[i-1][j-1] + 1;
            }else{
                dp[i][j] = max(dp[i-1][j], d[i][j-1]);
            }
        }
    }
    return dp[m][n];
}
```
## 7.6 背包问题
```cpp
// 0-1 背包问题
int knapsack(vector<int> weights, vector<int> values, int N, int W){
    vector<vector<int>> dp(N+1, vector<int>(W+1, 0));
    for(int i=1; i<=N; ++i){
        int w = weights[i-1],v= values[i-1];
        for(int j=1; j<=w;++j){
            if(j>w){
                dp[i][j] = max(dp[i-1][j], dp[i-1][j-w]+v);
            }else{
                dp[i][j] = dp[i-1][j];
            }
        }
    }
    return dp[N][W];
}
int knapsack(vector<int> weights, vector<int> values, int N, int W){
    vector<int> dp(W+1, 0);
    for(int i=1; i<=N; ++i){
        int w = weights[i-1],v= values[i-1];
        for(int j=W; j>=w;--j){
            dp[j] = max(dp[j], dp[j-W] + V);
        }
    }
    return dp[W];
}

```
416. Partition Equal Subset Sum
```cpp
bool canPartition(vector<int> &nums){
    int sum = accumulate(nums.begin(), nums.end(), 0);
    if(sum % 2) return false;
    int target = sum/2, n=nums.size();
    vector<vector<bool>> dp(n+1, vector<bool>(target + 1, false));
    dp[0][0] = true;
    for(int i=1; i<=n; ++i){
        for(int j=0;j<=target; ++j){
            if(j<nums[i-1]){
                dp[i][j] = dp[i-1][j];
            }else{
                dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i-1]];
            }
        }
    }
    return dp[n][target];
}

bool canPartition(vector<int> &nums){
    int sum = accumulate(nums.begin(), nums.end(), 0);
    if(sum%2) return false;
    int target = sum/2; n=sums.size();
    vector<bool> dp(target+1, false);
    dp[0] = true;
    for(int i=1; i<=n; ++i){
        for(int j=target; j>=nums[i-1]; --j){
            dp[j] = dp[j] || dp[j-nums[i-1]];
        }
    }
    return dp[target];
}
```
474. Ones and Zeros
```cpp
int findMaxForm(vector<string>& strs, int m, int n){
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for(const string & str:strs){
        auto[count0, count1] = count(str);
        for(int i=m; i>=count0; --i){
            for(j=n; j>=cont1; --j){
                dp[i][j] = max(dp[i][j], 1+ dp[i-count0][j-count1]);
            }
        }
    }
    return dp[m][n];
}

```
322. Coin Change
```cpp
int coinChange(vector<int>& coins, int amount){
    if(coins.empty()) return -1;
    vector<int> dp (amount+1, amount+1);
    dp[0] = 0;
    for(int i=1; i<=amount; ++i){
        for(count int & coin : coins){
            if(i>=coin){
                dp[i] = min(dp[i], dp[i-coin]+1);
            }
        }
    }
    return dp[amount] = amount + 1? -1:dp[amount];
}
```
## 7.7 字符串编辑
72. Edit Distance
```cpp
int minDistance(string word1, string word2){
    int m=word1.length(), n=word2.length();
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for(int i=0; i<=m; ++i){
        for(int j=0; j<=n; ++j){
            if(i==0){
                dp[i][j] = j;
            }else if(j==0){
                dp[i][j] = i;
            }else{
                dp[i][j] = min(dp[i-1] + ((word1[i-1]==word2[j-1]) ? 0 : 1), min(dp[i-1][j]+1, dp[i][j-1]+1));
            }
        }
    }
    return dp[m][n];
}
```
 650. 2 Keys Keyboard
 ```cpp
int minSteps(int n){
    vector<int> dp(n+1);
    for(int i=2; i<=n; ++i){
        dp[i] = i;
        for(int j=2; j*j<= i, ++j){
            if(i%j==0){
                dp[i] = dp[j] + dp[i/j];
                break;
            }
        }
    }
    return dp[n];
}
 ```
 10. Regular Expression Matching
 ```cpp
#todo
 ```
 ## 7.8 股票交易
 121. Best Time to Buy and Sell Stock
 ```cpp
int maxProfit(vector<int>& prices){
    int sell = 0; buy+INT_MIN;
    for(int i=0; i<prices.size(); ++i){
        buy = max(buy, -prices[i]);
        sell = max(sell, buy+prices[i]);
    }
    return sell;
}
 ```
 188. Best Time to Buy and Sell Stock IV
 ```cpp
int maxProfit(int k, vector<int>& prices){
    int days = prices.size();
    if(days<2){
        return 0;
    }
    if(k*2>=days){
        return maxProfitUnlimited(prices);
    }
    vector<int> buy(k+1, INT_MIN), sell(k+1, 0);
    for(int i=0; i< days; ++i){
        for(int j=1; j<=k; ++j){
            buy[j] = max(buy[j], sell[j-1] - prices[i]); 
            sell[j] = max(sell[j], buy[j] + prices[i]);
        }
    }
    return sell[k];
}
//辅函数
int maxProfitUnlimited(vector<int> prices){
    int maxProfit = 0;
    for(int i=1; i<prices.size(); ++i){
        if(prices[i] > prices[i-1]){
            maxProfit += prices[i] - prices[i-1];
        }
    }
    return maxProfit;
}
 ```
309. Best Time to Buy and Sell Stock with Cooldown
```cpp
int maxProfit(vector<int>& prices){
    int n = prices.size();
    if(n==0){
        return 0;
    }
    vector<int> buy(n), sell(n), s1(n), s2(n);
    s1[0] = buy[0] = -prices[0];
    sell[0] = s2[0] =0 ;
    for(int i=1; i<n ; i++){
        buy[i] = s2[i-1] - prices[i];
        s1[i] = max(buy[i-1], s1[i-1]);
        sell[i] = max(buy[i-1], s1[i-1] )+ prices[i];
        s2[i] = max(s2[i-1], sell[i-1]);
    }
    return max(sell[n-1], s2[n-1]);
}
```