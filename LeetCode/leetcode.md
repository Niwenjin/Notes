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

## 子串

### 和为 K 的子数组

[560. 和为 K 的子数组（中等）](https://leetcode.cn/problems/subarray-sum-equals-k/)

问题：给你一个整数数组 nums 和一个整数 k，请你统计并返回该数组中和为 k 的子数组的个数。子数组是数组中元素的连续非空序列。

思路：以为是滑动窗口，结果发现有负数，失败。尝试枚举，时间复杂为$O(n^2)$，超时。

优化：采用动态规划，定义 pre[i] 为 [0..i] 里所有数的和，则 pre[i]=pre[i−1]+nums[i]。那么「[j..i] 这个子数组和为 k 」这个条件我们可以转化为 pre[i]−pre[j−1]==k，即 pre[j−1]==pre[i]−k。建立哈希表 mp，以和为键，出现次数为对应的值，记录 pre[i] 出现的次数，从左往右边更新 mp 边计算答案，那么以 i 结尾的答案 mp[pre[i]−k] 即可在 O(1) 时间内得到。最后的答案即为所有下标结尾的和为 k 的子数组个数之和。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int, int> mp;
        mp[0] = 1;
        int count = 0, pre = 0;
        for (auto& x:nums) {
            pre += x;
            if (mp.find(pre - k) != mp.end()) {
                count += mp[pre - k];
            }
            mp[pre]++;
        }
        return count;
    }
};
```

### 滑动窗口最大值

[239. 滑动窗口最大值（困难）](https://leetcode.cn/problems/sliding-window-maximum/)

问题：给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。返回滑动窗口中的最大值。

思路：维护一个降序的单调队列。考虑三种情况：

-   窗口中的新元素比队头元素大，清空队列（新成员又新又大，前面的元素无用）；
-   窗口中的新元素比队尾的元素大，则从队尾开始，删除所有比新成员小的元素；
-   窗口中的新元素比队尾的元素小，加入队尾。

由于需要记录窗口中的字串，队列中存储的应该是元素的 idx，每次加入后判断头部元素是否已经离开窗口，循环删除这些旧元素。

技巧：`std::deque`是 C++标准库中的一个容器，全称为双端队列（double-ended queue），它提供了快速的两端访问和插入/删除操作。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(k)$

实现：

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        deque<int> q;
        for (int i = 0; i < k; ++i) {
            while (!q.empty() && nums[i] >= nums[q.back()]) {
                q.pop_back();
            }
            q.push_back(i);
        }

        vector<int> ans = {nums[q.front()]};
        for (int i = k; i < n; ++i) {
            while (!q.empty() && nums[i] >= nums[q.back()]) {
                q.pop_back();
            }
            q.push_back(i);
            while (q.front() <= i - k) {
                q.pop_front();
            }
            ans.push_back(nums[q.front()]);
        }
        return ans;
    }
};
```

### 最小覆盖子串

