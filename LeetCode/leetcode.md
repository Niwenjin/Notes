# LeetCode

## 哈希

### 两数之和

[1. 两数之和（简单）](https://leetcode.cn/problems/two-sum/)

问题：给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出和为目标值 target 的那两个整数，并返回它们的数组下标。

思路：创建一个哈希表，遍历 nums，对于每一个 x，查询哈希表中是否存在 target - x：若存在，返回两个数组下标；否则，将 x 插入到哈希表中。

复杂度分析：

-   时间复杂度：$O(N)$
-   空间复杂度：$O(N)$

### 字母异位词分组

[49. 字母异位词分组（中等）](https://leetcode.cn/problems/group-anagrams/)

问题：给你一个字符串数组，请你将字母异位词（重新排列源单词的所有字母得到的一个新单词）组合在一起。可以按任意顺序返回结果列表。

思路：由于互为字母异位词的两个字符串包含的字母相同，因此对两个字符串分别进行排序之后得到的字符串一定是相同的，故可以将排序之后的字符串作为哈希表的键。

复杂度分析：

-   时间复杂度：$O(Nk\log k)$
-   空间复杂度：$O(Nk)$

实现：

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        if(strs.empty())
            return {};
        std::unordered_map<string, vector<string>> map;
        for (string& str: strs){
            string key = str;
            sort(key.begin(),key.end());
            map[key].emplace_back(str);
        }
        vector<vector<string>> res;
        for(auto it = map.begin();it!=map.end();++it){
            res.emplace_back(it->second);
        }
        return res;
    }
};
```

### 最长连续序列

[128. 最长连续序列（中等）](https://leetcode.cn/problems/longest-consecutive-sequence/)

问题：给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

思路：枚举法，用 unordered_set 构建去重的数组集合；再遍历集合，对于每个序列起始的元素进行循环，判断该序列长度。

复杂度分析：

-   时间复杂度：$O(N)$
-   空间复杂度：$O(N)$

实现：

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> num_set;
        for (const int& num : nums) {
            num_set.insert(num);
        }

        int longestStreak = 0;

        for (const int& num : num_set) {
            // 剪枝，仅对序列开头的元素进行计数
            if (!num_set.count(num - 1)) {
                int currentNum = num;
                int currentStreak = 1;

                while (num_set.count(currentNum + 1)) {
                    currentNum += 1;
                    currentStreak += 1;
                }
                longestStreak = max(longestStreak, currentStreak);
            }
        }
        return longestStreak;
    }
};
```

## 双指针

### 移动零

[283. 移动零（简单）](https://leetcode.cn/problems/move-zeroes/)

问题：给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

思路：创建两个指针 i 和 j。j 遍历整个数组，每当遇到非 0 元素就交换 i 和 j 的元素，并且 i 向右移动。当 j 指针遍历完整个数组时，i 指针就指向最后一个非零元素后的位置。此时 i 向后遍历完数组，并将途径的元素全部赋值为 0。

_优化：每次只需要交换 i 和 j 的元素。_

复杂度分析：

-   时间复杂度：$O(N)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int n = nums.size(), left = 0, right = 0;
        while (right < n) {
            if (nums[right]) {
                swap(nums[left], nums[right]);
                left++;
            }
            right++;
        }
    }
};
```

### 盛最多水的容器

[11. 盛最多水的容器（中等）](#https://leetcode.cn/problems/container-with-most-water/)

问题：给定一个长度为 n 的整数数组 height 。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i])。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。返回容器可以储存的最大水量。

思路：使用双指针指向数组两端。每次计算当前容量`min(height[l], height[r])*(r-l)`，如果大于 max 就更新。之后将两个指针指向的较小的那端向中间移动，直到两个指针相遇。返回 max。

复杂度分析：

-   时间复杂度：$O(N)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int l = 0, r = height.size() - 1;
        int ans = 0;
        while (l < r) {
            int area = min(height[l], height[r]) * (r - l);
            ans = max(ans, area);
            if (height[l] <= height[r]) {
                ++l;
            }
            else {
                --r;
            }
        }
        return ans;
    }
};
```

### 三数之和

