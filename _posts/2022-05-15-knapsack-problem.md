---
layout: post


---

Given a set of items, each with a weight and a value, determine the number of each item to include in a collection so that the total weight is less than or equal to a given limit and the total value is as large as possible. -- wikipedia

### 0-1 Knapsack Problem

#### [416. Partition Equal Subset Sum](https://leetcode.cn/problems/partition-equal-subset-sum/)

This problem is equivalent to whether we can pick out some numbers whose sum is equal to half the sum of the whole array.

 We create a **len** by **target+1** array of boolean type, where **len** is the length of the oginal array. **dp\[i\]\[j\]** indicates whether there are some number between **\[0,i\]**, whose sum is equal to **j** (Each number can only be used once) .

$$dp[i][j]=dp[i-1][j]\text{ or }dp[i-1][j-nums[i]]$$

- $dp[i-1][j]$ -- $nums[i]$ is NOT chosen.
- $dp[i-1][j-nums[i]]$ -- $nums[i]$ is chosen.
- special case
  - if $nums[i]==j, dp[i][j]=true$

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int len = nums.length, target = 0;
        for(int t:nums) {
            target += t;
        }
        if(target%2 != 0) return false;
        target /= 2;
        boolean[][] dp = new boolean[len][target+1];
        dp[0][0] =  true;
        for(int i=1; i<len; i++) {
            for(int j=0; j<=target; j++) {
                if(nums[i]==j) {
                    dp[i][j] = true;
                }
                else if(nums[i]<j) {
                    dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i]];
                }
                else {
                    dp[i][j] = dp[i-1][j];
                }
                // pruning
                if(dp[i][target]==true) {
                    return true;
                }
            }
        }
    return dp[len-1][target];
    }
}
```

The time and space complexity is $O(NC)$, where $N$ is the number of elements in $nums$, $C$ is half of the sum of $nums$.

Try to reduce the space complexity to $O(C)$:

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int len = nums.length, target = 0;
        for(int temp: nums) target += temp;
        if(target%2!=0) return false;
        target /= 2;
        boolean[] dp = new boolean[target+1];
        dp[0] = true;
        for(int i=1; i<len; i++) {
            for(int j=target; j>=nums[i]; j--) {
                // pruning
                if(dp[target]) {
                    return true;
                }
                dp[j] = dp[j] || dp[j-nums[i]];
            }
        }
        return dp[target];
    }
}
```

When using 1d array, the reverse order is used. 

- dp[j] = dp[j] \|\| dp[j - nums[i]] can be understood as dp[j] (new) = dp[j] (old) \|\| dp[j - nums[i]] (old)
- if sequential order is used, dp[j - nums[i]] will be updated to the new value by the previous operation

#### [494. Target Sum](https://leetcode-cn.com/problems/target-sum/)

** pay attention that `0 <= nums[i] <= 1000`

