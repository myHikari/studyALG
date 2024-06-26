# 动态规划

## 状态压缩DP题目

### [1879. 两个数组最小的异或值之和](https://leetcode.cn/problems/minimum-xor-sum-of-two-arrays/)

困难

给你两个整数数组 `nums1` 和 `nums2` ，它们长度都为 `n` 。

两个数组的 **异或值之和** 为 `(nums1[0] XOR nums2[0]) + (nums1[1] XOR nums2[1]) + ... + (nums1[n - 1] XOR nums2[n - 1])` （**下标从 0 开始**）。

- 比方说，`[1,2,3]` 和 `[3,2,1]` 的 **异或值之和** 等于 `(1 XOR 3) + (2 XOR 2) + (3 XOR 1) = 2 + 0 + 2 = 4` 。

请你将 `nums2` 中的元素重新排列，使得 **异或值之和** **最小** 。

请你返回重新排列之后的 **异或值之和** 。

**示例 1：**

```
输入：nums1 = [1,2], nums2 = [2,3]
输出：2
解释：将 nums2 重新排列得到 [3,2] 。
异或值之和为 (1 XOR 3) + (2 XOR 2) = 2 + 0 = 2 。
```

C++版本

```c++
// 方法一：状态压缩动态规划
class Solution {
public:
    int minimumXORSum(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();
        vector<int> f(1 << n, INT_MAX);
        f[0] = 0;
        for (int mask = 1; mask < (1 << n); ++mask) {
            for (int i = 0; i < n; ++i) {
                if (mask & (1 << i)) {
                    f[mask] = min(f[mask], f[mask ^ (1 << i)] + (nums1[__builtin_popcount(mask) - 1] ^ nums2[i]));
                }
            }
        }
        return f[(1 << n) - 1];
    }
};
```

Java版本

```java
// 方法一：状态压缩动态规划
class Solution {
    public int minimumXORSum(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int[] f = new int[1 << n];
        Arrays.fill(f, Integer.MAX_VALUE);
        f[0] = 0;

        for (int mask = 1; mask < (1 << n); ++mask) {
            int count = Integer.bitCount(mask);
            for (int i = 0; i < n; ++i) {
                if ((mask & (1 << i)) != 0) {
                    f[mask] = Math.min(f[mask], f[mask ^ (1 << i)] + (nums1[count - 1] ^ nums2[i]));
                }
            }
        }
        
        return f[(1 << n) - 1];
    }
}
```



### [2172. 数组的最大与和](https://leetcode.cn/problems/maximum-and-sum-of-array/)

困难

给你一个长度为 `n` 的整数数组 `nums` 和一个整数 `numSlots` ，满足`2 * numSlots >= n` 。总共有 `numSlots` 个篮子，编号为 `1` 到 `numSlots` 。

你需要把所有 `n` 个整数分到这些篮子中，且每个篮子 **至多** 有 2 个整数。一种分配方案的 **与和** 定义为每个数与它所在篮子编号的 **按位与运算** 结果之和。

- 比方说，将数字 `[1, 3]` 放入篮子 ***`1`\*** 中，`[4, 6]` 放入篮子 ***`2`\*** 中，这个方案的与和为 `(1 AND ***1\***) + (3 AND ***1\***) + (4 AND ***2***) + (6 AND ***2***) = 1 + 1 + 0 + 2 = 4` 。

请你返回将 `nums` 中所有数放入 `numSlots` 个篮子中的最大与和。

**示例 1：**

```
输入：nums = [1,2,3,4,5,6], numSlots = 3
输出：9
解释：一个可行的方案是 [1, 4] 放入篮子 1 中，[2, 6] 放入篮子 2 中，[3, 5] 放入篮子 3 中。
最大与和为 (1 AND 1) + (4 AND 1) + (2 AND 2) + (6 AND 2) + (3 AND 3) + (5 AND 3) = 1 + 0 + 2 + 2 + 3 + 1 = 9 。
```

C++版本

```c++
// 方法一：三进制状态压缩动态规划
class Solution {
public:
    int maximumANDSum(vector<int>& nums, int numSlots) {
        int n = nums.size();
        int mask_max = 1;
        for (int i = 0; i < numSlots; ++i) {
            mask_max *= 3;
        }
        
        vector<int> f(mask_max);
        for (int mask = 1; mask < mask_max; ++mask) {
            int cnt = 0;
            for (int i = 0, dummy = mask; i < numSlots; ++i, dummy /= 3) {
                cnt += dummy % 3;
            }
            if (cnt > n) {
                continue;
            }
            for (int i = 0, dummy = mask, w = 1; i < numSlots; ++i, dummy /= 3, w *= 3) {
                int has = dummy % 3;
                if (has) {
                    f[mask] = max(f[mask], f[mask - w] + (nums[cnt - 1] & (i + 1)));
                }
            }
        }
        
        return *max_element(f.begin(), f.end());
    }
};
```

Java版本

```java
// 方法一：三进制状态压缩动态规划
class Solution {
    public int maximumANDSum(int[] nums, int numSlots) {
        int n = nums.length;
        int maskMax = 1;
        for (int i = 0; i < numSlots; ++i) {
            maskMax *= 3;
        }
        
        int[] f = new int[maskMax];
        
        for (int mask = 1; mask < maskMax; ++mask) {
            int cnt = 0;
            for (int i = 0, dummy = mask; i < numSlots; ++i, dummy /= 3) {
                cnt += dummy % 3;
            }
            if (cnt > n) {
                continue;
            }
            for (int i = 0, dummy = mask, w = 1; i < numSlots; ++i, dummy /= 3, w *= 3) {
                int has = dummy % 3;
                if (has != 0) {
                    f[mask] = Math.max(f[mask], f[mask - w] + (nums[cnt - 1] & (i + 1)));
                }
            }
        }
        
        return Arrays.stream(f).max().getAsInt();
    }
}
```



### [1947. 最大兼容性评分和](https://leetcode.cn/problems/maximum-compatibility-score-sum/)

中等

有一份由 `n` 个问题组成的调查问卷，每个问题的答案要么是 `0`（no，否），要么是 `1`（yes，是）。

这份调查问卷被分发给 `m` 名学生和 `m` 名导师，学生和导师的编号都是从 `0` 到 `m - 1` 。学生的答案用一个二维整数数组 `students` 表示，其中 `students[i]` 是一个整数数组，包含第 `i` 名学生对调查问卷给出的答案（**下标从 0 开始**）。导师的答案用一个二维整数数组 `mentors` 表示，其中 `mentors[j]` 是一个整数数组，包含第 `j` 名导师对调查问卷给出的答案（**下标从 0 开始**）。

每个学生都会被分配给 **一名** 导师，而每位导师也会分配到 **一名** 学生。配对的学生与导师之间的兼容性评分等于学生和导师答案相同的次数。

- 例如，学生答案为`[1, ***0\***, ***1\***]` 而导师答案为 `[0, ***0\***, ***1\***]` ，那么他们的兼容性评分为 2 ，因为只有第二个和第三个答案相同。

请你找出最优的学生与导师的配对方案，以 **最大程度上** 提高 **兼容性评分和** 。

给你 `students` 和 `mentors` ，返回可以得到的 **最大兼容性评分和** 。

**示例 1：**

```
输入：students = [[1,1,0],[1,0,1],[0,0,1]], mentors = [[1,0,0],[0,0,1],[1,1,0]]
输出：8
解释：按下述方式分配学生和导师：
- 学生 0 分配给导师 2 ，兼容性评分为 3 。
- 学生 1 分配给导师 0 ，兼容性评分为 2 。
- 学生 2 分配给导师 1 ，兼容性评分为 3 。
最大兼容性评分和为 3 + 2 + 3 = 8 。
```

C++版本

```c++
// 方法一：枚举排列
class Solution {
public:
    int maxCompatibilitySum(vector<vector<int>>& students, vector<vector<int>>& mentors) {
        int m = students.size();
        int n = students[0].size();
        vector<vector<int>> g(m, vector<int>(m));
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < m; ++j) {
                for (int k = 0; k < n; ++k) {
                    g[i][j] += (students[i][k] == mentors[j][k]);
                }
            }
        }

        vector<int> p(m);
        iota(p.begin(), p.end(), 0);
        int ans = 0;
        do {
            int cur = 0;
            for (int i = 0; i < m; ++i) {
                cur += g[i][p[i]];
            }
            ans = max(ans, cur);
        } while (next_permutation(p.begin(), p.end()));
        return ans;
    }
};

// 方法二：状态压缩动态规划
class Solution {
public:
    int maxCompatibilitySum(vector<vector<int>>& students, vector<vector<int>>& mentors) {
        int m = students.size();
        int n = students[0].size();
        vector<vector<int>> g(m, vector<int>(m));
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < m; ++j) {
                for (int k = 0; k < n; ++k) {
                    g[i][j] += (students[i][k] == mentors[j][k]);
                }
            }
        }

        vector<int> f(1 << m);
        for (int mask = 1; mask < (1 << m); ++mask) {
            int c = __builtin_popcount(mask);
            for (int i = 0; i < m; ++i) {
                // 判断 mask 的第 i 位是否为 1
                if (mask & (1 << i)) {
                    f[mask] = max(f[mask], f[mask ^ (1 << i)] + g[c - 1][i]);
                }
            }
        }
        return f[(1 << m) - 1];
    }
};
```

Java版本

```java
// 方法一：枚举排列
class Solution {
    public int maxCompatibilitySum(int[][] students, int[][] mentors) {
        int m = students.length;
        int n = students[0].length;
        int[][] g = new int[m][m];

        // Calculate compatibility scores
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < m; ++j) {
                for (int k = 0; k < n; ++k) {
                    g[i][j] += (students[i][k] == mentors[j][k]) ? 1 : 0;
                }
            }
        }

        // Generate all permutations
        List<Integer> p = new ArrayList<>();
        for (int i = 0; i < m; ++i) {
            p.add(i);
        }

        int ans = 0;
        do {
            int cur = 0;
            for (int i = 0; i < m; ++i) {
                cur += g[i][p.get(i)];
            }
            ans = Math.max(ans, cur);
        } while (nextPermutation(p));
        return ans;
    }

    private boolean nextPermutation(List<Integer> p) {
        int n = p.size();
        int i = n - 2;
        while (i >= 0 && p.get(i) >= p.get(i + 1)) {
            i--;
        }
        if (i < 0) {
            return false;
        }
        int j = n - 1;
        while (p.get(j) <= p.get(i)) {
            j--;
        }
        Collections.swap(p, i, j);
        Collections.reverse(p.subList(i + 1, n));
        return true;
    }
}


// 方法二：状态压缩动态规划
class Solution {
    public int maxCompatibilitySum(int[][] students, int[][] mentors) {
        int m = students.length;
        int n = students[0].length;
        int[][] g = new int[m][m];

        // Calculate compatibility scores
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < m; ++j) {
                for (int k = 0; k < n; ++k) {
                    g[i][j] += (students[i][k] == mentors[j][k]) ? 1 : 0;
                }
            }
        }

        int[] f = new int[1 << m];
        for (int mask = 1; mask < (1 << m); ++mask) {
            int c = Integer.bitCount(mask);
            for (int i = 0; i < m; ++i) {
                if ((mask & (1 << i)) != 0) {
                    f[mask] = Math.max(f[mask], f[mask ^ (1 << i)] + g[c - 1][i]);
                }
            }
        }
        return f[(1 << m) - 1];
    }
}
```