[76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

问题：给你一个字符串 s 、一个字符串 t 。返回 s 中涵盖 t 所有字符的最小子串。如果 s 中不存在涵盖 t 所有字符的子串，则返回空字符串 "" 。

思路：滑动窗口，用哈希表存储 t 中字母的出现次数。窗口右边界向右移动，如果覆盖成功，则不断将左边界向右移动，直到得到该右边界下的最小覆盖，与记录的最小覆盖比较。直到遍历完整个字符串。

复杂度分析：

-   时间复杂度：$O(|\Sigma|m+n)$。其中 m 为 s 的长度，n 为 t 的长度，$\Sigma$为字符集合的大小。
-   空间复杂度：$O(\Sigma)$。

实现：

```cpp
class Solution {
    bool is_covered(int cnt_s[], int cnt_t[]) {
        for (int i = 'A'; i <= 'Z'; i++) {
            if (cnt_s[i] < cnt_t[i]) {
                return false;
            }
        }
        for (int i = 'a'; i <= 'z'; i++) {
            if (cnt_s[i] < cnt_t[i]) {
                return false;
            }
        }
        return true;
    }

public:
    string minWindow(string s, string t) {
        int m = s.length();
        int ans_left = -1, ans_right = m, left = 0;
        int cnt_s[128]{}, cnt_t[128]{};
        for (char c : t) {
            cnt_t[c]++;
        }
        for (int right = 0; right < m; right++) { // 移动子串右端点
            cnt_s[s[right]]++; // 右端点字母移入子串
            while (is_covered(cnt_s, cnt_t)) { // 涵盖
                if (right - left < ans_right - ans_left) { // 找到更短的子串
                    ans_left = left; // 记录此时的左右端点
                    ans_right = right;
                }
                cnt_s[s[left++]]--; // 左端点字母移出子串
            }
        }
        return ans_left < 0 ? "" : s.substr(ans_left, ans_right - ans_left + 1);
    }
};
```

## 普通数组

### 最大子数组和

[53. 最大子数组和（中等）](https://leetcode.cn/problems/maximum-subarray/)

问题：给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

思路：动态规划。$f(i)$代表以第$i$个数结尾的“连续子数组的最大和”，$f(i)=max\{f(i−1)+nums[i],nums[i]\}$。只需要求出每个位置的 $f(i)$，然后返回数组中的最大值即可。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int pre = 0, maxAns = nums[0];
        for (const auto &x: nums) {
            pre = max(pre + x, x);
            maxAns = max(maxAns, pre);
        }
        return maxAns;
    }
};
```

### 合并区间

[56. 合并区间（中等）](https://leetcode.cn/problems/merge-intervals/)

问题：以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals[i] = [starti, endi] 。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

思路：先按照第一个元素进行排序，排序后，所有能合并的区间必定都是连续的。遍历数组，维护两个端点的下标：如果能合并，更新右端点；如果不能合并，将当前区间压入结果中，并更新左端点，开始计算下一个区间。

技巧：对于多层嵌套数组，sort()可以直接使用外层迭代器进行排序。

复杂度分析：

-   时间复杂度：$O(nlogn)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        if (intervals.size() == 0) {
            return {};
        }
        sort(intervals.begin(), intervals.end());
        vector<vector<int>> merged;
        for (int i = 0; i < intervals.size(); ++i) {
            int L = intervals[i][0], R = intervals[i][1];
            if (!merged.size() || merged.back()[1] < L) {
                merged.push_back({L, R});
            }
            else {
                merged.back()[1] = max(merged.back()[1], R);
            }
        }
        return merged;
    }
};
```

### 轮转数组

[189. 轮转数组（中等）](https://leetcode.cn/problems/rotate-array/)

问题：给定一个整数数组 nums，将数组中的元素向右轮转 k 个位置，其中 k 是非负数。

思路：使用额外的数组，遍历原数组，将原数组下标为 i 的元素放至新数组下标为 (i+k)mod n 的位置。

优化：数组翻转，原地操作，不需要额外空间。先将所有元素翻转，这样尾部的 kmodn 个元素就被移至数组头部，然后再翻转 [0,k mod n−1] 区间的元素和 [k mod n,n−1] 区间的元素。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    void reverse(vector<int>& nums, int start, int end) {
        while (start < end) {
            swap(nums[start], nums[end]);
            start += 1;
            end -= 1;
        }
    }

    void rotate(vector<int>& nums, int k) {
        k %= nums.size();
        reverse(nums, 0, nums.size() - 1);
        reverse(nums, 0, k - 1);
        reverse(nums, k, nums.size() - 1);
    }
};
```

### 除自身以外数组的乘积

[238. 除自身以外数组的乘积（中等）](https://leetcode.cn/problems/product-of-array-except-self/)

问题：给你一个整数数组 nums，返回数组 answer ，其中 answer[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积。请不要使用除法，且在 O(n) 时间复杂度内完成此题。

思路：维护前后缀乘积。第一次从前往后遍历，计算每个位置的前缀元素的乘积；第一次从后往前遍历，计算每个位置的后缀元素的乘积。完成后每个位置的除自身以外的乘积为前缀与后缀的乘积。

优化：用一个元素来跟踪后缀元素的乘积，前缀元素乘积原地维护。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int length = nums.size();
        vector<int> answer(length);

        // answer[i] 表示索引 i 左侧所有元素的乘积
        // 因为索引为 '0' 的元素左侧没有元素， 所以 answer[0] = 1
        answer[0] = 1;
        for (int i = 1; i < length; i++) {
            answer[i] = nums[i - 1] * answer[i - 1];
        }

        // R 为右侧所有元素的乘积
        // 刚开始右边没有元素，所以 R = 1
        int R = 1;
        for (int i = length - 1; i >= 0; i--) {
            // 对于索引 i，左边的乘积为 answer[i]，右边的乘积为 R
            an：swer[i] = answer[i] * R;
            // R 需要包含右边所有的乘积，所以计算下一个结果时需要将当前值乘到 R 上
            R *= nums[i];
        }
        return answer;
    }
};
```

