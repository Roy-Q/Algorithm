# 动态规划

## 1.九章算法题解

> 视频地址：https://www.bilibili.com/video/BV1xb411e7ww?from=search&seid=11651188262828801435
>
> 博客地址：https://blog.csdn.net/weixin_44550963/article/details/107282087

- 题目类型
- 解题步骤

### 1.硬币问题

> 题目：有三种硬币，面值2.5.7，买一本书需要27元，如何用最少的硬币整好付清。
>
> 首先经过分析，这是一个求最大最小值问题，可用动态规划来求解。
>
> LintCode: https://www.lintcode.com/problem/669/

```C++
class Solution {
public:
    int coinChange(vector<int> &coins, int amount) {
        int f[amount + 1];
        f[0] = 0;           //确定初始状态
        int n = coins.size();

        for(int i = 1;i <= amount; ++i){       //虽然动态规划是向前推导的，但实际计算是从初始条件向后计算的
            f[i] = INT_MAX;                    //这里先将每个f[i]设为INT_MAX   
            								   //1.有利于进行比较（取小值）
            								   //2.越界后会报waring     
            
            for(int j = 0;j < n;++j){           //每一步的结果取决于上一次的结果，而上一次有三种情况
                if(i >= coins[j] && f[i - coins[j] ]!= INT_MAX){ 
                    //第一个条件是为了防止数组向前越界，由来：下面的程序代码
                    //第二个条件是防止INT_MAX越界，由来：下面的程序代码
                    f[i] = min(f[i] , f[i-coins[j]]+1 ); //我们的目的是为了取三次的最小值，通过三次循环的两两比较实现
                }                
            }
        }
        if(f[amount] == INT_MAX){    //不可能出现的情况返回-1
            return -1;
        }
        return f[amount];     
    }
};
```

总结：

- 注意边界条件

### 2.Unique Path(机器人)

> 有一个机器人的位于一个 *m* × *n* 个网格左上角。
>
> 机器人每一时刻只能向下或者向右移动一步。机器人试图达到网格的右下角。
>
> 问有多少条不同的路径？
>
> LintCode:https://www.lintcode.com/problem/114/

```c++
class Solution {
public:
    /**
     * @param m: positive integer (1 <= m <= 100)
     * @param n: positive integer (1 <= n <= 100)
     * @return: An integer
     */
    int uniquePaths(int m, int n) {
        // write your code here
        int f[m][n];
        f[0][0] = 1;  
        for(int i = 0;i < m; ++i ){  //行标
            for(int j = 0;j < n; ++j){
                if(i >= 1 && j >= 1){
                    f[i][j] = f[i-1][j] + f[i][j-1];
                }
                else{
                    f[i][j] = 1;
                }                
            }
        }
        return f[m-1][n-1];
    }
};
```

总结

- 结合上一道硬币的题目，深入了解解题思路和步骤
- 1.确立子问题和状态方程
- 2.代码中：初始情况 + 状态方程
- 3.状态方程的边界情况
- 4.计算顺序（不太懂如何利用）

### 3.Jump Game（青蛙）

> 给出一个非负整数数组，你最初定位在数组的第一个位置。　　　
>
> 数组中的每个元素代表你在那个位置可以跳跃的最大长度。　　　　
>
> 判断你是否能到达数组的最后一个位置。
>
> LintCode:https://www.lintcode.com/problem/116/

```c++
class Solution {
public:
    /**
     * @param A: A list of integers
     * @return: A boolean
     */
    bool canJump(vector<int> &A) {
        // write your code here
        int n = A.size();
        bool f[n];
        f[0] = true;
        for(int i = 1;i < n; ++i ){                 //注意i是从1开始的，因为i==0的时候已经初始化了
            f[i] = false;
            for(int j = 0;j < i; j++ ){            //j是从0开始的，因为能否跳到最后一格取决于A[0] ~ A[n-1]
                if(f[j] == true && A[j] >= i-j){        
                    f[i] = true;
                    break;
                }
            }
        }
        return f[n-1];        
    }
};
```

### 4.剪绳子

> 给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18
>
> LeetCode：https://leetcode-cn.com/problems/jian-sheng-zi-lcof/