### [1595. 连通两组点的最小成本](https://leetcode.cn/problems/minimum-cost-to-connect-two-groups-of-points/)

困难

给你两组点，其中第一组中有 `size1` 个点，第二组中有 `size2` 个点，且 `size1 >= size2` 。

任意两点间的连接成本 `cost` 由大小为 `size1 x size2` 矩阵给出，其中 `cost[i][j]` 是第一组中的点 `i` 和第二组中的点 `j` 的连接成本。**如果两个组中的每个点都与另一组中的一个或多个点连接，则称这两组点是连通的。**换言之，第一组中的每个点必须至少与第二组中的一个点连接，且第二组中的每个点必须至少与第一组中的一个点连接。

返回连通两组点所需的最小成本。

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/09/20/ex1.jpg)

```
输入：cost = [[15, 96], [36, 2]]
输出：17
解释：连通两组点的最佳方法是：
1--A
2--B
总成本为 17 。
```

C++版本

```c++
// 方法一：状态压缩 + 动态规划
class Solution {
public:
    int connectTwoGroups(vector<vector<int>>& cost) {
        int size1 = cost.size(), size2 = cost[0].size(), m = 1 << size2;
        vector<vector<int>> dp(size1 + 1, vector<int>(m, INT_MAX / 2));
        dp[0][0] = 0;
        for (int i = 1; i <= size1; i++) {
            for (int s = 0; s < m; s++) {
                for (int k = 0; k < size2; k++) {
                    if ((s & (1 << k)) == 0) {
                        continue;
                    }
                    dp[i][s] = min(dp[i][s], dp[i][s ^ (1 << k)] + cost[i - 1][k]);
                    dp[i][s] = min(dp[i][s], dp[i - 1][s] + cost[i - 1][k]);
                    dp[i][s] = min(dp[i][s], dp[i - 1][s ^ (1 << k)] + cost[i - 1][k]);
                }
            }
        }
        return dp[size1][m - 1];
    }
};

// 优化空间
class Solution {
public:
    int connectTwoGroups(vector<vector<int>>& cost) {
        int size1 = cost.size(), size2 = cost[0].size(), m = 1 << size2;
        vector<int> dp1(m, INT_MAX / 2), dp2(m);
        dp1[0] = 0;
        for (int i = 1; i <= size1; i++) {
            for (int s = 0; s < m; s++) {
                dp2[s] = INT_MAX / 2;
                for (int k = 0; k < size2; k++) {
                    if ((s & (1 << k)) == 0) {
                        continue;
                    }
                    dp2[s] = min(dp2[s], dp2[s ^ (1 << k)] + cost[i - 1][k]);
                    dp2[s] = min(dp2[s], dp1[s] + cost[i - 1][k]);
                    dp2[s] = min(dp2[s], dp1[s ^ (1 << k)] + cost[i - 1][k]);
                }
            }
            dp1.swap(dp2);
        }
        return dp1[m - 1];
    }
};
```

Java版本

```java
// 方法一：状态压缩 + 动态规划
class Solution {
    public int connectTwoGroups(List<List<Integer>> cost) {
        int size1 = cost.size(), size2 = cost.get(0).size(), m = 1 << size2;
        int[][] dp = new int[size1 + 1][m];
        for (int i = 0; i <= size1; i++) {
            Arrays.fill(dp[i], Integer.MAX_VALUE / 2);
        }
        dp[0][0] = 0;
        for (int i = 1; i <= size1; i++) {
            for (int s = 0; s < m; s++) {
                for (int k = 0; k < size2; k++) {
                    if ((s & (1 << k)) == 0) {
                        continue;
                    }
                    dp[i][s] = Math.min(dp[i][s], dp[i][s ^ (1 << k)] + cost.get(i - 1).get(k));
                    dp[i][s] = Math.min(dp[i][s], dp[i - 1][s] + cost.get(i - 1).get(k));
                    dp[i][s] = Math.min(dp[i][s], dp[i - 1][s ^ (1 << k)] + cost.get(i - 1).get(k));
                }
            }
        }
        return dp[size1][m - 1];
    }
}

// 优化空间
class Solution {
    public int connectTwoGroups(List<List<Integer>> cost) {
        int size1 = cost.size(), size2 = cost.get(0).size(), m = 1 << size2;
        int[] dp1 = new int[m];
        Arrays.fill(dp1, Integer.MAX_VALUE / 2);
        int[] dp2 = new int[m];
        dp1[0] = 0;
        for (int i = 1; i <= size1; i++) {
            for (int s = 0; s < m; s++) {
                dp2[s] = Integer.MAX_VALUE / 2;
                for (int k = 0; k < size2; k++) {
                    if ((s & (1 << k)) == 0) {
                        continue;
                    }
                    dp2[s] = Math.min(dp2[s], dp2[s ^ (1 << k)] + cost.get(i - 1).get(k));
                    dp2[s] = Math.min(dp2[s], dp1[s] + cost.get(i - 1).get(k));
                    dp2[s] = Math.min(dp2[s], dp1[s ^ (1 << k)] + cost.get(i - 1).get(k));
                }
            }
            System.arraycopy(dp2, 0, dp1, 0, m);
        }
        return dp1[m - 1];
    }
}
```



### [1494. 并行课程 II](https://leetcode.cn/problems/parallel-courses-ii/)

困难

给你一个整数 `n` 表示某所大学里课程的数目，编号为 `1` 到 `n` ，数组 `relations` 中， `relations[i] = [xi, yi]` 表示一个先修课的关系，也就是课程 `xi` 必须在课程 `yi` 之前上。同时你还有一个整数 `k` 。

在一个学期中，你 **最多** 可以同时上 `k` 门课，前提是这些课的先修课在之前的学期里已经上过了。

请你返回上完所有课最少需要多少个学期。题目保证一定存在一种上完所有课的方式。

**示例 1：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/06/27/leetcode_parallel_courses_1.png)**

```
输入：n = 4, relations = [[2,1],[3,1],[1,4]], k = 2
输出：3 
解释：上图展示了题目输入的图。在第一个学期中，我们可以上课程 2 和课程 3 。然后第二个学期上课程 1 ，第三个学期上课程 4 。
```

C++版本

```c++
// 方法一：动态规划 + 状态压缩
class Solution {
public:
    int minNumberOfSemesters(int n, vector<vector<int>>& relations, int k) {
        vector<int> dp(1 << n, INT_MAX);
        vector<int> need(1 << n, 0);
        for (auto& edge : relations) {
            need[(1 << (edge[1] - 1))] |= 1 << (edge[0] - 1);
        }
        dp[0] = 0;
        for (int i = 1; i < (1 << n); ++i) {
            need[i] = need[i & (i - 1)] | need[i & (-i)];
            if ((need[i] | i) != i) { // i 中有任意一门课程的前置课程没有完成学习
                continue;
            }
            int valid = i ^ need[i]; // 当前学期可以进行学习的课程集合
            if (__builtin_popcount(valid) <= k) { // 如果个数小于 k，则可以全部学习，不再枚举子集
                dp[i] = min(dp[i], dp[i ^ valid] + 1);
            } else { // 否则枚举当前学期需要进行学习的课程集合
                for (int sub = valid; sub; sub = (sub - 1) & valid) {
                    if (__builtin_popcount(sub) <= k) {
                        dp[i] = min(dp[i], dp[i ^ sub] + 1);
                    }
                }
            }
        }
        return dp[(1 << n) - 1];
    }
};
```

Java版本

```java
// 方法一：动态规划 + 状态压缩
class Solution {
    public int minNumberOfSemesters(int n, int[][] relations, int k) {
        int[] dp = new int[1 << n];
        Arrays.fill(dp, Integer.MAX_VALUE);
        int[] need = new int[1 << n];
        for (int[] edge : relations) {
            need[(1 << (edge[1] - 1))] |= 1 << (edge[0] - 1);
        }
        dp[0] = 0;
        for (int i = 1; i < (1 << n); ++i) {
            need[i] = need[i & (i - 1)] | need[i & (-i)];
            if ((need[i] | i) != i) { // i 中有任意一门课程的前置课程没有完成学习
                continue;
            }
            int valid = i ^ need[i]; // 当前学期可以进行学习的课程集合
            if (Integer.bitCount(valid) <= k) { // 如果个数小于 k，则可以全部学习，不再枚举子集
                dp[i] = Math.min(dp[i], dp[i ^ valid] + 1);
            } else { // 否则枚举当前学期需要进行学习的课程集合
                for (int sub = valid; sub > 0; sub = (sub - 1) & valid) {
                    if (Integer.bitCount(sub) <= k) {
                        dp[i] = Math.min(dp[i], dp[i ^ sub] + 1);
                    }
                }
            }
        }
        return dp[(1 << n) - 1];
    }
}
```



### [1655. 分配重复整数](https://leetcode.cn/problems/distribute-repeating-integers/)

困难

给你一个长度为 `n` 的整数数组 `nums` ，这个数组中至多有 `50` 个不同的值。同时你有 `m` 个顾客的订单 `quantity` ，其中，整数 `quantity[i]` 是第 `i` 位顾客订单的数目。请你判断是否能将 `nums` 中的整数分配给这些顾客，且满足：

- 第 `i` 位顾客 **恰好** 有 `quantity[i]` 个整数。
- 第 `i` 位顾客拿到的整数都是 **相同的** 。
- 每位顾客都满足上述两个要求。

如果你可以分配 `nums` 中的整数满足上面的要求，那么请返回 `true` ，否则返回 `false` 。

**示例 1：**

```
输入：nums = [1,2,3,4], quantity = [2]
输出：false
解释：第 0 位顾客没办法得到两个相同的整数。
```

C++版本

```c++
class Solution {
public:
    bool canDistribute(vector<int>& nums, vector<int>& quantity) {
        unordered_map<int, int> cache;
        for (int x: nums) {
            cache[x]++;
        }
        vector<int> cnt;
        for (auto& kv: cache) {
            cnt.push_back(kv.second);
        }
        
        int n = cnt.size(), m = quantity.size();
        vector<int> sum(1 << m, 0);
        for (int i = 1; i < (1 << m); i++) {
            for (int j = 0; j < m; j++) {
                if ((i & (1 << j)) != 0) {
                    int left = i - (1 << j);
                    sum[i] = sum[left] + quantity[j];
                    break;
                }
            }
        }
        
        vector<vector<bool>> dp(n, vector<bool>(1 << m, false));
        for (int i = 0; i < n; i++) {
            dp[i][0] = true;
        }
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < (1 << m); j++) {
                if (i > 0 && dp[i-1][j]) {
                    dp[i][j] = true;
                    continue;
                }
                for (int s = j; s != 0; s = ((s - 1) & j)) { // 子集枚举，详见 https://oi-wiki.org/math/bit/#_14
                    int prev = j - s; // 前 i-1 个元素需要满足子集 prev = j-s
                    bool last = (i == 0) ? (prev == 0): dp[i-1][prev]; // cnt[0..i-1] 能否满足子集 prev
                    bool need = sum[s] <= cnt[i]; // cnt[i] 能否满足子集 s
                    if (last && need) {
                        dp[i][j] = true;
                        break;
                    }
                }
            }
        }
        return dp[n-1][(1<<m)-1];
    }
};
```

