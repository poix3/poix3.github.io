---
layout: post

---

Problems about dynamic programming (dp) on leetcode.

#### [5.Longest Palindromic Substring](https://leetcode-cn.com/problems/longest-palindromic-substring/)

1. dp table and the meaning of subscript -- $dp[i][j]=true$ i.i.f the substring $[i,j]$ is palindromic
2. recursion formula
   - when $s[i]\neq s[j]$, **false** 
   - when $s[i] = s[j]$, 
     - when $i=j$ or $\lvert i-j\rvert=1$, **true**
     - when $dp[i+1][j-1]=true$, **true**
3. initialize dp table all to **false**
4. traverse from bottom to top, left to right

<img src="/Users/wangfm/Documents/GitHub/leetcodenotebook/dp/5.png" alt="5" style="zoom:50%;" />

```java
class Solution {
    public String longestPalindrome(String s) {
        if(s==null || s.length()<2) return s;
        int len = s.length(), max_length = 0, start = 0;
        boolean dp[][] = new boolean[len][len];
        for(int i=len-1; i>=0; i--) {
            for(int j=i; j<len; j++) {
                if(s.charAt(i)==s.charAt(j) && (j-i+1<=2 || dp[i+1][j-1])) {
                    dp[i][j] = true;
                }
                if(dp[i][j] && j-i+1>max_length) {
                    start = i;
                    max_length = j-i+1;
                }
            }
        }
        return s.substring(start, start+max_length);
    }
}
```

Both the time and space complexity are $O(n^2)$ because of the nested for loops and the two-dimensional **dp** array.

#### [70. Climbing Stairs](https://leetcode-cn.com/problems/climbing-stairs/)

```java
class Solution {
    public int climbStairs(int n) {
        if(n<=2) return n;
        int[] dp = new int[n+1];
        dp[1] = 1;
        dp[2] = 2;
        for(int i=3; i<n+1; i++) {
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];
    }
}
```

Both the time and space complexity are $O(n)$. We can optimize the space complexity since we only need $dp[n]$ at last.

```java
class Solution {
    public int climbStairs(int n) {
        if(n<=2) return n;
        int a=1, b=2, c=0;
        for(int i=2; i<n; i++) {
            c = a+b;
            a = b;
            b = c;
        }
        return c;
    }
}
```

The space complexity is reduced to $O(1)$.

#### [198. House Robber](https://leetcode-cn.com/problems/house-robber/)

*"only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and it will automatically contact the police if two **adjacent** houses were broken into on the same night."*

The recursion formula is $dp[i]=\max{\{dp[i-2]+nums[i],dp[i-1]\}}$, where $dp[i]$ is the maximum benefit from the house 0 to house $i$.

```java
class Solution {
    public int rob(int[] nums) {
        int len = nums.length;
        if(len<=1) return nums[0];
        int[] dp = new int[len];
        dp[0] = nums[0];
        dp[1] = Math.max(dp[0], nums[1]);
        for(int i=2; i<len; i++) {
            dp[i] = Math.max(dp[i-2]+nums[i], dp[i-1]);
        }
        return dp[len-1];
    }
}
```

Similar to Problem 70, we can optimize its space complexity to $O(1)$:

```java
class Solution {
    public int rob(int[] nums) {
        int len = nums.length;
        if(len==1) return nums[0];
        int a = nums[0], b = Math.max(nums[0], nums[1]);
        int c = b;
        for(int i=2; i<len; i++) {
            c = Math.max(a+nums[i], b);
            a = b;
            b = c;
        }
        return c;
    }
}
```

#### [213. House Robber II](https://leetcode-cn.com/problems/house-robber-ii/)

Though similar to Problem 198, since "*All houses at this place are **arranged in a circle***", we can only choose the first or last house to rob at a time.

```java
class Solution {
    public int rob(int[] nums) {
        int len = nums.length;
        if(len==1) return nums[0];
        if(len==2) return Math.max(nums[0],nums[1]);
        return Math.max(robrob(nums, 0, len-1), 
                        robrob(nums, 1, len));
    }
    public int robrob(int[] nums, int start, int end) {
        int a = nums[start], b = Math.max(nums[start], nums[start+1]);
        int c = b;
        for(int i=start+2; i<end; i++) {
            c = Math.max(a+nums[i], b);
            a = b;
            b = c;
        }
        return c;
    }
}
```