### 缺失的第一个正数

[41. 缺失的第一个正数（困难）](https://leetcode.cn/problems/first-missing-positive/)

问题：给你一个未排序的整数数组 nums ，请你找出其中没有出现的最小的正整数。请你实现时间复杂度为 O(n) 并且只使用常数级别额外空间的解决方案。

思路：将数组恢复成 [1, 2, 3, ..., N] 的形式，之后遍历数组，其中如果有某个位置上的数对应不上，就是缺失的正数。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        for (int i = 0; i < n; ++i) {
            while (nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                swap(nums[nums[i] - 1], nums[i]);
            }
        }
        for (int i = 0; i < n; ++i) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return n + 1;
    }
};
```

## 矩阵

### 矩阵置零

[73. 矩阵置零（中等）](https://leetcode.cn/problems/set-matrix-zeroes/)

问题：给定一个 m x n 的矩阵，如果一个元素为 0 ，则将其所在行和列的所有元素都设为 0 。请使用原地算法。

思路：使用标记数组，记录每一行和每一列是否有零出现。

优化：用矩阵的第一行和第一列代替方法一中的两个标记数组，以达到 O(1) 的额外空间。但这样会导致原数组的第一行和第一列被修改，无法记录它们是否原本包含 0。因此我们需要额外使用两个标记变量分别记录第一行和第一列是否原本包含 0。

```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        int flag_col0 = false, flag_row0 = false;
        for (int i = 0; i < m; i++) {
            if (!matrix[i][0]) {
                flag_col0 = true;
            }
        }
        for (int j = 0; j < n; j++) {
            if (!matrix[0][j]) {
                flag_row0 = true;
            }
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (!matrix[i][j]) {
                    matrix[i][0] = matrix[0][j] = 0;
                }
            }
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (!matrix[i][0] || !matrix[0][j]) {
                    matrix[i][j] = 0;
                }
            }
        }
        if (flag_col0) {
            for (int i = 0; i < m; i++) {
                matrix[i][0] = 0;
            }
        }
        if (flag_row0) {
            for (int j = 0; j < n; j++) {
                matrix[0][j] = 0;
            }
        }
    }
};
```

### 螺旋矩阵

[54. 螺旋矩阵（中等）](https://leetcode.cn/problems/spiral-matrix/)

问题：给你一个 m 行 n 列的矩阵 matrix ，请按照顺时针螺旋顺序，返回矩阵中的所有元素。

思路：维护上下左右边界，每次碰到边界就进行收缩，直到上下边界相遇。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector <int> ans;
        if(matrix.empty()) return ans; //若数组为空，直接返回答案
        int u = 0; //赋值上下左右边界
        int d = matrix.size() - 1;
        int l = 0;
        int r = matrix[0].size() - 1;
        while(true)
        {
            for(int i = l; i <= r; ++i) ans.push_back(matrix[u][i]); //向右移动直到最右
            if(++ u > d) break; //重新设定上边界，若上边界大于下边界，则遍历遍历完成，下同
            for(int i = u; i <= d; ++i) ans.push_back(matrix[i][r]); //向下
            if(-- r < l) break; //重新设定有边界
            for(int i = r; i >= l; --i) ans.push_back(matrix[d][i]); //向左
            if(-- d < u) break; //重新设定下边界
            for(int i = d; i >= u; --i) ans.push_back(matrix[i][l]); //向上
            if(++ l > r) break; //重新设定左边界
        }
        return ans;
    }
};
```