Java版本

```java
public class Solution {
    public boolean canDistribute(int[] nums, int[] quantity) {
        // Count the frequency of each number in nums
        Map<Integer, Integer> cache = new HashMap<>();
        for (int x : nums) {
            cache.put(x, cache.getOrDefault(x, 0) + 1);
        }

        // Extract the frequencies into an array cnt
        int[] cnt = new int[cache.size()];
        int index = 0;
        for (int value : cache.values()) {
            cnt[index++] = value;
        }

        int n = cnt.length;
        int m = quantity.length;

        // Calculate the sum of quantities for each subset
        int[] sum = new int[1 << m];
        for (int i = 1; i < (1 << m); i++) {
            for (int j = 0; j < m; j++) {
                if ((i & (1 << j)) != 0) {
                    int left = i - (1 << j);
                    sum[i] = sum[left] + quantity[j];
                    break;
                }
            }
        }

        // Initialize the dp table
        boolean[][] dp = new boolean[n][1 << m];
        for (int i = 0; i < n; i++) {
            dp[i][0] = true;
        }

        // Fill the dp table
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < (1 << m); j++) {
                if (i > 0 && dp[i-1][j]) {
                    dp[i][j] = true;
                    continue;
                }
                for (int s = j; s != 0; s = (s - 1) & j) {
                    int prev = j - s;
                    boolean last = (i == 0) ? (prev == 0) : dp[i-1][prev];
                    boolean need = sum[s] <= cnt[i];
                    if (last && need) {
                        dp[i][j] = true;
                        break;
                    }
                }
            }
        }

        // Return the final result
        return dp[n-1][(1 << m) - 1];
    }

}
```



### [1986. 完成任务的最少工作时间段](https://leetcode.cn/problems/minimum-number-of-work-sessions-to-finish-the-tasks/)

中等

你被安排了 `n` 个任务。任务需要花费的时间用长度为 `n` 的整数数组 `tasks` 表示，第 `i` 个任务需要花费 `tasks[i]` 小时完成。一个 **工作时间段** 中，你可以 **至多** 连续工作 `sessionTime` 个小时，然后休息一会儿。

你需要按照如下条件完成给定任务：

- 如果你在某一个时间段开始一个任务，你需要在 **同一个** 时间段完成它。
- 完成一个任务后，你可以 **立马** 开始一个新的任务。
- 你可以按 **任意顺序** 完成任务。

给你 `tasks` 和 `sessionTime` ，请你按照上述要求，返回完成所有任务所需要的 **最少** 数目的 **工作时间段** 。

测试数据保证 `sessionTime` **大于等于** `tasks[i]` 中的 **最大值** 。

**示例 1：**

```
输入：tasks = [1,2,3], sessionTime = 3
输出：2
解释：你可以在两个工作时间段内完成所有任务。
- 第一个工作时间段：完成第一和第二个任务，花费 1 + 2 = 3 小时。
- 第二个工作时间段：完成第三个任务，花费 3 小时。
```

C++版本

```c++
// 方法一：枚举子集的动态规划
class Solution {
public:
    int minSessions(vector<int>& tasks, int sessionTime) {
        int n = tasks.size();
        vector<int> valid(1 << n);
        for (int mask = 1; mask < (1 << n); ++mask) {
            int needTime = 0;
            for (int i = 0; i < n; ++i) {
                if (mask & (1 << i)) {
                    needTime += tasks[i];
                }
            }
            if (needTime <= sessionTime) {
                valid[mask] = true;
            }
        }

        vector<int> f(1 << n, INT_MAX / 2);
        f[0] = 0;
        for (int mask = 1; mask < (1 << n); ++mask) {
            for (int subset = mask; subset; subset = (subset - 1) & mask) {
                if (valid[subset]) {
                    f[mask] = min(f[mask], f[mask ^ subset] + 1);
                }
            }
        }
        return f[(1 << n) - 1];
    }
};

// 方法二：存储两个值的动态规划
class Solution {
public:
    int minSessions(vector<int>& tasks, int sessionTime) {
        int n = tasks.size();
        vector<pair<int, int>> f(1 << n, {INT_MAX, INT_MAX});
        f[0] = {1, 0};
        
        auto add = [&](const pair<int, int>& o, int x) -> pair<int, int> {
            if (o.second + x <= sessionTime) {
                return {o.first, o.second + x};
            }
            return {o.first + 1, x};
        };
        
        for (int mask = 1; mask < (1 << n); ++mask) {
            for (int i = 0; i < n; ++i) {
                if (mask & (1 << i)) {
                    f[mask] = min(f[mask], add(f[mask ^ (1 << i)], tasks[i]));
                }
            }
        }
        return f[(1 << n) - 1].first;
    }
};
```

Java版本

```java
// 方法一：枚举子集的动态规划
class Solution {
    public int minSessions(int[] tasks, int sessionTime) {
        int n = tasks.length;
        int[] valid = new int[1 << n];
        for (int mask = 1; mask < (1 << n); ++mask) {
            int needTime = 0;
            for (int i = 0; i < n; ++i) {
                if ((mask & (1 << i)) != 0) {
                    needTime += tasks[i];
                }
            }
            if (needTime <= sessionTime) {
                valid[mask] = 1;
            }
        }

        int[] f = new int[1 << n];
        Arrays.fill(f, Integer.MAX_VALUE / 2);
        f[0] = 0;
        for (int mask = 1; mask < (1 << n); ++mask) {
            for (int subset = mask; subset > 0; subset = (subset - 1) & mask) {
                if (valid[subset] == 1) {
                    f[mask] = Math.min(f[mask], f[mask ^ subset] + 1);
                }
            }
        }
        return f[(1 << n) - 1];
    }
}

// 方法二：存储两个值的动态规划
class Solution {
    public int minSessions(int[] tasks, int sessionTime) {
        int n = tasks.length;
        Pair[] f = new Pair[1 << n];
        for (int i = 0; i < (1 << n); i++) {
            f[i] = new Pair(Integer.MAX_VALUE, Integer.MAX_VALUE);
        }
        f[0] = new Pair(1, 0);

        for (int mask = 1; mask < (1 << n); ++mask) {
            for (int i = 0; i < n; ++i) {
                if ((mask & (1 << i)) != 0) {
                    f[mask] = minPair(f[mask], addPair(f[mask ^ (1 << i)], tasks[i], sessionTime));
                }
            }
        }
        return f[(1 << n) - 1].first;
    }

    private Pair addPair(Pair o, int x, int sessionTime) {
        if (o.second + x <= sessionTime) {
            return new Pair(o.first, o.second + x);
        }
        return new Pair(o.first + 1, x);
    }

    private Pair minPair(Pair a, Pair b) {
        if (a.first != b.first) {
            return a.first < b.first ? a : b;
        }
        return a.second <= b.second ? a : b;
    }

    class Pair {
        int first, second;

        Pair(int f, int s) {
            this.first = f;
            this.second = s;
        }
    }
}
```



### [1434. 每个人戴不同帽子的方案数](https://leetcode.cn/problems/number-of-ways-to-wear-different-hats-to-each-other/)

困难

总共有 `n` 个人和 `40` 种不同的帽子，帽子编号从 `1` 到 `40` 。

给你一个整数列表的列表 `hats` ，其中 `hats[i]` 是第 `i` 个人所有喜欢帽子的列表。

请你给每个人安排一顶他喜欢的帽子，确保每个人戴的帽子跟别人都不一样，并返回方案数。

由于答案可能很大，请返回它对 `10^9 + 7` 取余后的结果。

**示例 1：**

```
输入：hats = [[3,4],[4,5],[5]]
输出：1
解释：给定条件下只有一种方法选择帽子。
第一个人选择帽子 3，第二个人选择帽子 4，最后一个人选择帽子 5。
```

C++版本

```c++
// 方法一：状态压缩动态规划
using LL = long long;

class Solution {
private:
    static constexpr int mod = 1000000007;
    
public:
    int numberWays(vector<vector<int>>& hats) {
        int n = hats.size();
        // 找到帽子编号的最大值，这样我们只需要求出 $f[maxhatid][2^n - 1]$ 作为答案
        int maxHatId = 0;
        for (int i = 0; i < n; ++i) {
            for (int h: hats[i]) {
                maxHatId = max(maxHatId, h);
            }
        }
        
        // 对于每一顶帽子 h，hatToPerson[h] 中存储了喜欢这顶帽子的所有人，方便进行动态规划
        vector<vector<int>> hatToPerson(maxHatId + 1);
        for (int i = 0; i < n; ++i) {
            for (int h: hats[i]) {
                hatToPerson[h].push_back(i);
            }
        }
        
        vector<vector<int>> f(maxHatId + 1, vector<int>(1 << n));
        // 边界条件
        f[0][0] = 1;
        for (int i = 1; i <= maxHatId; ++i) {
            for (int mask = 0; mask < (1 << n); ++mask) {
                f[i][mask] = f[i - 1][mask];
                for (int j: hatToPerson[i]) {
                    if (mask & (1 << j)) {
                        f[i][mask] += f[i - 1][mask ^ (1 << j)];
                        f[i][mask] %= mod;
                    }
                }
            }
        }
        
        return f[maxHatId][(1 << n) - 1];
    }
};
```

Java版本

```java
// 方法一：状态压缩动态规划
class Solution {
    public int numberWays(List<List<Integer>> hats) {
        final int MOD = 1000000007;
        int n = hats.size();
        // 找到帽子编号的最大值，这样我们只需要求出 f[maxhatid][2^n - 1] 作为答案
        int maxHatId = 0;
        for (int i = 0; i < n; ++i) {
            List<Integer> list = hats.get(i);
            for (int h: list) {
                maxHatId = Math.max(maxHatId, h);
            }
        }
        
        // 对于每一顶帽子 h，hatToPerson[h] 中存储了喜欢这顶帽子的所有人，方便进行动态规划
        List<List<Integer>> hatToPerson = new ArrayList<List<Integer>>();
        for (int i = 0; i <= maxHatId; i++) {
            hatToPerson.add(new ArrayList<Integer>());
        }
        for (int i = 0; i < n; ++i) {
            List<Integer> list = hats.get(i);
            for (int h: list) {
                hatToPerson.get(h).add(i);
            }
        }
        
        int[][] f = new int[maxHatId + 1][1 << n];
        // 边界条件
        f[0][0] = 1;
        for (int i = 1; i <= maxHatId; ++i) {
            for (int mask = 0; mask < (1 << n); ++mask) {
                f[i][mask] = f[i - 1][mask];
                List<Integer> list = hatToPerson.get(i);
                for (int j: list) {
                    if ((mask & (1 << j)) != 0) {
                        f[i][mask] += f[i - 1][mask ^ (1 << j)];
                        f[i][mask] %= MOD;
                    }
                }
            }
        }
        
        return f[maxHatId][(1 << n) - 1];
    }
}
```