#### [64. Minimum Path Sum](https://leetcode-cn.com/problems/minimum-path-sum/)

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dp = new int[m][n];
        for(int i=0; i<m; i++) {
            for(int j=0; j<n; j++) {
                if(i==0 && j==0) {
                    dp[0][0] = grid[0][0];
                } 
                else if(i==0) {
                    dp[i][j] = grid[i][j]+dp[i][j-1];
                }
                else if(j==0) {
                    dp[i][j] = grid[i][j]+dp[i-1][j];
                }
                else {
                    dp[i][j] = grid[i][j]+Math.min(dp[i-1][j],dp[i][j-1]);
                }
            }
        }
        return dp[m-1][n-1];
    }
}
```

#### [62. Unique Paths](https://leetcode-cn.com/problems/unique-paths/)

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        for(int i=0; i<m; i++) dp[i][0] = 1;
        for(int j=0; j<n; j++) dp[0][j] = 1;
        for(int i=1; i<m; i++) {
            for(int j=1; j<n; j++) {
                dp[i][j] = dp[i-1][j]+dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
}
```

#### [303. Range Sum Query - Immutable](https://leetcode-cn.com/problems/range-sum-query-immutable/)

The key idea is the sumRange between index $i$ and $j$ is $sums[right+1]-sums[left]$.

```java
class NumArray {

    private int[] sums;

    public NumArray(int[] nums) {
        int len = nums.length;
        sums = new int[len+1];
        for (int i = 0; i < len; i++) {
            sums[i+1] = sums[i] + nums[i];
        }
    }
    
    public int sumRange(int left, int right) {
        return sums[right+1]-sums[left];
    }
}
```

#### [413. Arithmetic Slices](https://leetcode-cn.com/problems/arithmetic-slices/)

$dp[i]$ is the number of arithmetic subarrays **ending** with $nums[i]$.

```java
class Solution {
    public int numberOfArithmeticSlices(int[] nums) {
        if(nums==null || nums.length<3) return 0;
        int len = nums.length, ans = 0;
        int[] dp = new int[len];
        for(int i=2; i<len; i++) {
            if(nums[i]-nums[i-1]==nums[i-1]-nums[i-2]) {
                dp[i] = dp[i-1]+1;
                ans += dp[i];
            }
        }
        return ans;
    }
}
```

#### [343. Integer Break](https://leetcode-cn.com/problems/integer-break/)

$dp[i]$ is the max product, and there are two situations when breaking the number $i$ into $j$ and $i-j$.

1. $i-j$ will NOT be broken, so the product is $j*(i-j)$;
2. $i-j$ will be broken into several positive integers, so the product is $j*dp[i-j]$.

```java
class Solution {
    public int integerBreak(int n) {
        int[] dp = new int[n+1];
        for(int i=2; i<=n; i++) {
            int curr = 0;
            for(int j=1; j<i; j++) {
                // try every number smaller than i
                int temp = Math.max(j*(i-j), j*dp[i-j]);
                curr = Math.max(temp, curr);
            }
            dp[i] = curr;
        }
        return dp[n];
    }
}
```

#### [279. Perfect Squares](https://leetcode-cn.com/problems/perfect-squares/)

$dp[i]$ is the least number of perfect square numbers that sum to $i$. 

$dp[i]=dp[i-j*j]+1$, where $j\textless i$.

```java
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n+1];
        for(int i=1; i<=n; i++) {
            int min = Integer.MAX_VALUE;
            for(int j=1; j*j<=i; j++) {
                // j*j\leq i, so O(\sqrt{n})
                min = Math.min(min, dp[i-j*j]);
            }
            dp[i] = min+1;
        }
        return dp[n];
    }
}
```

The time complexity is $O(n\sqrt{n})$, where $n$ is the given postive integer.

#### [91. Decode Ways](https://leetcode-cn.com/problems/decode-ways/)

- $dp[i]$ is the number of ways to encode the subarray from index 0 to $i$.
- If the "current position" $nums[i]$ forms an item alone, $dp[i]=dp[i-1]$
- If the "current" and "previous position" $nums[i]$ and $nums[i-1]$ together form the item, $dp[i]=dp[i-1]+dp[i-2]$