```c++
class Solution {
public:
    int cuttingRope(int n) {
        int dp[n+1] ;                 //dp[i]指长度为i的绳子所得的最大乘积
        memset(dp,0,sizeof(dp));      //java如果数组未初始化则为0（好像还可能有null？）
        							  //这里C++必须memset初始化为0，否则为垃圾值，影响后续结果
        dp[2] = 1;   				  //初始条件
        dp[1] = 1;
        dp[0] = 0;
        for(int i = 3;i < n+1 ; i++ ){   //顺序为正序，故从dp[i] ，i从3开始
            for(int j = 2;j < i; j++ ){  //每循环一次代表对当前进行一次分割，由于切1时对结果无影响，所以从长度为2开始切
                dp[i] = max(dp[i], max(j * (i - j), j * dp[i - j]) );     
                //这里的状态转移方程比较难懂
            }
        }
        return dp[n];
    }
};
```





> 1. 我们想求长度为n的绳子剪掉后的最大乘积，可以从前面比n小的绳子转移而来
> 2. dp[i]记录长度为 i 的绳子剪成 m 段后的最大成绩；初始化dp[2] = 1
> 3. 类比三枚硬币问题，dp[i]的取值取决于之前dp[i - 2] ，dp[i - 5] , dp[i - 7] 三项的值，本题取决于从dp[i - j]的值，j 从2 ~ i -1 ,从2开始是因为如果减去1的话，对之后的成绩无任何增益
> 4. 状态方程：max( j * dp[i - j] )           
> 5. 上述方程中dp[i - j]是默认余下的i - j的绳子可分的，但实际上, 当i - j = 2 、 3 时，应该直接乘以本身的长度，而不是dp[i - j];
> 6. 利用条件语句将上述状态方程完善
> 7. 检查的时候先看状态方程有无问题，再从最开始的几步进行检验



```c++
class Solution {
public:
    int cuttingRope(int n) {
        int dp[n+1];
        memset(dp,0,sizeof(dp));

        dp[2] = 1;   

        for(int i = 3;i <= n; ++i ){
            for(int j = 2;j < i; j++ ){
                if(i - j == 1 || i - j == 2 || i - j == 3){
                    dp[i] = max(dp[i],j * (i - j));
                    continue;
                }
                dp[i] = max(dp[i] , j * dp[i - j]);
                //这里默认了剩余的 i - j 是可分的
                //但实际上 当i - j = 2 、 3 时，应该直接乘以本身的长度，而不是dp[i - j];
            
            }
        }

        return dp[n];
    }
};
```

### 5.连续子数组的最大和

> 问题描述：
>
> 输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。
>
> 要求时间复杂度为O(n)。
>
> LeetCode:https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/

分析：

难点1：如何定义dp[i] 的含义是一个棘手点

难点2：状态方程的建立

- 状态方程：dp[i] = max{ dp[i - 1] + nums[i] , nums[i]}      //要么接上，要么自己干
- 最终结果不像之前为dp[n] ， 而是dp[]中的最大值

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        int dp[n];  //dp[i]为以nums[i]结尾数组的子数组和的最大值
        memset(dp,0,sizeof(dp));
       

        dp[0] = nums[0];
        int result = dp[0];
        for(int i = 1;i < n ;++i){
            dp[i] = max(dp[i - 1] + nums[i],nums[i]);
            result = max(dp[i] , result);
        }
        
        return result;
    }
};
```

### 6.无重复字符的最长子串（未完成）

> 题目描述：
>
> 给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。
>
> LeetCode:https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/

状态方程：

- 记 i 为当前位置，当存在s[j] = s[i] ，记此时 j 为距离 s[i] 最近的字符下标
- 若 i - j >=dp[i - 1] + 1时，dp[i] = dp[i - 1] + 1
- 若 i - j < dp[i - 1] 时，dp[i] = i - j ;

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
     	int n = s.size();
        map<char,int> Hashmap;
        int dp[n];       //dp[i]为以s[n]为结尾的不含字符的最长子串的长度
        				 //variable length array bound evaluates to non-positive value 0 
        dp[0] = 1;
        Hashmap[s[0]] = 0;
        int result = dp[0];
        for(int i = 1;i < n; ++i ){
            if(Hashmap.find(s[i]) == Hashmap.end()){   //如果找不到
                Hashmap[s[i]] = i;
                dp[i] = dp[i - 1] + 1;
            }
            else{
                int j = Hashmap[s[i]];        //获取s[i]在Hashmap中最近的索引
                Hashmap[s[i]] = i;            //更新Hashmap
                dp[i] = i - j >= dp[i - 1] + 1 ? dp[i - 1] + 1: i - j;
            }
            result = max(dp[i] , result);
        }
        return result;
    }
};
```