### [1799. N 次操作后的最大分数和](https://leetcode.cn/problems/maximize-score-after-n-operations/)

困难

给你 `nums` ，它是一个大小为 `2 * n` 的正整数数组。你必须对这个数组执行 `n` 次操作。

在第 `i` 次操作时（操作编号从 **1** 开始），你需要：

- 选择两个元素 `x` 和 `y` 。
- 获得分数 `i * gcd(x, y)` 。
- 将 `x` 和 `y` 从 `nums` 中删除。

请你返回 `n` 次操作后你能获得的分数和最大为多少。

函数 `gcd(x, y)` 是 `x` 和 `y` 的最大公约数。

**示例 1：**

```
输入：nums = [1,2]
输出：1
解释：最优操作是：
(1 * gcd(1, 2)) = 1
```

C++版本

```c++
// 方法一：状态压缩 + 动态规划
class Solution {
public:
    int maxScore(vector<int>& nums) {
        int m = nums.size();
        vector<int> dp(1 << m, 0);
        vector<vector<int>> gcd_tmp(m, vector<int>(m, 0));
        for (int i = 0; i < m; ++i) {
            for (int j = i + 1; j < m; ++j) {
                gcd_tmp[i][j] = gcd(nums[i], nums[j]);
            }
        }
        int all = 1 << m;
        for (int s = 1; s < all; ++s) {
            int t = __builtin_popcount(s);
            if (t & 1) {
                continue;
            }
            for (int i = 0; i < m; ++i) {
                if ((s >> i) & 1) {
                    for (int j = i + 1; j < m; ++j) {
                        if ((s >> j) & 1) {
                            dp[s] = max(dp[s], dp[s ^ (1 << i) ^ (1 << j)] + t / 2 * gcd_tmp[i][j]);
                        }
                    }
                }
            }
        }
        return dp[all - 1];
    }
};
```

Java版本

```java
// 方法一：状态压缩 + 动态规划
class Solution {
    public int maxScore(int[] nums) {
        int m = nums.length;
        int[] dp = new int[1 << m];
        int[][] gcdTmp = new int[m][m];
        for (int i = 0; i < m; ++i) {
            for (int j = i + 1; j < m; ++j) {
                gcdTmp[i][j] = gcd(nums[i], nums[j]);
            }
        }
        int all = 1 << m;
        for (int s = 1; s < all; ++s) {
            int t = Integer.bitCount(s);
            if ((t & 1) != 0) {
                continue;
            }
            for (int i = 0; i < m; ++i) {
                if (((s >> i) & 1) != 0) {
                    for (int j = i + 1; j < m; ++j) {
                        if (((s >> j) & 1) != 0) {
                            dp[s] = Math.max(dp[s], dp[s ^ (1 << i) ^ (1 << j)] + t / 2 * gcdTmp[i][j]);
                        }
                    }
                }
            }
        }
        return dp[all - 1];
    }

    public int gcd(int num1, int num2) {
        while (num2 != 0) {
            int temp = num1;
            num1 = num2;
            num2 = temp % num2;
        }
        return num1;
    }
}
```



### [1681. 最小不兼容性](https://leetcode.cn/problems/minimum-incompatibility/)

困难

给你一个整数数组 `nums` 和一个整数 `k` 。你需要将这个数组划分到 `k` 个相同大小的子集中，使得同一个子集里面没有两个相同的元素。

一个子集的 **不兼容性** 是该子集里面最大值和最小值的差。

请你返回将数组分成 `k` 个子集后，各子集 **不兼容性** 的 **和** 的 **最小值** ，如果无法分成分成 `k` 个子集，返回 `-1` 。

子集的定义是数组中一些数字的集合，对数字顺序没有要求。

**示例 1：**

```
输入：nums = [1,2,1,4], k = 2
输出：4
解释：最优的分配是 [1,2] 和 [1,4] 。
不兼容性和为 (2-1) + (4-1) = 4 。
注意到 [1,1] 和 [2,4] 可以得到更小的和，但是第一个集合有 2 个相同的元素，所以不可行。
```

C++版本

```c++
// 方法一：动态规划 + 状态压缩
class Solution {
public:
    int minimumIncompatibility(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> dp(1 << n, INT_MAX);
        dp[0] = 0;
        int group = n / k;
        unordered_map<int, int> values;

        for (int mask = 1; mask < (1 << n); mask++) {
            if (__builtin_popcount(mask) != group) {
                continue;
            }
            int mn = 20, mx = 0;
            unordered_set<int> cur;
            for (int i = 0; i < n; i++) {
                if (mask & (1 << i)) {
                    if (cur.count(nums[i]) > 0) {
                        break;
                    }
                    cur.insert(nums[i]);
                    mn = min(mn, nums[i]);
                    mx = max(mx, nums[i]);
                }
            }
            if (cur.size() == group) {
                values[mask] = mx - mn;
            }
        }

        for (int mask = 0; mask < (1 << n); mask++) {
            if (dp[mask] == INT_MAX) {
                continue;
            }
            unordered_map<int, int> seen;
            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) == 0) {
                    seen[nums[i]] = i;
                }
            }
            if (seen.size() < group) {
                continue;
            }
            int sub = 0;
            for (auto& pair : seen) {
                sub |= (1 << pair.second);
            }
            int nxt = sub;
            while (nxt > 0) {
                if (values.count(nxt) > 0) {
                    dp[mask | nxt] = min(dp[mask | nxt], dp[mask] + values[nxt]);
                }
                nxt = (nxt - 1) & sub;
            }
        }

        return (dp[(1 << n) - 1] < INT_MAX) ? dp[(1 << n) - 1] : -1;
    }
};
```

Java版本

```java
// 方法一：动态规划 + 状态压缩
class Solution {
    public int minimumIncompatibility(int[] nums, int k) {
        int n = nums.length, group = n / k, inf = Integer.MAX_VALUE;
        int[] dp = new int[1 << n];
        Arrays.fill(dp, inf);
        dp[0] = 0;
        HashMap<Integer, Integer> values = new HashMap<>();

        for (int mask = 1; mask < (1 << n); mask++) {
            if (Integer.bitCount(mask) != group) {
                continue;
            }
            int mn = 20, mx = 0;
            HashSet<Integer> cur = new HashSet<>();
            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) > 0) {
                    if (cur.contains(nums[i])) {
                        break;
                    }
                    cur.add(nums[i]);
                    mn = Math.min(mn, nums[i]);
                    mx = Math.max(mx, nums[i]);
                }
            }
            if (cur.size() == group) {
                values.put(mask, mx - mn);
            }
        }

        for (int mask = 0; mask < (1 << n); mask++) {
            if (dp[mask] == inf) {
                continue;
            }
            HashMap<Integer, Integer> seen = new HashMap<>();
            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) == 0) {
                    seen.put(nums[i], i);
                }
            }
            if (seen.size() < group) {
                continue;
            }
            int sub = 0;
            for (int v : seen.values()) {
                sub |= (1 << v);
            }
            int nxt = sub;
            while (nxt > 0) {
                if (values.containsKey(nxt)) {
                    dp[mask | nxt] = Math.min(dp[mask | nxt], dp[mask] + values.get(nxt));
                }
                nxt = (nxt - 1) & sub;
            }
        }

        return (dp[(1 << n) - 1] < inf) ? dp[(1 << n) - 1] : -1;
    }
}
```



### [526. 优美的排列](https://leetcode.cn/problems/beautiful-arrangement/)

中等

假设有从1到n的n个整数。用这些整数构造一个数组 `perm`（**下标从 1 开始**），只要满足下述条件 **之一** ，该数组就是一个**优美的排列** ：

- `perm[i]` 能够被 `i` 整除
- `i` 能够被 `perm[i]` 整除

给你一个整数 `n` ，返回可以构造的 **优美排列** 的 **数量** 。

**示例 1：**

```
输入：n = 2
输出：2
解释：
第 1 个优美的排列是 [1,2]：
    - perm[1] = 1 能被 i = 1 整除
    - perm[2] = 2 能被 i = 2 整除
第 2 个优美的排列是 [2,1]:
    - perm[1] = 2 能被 i = 1 整除
    - i = 2 能被 perm[2] = 1 整除
```

C++版本

```C++
// 方法一：回溯
class Solution {
public:
    vector<vector<int>> match;
    vector<int> vis;
    int num;

    void backtrack(int index, int n) {
        if (index == n + 1) {
            num++;
            return;
        }
        for (auto &x : match[index]) {
            if (!vis[x]) {
                vis[x] = true;
                backtrack(index + 1, n);
                vis[x] = false;
            }
        }
    }

    int countArrangement(int n) {
        vis.resize(n + 1);
        match.resize(n + 1);
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                if (i % j == 0 || j % i == 0) {
                    match[i].push_back(j);
                }
            }
        }
        backtrack(1, n);
        return num;
    }
};

// 方法二：状态压缩 + 动态规划
class Solution {
public:
    int countArrangement(int n) {
        vector<int> f(1 << n);
        f[0] = 1;
        for (int mask = 1; mask < (1 << n); mask++) {
            int num = __builtin_popcount(mask);
            for (int i = 0; i < n; i++) {
                if (mask & (1 << i) && (num % (i + 1) == 0 || (i + 1) % num == 0)) {
                    f[mask] += f[mask ^ (1 << i)];
                }
            }
        }
        return f[(1 << n) - 1];
    }
};
```

Java版本

```java
// 方法一：回溯
class Solution {
    List<Integer>[] match;
    boolean[] vis;
    int num;

    public int countArrangement(int n) {
        vis = new boolean[n + 1];
        match = new List[n + 1];
        for (int i = 0; i <= n; i++) {
            match[i] = new ArrayList<Integer>();
        }
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                if (i % j == 0 || j % i == 0) {
                    match[i].add(j);
                }
            }
        }
        backtrack(1, n);
        return num;
    }

    public void backtrack(int index, int n) {
        if (index == n + 1) {
            num++;
            return;
        }
        for (int x : match[index]) {
            if (!vis[x]) {
                vis[x] = true;
                backtrack(index + 1, n);
                vis[x] = false;
            }
        }
    }
}

// 方法二：状态压缩 + 动态规划
class Solution {
    public int countArrangement(int n) {
        int[] f = new int[1 << n];
        f[0] = 1;
        for (int mask = 1; mask < (1 << n); mask++) {
            int num = Integer.bitCount(mask);
            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) != 0 && ((num % (i + 1)) == 0 || (i + 1) % num == 0)) {
                    f[mask] += f[mask ^ (1 << i)];
                }
            }
        }
        return f[(1 << n) - 1];
    }
}
```