```java
class Solution {
    public int numDecodings(String s) {
        int len = s.length();
        s = " " + s;
        char[] nums = s.toCharArray();
        int[] dp = new int[len+1];
        dp[0] = 1;
        for (int i=1; i<=len; i++) { 
            int a = nums[i]-'0', b = 10*(nums[i-1]-'0')+(nums[i]-'0');
            if (1 <= a && a <= 9) dp[i] = dp[i - 1];
            if (10 <= b && b <= 26) dp[i] += dp[i - 2];
        }
        return dp[len];
    }
}
```

Also can use loop array to reduce the Space complexity to $O(1)$.

```java
class Solution {
    public int numDecodings(String s) {
        int len = s.length();
        s = " " + s;
        char[] nums = s.toCharArray();
        int[] dp = new int[3];
        dp[0] = 1;
        for (int i=1; i<=len; i++) { 
            dp[i%3] = 0;
            int a = nums[i]-'0', b = 10*(nums[i-1]-'0')+(nums[i]-'0');
            if (1 <= a && a <= 9) dp[i%3] = dp[(i-1)%3];
            if (10 <= b && b <= 26) dp[i%3] += dp[(i-2)%3];
        }
        return dp[len%3];
    }
}
```

#### [300. Longest Increasing Subsequence](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int ans = 1, len = nums.length;
        int[] dp = new int[len];
        Arrays.fill(dp,1);
        for(int i=1; i<len; i++) {
            for(int j=0; j<i; j++) {
                if(nums[i]>nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j]+1);
                }
            }
            ans = Math.max(ans, dp[i]);
        }
        return ans;
    }
}
```

#### [646. Maximum Length of Pair Chain](https://leetcode-cn.com/problems/maximum-length-of-pair-chain/)

```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        Arrays.sort(pairs, (a, b) -> a[0] - b[0]);
        int len = pairs.length, ans = 0;
        int[] dp = new int[len];
        Arrays.fill(dp, 1);
        for(int i=1; i<len; i++) {
            for(int j=i-1; j>=0; j--) {
                if(pairs[j][1] < pairs[i][0]) {
                    dp[i] = dp[j]+1;
                    break;
                }
            }
        }
        return dp[len-1];
    }
}
```

#### [1143. Longest Common Subsequence](https://leetcode-cn.com/problems/longest-common-subsequence/)

$dp[i][j]$ is the longest common subsequence of $text1[0:i]$ and $text2[0:j]$. 

- $dp[i][j]=dp[i-1][j-1]$ *if* $text1[i]==text2[j]$
- *else*: $dp[i][j]=\max{(dp[i-1][j],dp[i][j-1])}$
  - pay attention that under such circumstance, $dp[i-1][j-1]\leq dp[i][j-1]\text{ and }dp[i-1][j]$


```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length(), n = text2.length();
        int[][] dp = new int[m+1][n+1];
        for(int i=1; i<=m; i++) {
            for(int j=1; j<=n; j++) {
                if(text1.charAt(i-1)==text2.charAt(j-1)) {
                    dp[i][j] = dp[i-1][j-1]+1;
                }
                else {
                    dp[i][j] = Math.max(dp[i-1][j],dp[i][j-1]);
                }
            }
        }
        return dp[m][n];
    }
}
```

#### [416. Partition Equal Subset Sum](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

This problem is equivalent to whether we can pick out some numbers whose sum is equal to half the sum of the whole array.

 We create a **len** by **target+1** array of boolean type, where **len** is the length of the oginal array. **dp\[i\]\[j\]** indicates whether there are some number between **\[0,i\]**, whose sum is equal to **j** (Each number can only be used once) .

$$dp[i][j]=dp[i-1][j]\text{ or }dp[i][j-nums[i]]$$

- $dp[i-1][j]$ -- $nums[i]$ is NOT chosen.
- $dp[i][j-nums[i]]$ -- $nums[i]$ is chosen.
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

- dp[j] = dp[j] || dp[j - nums[i]] can be understood as dp[j] (new) = dp[j] (old) || dp[j - nums[i]] (old)
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