[15. 三数之和（中等）](https://leetcode.cn/problems/3sum/)

问题：给你一个整数数组 nums ，判断是否存在三元组 [nums[i], nums[j], nums[k]] 满足 i != j、i != k 且 j != k ，同时还满足 nums[i] + nums[j] + nums[k] == 0 。请你返回所有和为 0 且不重复的三元组。

思路：先排序，再遍历数组，确定第一个数，在右侧的元素中寻找另外两个数。由于元素有序，只需要用双指针指向右侧元素的两端，如果和较大，就将右指针左移；如果和较小，就将左侧指针右移；如果找到结果，返回三元组。由于要求结果不重复，第一和第二个元素指针右移时需要判断是否和上一个重复，如果重复则继续移动。

复杂度分析：

-   时间复杂度：$O(N^2)$。双重循环遍历数组。
-   空间复杂度：$O(\log N)$。如果原地排序，考虑排序需要的额外空间为$O(\log N)$；如果要存储 nums 的排序副本，则为$O(N)$

实现：

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        // 枚举 a
        for (int first = 0; first < n; ++first) {
            // 需要和上一次枚举的数不相同
            if (first > 0 && nums[first] == nums[first - 1]) {
                continue;
            }
            // c 对应的指针初始指向数组的最右端
            int third = n - 1;
            int target = -nums[first];
            // 枚举 b
            for (int second = first + 1; second < n; ++second) {
                // 需要和上一次枚举的数不相同
                if (second > first + 1 && nums[second] == nums[second - 1]) {
                    continue;
                }
                // 需要保证 b 的指针在 c 的指针的左侧
                while (second < third && nums[second] + nums[third] > target) {
                    --third;
                }
                // 如果指针重合，随着 b 后续的增加
                // 就不会有满足 a+b+c=0 并且 b<c 的 c 了，可以退出循环
                if (second == third) {
                    break;
                }
                if (nums[second] + nums[third] == target) {
                    ans.push_back({nums[first], nums[second], nums[third]});
                }
            }
        }
        return ans;
    }
};
```

### 接雨水

[42. 接雨水（困难）](https://leetcode.cn/problems/trapping-rain-water/)

问题：给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

思路：用双指针从两端向中间遍历，记录 leftmax 和 rightmax。当左侧墙较低时，该列能装的水为`leftmax - height[left]`；右侧同理。

复杂度分析：

-   时间复杂度：$O(N)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int ans = 0;
        int left = 0, right = height.size() - 1;
        int leftMax = 0, rightMax = 0;
        while (left < right) {
            leftMax = max(leftMax, height[left]);
            rightMax = max(rightMax, height[right]);
            if (height[left] < height[right]) {
                ans += leftMax - height[left];
                ++left;
            } else {
                ans += rightMax - height[right];
                --right;
            }
        }
        return ans;
    }
};
```

## 滑动窗口

### 无重复字符的最长子串

[3. 无重复字符的最长子串（中等）](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

问题：给定一个字符串 s，请你找出其中不含有重复字符的最长子串的长度。

思路：使用滑动窗口遍历字符串。每一步操作时，都将左边界右移一步，然后不断向右移动右边界，直到子串中出现重复字符（用哈希表判断）。在移动结束后，这个子串就对应着以左指针开始的，不包含重复字符的最长子串。

复杂度分析：

-   时间复杂度：$O(N)$
-   空间复杂度：$O(|\Sigma|)$。存储字符集的哈希表所用空间。

实现：

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        // 哈希集合，记录每个字符是否出现过
        unordered_set<char> occ;
        int n = s.size();
        // 右指针，初始值为 -1，相当于我们在字符串的左边界的左侧，还没有开始移动
        int rk = -1, ans = 0;
        // 枚举左指针的位置，初始值隐性地表示为 -1
        for (int i = 0; i < n; ++i) {
            if (i != 0) {
                // 左指针向右移动一格，移除一个字符
                occ.erase(s[i - 1]);
            }
            while (rk + 1 < n && !occ.count(s[rk + 1])) {
                // 不断地移动右指针
                occ.insert(s[rk + 1]);
                ++rk;
            }
            // 第 i 到 rk 个字符是一个极长的无重复字符子串
            ans = max(ans, rk - i + 1);
        }
        return ans;
    }
};
```

### 找到字符串中所有字母异位词

[438. 找到字符串中所有字母异位词（中等）](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)

问题：给定两个字符串 s 和 p，找到 s 中所有 p 的异位词的子串，返回这些子串的起始索引。异位词指由相同字母重排列形成的字符串（包括相同的字符串）。

思路：用滑动窗口遍历字符串 s，维护窗口中每种字母的数量。当窗口中每种字母的数量与字符串 p 中每种字母的数量相同时，则说明当前窗口为字符串 p 的异位词。

优化：不再分别统计滑动窗口和字符串 p 中每种字母的数量，而是统计滑动窗口和字符串 p 中每种字母数量的差；并引入变量 differ 来记录当前窗口与字符串 p 中数量不同的字母的个数，并在滑动窗口的过程中维护它。

复杂度分析：

-   时间复杂度：$O(n+m+\Sigma)$。其中 n 为字符串 s 的长度，m 为字符串 p 的长度，其中 Σ 为所有可能的字符数。
-   空间复杂度：$O(|\Sigma|)$。用于存储滑动窗口和字符串 p 中每种字母数量的差。

实现：

```cpp
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        int sLen = s.size(), pLen = p.size();

        if (sLen < pLen) {
            return vector<int>();
        }

        vector<int> ans;
        vector<int> count(26);
        for (int i = 0; i < pLen; ++i) {
            ++count[s[i] - 'a'];
            --count[p[i] - 'a'];
        }

        int differ = 0;
        for (int j = 0; j < 26; ++j) {
            if (count[j] != 0) {
                ++differ;
            }
        }

        if (differ == 0) {
            ans.emplace_back(0);
        }

        for (int i = 0; i < sLen - pLen; ++i) {
            if (count[s[i] - 'a'] == 1) {  // 窗口中字母 s[i] 的数量与字符串 p 中的数量从不同变得相同
                --differ;
            } else if (count[s[i] - 'a'] == 0) {  // 窗口中字母 s[i] 的数量与字符串 p 中的数量从相同变得不同
                ++differ;
            }
            --count[s[i] - 'a'];

            if (count[s[i + pLen] - 'a'] == -1) {  // 窗口中字母 s[i+pLen] 的数量与字符串 p 中的数量从不同变得相同
                --differ;
            } else if (count[s[i + pLen] - 'a'] == 0) {  // 窗口中字母 s[i+pLen] 的数量与字符串 p 中的数量从相同变得不同
                ++differ;
            }
            ++count[s[i + pLen] - 'a'];

            if (differ == 0) {
                ans.emplace_back(i + 1);
            }
        }

        return ans;
    }
};
```