### [464. 我能赢吗](https://leetcode.cn/problems/can-i-win/)

中等

在 "100 game" 这个游戏中，两名玩家轮流选择从 `1` 到 `10` 的任意整数，累计整数和，先使得累计整数和 **达到或超过** 100 的玩家，即为胜者。

如果我们将游戏规则改为 “玩家 **不能** 重复使用整数” 呢？

例如，两个玩家可以轮流从公共整数池中抽取从 1 到 15 的整数（不放回），直到累计整数和 >= 100。

给定两个整数 `maxChoosableInteger` （整数池中可选择的最大数）和 `desiredTotal`（累计和），若先出手的玩家能稳赢则返回 `true` ，否则返回 `false` 。假设两位玩家游戏时都表现 **最佳** 。

**示例 1：**

```
输入：maxChoosableInteger = 10, desiredTotal = 11
输出：false
解释：
无论第一个玩家选择哪个整数，他都会失败。
第一个玩家可以选择从 1 到 10 的整数。
如果第一个玩家选择 1，那么第二个玩家只能选择从 2 到 10 的整数。
第二个玩家可以通过选择整数 10（那么累积和为 11 >= desiredTotal），从而取得胜利.
同样地，第一个玩家选择任意其他整数，第二个玩家都会赢。
```

C++版本

```C++
// 方法一：记忆化搜索 + 状态压缩
class Solution {
public:
    unordered_map<int, bool> memo;

    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        if ((1 + maxChoosableInteger) * (maxChoosableInteger) / 2 < desiredTotal) {
            return false;
        }
        return dfs(maxChoosableInteger, 0, desiredTotal, 0);
    }

    bool dfs(int maxChoosableInteger, int usedNumbers, int desiredTotal, int currentTotal) {
        if (!memo.count(usedNumbers)) {
            bool res = false;
            for (int i = 0; i < maxChoosableInteger; i++) {
                if (((usedNumbers >> i) & 1) == 0) {
                    if (i + 1 + currentTotal >= desiredTotal) {
                        res = true;
                        break;
                    }
                    if (!dfs(maxChoosableInteger, usedNumbers | (1 << i), desiredTotal, currentTotal + i + 1)) {
                        res = true;
                        break;
                    }
                }
            }
            memo[usedNumbers] = res;
        }
        return memo[usedNumbers];
    }
};
```

Java版本

```java
// 方法一：记忆化搜索 + 状态压缩
class Solution {
    Map<Integer, Boolean> memo = new HashMap<Integer, Boolean>();

    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        if ((1 + maxChoosableInteger) * (maxChoosableInteger) / 2 < desiredTotal) {
            return false;
        }
        return dfs(maxChoosableInteger, 0, desiredTotal, 0);
    }

    public boolean dfs(int maxChoosableInteger, int usedNumbers, int desiredTotal, int currentTotal) {
        if (!memo.containsKey(usedNumbers)) {
            boolean res = false;
            for (int i = 0; i < maxChoosableInteger; i++) {
                if (((usedNumbers >> i) & 1) == 0) {
                    if (i + 1 + currentTotal >= desiredTotal) {
                        res = true;
                        break;
                    }
                    if (!dfs(maxChoosableInteger, usedNumbers | (1 << i), desiredTotal, currentTotal + i + 1)) {
                        res = true;
                        break;
                    }
                }
            }
            memo.put(usedNumbers, res);
        }
        return memo.get(usedNumbers);
    }
}
```



### [847. 访问所有节点的最短路径](https://leetcode.cn/problems/shortest-path-visiting-all-nodes/)

困难

存在一个由 `n` 个节点组成的无向连通图，图中的节点按从 `0` 到 `n - 1` 编号。

给你一个数组 `graph` 表示这个图。其中，`graph[i]` 是一个列表，由所有与节点 `i` 直接相连的节点组成。

返回能够访问所有节点的最短路径的长度。你可以在任一节点开始和停止，也可以多次重访节点，并且可以重用边。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/05/12/shortest1-graph.jpg)

```
输入：graph = [[1,2,3],[0],[0],[0]]
输出：4
解释：一种可能的路径为 [1,0,2,0,3]
```

C++版本

```java
// 方法一：状态压缩 + 广度优先搜索
class Solution {
public:
    int shortestPathLength(vector<vector<int>>& graph) {
        int n = graph.size();
        queue<tuple<int, int, int>> q;
        vector<vector<int>> seen(n, vector<int>(1 << n));
        for (int i = 0; i < n; ++i) {
            q.emplace(i, 1 << i, 0);
            seen[i][1 << i] = true;
        }

        int ans = 0;
        while (!q.empty()) {
            auto [u, mask, dist] = q.front();
            q.pop();
            if (mask == (1 << n) - 1) {
                ans = dist;
                break;
            }
            // 搜索相邻的节点
            for (int v: graph[u]) {
                // 将 mask 的第 v 位置为 1
                int mask_v = mask | (1 << v);
                if (!seen[v][mask_v]) {
                    q.emplace(v, mask_v, dist + 1);
                    seen[v][mask_v] = true;
                }
            }
        }
        return ans;
    }
};

// 方法二：预处理点对间最短路 + 状态压缩动态规划
class Solution {
public:
    int shortestPathLength(vector<vector<int>>& graph) {
        int n = graph.size();
        vector<vector<int>> d(n, vector<int>(n, n + 1));
        for (int i = 0; i < n; ++i) {
            for (int j: graph[i]) {
                d[i][j] = 1;
            }
        }
        // 使用 floyd 算法预处理出所有点对之间的最短路径长度
        for (int k = 0; k < n; ++k) {
            for (int i = 0; i < n; ++i) {
                for (int j = 0; j < n; ++j) {
                    d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
                }
            }
        }

        vector<vector<int>> f(n, vector<int>(1 << n, INT_MAX / 2));
        for (int mask = 1; mask < (1 << n); ++mask) {
            // 如果 mask 只包含一个 1，即 mask 是 2 的幂
            if ((mask & (mask - 1)) == 0) {
                int u = __builtin_ctz(mask);
                f[u][mask] = 0;
            }
            else {
                for (int u = 0; u < n; ++u) {
                    if (mask & (1 << u)) {
                        for (int v = 0; v < n; ++v) {
                            if ((mask & (1 << v)) && u != v) {
                                f[u][mask] = min(f[u][mask], f[v][mask ^ (1 << u)] + d[v][u]);
                            }
                        }
                    }
                }
            }
        }

        int ans = INT_MAX;
        for (int u = 0; u < n; ++u) {
            ans = min(ans, f[u][(1 << n) - 1]);
        }
        return ans;
    }
};
```

Java版本

```java
// 方法一：状态压缩 + 广度优先搜索
class Solution {
    public int shortestPathLength(int[][] graph) {
        int n = graph.length;
        Queue<int[]> queue = new LinkedList<int[]>();
        boolean[][] seen = new boolean[n][1 << n];
        for (int i = 0; i < n; ++i) {
            queue.offer(new int[]{i, 1 << i, 0});
            seen[i][1 << i] = true;
        }

        int ans = 0;
        while (!queue.isEmpty()) {
            int[] tuple = queue.poll();
            int u = tuple[0], mask = tuple[1], dist = tuple[2];
            if (mask == (1 << n) - 1) {
                ans = dist;
                break;
            }
            // 搜索相邻的节点
            for (int v : graph[u]) {
                // 将 mask 的第 v 位置为 1
                int maskV = mask | (1 << v);
                if (!seen[v][maskV]) {
                    queue.offer(new int[]{v, maskV, dist + 1});
                    seen[v][maskV] = true;
                }
            }
        }
        return ans;
    }
}

// 方法二：预处理点对间最短路 + 状态压缩动态规划
class Solution {
    public int shortestPathLength(int[][] graph) {
        int n = graph.length;
        int[][] d = new int[n][n];
        for (int i = 0; i < n; ++i) {
            Arrays.fill(d[i], n + 1);
        }
        for (int i = 0; i < n; ++i) {
            for (int j : graph[i]) {
                d[i][j] = 1;
            }
        }
        // 使用 floyd 算法预处理出所有点对之间的最短路径长度
        for (int k = 0; k < n; ++k) {
            for (int i = 0; i < n; ++i) {
                for (int j = 0; j < n; ++j) {
                    d[i][j] = Math.min(d[i][j], d[i][k] + d[k][j]);
                }
            }
        }

        int[][] f = new int[n][1 << n];
        for (int i = 0; i < n; ++i) {
            Arrays.fill(f[i], Integer.MAX_VALUE / 2);
        }
        for (int mask = 1; mask < (1 << n); ++mask) {
            // 如果 mask 只包含一个 1，即 mask 是 2 的幂
            if ((mask & (mask - 1)) == 0) {
                int u = Integer.bitCount((mask & (-mask)) - 1);
                f[u][mask] = 0;
            } else {
                for (int u = 0; u < n; ++u) {
                    if ((mask & (1 << u)) != 0) {
                        for (int v = 0; v < n; ++v) {
                            if ((mask & (1 << v)) != 0 && u != v) {
                                f[u][mask] = Math.min(f[u][mask], f[v][mask ^ (1 << u)] + d[v][u]);
                            }
                        }
                    }
                }
            }
        }

        int ans = Integer.MAX_VALUE;
        for (int u = 0; u < n; ++u) {
            ans = Math.min(ans, f[u][(1 << n) - 1]);
        }
        return ans;
    }
}
```



### [638. 大礼包](https://leetcode.cn/problems/shopping-offers/)

中等

在 LeetCode 商店中， 有 `n` 件在售的物品。每件物品都有对应的价格。然而，也有一些大礼包，每个大礼包以优惠的价格捆绑销售一组物品。

给你一个整数数组 `price` 表示物品价格，其中 `price[i]` 是第 `i` 件物品的价格。另有一个整数数组 `needs` 表示购物清单，其中 `needs[i]` 是需要购买第 `i` 件物品的数量。

还有一个数组 `special` 表示大礼包，`special[i]` 的长度为 `n + 1` ，其中 `special[i][j]` 表示第 `i` 个大礼包中内含第 `j` 件物品的数量，且 `special[i][n]` （也就是数组中的最后一个整数）为第 `i` 个大礼包的价格。

返回 **确切** 满足购物清单所需花费的最低价格，你可以充分利用大礼包的优惠活动。你不能购买超出购物清单指定数量的物品，即使那样会降低整体价格。任意大礼包可无限次购买。

**示例 1：**