### 旋转图像

[48. 旋转图像（中等）](https://leetcode.cn/problems/rotate-image/)

问题：给定一个 n × n 的二维矩阵 `matrix` 表示一个图像，请你将图像顺时针旋转 90 度。你必须在原地旋转图像。

思路：找规律，从外层到内层循环，每次交换四个元素的位置。

复杂度分析：

-   时间复杂度：$O(n^2)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        for (int i = 0; i < n / 2; i++) {
            for (int j = i; j < n - i - 1; j++) {
                int tmp = matrix[i][j];
                matrix[i][j] = matrix[n - j - 1][i];
                matrix[n - j - 1][i] = matrix[n - i - 1][n - j - 1];
                matrix[n - i - 1][n - j - 1] = matrix[j][n - i - 1];
                matrix[j][n - i - 1] = tmp;
            }
        }
    }
};
```

优化：用翻转代替旋转，先根据水平轴翻转一次，再根据对角线翻转一次，即得到答案。

### 搜索二维矩阵 II

[240. 搜索二维矩阵 II（中等）](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

问题：编写一个高效的算法来搜索 ` m x n` 矩阵 `matrix` 中的一个目标值 `target` 。该矩阵具有以下特性：

-   每行的元素从左到右升序排列。
-   每列的元素从上到下升序排列。

思路：阶梯形查找。从右上角开始搜索，当元素较大时下标左移，当元素较小时下标下移。

复杂度分析：

-   时间复杂度：$O(m+n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        int x = 0, y = n - 1;
        while (x < m && y >= 0) {
            if (matrix[x][y] == target) {
                return true;
            }
            if (matrix[x][y] > target) {
                --y;
            }
            else {
                ++x;
            }
        }
        return false;
    }
};
```

## 链表

### 相交链表

[160. 相交链表（简单）](https://leetcode.cn/problems/intersection-of-two-linked-lists/)

问题：给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 `null` 。

思路：用哈希集合存储第一个链表的指针，遍历第二个链表，判断指针是否在集合中，返回在集合中的第一个指针。

优化：双指针。每步操作更新指针 pA 和 pB：

-   每步操作需要同时更新指针 pA 和 pB。
-   如果指针 pA 不为空，则将指针 pA 移到下一个节点；如果指针 pB 不为空，则将指针 pB 移到下一个节点。
-   如果指针 pA 为空，则将指针 pA 移到链表 headB 的头节点；如果指针 pB 为空，则将指针 pB 移到链表 headA 的头节点。
-   当指针 pA 和 pB 指向同一个节点或者都为空时，返回它们指向的节点或者 null。

_提示：相当于在两条链表的末尾各接上另一条链表，A+B 和 B+A 长度相等。如果相遇时在末尾，说明没有相交；否则，会在相交处相遇。_

复杂度分析：

-   时间复杂度：$O(m+n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (headA == nullptr || headB == nullptr) {
            return nullptr;
        }
        ListNode *pA = headA, *pB = headB;
        while (pA != pB) {
            pA = pA == nullptr ? headB : pA->next;
            pB = pB == nullptr ? headA : pB->next;
        }
        return pA;
    }
};
```

### 反转链表

[206. 反转链表（简单）](https://leetcode.cn/problems/reverse-linked-list/)

问题：给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。

思路 1：遍历链表，维护每个节点的 pre , curr 和 next ，进行交换。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr) {
            ListNode* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
};
```

