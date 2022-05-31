---
layout: post

---

Buy and Sell *Stock* ...

#### [309. Best Time to Buy and Sell Stock with Cooldown](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

$dp[i][j]$ represents the maximum profit in state j on day i and there are 3 conditions:

1. don't hold stock：
   - no stock sold and didn't hold stock before(yesterday) -- $dp[i][0]$
   - sold stock -- $dp[i][1]$
2. hold stock -- $dp[i][2]$

initial state: $dp[i][0]=0, dp[i][1]=0, dp[i][2]=-prices[0]$

状态转移方程: 

- $dp[i][0]$ = Math.max($dp[i-1][0], dp[i-1][1]$) -- 状态0只能从状态0或1转移而来
- $dp[i][1]$ = $dp[i-1][2] + prices[i]$ -- 买了股票则昨天一定要持有股票
- $dp[i][2]$ = Math.max($dp[i-1][1]-prices[i], dp[i-1][2]$ -- 今天有股票，可以昨天没有今天现买，也可以本来（昨天）就持有股票

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len = prices.length;
        if(len<=1) return 0;
        int[][] dp = new int[len][3];
        dp[0][0] = 0;
        dp[0][1] = 0;
        dp[0][2] = -prices[0];
        for(int i=1; i<len; i++) {
            dp[i][0] = Math.max(dp[i-1][0],dp[i-1][1]);
            dp[i][1] = dp[i-1][2] + prices[i];
            dp[i][2] = Math.max(dp[i-1][0]-prices[i], dp[i-1][2]);
        }
        return Math.max(dp[len-1][0],dp[len-1][1]);
    }
}
```

Try to reduce the space complexity to $O(1)$.

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len = prices.length;
        if(len<=1) return 0;
        int dp_0 = 0, dp_1 = 0, dp_2 = -prices[0];
        for(int i=1; i<len; i++) {
            int new_0 = Math.max(dp_0, dp_1);
            int new_1 = dp_2 + prices[i];
            int new_2 = Math.max(dp_0-prices[i], dp_2);
            /* new dp states depend on the old ones, 
               so use 'new' states to avoid changing the old dp states. */
            dp_0 = new_0;
            dp_1 = new_1;
            dp_2 = new_2;
        }
        return Math.max(dp_0, dp_1);
    }
}
```

#### [714. Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

1. no stock hold: $dp[i][0]$=Max$(dp[i-1][1]+price,dp[i-1][0])$
2. hold stock: $dp[i][1]$=Max$(dp[i-1][0]-price-fee,dp[i-1][1])$

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int len = prices.length;
        int dp_1 = 0, dp_2 = -prices[0]-fee;
        for(int i=1; i<len; i++) {
            int new_1 = Math.max(dp_2+prices[i], dp_1);
            int new_2 = Math.max(dp_1-prices[i]-fee, dp_2);
            dp_1 = new_1;
            dp_2 = new_2;
        }
        return dp_1;
    }
}
```

#### [123. Best Time to Buy and Sell Stock III](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/)

5 states:

| State | hold stock? | number of transactions |                                                        |
| ----- | ----------- | ---------------------- | ------------------------------------------------------ |
| dp_00 | No/0        | 0                      | 恒为0因为一次都没买                                    |
| dp_01 | No/0        | 1                      | 卖出过一次现在未持有，可能今天卖出也可能之前(昨天)卖出 |
| dp_02 | No/0        | 2                      | 卖出了两次现在未持有，可能今天卖出也可能之前(昨天)卖出 |
| dp_10 | Yes/1       | 0                      | 一次都没卖现在持有，可能今天买的也可能之前(昨天)买的   |
| dp_11 | Yes/1       | 1                      | 卖出了一次现在持有，可能今天买的也可能之前(昨天)买的   |
| dp_12 | Yes/1       | 2                      | 卖出了两次次现在持有，不符合题意，不考虑               |

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len = prices.length;
        int dp_00 = 0, dp_01 = Integer.MIN_VALUE/2, dp_02 = Integer.MIN_VALUE/2;
        int dp_10 = -prices[0], dp_11 = Integer.MIN_VALUE/2;
        for (int i = 1; i < len; i++) {
            int new_dp_01 = Math.max(dp_10+prices[i], dp_01);
            int new_dp_02 = Math.max(dp_11+prices[i], dp_02);
            int new_dp_10 = Math.max(dp_00-prices[i], dp_10);
            int new_dp_11 = Math.max(dp_01-prices[i], dp_11);
            dp_01 = new_dp_01;
            dp_02 = new_dp_02;
            dp_10 = new_dp_10;
            dp_11 = new_dp_11;
        }
        return Math.max(0,Math.max(dp_02, dp_01));
    }
}
```

$O(N)$ and $O(1)$.

**OR** use time-sequential order: 更加通用的解法见188

0. before the first stock buying

1. holding the first stock before selling it
2. selling the first stock
3. before the stock buying but after selling the first stock
4. selling the second stock

#### [188. Best Time to Buy and Sell Stock IV](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/)

check [here](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/solution/dai-ma-sui-xiang-lu-188-mai-mai-gu-piao-w01v7/).

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        if(prices.length == 0) return 0;
        int[] dp=new int[2*k+1];
        /*
        	0初始状态，奇买，偶卖
        	*/
        for (int i=1; i<2*k; i+=2) {
            dp[i] = -prices[0]; 
            /*
            	-- dp[i] 是当前的最大收益 -- 
            	买入的节点初始化为 -prices[0]
            		为什么 ？？？
            		dp[i] = Integer.MIN_VALUE;也能过
            	卖出节点初始化为0
            		是因为买股票的最小的最大收益最少为0 -- 即不买
            	*/
        }
        for(int i = 0; i<prices.length; i++) {
            for(int j=1; j<2*k; j+=2) {
                dp[j+1] = Math.max(dp[j+1],dp[j]+prices[i]);
                dp[j] = Math.max(dp[j],dp[j-1]-prices[i]);
                /*
                	先更新 dp[j+1] 是为了保证其等号右边的 dp[j] 是"旧"的
                	*/
            }
        }
        return dp[2*k];
    }
}
```