```
输入：price = [2,5], special = [[3,0,5],[1,2,10]], needs = [3,2]
输出：14
解释：有 A 和 B 两种物品，价格分别为 ¥2 和 ¥5 。 
大礼包 1 ，你可以以 ¥5 的价格购买 3A 和 0B 。 
大礼包 2 ，你可以以 ¥10 的价格购买 1A 和 2B 。 
需要购买 3 个 A 和 2 个 B ， 所以付 ¥10 购买 1A 和 2B（大礼包 2），以及 ¥4 购买 2A 。
```

C++版本

```java
// 方法一：记忆化搜索
class Solution {
public:
    map<vector<int>, int> memo;

    int shoppingOffers(vector<int>& price, vector<vector<int>>& special, vector<int>& needs) {
        int n = price.size();

        // 过滤不需要计算的大礼包，只保留需要计算的大礼包
        vector<vector<int>> filterSpecial;
        for (auto & sp : special) {
            int totalCount = 0, totalPrice = 0;
            for (int i = 0; i < n; ++i) {
                totalCount += sp[i];
                totalPrice += sp[i] * price[i];
            }
            if (totalCount > 0 && totalPrice > sp[n]) {
                filterSpecial.emplace_back(sp);
            }
        }

        return dfs(price, special, needs, filterSpecial, n);
    }

    // 记忆化搜索计算满足购物清单所需花费的最低价格
    int dfs(vector<int> price,const vector<vector<int>> & special, vector<int> curNeeds, vector<vector<int>> & filterSpecial, int n) {
        if (!memo.count(curNeeds)) {
            int minPrice = 0;
            for (int i = 0; i < n; ++i) {
                minPrice += curNeeds[i] * price[i]; // 不购买任何大礼包，原价购买购物清单中的所有物品
            }
            for (auto & curSpecial : filterSpecial) {
                int specialPrice = curSpecial[n];
                vector<int> nxtNeeds;
                for (int i = 0; i < n; ++i) {
                    if (curSpecial[i] > curNeeds[i]) { // 不能购买超出购物清单指定数量的物品
                        break;
                    }
                    nxtNeeds.emplace_back(curNeeds[i] - curSpecial[i]);
                }
                if (nxtNeeds.size() == n) { // 大礼包可以购买
                    minPrice = min(minPrice, dfs(price, special, nxtNeeds, filterSpecial, n) + specialPrice);
                }
            }
            memo[curNeeds] = minPrice;
        }
        return memo[curNeeds];
    }
};
```

Java版本

```java
// 方法一：记忆化搜索
class Solution {
    Map<List<Integer>, Integer> memo = new HashMap<List<Integer>, Integer>();

    public int shoppingOffers(List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
        int n = price.size();

        // 过滤不需要计算的大礼包，只保留需要计算的大礼包
        List<List<Integer>> filterSpecial = new ArrayList<List<Integer>>();
        for (List<Integer> sp : special) {
            int totalCount = 0, totalPrice = 0;
            for (int i = 0; i < n; ++i) {
                totalCount += sp.get(i);
                totalPrice += sp.get(i) * price.get(i);
            }
            if (totalCount > 0 && totalPrice > sp.get(n)) {
                filterSpecial.add(sp);
            }
        }

        return dfs(price, special, needs, filterSpecial, n);
    }

    // 记忆化搜索计算满足购物清单所需花费的最低价格
    public int dfs(List<Integer> price, List<List<Integer>> special, List<Integer> curNeeds, List<List<Integer>> filterSpecial, int n) {
        if (!memo.containsKey(curNeeds)) {
            int minPrice = 0;
            for (int i = 0; i < n; ++i) {
                minPrice += curNeeds.get(i) * price.get(i); // 不购买任何大礼包，原价购买购物清单中的所有物品
            }
            for (List<Integer> curSpecial : filterSpecial) {
                int specialPrice = curSpecial.get(n);
                List<Integer> nxtNeeds = new ArrayList<Integer>();
                for (int i = 0; i < n; ++i) {
                    if (curSpecial.get(i) > curNeeds.get(i)) { // 不能购买超出购物清单指定数量的物品
                        break;
                    }
                    nxtNeeds.add(curNeeds.get(i) - curSpecial.get(i));
                }
                if (nxtNeeds.size() == n) { // 大礼包可以购买
                    minPrice = Math.min(minPrice, dfs(price, special, nxtNeeds, filterSpecial, n) + specialPrice);
                }
            }
            memo.put(curNeeds, minPrice);
        }
        return memo.get(curNeeds);
    }
}
```



### [1994. 好子集的数目](https://leetcode.cn/problems/the-number-of-good-subsets/)

困难

给你一个整数数组 `nums` 。如果 `nums` 的一个子集中，所有元素的乘积可以表示为一个或多个 **互不相同的质数** 的乘积，那么我们称它为 **好子集** 。

- 比方说，如果 nums = [1, 2, 3, 4]：
  - `[2, 3]` ，`[1, 2, 3]` 和 `[1, 3]` 是 **好** 子集，乘积分别为 `6 = 2*3` ，`6 = 2*3` 和 `3 = 3` 。
  - `[1, 4]` 和 `[4]` 不是 **好** 子集，因为乘积分别为 `4 = 2*2` 和 `4 = 2*2` 。

请你返回 `nums` 中不同的 **好** 子集的数目对 `109 + 7` **取余** 的结果。

`nums` 中的 **子集** 是通过删除 `nums` 中一些（可能一个都不删除，也可能全部都删除）元素后剩余元素组成的数组。如果两个子集删除的下标不同，那么它们被视为不同的子集。

**示例 1：**

```
输入：nums = [1,2,3,4]
输出：6
解释：好子集为：
- [1,2]：乘积为 2 ，可以表示为质数 2 的乘积。
- [1,2,3]：乘积为 6 ，可以表示为互不相同的质数 2 和 3 的乘积。
- [1,3]：乘积为 3 ，可以表示为质数 3 的乘积。
- [2]：乘积为 2 ，可以表示为质数 2 的乘积。
- [2,3]：乘积为 6 ，可以表示为互不相同的质数 2 和 3 的乘积。
- [3]：乘积为 3 ，可以表示为质数 3 的乘积。
```

C++版本

```java
// 方法一：状态压缩动态规划
class Solution {
private:
    static constexpr array<int, 10> primes = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29};
    static constexpr int num_max = 30;
    static constexpr int mod = 1000000007;

public:
    int numberOfGoodSubsets(vector<int>& nums) {
        vector<int> freq(num_max + 1);
        for (int num: nums) {
            ++freq[num];
        }

        vector<int> f(1 << primes.size());
        f[0] = 1;
        for (int _ = 0; _ < freq[1]; ++_) {
            f[0] = f[0] * 2 % mod;
        }
        
        for (int i = 2; i <= num_max; ++i) {
            if (!freq[i]) {
                continue;
            }
            
            // 检查 i 的每个质因数是否均不超过 1 个
            int subset = 0, x = i;
            bool check = true;
            for (int j = 0; j < primes.size(); ++j) {
                int prime = primes[j];
                if (x % (prime * prime) == 0) {
                    check = false;
                    break;
                }
                if (x % prime == 0) {
                    subset |= (1 << j);
                }
            }
            if (!check) {
                continue;
            }

            // 动态规划
            for (int mask = (1 << primes.size()) - 1; mask > 0; --mask) {
                if ((mask & subset) == subset) {
                    f[mask] = (f[mask] + static_cast<long long>(f[mask ^ subset]) * freq[i]) % mod;
                }
            }
        }

        int ans = 0;
        for (int mask = 1, mask_max = (1 << primes.size()); mask < mask_max; ++mask) {
            ans = (ans + f[mask]) % mod;
        }
        
        return ans;
    }
};
```

Java版本

```java
// 方法一：状态压缩动态规划
class Solution {
    static final int[] PRIMES = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29};
    static final int NUM_MAX = 30;
    static final int MOD = 1000000007;

    public int numberOfGoodSubsets(int[] nums) {
        int[] freq = new int[NUM_MAX + 1];
        for (int num : nums) {
            ++freq[num];
        }

        int[] f = new int[1 << PRIMES.length];
        f[0] = 1;
        for (int i = 0; i < freq[1]; ++i) {
            f[0] = f[0] * 2 % MOD;
        }
        
        for (int i = 2; i <= NUM_MAX; ++i) {
            if (freq[i] == 0) {
                continue;
            }
            
            // 检查 i 的每个质因数是否均不超过 1 个
            int subset = 0, x = i;
            boolean check = true;
            for (int j = 0; j < PRIMES.length; ++j) {
                int prime = PRIMES[j];
                if (x % (prime * prime) == 0) {
                    check = false;
                    break;
                }
                if (x % prime == 0) {
                    subset |= (1 << j);
                }
            }
            if (!check) {
                continue;
            }

            // 动态规划
            for (int mask = (1 << PRIMES.length) - 1; mask > 0; --mask) {
                if ((mask & subset) == subset) {
                    f[mask] = (int) ((f[mask] + ((long) f[mask ^ subset]) * freq[i]) % MOD);
                }
            }
        }

        int ans = 0;
        for (int mask = 1, maskMax = (1 << PRIMES.length); mask < maskMax; ++mask) {
            ans = (ans + f[mask]) % MOD;
        }
        
        return ans;
    }
}
```



### [1349. 参加考试的最大学生数](https://leetcode.cn/problems/maximum-students-taking-exam/)

困难

给你一个 `m * n` 的矩阵 `seats` 表示教室中的座位分布。如果座位是坏的（不可用），就用 `'#'` 表示；否则，用 `'.'` 表示。

学生可以看到左侧、右侧、左上、右上这四个方向上紧邻他的学生的答卷，但是看不到直接坐在他前面或者后面的学生的答卷。请你计算并返回该考场可以容纳的同时参加考试且无法作弊的 **最大** 学生人数。

学生必须坐在状况良好的座位上。

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/02/09/image.png)

```
输入：seats = [["#",".","#","#",".","#"],
              [".","#","#","#","#","."],
              ["#",".","#","#",".","#"]]
输出：4
解释：教师可以让 4 个学生坐在可用的座位上，这样他们就无法在考试中作弊。
```

C++版本

```c++
// 方法一：记忆化搜索 + 状态压缩
class Solution {
public:
    int maxStudents(vector<vector<char>>& seats) {
        int m = seats.size(), n = seats[0].size();
        unordered_map<int, int> memo;

        auto isSingleRowCompliant = [&](int status, int row) -> bool {
            for (int j = 0; j < n; j++) {
                if ((status >> j) & 1) {
                    if (seats[row][j] == '#') {
                        return false;
                    }
                    if (j > 0 && ((status >> (j - 1)) & 1)) {
                        return false;
                    }
                }
            }
            return true;
        };
        
        auto isCrossRowsCompliant = [&](int status, int upperRowStatus) -> bool {
            for (int j = 0; j < n; j++) {
                if ((status >> j) & 1) {
                    if (j > 0 && ((upperRowStatus >> (j - 1)) & 1)) {
                        return false;
                    }
                    if (j < n - 1 && ((upperRowStatus >> (j + 1)) & 1)) {
                        return false;
                    }
                }
            }
            return true;
        };

        function<int(int, int)> dp = [&](int row, int status) -> int {
            int key = (row << n) + status;
            if (!memo.count(key)) {
                if (!isSingleRowCompliant(status, row)) {
                    memo[key] = INT_MIN;
                    return INT_MIN;
                }
                int students = __builtin_popcount(status);
                if (row == 0) {
                    memo[key] = students;
                    return students;
                }
                int mx = 0;
                for (int upperRowStatus = 0; upperRowStatus < 1 << n; upperRowStatus++) {
                    if (isCrossRowsCompliant(status, upperRowStatus)) {
                        mx = max(mx, dp(row - 1, upperRowStatus));
                    }
                }
                memo[key] = students + mx;
            }
            return memo[key];
        };
        
        int mx = 0;
        for (int i = 0; i < (1 << n); i++) {
            mx = max(mx, dp(m - 1, i));
        }
        return mx;
    }
};
```