思路 2：头插法。创建一个头节点，遍历链表，逐个插入头节点的下一个位置。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head)
            return nullptr;
        ListNode p(0, nullptr);
        ListNode* q = head;
        while (q) {
            ListNode* tmp = q;
            q = q->next;
            tmp->next = p.next;
            p.next = tmp;
        }
        return p.next;
    }
};
```

### 回文链表

[234. 回文链表（简单）](https://leetcode.cn/problems/palindrome-linked-list/)

问题：给你一个单链表的头节点 `head` ，请你判断该链表是否为回文链表。如果是，返回 `true` ；否则，返回 `false` 。

思路：快慢指针找到链表后半部分的起点，翻转一半链表，与另一半进行比较。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        ListNode *fast = head, *q = head;
        int cnt = 0;
        // 快慢指针找到链表后半的起点
        while (fast) {
            q = q->next;
            fast = fast->next;
            cnt++;
            if (fast) {
                fast = fast->next;
                cnt++;
            }
        }
        // 翻转链表的前半部分（也可以翻转后半）
        ListNode newhead(0, nullptr);
        ListNode* p = head;
        while (p != q) {
            ListNode* tmp = p;
            p = p->next;
            tmp->next = newhead.next;
            newhead.next = tmp;
        }
        // 如果链表长度为奇数，忽略中间的节点（翻转后在头部）
        p = cnt % 2 == 0 ? newhead.next : newhead.next->next;
        while (p) {
            if (p->val != q->val)
                return false;
            p = p->next;
            q = q->next;
        }
        return true;
    }
};
```

### 环形链表

[141. 环形链表（简单）](https://leetcode.cn/problems/linked-list-cycle/)

问题：给你一个链表的头节点 `head` ，判断链表中是否有环。

思路：快慢指针，若相遇，则有环。「Floyd 判圈算法」

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    bool hasCycle(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return false;
        }
        ListNode* slow = head;
        ListNode* fast = head->next;
        while (slow != fast) {
            if (fast == nullptr || fast->next == nullptr) {
                return false;
            }
            slow = slow->next;
            fast = fast->next->next;
        }
        return true;
    }
};
```

### 环形链表 II

[142. 环形链表 II（中等）](https://leetcode.cn/problems/linked-list-cycle-ii/)

问题：给定一个链表的头节点 `head` ，返回链表开始入环的第一个节点。如果链表无环，则返回 `null`。

思路：快慢指针。设链表中环外部分的长度为 a ，慢指针进入环内后，又走了 b 长度与快指针相遇，此时，快指针走了 2(a + b) 长度。设环内剩余长度为 c ，则有 a + n(b + c) + b = 2(a + b) ，化简得 a = c + (n - 1)(b + c) ，即从相遇点到入环点的距离加上 n−1 圈的环长，恰好等于从链表头部到入环点的距离。此时，再让另一个慢指针从 head 出发，当它到达环的入口处时，走过了 a 的距离，之前出发的慢指针共走过了 a + a + b ，也就是 a + n(b + c) 的距离，两个慢指针在入口处相遇。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *slow = head, *fast = head;
        while (fast != nullptr) {
            slow = slow->next;
            if (fast->next == nullptr) {
                return nullptr;
            }
            fast = fast->next->next;
            if (fast == slow) {
                ListNode *ptr = head;
                while (ptr != slow) {
                    ptr = ptr->next;
                    slow = slow->next;
                }
                return ptr;
            }
        }
        return nullptr;
    }
};
```

### 合并两个有序链表