- $dp[i][j]$ is the number of different expressions out of nums from 0 to i.
- $dp[i][j]=dp[i-1][j-nums[i]]+dp[i-1][j+nums[i]]$, pay attention to the **boundary**.
- Since '+' and '-' are both possible, the columns' meaning of $dp$ is the all possible result.
  - see [here](https://leetcode-cn.com/problems/target-sum/solution/dong-tai-gui-hua-si-kao-quan-guo-cheng-by-keepal/)

```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int len = nums.length, sum = 0;
        for(int temp: nums) sum += temp;
        if(Math.abs(sum)<Math.abs(target)) return 0;

        int range = sum*2+1;
        int[][] dp = new int[len][range];
        // consider nums[0]==0
        dp[0][sum-nums[0]] += 1;
        dp[0][sum+nums[0]] += 1; 
        
        for(int i=1; i<len; i++) {
            for(int j=-sum; j<=sum; j++) {
                if((j+nums[i])>sum)
                    dp[i][j+sum] = dp[i-1][j-nums[i]+sum];
                else if((j-nums[i])<-sum)
                    dp[i][j+sum] = dp[i-1][j+nums[i]+sum];
                else 
                    dp[i][j+sum] = dp[i-1][j-nums[i]+sum]+dp[i-1][j+nums[i]+sum];
            }
        }
        return dp[len-1][sum+target];
    }
}
```

Moreover, this problem can be converted to 0-1 Knapsack Problem:

Let sum(P) be the set with '+' symbol, sum(N) be the set with '-' symbol.

- target = sum(P) - sum(N)
- target + sum(nums) = sum(P) - sum(N) + sum(nums)    //sum(nums) = sum(P) + sum(N)
- target + sum(nums) = 2 * sum(P)
- sum(P) = (target + sum(nums)) / 2

```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int len = nums.length, sum = 0;
        target = Math.abs(target); //why?
        /*
        	If this array somehow can be added to make up a positive number, 
        	then it must be able to use subtraction to make up its opposite number.
        	The rest is the same as problem 494 except the bagSize
        */
        for(int temp: nums) {
            sum += temp;
        }
        if((sum+target)%2==1 || sum<target) {
            /*
            	the requirement for sum<target here is because of the original question
            */
            return 0;
        } 
        int bagSize = (sum+target)/2;
        int[] dp = new int[bagSize+1];
        dp[0] = 1; 
        /*
            dp[i] indicates the number of ways to sum up to i
           	the number of ways to sum up to 0 is 1, so initialize it to 1
        */
        for(int i=0; i<len; i++) {
            for(int j=bagSize; j>=nums[i]; j--) {
                /*
            		j>=nums[i] to make sure j-nums[i] is non-negative
            	*/
                dp[j] += dp[j-nums[i]];
            }
        }
        return dp[bagSize];
    }
}
```

#### [474. Ones and Zeroes](https://leetcode.cn/problems/ones-and-zeroes/)

used 3d dp array.

```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int len = strs.length;
        int[][] count = new int[len][2];
        for(int i=0; i<len; i++) {
            int zero = 0, one = 0;
            for(char c: strs[i].toCharArray()) {
                if(c=='0') zero++; else one++;
            }
            count[i] = new int[]{zero,one};
        }  
        int[][][] dp = new int[len+1][m+1][n+1];
        for(int k=1; k<=len; k++) {
            int zero = count[k - 1][0], one = count[k - 1][1];
            for(int i=0; i<=m; i++) {
                for(int j=0; j<=n; j++) {
                    if(i>=zero && j>=one) {
                        dp[k][i][j] = Math.max(dp[k-1][i][j],dp[k-1][i-zero][j-one]+1);
                    }
                    else {
                        dp[k][i][j] = dp[k-1][i][j];
                    }
                }
            }
        }
        return dp[len][m][n];
    }
}
```

The time complexity for pre-processing is $O(\sum_{i=0}^{k-1}len(strs[i]))$ and for the state transition is $O(k*m*n)$.

The space complexity is $O(k*m*n)$

Now try to reduce the space complexity to $O(m*n)$, similar to Problem 416.

```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int len = strs.length;
        int[][] count = new int[len][2];
        for(int i=0; i<len; i++) {
            int zero = 0, one = 0;
            for(char c: strs[i].toCharArray()) {
                if(c=='0') zero++; else one++;
            }
            count[i] = new int[]{zero,one};
        }  
        int[][] dp = new int[m+1][n+1];
        for(int k=1; k<=len; k++) {
            int zero = count[k - 1][0], one = count[k - 1][1];
            for(int i=m; i>=zero; i--) {
                for(int j=n; j>=one; j--) {
                    dp[i][j] = Math.max(dp[i][j], dp[i-zero][j-one]+1);
                }
            }
        }
        return dp[m][n];
    }
}
```

### Unbounded Knapsack Problem (UKP) 

![](/pic/knapsack.png)

See [here](https://leetcode.cn/problems/coin-change/solution/by-flix-su7s/).

#### [322. Coin Change](https://leetcode.cn/problems/coin-change/)

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        if(amount == 0) return 0;
        int len = coins.length;
        int[] dp = new int[amount+1];
        Arrays.fill(dp, amount+1);
        dp[0] = 0;
        for(int coin : coins) {
            for(int j = coin; j <= amount; j++) {
                /*
                	note that the second loop traverse from left to right, 
                	which is different from 0-1 knapsack problem. 
                	This difference is determined by their 状态转移方程. 
                	See above.
                */
                dp[j] = Math.min(dp[j], dp[j-coin]+1);
            }
        }
        return dp[amount]==amount+1 ? -1 : dp[amount];
    }
}
```

