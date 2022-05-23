# LeetCode Notes

## Contest Recordes

### [Weekly Contest 294](https://leetcode.com/contest/weekly-contest-294/)

#### [✅ 2278. Percentage of Letter in String](https://leetcode.com/contest/weekly-contest-294/problems/percentage-of-letter-in-string/)

依照题意，计算出每个字符的比例即可

```cpp
class Solution {
public:
  int percentageLetter(string s, char letter) {
    int size = s.size();
    int cnt{0};
    for (auto& c : s) {
      if (c == letter)
        ++cnt;
    }
    int ret = cnt * 100 / size;
    return ret;

  }
};
```

#### [✅ 2279. Maximum Bags With Full Capacity of Rocks](https://leetcode.com/contest/weekly-contest-294/problems/maximum-bags-with-full-capacity-of-rocks/)

要求尽可能多的装满袋子，所以可以优先装那些已经快满的袋子。对每个袋子剩余容量进行由小到大排序，再依次装袋即可。

```cpp
class Solution {
public:
    int maximumBags(vector<int>& capacity, vector<int>& rocks, int additionalRocks) {
      int size = rocks.size();
      int ret{0};
      std::vector<int> diff(size);
      for (int i = 0; i < size; ++i) {
        diff[i] = capacity[i] - rocks[i];
      }
      std::sort(diff.begin(), diff.end());
      unsigned int cost{0};
      for (auto& p : diff) {
        cost += p;
        if (cost <= (unsigned)additionalRocks) {
          ++ret;
        } else {
          break;
        }
      }

      return ret;
    }
};
```

#### [✅ 2280. Minimum Lines to Represent a Line Chart](https://leetcode.com/contest/weekly-contest-294/problems/minimum-lines-to-represent-a-line-chart/)

题意白话翻译过来就是给定N个二维点坐标，需要几条折线才能将他们连接起来。

将所有点按坐标x排序，依次计算每两个点之间的斜率，如果斜率有改变即为多一条折线。

**注意**
1. 斜率不要使用浮点数去保存，存在精度问题。可以用`(dx, dy)`二元祖来表示。判定两个斜率是否相同用等式代换：`(dx1 / dy1 == dx2 / dy2) ==> (dx1 * dy2 == dx2 * dy1)`
2. 边界case处理： 单个点不能成线。

```cpp
struct slope_t {
  long long s1;
  long long s2;
};

class Solution {
public:
  int minimumLines(vector<vector<int>>& stockPrices) {
    auto& stocks = stockPrices;
    int size = stocks.size();
    
    if (size == 1) return 0;
    if (size == 2) return 1;
    
    std::sort(stocks.begin(), stocks.end(),
              [](std::vector<int>& s1, std::vector<int>& s2) {
                return s1[0] < s2[0];
              });
    
    int ret{1};
    auto lst_slope{compute_slope(stocks[0], stocks[1])};
    for (int i = 2; i < size; ++i) {
      auto cur_slope = compute_slope(stocks[i - 1], stocks[i]);
      if (!is_same_slope(cur_slope, lst_slope)) {
        //std::cout << cur_slope << ' ' << lst_slope << std::endl;
        ++ret;
        lst_slope = cur_slope;
      }
    }
    
    return ret;
  }
  
  bool is_same_slope(slope_t& s1, slope_t& s2) {
    return s1.s1 * s2.s2 == s1.s2 * s2.s1;
  }
  
  slope_t compute_slope(std::vector<int>& p1, std::vector<int>& p2) {
    return {(long long)p2[1] - p1[1], (long long)p2[0] - p1[0]};
  }
};
```
#### [❗️ 2281. Sum of Total Strength of Wizards](https://leetcode.com/contest/weekly-contest-294/problems/sum-of-total-strength-of-wizards/)

依据题意，需要计算子序列的最小值和sum的乘积的和。数据规模10^5，所以O(n^2)及以上的方法都会超时。

O(n^2)示例 [TLE]
```cpp
class Solution {
public:
  int totalStrength(vector<int>& strength) {
    long long sts{0};
    int size = strength.size();

    long long mod_base = 1000000007;
    for (int i = 0; i < size; ++i) {
      long long min = strength[i];
      long long sum = min;
      long long cur_sts = (min * sum) % mod_base;
      for (int j = i + 1; j < size; ++j) {
        min = std::min<long long>(min, strength[j]);
        sum += strength[j];
        sum %= mod_base;
        cur_sts += min * sum;
        cur_sts %= mod_base;
      }
      sts += cur_sts;
      sts %= mod_base;
    }
    return sts;
  }
};
```