[21. 合并两个有序链表（中等）](https://leetcode.cn/problems/merge-two-sorted-lists/)

问题：将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

思路：迭代。

复杂度分析：

-   时间复杂度：$O(n+m)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* preHead = new ListNode(-1);

        ListNode* prev = preHead;
        while (l1 != nullptr && l2 != nullptr) {
            if (l1->val < l2->val) {
                prev->next = l1;
                l1 = l1->next;
            } else {
                prev->next = l2;
                l2 = l2->next;
            }
            prev = prev->next;
        }

        // 合并后 l1 和 l2 最多只有一个还未被合并完，我们直接将链表末尾指向未合并完的链表即可
        prev->next = l1 == nullptr ? l2 : l1;

        return preHead->next;
    }
};
```

### 两数相加

[2. 两数相加（中等）](https://leetcode.cn/problems/add-two-numbers/)

问题：给你两个非空的链表，表示两个非负的整数。它们每位数字都是按照逆序的方式存储的，并且每个节点只能存储一位数字。请你将两个数相加，并以相同形式返回一个表示和的链表。

思路：逐位相加。重点在与判断进位。

复杂度分析：

-   时间复杂度：$O(\max(n, m))$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* head = new ListNode();
        ListNode* curr = head;
        int carry = 0;
        while(l1 || l2 || carry) {
            int a = l1 ? l1->val : 0;
            int b = l2 ? l2->val : 0;
            int sum = a + b + carry;
            carry = sum >= 10 ? 1 : 0;
            curr->next = new ListNode(sum % 10);
            curr = curr->next;
            if(l1) l1 = l1->next;
            if(l2) l2 = l2->next;
        }
        return head->next;
    }
};
```

### 删除链表的倒数第 N 个节点

[19. 删除链表的倒数第 N 个节点（中等）](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

问题：给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

思路：双指针。

复杂度分析：

-   时间复杂度：$O(L)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *p = head, *q = head;
        for (int i = 0; i < n; ++i)
            q = q->next;
        if (!q)
            return head->next;
        while (q->next) {
            p = p->next;
            q = q->next;
        }
        p->next = p->next->next;
        return head;
    }
};
```

### 两两交换链表中的节点

[24. 两两交换链表中的节点（中等）](https://leetcode.cn/problems/swap-nodes-in-pairs/)

问题：给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。

思路：迭代。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (!head || !head->next)
            return head;
        ListNode dummy(0, head);
        ListNode *pre = &dummy, *p = head, *q = head->next;
        while (true) {
            p->next = q->next;
            q->next = p;
            pre->next = q;
            pre = p;
            p = p->next;
            if (!p || !p->next)
                break;
            q = p->next;
        }
        return dummy.next;
    }
};
```

### K 个一组翻转链表

[25. K 个一组翻转链表（困难）](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

问题：给你链表的头节点 `head` ，每 `k` 个节点一组进行翻转，请你返回修改后的链表。

思路：迭代。关键在于子链表翻转后，前后的连接。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if (k < 2)
            return head;
        ListNode* dummy = new ListNode(0, head);
        ListNode* pre = dummy;
        ListNode* nextpre = pre;
        while (true) {
            for (int i = 0; i < k; ++i) {
                // 检查接下来的子链表长度是否足够
                if (nextpre->next)
                    nextpre = nextpre->next;
                else {
                    head = dummy->next;
                    delete (dummy);
                    return head;
                }
            }
            nextpre = pre->next;
            reverse(pre, k);
            pre = nextpre;
        }
        return dummy->next;
    }

private:
    // 翻转子链表
    void reverse(ListNode* pre, int k) {
        ListNode* cur = pre->next->next;
        ListNode* nextp = cur;
        ListNode* tail = pre->next;
        for (int i = 1; i < k; ++i) {
            nextp = cur->next;
            cur->next = pre->next;
            pre->next = cur;
            cur = nextp;
        }
        tail->next = nextp;
    }
};
```