Java版本

```java
// 方法一：记忆化搜索 + 状态压缩
class Solution {
    Map<Integer, Integer> memo = new HashMap<Integer, Integer>();

    public int maxStudents(char[][] seats) {
        int m = seats.length, n = seats[0].length;
        int mx = 0;
        for (int i = 0; i < 1 << n; i++) {
            mx = Math.max(mx, dp(seats, m - 1, i));
        }
        return mx;
    }

    public int dp(char[][] seats, int row, int status) {
        int n = seats[0].length;
        int key = (row << n) + status;
        if (!memo.containsKey(key)) {
            if (!isSingleRowCompliant(seats, status, n, row)) {
                memo.put(key, Integer.MIN_VALUE);
                return Integer.MIN_VALUE;
            }
            int students = Integer.bitCount(status);
            if (row == 0) {
                memo.put(key, students);
                return students;
            }
            int mx = 0;
            for (int upperRowStatus = 0; upperRowStatus < 1 << n; upperRowStatus++) {
                if (isCrossRowsCompliant(status, upperRowStatus, n)) {
                    mx = Math.max(mx, dp(seats, row - 1, upperRowStatus));
                }
            }
            memo.put(key, students + mx);
        }
        return memo.get(key);
    }

    public boolean isSingleRowCompliant(char[][] seats, int status, int n, int row) {
        for (int j = 0; j < n; j++) {
            if (((status >> j) & 1) == 1) {
                if (seats[row][j] == '#') {
                    return false;
                }
                if (j > 0 && ((status >> (j - 1)) & 1) == 1) {
                    return false;
                }
            }
        }
        return true;
    }

    public boolean isCrossRowsCompliant(int status, int upperRowStatus, int n) {
        for (int j = 0; j < n; j++) {
            if (((status >> j) & 1) == 1) {
                if (j > 0 && ((upperRowStatus >> (j - 1)) & 1) == 1) {
                    return false;
                }
                if (j < n - 1 && ((upperRowStatus >> (j + 1)) & 1) == 1) {
                    return false;
                }
            }
        }
        return true;
    }
}
```



### [698. 划分为k个相等的子集](https://leetcode.cn/problems/partition-to-k-equal-sum-subsets/)

中等

给定一个整数数组 `nums` 和一个正整数 `k`，找出是否有可能把这个数组分成 `k` 个非空子集，其总和都相等。

**示例 1：**

```
输入： nums = [4, 3, 2, 3, 5, 2, 1], k = 4
输出： True
说明： 有可能将其分成 4 个子集（5），（1,4），（2,3），（2,3）等于总和。
```

C++版本

```c++
// 方法一：状态压缩 + 记忆化搜索
class Solution {
public:
    bool canPartitionKSubsets(vector<int>& nums, int k) {
        int all = accumulate(nums.begin(), nums.end(), 0);
        if (all % k > 0) {
            return false;
        }
        int per = all / k; 
        sort(nums.begin(), nums.end());
        if (nums.back() > per) {
            return false;
        }
        int n = nums.size();
        vector<bool> dp(1 << n, true);
        function<bool(int,int)> dfs = [&](int s, int p)->bool {
            if (s == 0) {
                return true;
            }
            if (!dp[s]) {
                return dp[s];
            }
            dp[s] = false;
            for (int i = 0; i < n; i++) {
                if (nums[i] + p > per) {
                    break;
                }
                if ((s >> i) & 1) {
                    if (dfs(s ^ (1 << i), (p + nums[i]) % per)) {
                        return true;
                    }
                }
            }
            return false;
        };
        return dfs((1 << n) - 1, 0);
    }
};

// 方法二：状态压缩 + 动态规划
class Solution {
public:
    bool canPartitionKSubsets(vector<int>& nums, int k) {
        int all = accumulate(nums.begin(), nums.end(), 0);
        if (all % k > 0) {
            return false;
        }
        int per = all / k; 
        sort(nums.begin(), nums.end());
        if (nums.back() > per) {
            return false;
        }
        int n = nums.size();
        vector<bool> dp(1 << n, false);
        vector<int> curSum(1 << n, 0);
        dp[0] = true;
        for (int i = 0; i < 1 << n; i++) {
            if (!dp[i]) {
                continue;
            }
            for (int j = 0; j < n; j++) {
                if (curSum[i] + nums[j] > per) {
                    break;
                }
                if (((i >> j) & 1) == 0) {
                    int next = i | (1 << j);
                    if (!dp[next]) {
                        curSum[next] = (curSum[i] + nums[j]) % per;
                        dp[next] = true;
                    }
                }
            }
        }
        return dp[(1 << n) - 1];
    }
};
```

Java版本

```java
// 方法一：状态压缩 + 记忆化搜索
class Solution {
    int[] nums;
    int per, n;
    boolean[] dp;

    public boolean canPartitionKSubsets(int[] nums, int k) {
        this.nums = nums;
        int all = Arrays.stream(nums).sum();
        if (all % k != 0) {
            return false;
        }
        per = all / k;
        Arrays.sort(nums);
        n = nums.length;
        if (nums[n - 1] > per) {
            return false;
        }
        dp = new boolean[1 << n];
        Arrays.fill(dp, true);
        return dfs((1 << n) - 1, 0);
    }

    public boolean dfs(int s, int p) {
        if (s == 0) {
            return true;
        }
        if (!dp[s]) {
            return dp[s];
        }
        dp[s] = false;
        for (int i = 0; i < n; i++) {
            if (nums[i] + p > per) {
                break;
            }
            if (((s >> i) & 1) != 0) {
                if (dfs(s ^ (1 << i), (p + nums[i]) % per)) {
                    return true;
                }
            }
        }
        return false;
    }
}

// 方法二：状态压缩 + 动态规划
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int all = Arrays.stream(nums).sum();
        if (all % k != 0) {
            return false;
        }
        int per = all / k;
        Arrays.sort(nums);
        int n = nums.length;
        if (nums[n - 1] > per) {
            return false;
        }
        boolean[] dp = new boolean[1 << n];
        int[] curSum = new int[1 << n];
        dp[0] = true;
        for (int i = 0; i < 1 << n; i++) {
            if (!dp[i]) {
                continue;
            }
            for (int j = 0; j < n; j++) {
                if (curSum[i] + nums[j] > per) {
                    break;
                }
                if (((i >> j) & 1) == 0) {
                    int next = i | (1 << j);
                    if (!dp[next]) {
                        curSum[next] = (curSum[i] + nums[j]) % per;
                        dp[next] = true;
                    }
                }
            }
        }
        return dp[(1 << n) - 1];
    }
}
```



### [943. 最短超级串](https://leetcode.cn/problems/find-the-shortest-superstring/)

困难

给定一个字符串数组 `words`，找到以 `words` 中每个字符串作为子字符串的最短字符串。如果有多个有效最短字符串满足题目条件，返回其中 **任意一个** 即可。

我们可以假设 `words` 中没有字符串是 `words` 中另一个字符串的子字符串。

**示例 1：**

```
输入：words = ["alex","loves","leetcode"]
输出："alexlovesleetcode"
解释："alex"，"loves"，"leetcode" 的所有排列都会被接受。
```

C++版本

```c++
// 方法一：动态规划
class Solution {
public:
    string shortestSuperstring(vector<string>& A) {
        int N = A.size();

        // Populate overlaps
        vector<vector<int>> overlaps(N, vector<int>(N, 0));
        for (int i = 0; i < N; ++i) {
            for (int j = 0; j < N; ++j) {
                if (i != j) {
                    int m = min(A[i].length(), A[j].length());
                    for (int k = m; k >= 0; --k) {
                        if (A[i].substr(A[i].length() - k) == A[j].substr(0, k)) {
                            overlaps[i][j] = k;
                            break;
                        }
                    }
                }
            }
        }

        // dp[mask][i] = most overlap with mask, ending with ith element
        vector<vector<int>> dp(1 << N, vector<int>(N, 0));
        vector<vector<int>> parent(1 << N, vector<int>(N, -1));

        for (int mask = 0; mask < (1 << N); ++mask) {
            for (int bit = 0; bit < N; ++bit) {
                if ((mask & (1 << bit)) != 0) {
                    int pmask = mask ^ (1 << bit);
                    if (pmask == 0) continue;
                    for (int i = 0; i < N; ++i) {
                        if ((pmask & (1 << i)) != 0) {
                            int val = dp[pmask][i] + overlaps[i][bit];
                            if (val > dp[mask][bit]) {
                                dp[mask][bit] = val;
                                parent[mask][bit] = i;
                            }
                        }
                    }
                }
            }
        }

        // Answer will have length sum(len(A[i]) for i) - max(dp[-1])
        // Reconstruct answer, first as a sequence 'perm' representing
        // the indices of each word from left to right.
        vector<int> perm(N);
        vector<bool> seen(N, false);
        int t = 0;
        int mask = (1 << N) - 1;

        // p: the last element of perm (last word written left to right)
        int p = 0;
        for (int j = 0; j < N; ++j) {
            if (dp[(1 << N) - 1][j] > dp[(1 << N) - 1][p]) {
                p = j;
            }
        }

        // Follow parents down backwards path that retains maximum overlap
        while (p != -1) {
            perm[t++] = p;
            seen[p] = true;
            int p2 = parent[mask][p];
            mask ^= 1 << p;
            p = p2;
        }

        // Reverse perm
        reverse(perm.begin(), perm.begin() + t);

        // Fill in remaining words not yet added
        for (int i = 0; i < N; ++i) {
            if (!seen[i]) {
                perm[t++] = i;
            }
        }

        // Reconstruct final answer given perm
        string ans = A[perm[0]];
        for (int i = 1; i < N; ++i) {
            int overlap = overlaps[perm[i-1]][perm[i]];
            ans += A[perm[i]].substr(overlap);
        }

        return ans;
    }
};
```

Java版本

```java
// 方法一：动态规划
class Solution {
    public String shortestSuperstring(String[] A) {
        int N = A.length;

        // Populate overlaps
        int[][] overlaps = new int[N][N];
        for (int i = 0; i < N; ++i)
            for (int j = 0; j < N; ++j) if (i != j) {
                int m = Math.min(A[i].length(), A[j].length());
                for (int k = m; k >= 0; --k)
                    if (A[i].endsWith(A[j].substring(0, k))) {
                        overlaps[i][j] = k;
                        break;
                    }
            }

        // dp[mask][i] = most overlap with mask, ending with ith element
        int[][] dp = new int[1<<N][N];
        int[][] parent = new int[1<<N][N];
        for (int mask = 0; mask < (1<<N); ++mask) {
            Arrays.fill(parent[mask], -1);

            for (int bit = 0; bit < N; ++bit) if (((mask >> bit) & 1) > 0) {
                // Let's try to find dp[mask][bit].  Previously, we had
                // a collection of items represented by pmask.
                int pmask = mask ^ (1 << bit);
                if (pmask == 0) continue;
                for (int i = 0; i < N; ++i) if (((pmask >> i) & 1) > 0) {
                    // For each bit i in pmask, calculate the value
                    // if we ended with word i, then added word 'bit'.
                    int val = dp[pmask][i] + overlaps[i][bit];
                    if (val > dp[mask][bit]) {
                        dp[mask][bit] = val;
                        parent[mask][bit] = i;
                    }
                }
            }
        }

        // # Answer will have length sum(len(A[i]) for i) - max(dp[-1])
        // Reconstruct answer, first as a sequence 'perm' representing
        // the indices of each word from left to right.

        int[] perm = new int[N];
        boolean[] seen = new boolean[N];
        int t = 0;
        int mask = (1 << N) - 1;

        // p: the last element of perm (last word written left to right)
        int p = 0;
        for (int j = 0; j < N; ++j)
            if (dp[(1<<N) - 1][j] > dp[(1<<N) - 1][p])
                p = j;

        // Follow parents down backwards path that retains maximum overlap
        while (p != -1) {
            perm[t++] = p;
            seen[p] = true;
            int p2 = parent[mask][p];
            mask ^= 1 << p;
            p = p2;
        }

        // Reverse perm
        for (int i = 0; i < t/2; ++i) {
            int v = perm[i];
            perm[i] = perm[t-1-i];
            perm[t-1-i] = v;
        }

        // Fill in remaining words not yet added
        for (int i = 0; i < N; ++i) if (!seen[i])
            perm[t++] = i;

        // Reconstruct final answer given perm
        StringBuilder ans = new StringBuilder(A[perm[0]]);
        for (int i = 1; i < N; ++i) {
            int overlap = overlaps[perm[i-1]][perm[i]];
            ans.append(A[perm[i]].substring(overlap));
        }

        return ans.toString();
    }
}
```



### [691. 贴纸拼词](https://leetcode.cn/problems/stickers-to-spell-word/)

困难

我们有 `n` 种不同的贴纸。每个贴纸上都有一个小写的英文单词。

您想要拼写出给定的字符串 `target` ，方法是从收集的贴纸中切割单个字母并重新排列它们。如果你愿意，你可以多次使用每个贴纸，每个贴纸的数量是无限的。

返回你需要拼出 `target` 的最小贴纸数量。如果任务不可能，则返回 `-1` 。

**注意：**在所有的测试用例中，所有的单词都是从 `1000` 个最常见的美国英语单词中随机选择的，并且 `target` 被选择为两个随机单词的连接。

**示例 1：**

```
输入： stickers = ["with","example","science"], target = "thehat"
输出：3
解释：
我们可以使用 2 个 "with" 贴纸，和 1 个 "example" 贴纸。
把贴纸上的字母剪下来并重新排列后，就可以形成目标 “thehat“ 了。
此外，这是形成目标字符串所需的最小贴纸数量。
```

C++版本

```c++
// 方法一：记忆化搜索 + 状态压缩
class Solution {
public:
    int minStickers(vector<string>& stickers, string target) {
        int m = target.size();
        vector<int> dp(1 << m, -1);
        dp[0] = 0;
        function<int(int)> helper = [&](int mask) {
            if (dp[mask] != -1) {
                return dp[mask];
            }
            dp[mask] = m + 1;
            for (auto & sticker : stickers) {
                int left = mask;
                vector<int> cnt(26);
                for (char & c : sticker) {
                    cnt[c - 'a']++;
                }
                for (int i = 0; i < m; i++) {
                    if ((mask >> i & 1) && cnt[target[i] - 'a'] > 0) {
                        cnt[target[i] - 'a']--;
                        left ^= 1 << i;
                    }
                }
                if (left < mask) {
                    dp[mask] = min(dp[mask], helper(left) + 1);
                }
            }
            return dp[mask];
        };
        int res = helper((1 << m) - 1);
        return res > m ? -1 : res;
    }
};
```

Java版本

```java
// 方法一：记忆化搜索 + 状态压缩
class Solution {
    public int minStickers(String[] stickers, String target) {
        int m = target.length();
        int[] memo = new int[1 << m];
        Arrays.fill(memo, -1);
        memo[0] = 0;
        int res = dp(stickers, target, memo, (1 << m) - 1);
        return res <= m ? res : -1;
    }

    public int dp(String[] stickers, String target, int[] memo, int mask) {
        int m = target.length();
        if (memo[mask] < 0) {
            int res = m + 1;
            for (String sticker : stickers) {
                int left = mask;
                int[] cnt = new int[26];
                for (int i = 0; i < sticker.length(); i++) {
                    cnt[sticker.charAt(i) - 'a']++;
                }
                for (int i = 0; i < target.length(); i++) {
                    char c = target.charAt(i);
                    if (((mask >> i) & 1) == 1 && cnt[c - 'a'] > 0) {
                        cnt[c - 'a']--;
                        left ^= 1 << i;
                    }
                }
                if (left < mask) {
                    res = Math.min(res, dp(stickers, target, memo, left) + 1);
                }
            }
            memo[mask] = res;
        }
        return memo[mask];
    }
}

// 其他
class Solution {
    public static int minStickers(String[] stickers, String target) {
        if (stickers == null || stickers.length == 0 || target == null || target.length() == 0) {
            return 0;
        }
        int N = stickers.length;
        int[][] scount = new int[N][26];
        for (int i = 0; i < N; i++) {
            char[] chs = stickers[i].toCharArray();
            for (char c : chs) {
                scount[i][c - 'a']++;
            }
        }
        Map<String, Integer> map = new HashMap<>();
        int res = process(scount, target, map);
        return res == Integer.MAX_VALUE ? -1 : res;
    }

    private static int process(int[][] stickers, String target, Map<String,Integer> map) {
        if (target.length() == 0) {
            return 0;
        }
        if(map.containsKey(target)){
            return map.get(target);
        }
        int[] tcount = new int[26];
        char[] tchas = target.toCharArray();
        for (int i = 0; i < tchas.length; i++) {
            tcount[(tchas[i] - 'a')]++;
        }
        int N = stickers.length;
        int min = Integer.MAX_VALUE;
        for (int i = 0; i < N; i++) {
            // 重要优化 减支
            int[] sticker = stickers[i];
            if(sticker[tchas[0] - 'a'] > 0){
                StringBuilder sb = new StringBuilder();
                for(int j = 0;j < tcount.length;j++){
                    if(tcount[j] > 0){
                        int num = tcount[j] - sticker[j];
                        for(int times = 0; times < num; times++){
                            sb.append((char)('a' + j));
                        }
                    }
                }
                String rest = sb.toString();
                min = Math.min(process(stickers, rest, map), min);
            }
        }
        int res = min + (min == Integer.MAX_VALUE ? 0 : 1);
        map.put(target, res);
        return res;
    }
}
```



### [982. 按位与为零的三元组](https://leetcode.cn/problems/triples-with-bitwise-and-equal-to-zero/)

困难

给你一个整数数组 `nums` ，返回其中 **按位与三元组** 的数目。

**按位与三元组** 是由下标 `(i, j, k)` 组成的三元组，并满足下述全部条件：

- `0 <= i < nums.length`
- `0 <= j < nums.length`
- `0 <= k < nums.length`
- `nums[i] & nums[j] & nums[k] == 0` ，其中 `&` 表示按位与运算符。

**示例 1：**

```
输入：nums = [2,1,3]
输出：12
解释：可以选出如下 i, j, k 三元组：
(i=0, j=0, k=1) : 2 & 2 & 1
(i=0, j=1, k=0) : 2 & 1 & 2
(i=0, j=1, k=1) : 2 & 1 & 1
(i=0, j=1, k=2) : 2 & 1 & 3
(i=0, j=2, k=1) : 2 & 3 & 1
(i=1, j=0, k=0) : 1 & 2 & 2
(i=1, j=0, k=1) : 1 & 2 & 1
(i=1, j=0, k=2) : 1 & 2 & 3
(i=1, j=1, k=0) : 1 & 1 & 2
(i=1, j=2, k=0) : 1 & 3 & 2
(i=2, j=0, k=1) : 3 & 2 & 1
(i=2, j=1, k=0) : 3 & 1 & 2
```

C++版本

```c++
// 方法一：枚举
class Solution {
public:
    int countTriplets(vector<int>& nums) {
        vector<int> cnt(1 << 16);
        for (int x: nums) {
            for (int y: nums) {
                ++cnt[x & y];
            }
        }
        int ans = 0;
        for (int x: nums) {
            for (int mask = 0; mask < (1 << 16); ++mask) {
                if ((x & mask) == 0) {
                    ans += cnt[mask];
                }
            }
        }
        return ans;
    }
};

// 方法二：枚举 + 子集优化
class Solution {
public:
    int countTriplets(vector<int>& nums) {
        vector<int> cnt(1 << 16);
        for (int x: nums) {
            for (int y: nums) {
                ++cnt[x & y];
            }
        }
        int ans = 0;
        for (int x: nums) {
            x = x ^ 0xffff;
            for (int sub = x; sub; sub = (sub - 1) & x) {
                ans += cnt[sub];
            }
            ans += cnt[0];
        }
        return ans;
    }
};
```

Java版本

```java
// 方法一：枚举
class Solution {
    public int countTriplets(int[] nums) {
        int[] cnt = new int[1 << 16];
        for (int x : nums) {
            for (int y : nums) {
                ++cnt[x & y];
            }
        }
        int ans = 0;
        for (int x : nums) {
            for (int mask = 0; mask < (1 << 16); ++mask) {
                if ((x & mask) == 0) {
                    ans += cnt[mask];
                }
            }
        }
        return ans;
    }
}

// 方法二：枚举 + 子集优化
class Solution {
    public int countTriplets(int[] nums) {
        int[] cnt = new int[1 << 16];
        for (int x : nums) {
            for (int y : nums) {
                ++cnt[x & y];
            }
        }
        int ans = 0;
        for (int x : nums) {
            x = x ^ 0xffff;
            for (int sub = x; sub != 0; sub = (sub - 1) & x) {
                ans += cnt[sub];
            }
            ans += cnt[0];
        }
        return ans;
    }
}
```

