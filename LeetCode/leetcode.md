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

### 随机链表的复制

[138. 随机链表的复制（中等）](https://leetcode.cn/problems/copy-list-with-random-pointer/)

问题：给你一个长度为 `n` 的链表，每个节点包含一个额外增加的随机指针 `random` ，该指针可以指向链表中的任何节点或空节点。构造这个链表的深拷贝。 深拷贝应该正好由 `n` 个全新节点组成，其中每个新节点的值都设为其对应的原节点的值。新节点的 `next` 指针和 `random` 指针也都应指向复制链表中的新节点，并使原链表和复制链表中的这些指针能够表示相同的链表状态。

思路：迭代。第一次遍历链表，复制 val ，将 random 对应的 <end, start[]> 存入哈希表中（可能存在多个相同的 start 指向同一个 end）。第二次遍历链表，根据哈希表中的键值对更新复制表中每一个 random 指针。

官解（更清晰）：第一次遍历链表，复制 val ，建立 “原节点 -> 新节点” 的 Map 映射。第二次遍历链表，根据 Map 复制 next 和 random 指针。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if(head == nullptr) return nullptr;
        Node* cur = head;
        unordered_map<Node*, Node*> map;
        // 1. 复制各节点，并建立 “原节点 -> 新节点” 的 Map 映射
        while(cur != nullptr) {
            map[cur] = new Node(cur->val);
            cur = cur->next;
        }
        cur = head;
        // 2. 构建新链表的 next 和 random 指向
        while(cur != nullptr) {
            map[cur]->next = map[cur->next];
            map[cur]->random = map[cur->random];
            cur = cur->next;
        }
        // 3. 返回新链表的头节点
        return map[head];
    }
};
```

优化：拼接 + 拆分。考虑构建`原节点 1 -> 新节点 1 -> 原节点 2 -> 新节点 2 -> …… `的拼接链表，如此便可在访问原节点的 random 指向节点的同时找到新对应新节点的 random 指向节点。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if(head == nullptr) return nullptr;
        Node* cur = head;
        // 1. 复制各节点，并构建拼接链表
        while(cur != nullptr) {
            Node* tmp = new Node(cur->val);
            tmp->next = cur->next;
            cur->next = tmp;
            cur = tmp->next;
        }
        // 2. 构建各新节点的 random 指向
        cur = head;
        while(cur != nullptr) {
            if(cur->random != nullptr)
                cur->next->random = cur->random->next;
            cur = cur->next->next;
        }
        // 3. 拆分两链表
        cur = head->next;
        Node* pre = head, *res = head->next;
        while(cur->next != nullptr) {
            pre->next = pre->next->next;
            cur->next = cur->next->next;
            pre = pre->next;
            cur = cur->next;
        }
        pre->next = nullptr; // 单独处理原链表尾节点
        return res;      // 返回新链表头节点
    }
};
```

### 排序链表

[148. 排序链表（中等）](https://leetcode.cn/problems/sort-list/)

问题：给你链表的头结点 `head` ，请将其按升序排列并返回排序后的链表 。

思路：归并排序。

复杂度分析：

-   时间复杂度：$O(n \log n)$
-   空间复杂度：$O(1)$

实现(背诵)：

```cpp
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        if (!head) {
            return head;
        }

        int length = 0;
        ListNode* p = head;
        while (p) {
            length++;
            p = p->next;
        }

        ListNode* dummy = new ListNode(0, head);
        for (int curlen = 1; curlen < length; curlen <<= 1) {
            ListNode *pre = dummy, *cur = dummy->next;
            while (cur) {
                ListNode* head1 = cur;
                for (int i = 1; i < curlen && cur->next; ++i) {
                    cur = cur->next;
                }
                ListNode* head2 = cur->next;
                cur->next = nullptr;
                cur = head2;
                for (int i = 1; i < curlen && cur && cur->next; ++i) {
                    cur = cur->next;
                }
                ListNode* next = nullptr;
                if (cur) {
                    next = cur->next;
                    cur->next = nullptr;
                }
                ListNode* merged = merge(head1, head2);
                pre->next = merged;
                while (pre->next)
                    pre = pre->next;
                cur = next;
            }
        }
        return dummy->next;
    }

private:
    ListNode* merge(ListNode* head1, ListNode* head2) {
        ListNode *dummy = new ListNode(), *cur = dummy, *p = head1, *q = head2;
        while (p && q) {
            if (p->val < q->val) {
                cur->next = p;
                p = p->next;
            } else {
                cur->next = q;
                q = q->next;
            }
            cur = cur->next;
        }
        if (p) {
            cur->next = p;
        } else {
            cur->next = q;
        }
        cur = dummy->next;
        delete (dummy);
        return cur;
    }
};
```

### 合并 K 个升序链表

[23. 合并 K 个升序链表（困难）](https://leetcode.cn/problems/merge-k-sorted-lists/)

问题：给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。

思路：分治合并。将 k 个链表两两合并，直到剩下一个链表。

复杂度分析：

-   时间复杂度：$O(kn \log k)$
-   空间复杂度：$O(\log k)$

实现：

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode *a, ListNode *b) {
        if ((!a) || (!b)) return a ? a : b;
        ListNode head, *tail = &head, *aPtr = a, *bPtr = b;
        while (aPtr && bPtr) {
            if (aPtr->val < bPtr->val) {
                tail->next = aPtr; aPtr = aPtr->next;
            } else {
                tail->next = bPtr; bPtr = bPtr->next;
            }
            tail = tail->next;
        }
        tail->next = (aPtr ? aPtr : bPtr);
        return head.next;
    }

    ListNode* merge(vector <ListNode*> &lists, int l, int r) {
        if (l == r) return lists[l];
        if (l > r) return nullptr;
        int mid = (l + r) >> 1;
        return mergeTwoLists(merge(lists, l, mid), merge(lists, mid + 1, r));
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return merge(lists, 0, lists.size() - 1);
    }
};
```

思路：优先队列合并。维护当前每个链表没有被合并的元素的最前面一个，每次在这些元素中选取 val 最小的元素合并到答案中。

技巧：在选取最小元素时，使用**优先队列**来优化这个过程。

复杂度分析：

-   时间复杂度：$O(kn \log k)$
-   空间复杂度：$O(k)$

实现：

```cpp
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        lists.erase(remove_if(lists.begin(), lists.end(), [](auto p) { return !p; }), lists.end());
        priority_queue q{ [](auto& a, auto& b) { return a->val > b->val; }, lists };
        ListNode dummy;
        for (auto p = &dummy; !q.empty(); q.pop()) {
            p = p->next = q.top();
            if (p->next) q.push(p->next);
        }
        return dummy.next;
    }
};
```

### LRU 缓存

[146. LRU 缓存（中等）](https://leetcode.cn/problems/lru-cache/)

问题：请你设计并实现一个满足 LRU (最近最少使用) 缓存约束的数据结构。函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。

思路(背诵)：哈希表 + 双向链表。链表节点存储 key 和 value，哈希表存储 key 和对应链表节点的指针，当访问或新增链表节点时将其插入头部，若超出容量则从尾部移除。

复杂度分析：

-   时间复杂度：$O(1)$
-   空间复杂度：$O(capacity)$

实现：

```cpp
struct DLinkedNode {
    int key, value;
    DLinkedNode* prev;
    DLinkedNode* next;
    DLinkedNode(): key(0), value(0), prev(nullptr), next(nullptr) {}
    DLinkedNode(int _key, int _value): key(_key), value(_value), prev(nullptr), next(nullptr) {}
};

class LRUCache {
private:
    unordered_map<int, DLinkedNode*> cache;
    DLinkedNode* head;
    DLinkedNode* tail;
    int size;
    int capacity;

public:
    LRUCache(int _capacity): capacity(_capacity), size(0) {
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head->next = tail;
        tail->prev = head;
    }

    int get(int key) {
        if (!cache.count(key)) {
            return -1;
        }
        DLinkedNode* node = cache[key];
        moveToHead(node);
        return node->value;
    }

    void put(int key, int value) {
        // key不存在
        if (!cache.count(key)) {
            DLinkedNode* node = new DLinkedNode(key, value);
            cache[key] = node;
            addToHead(node);
            ++size;
            if (size > capacity) {
                DLinkedNode* removed = removeTail();
                cache.erase(removed->key);
                delete removed;
                --size;
            }
        }
        // key存在
        else {
            DLinkedNode* node = cache[key];
            node->value = value;
            moveToHead(node);
        }
    }

    void addToHead(DLinkedNode* node) {
        node->prev = head;
        node->next = head->next;
        head->next->prev = node;
        head->next = node;
    }

    void removeNode(DLinkedNode* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }

    void moveToHead(DLinkedNode* node) {
        removeNode(node);
        addToHead(node);
    }

    DLinkedNode* removeTail() {
        DLinkedNode* node = tail->prev;
        removeNode(node);
        return node;
    }
};
```

## 二叉树

### 二叉树的中序遍历

[94. 二叉树的中序遍历（简单）](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

问题：给定一个二叉树的根节点 `root` ，返回它的中序遍历 。

思路：递归。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    void inorder(TreeNode* root, vector<int>& res) {
        if (!root) {
            return;
        }
        inorder(root->left, res);
        res.push_back(root->val);
        inorder(root->right, res);
    }
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        inorder(root, res);
        return res;
    }
};
```

思路：回溯。显式维护一个栈，存储访问过的节点。当左子树遍历完后通过栈回溯到根节点。

实现(背诵)：

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> stk;
        while (root != nullptr || !stk.empty()) {
            while (root != nullptr) {
                stk.push(root);
                root = root->left;
            }
            root = stk.top();
            stk.pop();
            res.push_back(root->val);
            root = root->right;
        }
        return res;
    }
};
```

优化：Morris 中序遍历。**Morris 遍历算法**是另一种遍历二叉树的方法，它能将非递归的中序遍历空间复杂度降为 _O_(1)。整体步骤如下：

1. 如果 _x_ 无左孩子，先将 _x_ 的值加入答案数组，再访问 _x_ 的右孩子。
2. 如果 x 有左孩子，则找到 x 左子树上最右的节点（即左子树中序遍历的最后一个节点，x 在中序遍历中的前驱节点），我们记为 predecessor。根据 predecessor 的右孩子是否为空，进行如下操作：如果 _predecessor_ 的右孩子为空，则将其右孩子指向 _x_，然后访问 _x_ 的左孩子；如果 predecessor 的右孩子不为空，则此时其右孩子指向 x，说明我们已经遍历完 x 的左子树，我们将 predecessor 的右孩子置空，将 x 的值加入答案数组，然后访问 x 的右孩子。
3. 重复上述操作，直至访问完整棵树。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        TreeNode *predecessor = nullptr;

        while (root != nullptr) {
            if (root->left != nullptr) {
                // predecessor 节点就是当前 root 节点向左走一步，然后一直向右走至无法走为止
                predecessor = root->left;
                while (predecessor->right != nullptr && predecessor->right != root) {
                    predecessor = predecessor->right;
                }

                // 让 predecessor 的右指针指向 root，继续遍历左子树
                if (predecessor->right == nullptr) {
                    predecessor->right = root;
                    root = root->left;
                }
                // 说明左子树已经访问完了，我们需要断开链接
                else {
                    res.push_back(root->val);
                    predecessor->right = nullptr;
                    root = root->right;
                }
            }
            // 如果没有左孩子，则直接访问右孩子
            else {
                res.push_back(root->val);
                root = root->right;
            }
        }
        return res;
    }
};
```

### 二叉树的后序遍历

[145. 二叉树的后序遍历（简单）](https://leetcode.cn/problems/binary-tree-postorder-traversal/)

问题：给你一棵二叉树的根节点 `root` ，返回其节点值的后序遍历。

思路：递归（简单）；迭代法(背诵)，考虑后序遍历的顺序为`左-右-中`，而先序遍历的顺序为`中-左-右`，可以采用类似先序遍历的方法，但是把顺序改为`中-右-左`，之后进行翻转。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(height)$

实现：

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        if (!root)
            return {};
        std::stack<TreeNode*> stack;
        std::vector<int> ans;
        while (root || !stack.empty()) {
            while (root) {
                ans.push_back(root->val);
                stack.push(root);
                root = root->right;
            }
            root = stack.top();
            stack.pop();
            root = root->left;
        }
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

### 二叉树的最大深度

[104. 二叉树的最大深度（简单）](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

问题：给定一个二叉树 `root` ，返回其最大深度。

思路：递归实现深度优先搜索。叶子节点深度为 0, 当前节点的最大深度 = max { 左子树的最大深度, 右子树的最大深度 } + 1

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(height)$

实现(背诵)：

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```

### 翻转二叉树

[226. 翻转二叉树（简单）](https://leetcode.cn/problems/invert-binary-tree/)

问题：给你一棵二叉树的根节点 `root` ，翻转这棵二叉树，并返回其根节点。

思路：递归遍历二叉树，从叶子节点开始翻转。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$。使用的空间由递归栈的深度决定，它等于当前节点在二叉树中的高度。在平均情况下，二叉树的高度与节点个数为对数关系，即 O(logN)。而在最坏情况下，树形成链状，空间复杂度为 O(N)。

实现：

```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == nullptr) {
            return nullptr;
        }
        TreeNode* left = invertTree(root->left);
        TreeNode* right = invertTree(root->right);
        root->left = right;
        root->right = left;
        return root;
    }
};
```

### 对称二叉树

[101. 对称二叉树（简单）](https://leetcode.cn/problems/symmetric-tree/)

问题：给你一个二叉树的根节点 `root` ， 检查它是否轴对称。

思路：如果一个树的左子树与右子树镜像对称，那么这个树是对称的。两棵树互为镜像需要满足下列条件：它们的两个根结点具有相同的值；每个树的右子树都与另一个树的左子树镜像对称。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    bool check(TreeNode *p, TreeNode *q) {
        if (!p && !q) return true;
        if (!p || !q) return false;
        return p->val == q->val && check(p->left, q->right) && check(p->right, q->left);
    }

    bool isSymmetric(TreeNode* root) {
        return check(root, root);
    }
};
```

### 二叉树的直径

[543. 二叉树的直径（简单）](https://leetcode.cn/problems/diameter-of-binary-tree/)

问题：给你一棵二叉树的根节点，返回该树的直径。二叉树的直径是指树中任意两个节点之间最长路径的长度。这条路径可能经过也可能不经过根节点 `root` 。

思路：递归计算二叉树左右子树的高度，当前节点的高度为`max(L, R) + 1`。遍历过程中更新最长路径。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(height)$。需要额外的空间且该空间取决于递归的深度，而递归的深度显然为二叉树的高度。

实现：

```cpp
class Solution {
public:
    int diameterOfBinaryTree(TreeNode* root) {
        depth(root);
        return maxdia_;
    }
private:
    int maxdia_ = 0;
    int depth(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }
        int L = depth(root->left);
        int R = depth(root->right);
        maxdia_ = max(maxdia_, L + R);
        return max(L, R) + 1;
    }
};
```

### 二叉树的层序遍历

[102. 二叉树的层序遍历（中等）](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

问题：给你二叉树的根节点 `root` ，返回其节点值的层序遍历。

思路：用队列存储未遍历的元素。首先根节点入队，当队列不为空时，求当前队列长度 len，从队列中取 len 个元素作为下一层，并将孩子节点入队。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector <vector <int>> ret;
        if (!root) {
            return ret;
        }
        queue <TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            int currentLevelSize = q.size();
            ret.push_back(vector <int> ());
            for (int i = 1; i <= currentLevelSize; ++i) {
                auto node = q.front(); q.pop();
                ret.back().push_back(node->val);
                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
        }
        return ret;
    }
};
```

### 将有序数组转换为二叉搜索树

[108. 将有序数组转换为二叉搜索树（简单）](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)

问题：给你一个整数数组 `nums` ，其中元素已经按升序排列，请你将其转换为一棵平衡二叉搜索树。

思路：分治。将中间节点作为根，左右递归建树。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(\log n)$

实现：

```cpp
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return makeTree(nums, 0, nums.size() - 1);
    }
private:
    TreeNode* makeTree(vector<int>& nums, int l, int r) {
        if (r < l)
            return nullptr;
        int mid = (l + r) / 2;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = makeTree(nums, l, mid - 1);
        root->right = makeTree(nums, mid + 1, r);
        return root;
    }
};
```

### 验证二叉搜索树

[98. 验证二叉搜索树（中等）](https://leetcode.cn/problems/validate-binary-search-tree/)

问题：给你一个二叉树的根节点 `root` ，判断其是否是一个有效的二叉搜索树。

思路：中序遍历递增。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        stack<TreeNode*> stack;
        long long inorder = (long long)INT_MIN - 1;  // 考虑起始节点的数值为INT_MIN的情况
        while (!stack.empty() || root != nullptr) {
            while (root != nullptr) {
                stack.push(root);
                root = root -> left;
            }
            root = stack.top();
            stack.pop();
            // 如果中序遍历得到的节点的值小于等于前一个 inorder，说明不是二叉搜索树
            if (root -> val <= inorder) {
                return false;
            }
            inorder = root -> val;
            root = root -> right;
        }
        return true;
    }
};
```

### 二叉搜索树中第 K 小的元素

[230. 二叉搜索树中第 K 小的元素（中等）](https://leetcode.cn/problems/kth-smallest-element-in-a-bst/)

问题：给定一个二叉搜索树的根节点 `root` ，和一个整数 `k` ，请你设计一个算法查找其中第 `k` 小的元素（从 1 开始计数）。

思路：中序遍历二叉树并计数。

复杂度分析：

-   时间复杂度：$O(H+k)$
-   空间复杂度：$O(H)$

实现：

```cpp
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        stack<TreeNode *> stack;
        while (root != nullptr || stack.size() > 0) {
            while (root != nullptr) {
                stack.push(root);
                root = root->left;
            }
            root = stack.top();
            stack.pop();
            --k;
            if (k == 0) {
                break;
            }
            root = root->right;
        }
        return root->val;
    }
};
```

### 二叉树的右视图

[199. 二叉树的右视图（中等）](https://leetcode.cn/problems/binary-tree-right-side-view/)

问题：给定一个二叉树的根节点`root`，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

思路：层序遍历二叉树，将每一层最后一个元素加入返回数组中。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        if (!root)
            return {};
        std::queue<TreeNode*> q;
        q.push(root);
        std::vector<int> ans;
        while (!q.empty()) {
            int curlen = q.size();
            for (int i = 0; i < curlen; ++i) {
                if (i == curlen - 1)
                    ans.push_back(q.front()->val);
                if (q.front()->left)
                    q.push(q.front()->left);
                if (q.front()->right)
                    q.push(q.front()->right);
                q.pop();
            }
        }
        return ans;
    }
};
```

### 二叉树展开为链表

[114. 二叉树展开为链表（中等）](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

问题：给你二叉树的根结点 `root` ，请你将它展开为一个单链表。展开后的单链表应该与二叉树先序遍历顺序相同，同样使用 `TreeNode` ，其中 `right` 子指针指向链表中下一个结点，而左子指针始终为 `null` 。

思路：前序遍历访问各节点的顺序是根左右。如果一个节点的左子节点为空，则该节点不需要进行展开操作。如果一个节点的左子节点不为空，则该节点的左子树中的最后一个节点被访问之后，该节点的右子节点被访问。该节点的左子树中最后一个被访问的节点是左子树中的最右边的节点，也是该节点的前驱节点。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    void flatten(TreeNode* root) {
        TreeNode *curr = root;
        while (curr != nullptr) {
            if (curr->left != nullptr) {
                auto next = curr->left;
                auto predecessor = next;
                while (predecessor->right != nullptr) {
                    predecessor = predecessor->right;
                }
                predecessor->right = curr->right;
                curr->left = nullptr;
                curr->right = next;
            }
            curr = curr->right;
        }
    }
};
```

### 从前序和中序遍历序列构造二叉树

[105. 从前序和中序遍历序列构造二叉树（中等）](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

问题：给定两个整数数组 `preorder` 和 `inorder` ，其中 `preorder` 是二叉树的先序遍历， `inorder` 是同一棵树的中序遍历，请构造二叉树并返回其根节点。

思路：递归。

实现：

```cpp
class Solution {
private:
    unordered_map<int, int> index;

    TreeNode* myBuildTree(const vector<int>& preorder, const vector<int>& inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right) {
        if (preorder_left > preorder_right) {
            return nullptr;
        }
        // 前序遍历中的第一个节点就是根节点
        int preorder_root = preorder_left;
        // 在中序遍历中定位根节点
        int inorder_root = index[preorder[preorder_root]];
        // 先把根节点建立出来
        TreeNode* root = new TreeNode(preorder[preorder_root]);
        // 得到左子树中的节点数目
        int size_left_subtree = inorder_root - inorder_left;
        // 递归地构造左子树，并连接到根节点
        // 先序遍历中「从 左边界+1 开始的 size_left_subtree」个元素就对应了中序遍历中「从 左边界 开始到 根节点定位-1」的元素
        root->left = myBuildTree(preorder, inorder, preorder_left + 1, preorder_left + size_left_subtree, inorder_left, inorder_root - 1);
        // 递归地构造右子树，并连接到根节点
        // 先序遍历中「从 左边界+1+左子树节点数目 开始到 右边界」的元素就对应了中序遍历中「从 根节点定位+1 到 右边界」的元素
        root->right = myBuildTree(preorder, inorder, preorder_left + size_left_subtree + 1, preorder_right, inorder_root + 1, inorder_right);
        return root;
    }

public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        // 构造哈希映射，帮助我们快速定位根节点
        for (int i = 0; i < n; ++i) {
            index[inorder[i]] = i;
        }
        return myBuildTree(preorder, inorder, 0, n - 1, 0, n - 1);
    }
};
```

思路：迭代。对于前序遍历中的任意两个连续节点 u 和 v，有两种可能的关系：

1. v 是 u 的左儿子。这是因为在遍历到 u 之后，下一个遍历的节点就是 u 的左儿子，即 v；
2. u 没有左儿子，并且 v 是 u 的某个祖先节点（或者 u 本身）的右儿子。

算法流程：

-   用一个栈和一个指针辅助进行二叉树的构造。初始时栈中存放了根节点（前序遍历的第一个节点），指针指向中序遍历的第一个节点；
-   枚举前序遍历中除了第一个节点以外的每个节点。如果 index 恰好指向栈顶节点，那么不断地弹出栈顶节点并向右移动 index，并将当前节点作为最后一个弹出的节点的右儿子；如果 index 和栈顶节点不同，将当前节点作为栈顶节点的左儿子；
-   无论是哪一种情况，最后都将当前的节点入栈。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (!preorder.size()) {
            return nullptr;
        }
        TreeNode* root = new TreeNode(preorder[0]);
        std::stack<TreeNode*> stk;
        stk.push(root);
        int inorderIdx = 0;
        for (int i = 1; i < preorder.size(); ++i) {
            int preorderVal = preorder[i];
            TreeNode* node = stk.top();
            if (node->val != inorder[inorderIdx]) {
                node->left = new TreeNode(preorderVal);
                stk.push(node->left);
            } else {
                while (!stk.empty() && stk.top()->val == inorder[inorderIdx]) {
                    node = stk.top();
                    stk.pop();
                    ++inorderIdx;
                }
                node->right = new TreeNode(preorderVal);
                stk.push(node->right);
            }
        }
        return root;
    }
};
```

### 路径总和 III

[437. 路径总和 III（中等）](https://leetcode.cn/problems/path-sum-iii/)

问题：给定一个二叉树的根节点 `root` ，和一个整数 `targetSum` ，求该二叉树里节点值之和等于 `targetSum` 的路径的数目。

思路：前缀和。定义节点的前缀和为由根节点到当前节点路径上所有节点的和。利用先序遍历二叉树，记录下根节点 root 到当前节点 p 的路径上除当前节点以外所有节点的前缀和，在已保存的路径前缀和中查找是否存在前缀和刚好等于当前节点到根节点的前缀和 curr 减去 targetSum。每个节点的过程是 ： 求当前节点结束的路径数量，本节点前缀和入表，递归左右，回溯（本节点前缀和出表）。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现(背诵)：

```cpp
class Solution {
public:
    int pathSum(TreeNode* root, int targetSum) {
        prefix[0] = 1;
        return dfs(root, 0, targetSum);
    }

private:
    std::unordered_map<long long, int> prefix;
    int dfs(TreeNode* root, long long curr, int targetSum) {
        if (!root) {
            return 0;
        }
        int ret = 0;
        curr += root->val;
        if (prefix.count(curr - targetSum)) {
            ret = prefix[curr - targetSum];
        }
        ++prefix[curr];
        ret += dfs(root->left, curr, targetSum);
        ret += dfs(root->right, curr, targetSum);
        --prefix[curr];
        return ret;
    }
};
```

### 二叉树的最近公共祖先

[236. 二叉树的最近公共祖先（中等）](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree)

问题：给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

思路：递归遍历二叉树，判断子树中是否包含 _p_ 节点或 _q_ 节点，如果包含为 `true`，否则为 `false`。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        dfs(root, p, q);
        return ans;
    }

private:
    TreeNode* ans;
    bool dfs(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (root == nullptr) {
            return false;
        }
        bool lson = dfs(root->left, p, q);
        bool rson = dfs(root->right, p, q);
        if ((lson && rson) || ((root->val == p->val || root->val == q->val) && (lson || rson))) {
            ans = root;  // 在找到最近公共祖先 x 以后，其他公共祖先只有一边子树为 true，不会被判定为符合条件。
        }
        return lson || rson || (root->val == p->val || root->val == q->val);
    }
};
```

### 二叉树中的最大路径和

[124. 二叉树中的最大路径和（困难）](https://leetcode.cn/problems/binary-tree-maximum-path-sum)

问题：给你一个二叉树的根节点 `root` ，返回其最大路径和。路径和是路径中各节点值的总和。

思路：递归遍历二叉树，记录左子树和右子树*向下*的最大路径和，用`左子树路径和 + 右子树路径和 + 根节点`更新最大值。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int maxPathSum(TreeNode* root) {
        dfs(root);
        return max_;
    }

private:
    int max_ = INT_MIN;
    int dfs(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }
        int left = max(0, dfs(root->left));
        int right = max(0, dfs(root->right));
        max_ = max(max_, left + right + root->val);
        return root->val + max(left, right);
    }
};
```

## 图论

### 岛屿数量

[200. 岛屿数量（中等）](https://leetcode.cn/problems/number-of-islands)

问题：给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

思路：深度优先搜索，将搜索过的网格标记为 '2' （也可以标记为“水”，当使用 bool 变量存储时有用）。

复杂度分析：

-   时间复杂度：$O(n*m)$
-   空间复杂度：$O(n*m)$

实现：

```cpp
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        n_ = grid.size();
        m_ = grid[0].size();
        grid_ = grid;
        int cnt = 0;
        for (int i = 0; i < n_; ++i) {
            for (int j = 0; j < m_; ++j) {
                if (grid_[i][j] == '1') {
                    ++cnt;
                    dfs(i, j);
                }
            }
        }
        return cnt;
    }

private:
    int n_, m_;
    vector<vector<char>> grid_;
    void dfs(int x, int y) {
        if (x < 0 || x >= n_ || y < 0 || y >= m_ || grid_[x][y] == '0' || grid_[x][y] == '2') {
            return;
        }
        grid_[x][y] = '2';
        dfs(x - 1, y);
        dfs(x + 1, y);
        dfs(x, y - 1);
        dfs(x, y + 1);
    }
};
```

### 腐烂的橘子

[994. 腐烂的橘子（中等）](https://leetcode.cn/problems/rotting-oranges)

问题：'0' 代表空单元格，'1' 代表新鲜橘子，'2' 代表腐烂的橘子。每分钟，腐烂的橘子周围 4 个方向上相邻的新鲜橘子都会腐烂。返回直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。

思路：广度优先搜索（BFS）。维护一个队列，每次将刚腐烂的橘子入队，不断遍历队列。

复杂度分析：

-   时间复杂度：$O(n*m)$
-   空间复杂度：$O(n*m)$

实现：

```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        std::queue<std::pair<int, int>> q;
        int count = 0;
        for (int i = 0; i < n; ++i)
            for (int j = 0; j < m; ++j)
                if (grid[i][j] == 1)
                    count++;
                else if (grid[i][j] == 2)
                    q.push({i, j});
        int round = 0;
        while (count > 0 && !q.empty()) {
            ++round;
            int size = q.size();
            for (int i = 0; i < size; ++i) {
                auto it = q.front();
                q.pop();
                int x = it.first, y = it.second;
                if (x > 0 && grid[x - 1][y] == 1) {
                    grid[x - 1][y] = 2;
                    --count;
                    q.push({x - 1, y});
                }
                if (x + 1 < n && grid[x + 1][y] == 1) {
                    grid[x + 1][y] = 2;
                    --count;
                    q.push({x + 1, y});
                }
                if (y > 0 && grid[x][y - 1] == 1) {
                    grid[x][y - 1] = 2;
                    --count;
                    q.push({x, y - 1});
                }
                if (y + 1 < m && grid[x][y + 1] == 1) {
                    grid[x][y + 1] = 2;
                    --count;
                    q.push({x, y + 1});
                }
            }
        }
        return count > 0 ? -1 : round;
    }
};
```

### 课程表

[207. 课程表（中等）](https://leetcode.cn/problems/course-schedule/)

问题：你这个学期必须选修 `numCourses` 门课程，记为 `0` 到 `numCourses - 1` ，在选修某些课程之前需要一些先修课程。请你判断是否可能完成所有课程的学习？

思路：使用 BFS 寻找一个拓扑排序。当我们将一个节点加入答案中后，就可以移除它的所有出边，代表着它的相邻节点少了一门先修课程的要求。如果某个相邻节点变成了「没有任何入边的节点」，那么就代表着这门课可以开始学习了。按照这样的流程，不断地将没有入边的节点加入答案，直到答案中包含所有的节点（得到了一种拓扑排序）或者不存在没有入边的节点（图中包含环）。

复杂度分析：

-   时间复杂度：$O(n+m)$
-   空间复杂度：$O(n+m)$

实现：

```cpp
class Solution {
private:
    vector<vector<int>> edges;
    vector<int> indeg;

public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        edges.resize(numCourses);
        indeg.resize(numCourses);
        for (const auto& info: prerequisites) {
            edges[info[1]].push_back(info[0]);
            ++indeg[info[0]];
        }

        queue<int> q;
        for (int i = 0; i < numCourses; ++i) {
            if (indeg[i] == 0) {
                q.push(i);
            }
        }

        int visited = 0;
        while (!q.empty()) {
            ++visited;
            int u = q.front();
            q.pop();
            for (int v: edges[u]) {
                if (--indeg[v] == 0) {
                    q.push(v);
                }
            }
        }

        return visited == numCourses;
    }
};
```

### 实现前缀树

[208. 实现前缀树（中等）](https://leetcode.cn/problems/implement-trie-prefix-tree/)

问题：Trie（发音类似 "try"）或者说前缀树是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补全和拼写检查。

思路：构建前缀树（字典树），其每个节点包含指向子节点的指针数组`children`和判断字符串结尾的布尔字段`isEnd`。插入时，遍历字符串的每一个字符，从根节点出发沿着指针移动到子节点，如果子节点不存在就创建，结束时将末尾节点的`isEnd`字段置为`true`。查找时，同样遍历字符串，沿着指针移动，若访问到空指针则返回，否则返回末尾节点。

复杂度分析：

-   时间复杂度：初始化为$O(1)$，其余操作为$O(|S|)$。
-   空间复杂度：$O(|T|⋅Σ)$，其中 |T| 为所有插入字符串的长度之和，Σ 为字符集的大小，本题 Σ=26。

实现：

```cpp
class Trie {
private:
    vector<Trie*> children;
    bool isEnd;

    Trie* searchPrefix(string prefix) {
        Trie* node = this;
        for (char ch : prefix) {
            ch -= 'a';
            if (node->children[ch] == nullptr) {
                return nullptr;
            }
            node = node->children[ch];
        }
        return node;
    }

public:
    Trie() : children(26), isEnd(false) {}

    void insert(string word) {
        Trie* node = this;
        for (char ch : word) {
            ch -= 'a';
            if (node->children[ch] == nullptr) {
                node->children[ch] = new Trie();
            }
            node = node->children[ch];
        }
        node->isEnd = true;
    }

    bool search(string word) {
        Trie* node = this->searchPrefix(word);
        return node != nullptr && node->isEnd;
    }

    bool startsWith(string prefix) {
        return this->searchPrefix(prefix) != nullptr;
    }
};
```

## 回溯

回溯法模板：

```cpp
void backtrack(params) {
    if(终止条件) {
        存放结果;
        return;
    }
    for(选择本层集合中元素) {
        处理节点;
        backtrack(路径, 选择列表);
        回溯;
    }
}
```

### 全排列

[46. 全排列（中等）](https://leetcode.cn/problems/permutations)

问题：给定一个不含重复数字的数组 `nums` ，返回其所有可能的全排列。

思路：回溯。定义递归函数 `backtrack(first, output)` 表示从左往右填到第 first 个位置，当前排列为 output。循环将第 i 个数和 firrst 交换，填完下一个数后交换回来完成回溯。

复杂度分析：

-   时间复杂度：$O(n×n!)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    void backtrack(vector<vector<int>>& res, vector<int>& output, int first, int len){
        // 所有数都填完了
        if (first == len) {
            res.emplace_back(output);
            return;
        }
        for (int i = first; i < len; ++i) {
            // 动态维护数组
            swap(output[i], output[first]);
            // 继续递归填下一个数
            backtrack(res, output, first + 1, len);
            // 撤销操作
            swap(output[i], output[first]);
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int> > res;
        backtrack(res, nums, 0, (int)nums.size());
        return res;
    }
};
```

### 子集

[78. 子集（中等）](https://leetcode.cn/problems/subsets)

问题：给你一个整数数组 `nums` ，数组中的元素互不相同。返回该数组所有可能的子集（幂集）。

思路：回溯。

复杂度分析：

-   时间复杂度：$O(n×2^n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    vector<vector<int>> ans;
    void backtrack(vector<int>& nums, vector<int>& cur, int index){
        ans.push_back(cur);
        for (int i = index; i < nums.size(); i++){
            cur.push_back(nums[i]);
            backtrack(nums, cur, i + 1);
            cur.pop_back();
        }
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<int> cur;
        backtrack(nums, cur, 0);
        return ans;
    }
};
```

### 电话号码的字母组合

[17. 电话号码的字母组合（中等）](https://leetcode.cn/problems/letter-combinations-of-a-phone-number)

问题：给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按任意顺序返回。

思路：回溯。

复杂度分析：

-   时间复杂度：$O(3^m×4^n)$
-   空间复杂度：$O(m+n)$

实现：

```cpp
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        if (digits.length() == 0) {
            return {};
        }
        string cur{};
        backtrack(digits, cur, 0);
        return res;
    }

private:
    vector<string> res;
    vector<string> strs = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    void backtrack(string& digits, string& cur, int idx) {
        if (idx == digits.length()) {
            res.emplace_back(cur);
            return;
        }
        // 遍历当前字符串的每一个字符
        string str = strs[digits[idx] - '0'];
        for (int i = 0; i < str.length(); ++i) {
            cur.push_back(str[i]);
            // 进入下一层
            backtrack(digits, cur, idx + 1);
            // 回溯
            cur.pop_back();
        }
    }
};
```

### 组合总和

[39. 组合总和（中等）](https://leetcode.cn/problems/combination-sum)

问题：给你一个无重复元素的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有不同组合，并以列表形式返回。你可以按任意顺序返回这些组合。

思路：回溯。

复杂度分析：

-   时间复杂度：$O(S)$，其中 S 为所有可行解的长度之和。
-   空间复杂度：$O(target)$

实现：

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end(),
             [](int a, int b) { return a > b; });
        backtrack(candidates, {}, 0, target);
        return res;
    }

private:
    vector<vector<int>> res;
    void backtrack(vector<int>& candidates, vector<int> cur, int idx,
                   int target) {
        if (target < 0) { return; }
        if (target == 0) {
            res.emplace_back(cur);
            return;
        }
        for (int i = idx; i < candidates.size(); ++i) {
            cur.push_back(candidates[i]);
            backtrack(candidates, cur, i, target - candidates[i]);
            cur.pop_back();
        }
    }
};
```

### 括号生成

[22. 括号生成（中等）](https://leetcode.cn/problems/generate-parentheses)

问题：数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且有效的括号组合。

思路：回溯。如果左括号数量不大于 n ，可以放一个左括号。如果右括号数量小于左括号的数量，可以放一个右括号。

复杂度分析：

-   时间复杂度：$O(\frac{4^n}{\sqrt{n}})$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
    void backtrack(vector<string>& ans, string& cur, int open, int close, int n) {
        if (cur.size() == n * 2) {
            ans.push_back(cur);
            return;
        }
        if (open < n) {
            cur.push_back('(');
            backtrack(ans, cur, open + 1, close, n);
            cur.pop_back();
        }
        if (close < open) {
            cur.push_back(')');
            backtrack(ans, cur, open, close + 1, n);
            cur.pop_back();
        }
    }
public:
    vector<string> generateParenthesis(int n) {
        vector<string> result;
        string current;
        backtrack(result, current, 0, 0, n);
        return result;
    }
};
```

### 单词搜索

[79. 单词搜索（中等）](https://leetcode.cn/problems/word-search)

问题：给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

思路：回溯。使用深度优先搜索（DFS）+ 剪枝，标记已访问的节点为空，防止重复访问，回溯时还原该节点。

复杂度分析：

-   时间复杂度：$O(3^KMN)$，M, N 分别为矩阵行列大小，K 为字符串 `word` 长度。
-   空间复杂度：$O(K)$

实现：

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        rows = board.size();
        cols = board[0].size();
        for(int i = 0; i < rows; i++) {
            for(int j = 0; j < cols; j++) {
                if (dfs(board, word, i, j, 0)) return true;
            }
        }
        return false;
    }
private:
    int rows, cols;
    bool dfs(vector<vector<char>>& board, string word, int i, int j, int k) {
        if (i >= rows || i < 0 || j >= cols || j < 0 || board[i][j] != word[k]) return false;
        if (k == word.size() - 1) return true;
        board[i][j] = '\0';
        bool res = dfs(board, word, i + 1, j, k + 1) || dfs(board, word, i - 1, j, k + 1) ||
                      dfs(board, word, i, j + 1, k + 1) || dfs(board, word, i , j - 1, k + 1);
        board[i][j] = word[k];
        return res;
    }
};
```

### 分割回文串

[131. 分割回文串（中等）](https://leetcode.cn/problems/palindrome-partitioning)

问题：给你一个字符串 `s`，请你将 `s` 分割成一些子串，使每个子串都是回文串。返回 `s` 所有可能的分割方案。

思路：回溯。用指针逐步分割子串，如果是回文串就加入当前集，如果指针走完字符串就将当前集添加至结果中。

复杂度分析：

-   时间复杂度：$O(n \cdot 2^n)$
-   空间复杂度：$O(n^2)$

实现：

```cpp
class Solution {
public:
    vector<vector<string>> partition(string s) {
        vector<string> cur;
        backtrack(s, cur, 0);
        return res;
    }

private:
    vector<vector<string>> res;
    bool isPalindrome(string& s) {
        int n = s.size();
        for (int i = 0; i < n / 2; ++i) {
            if (s[i] != s[n - i - 1]) { return false; }
        }
        return true;
    }
    void backtrack(string& s, vector<string>& cur, int idx) {
        if (idx == s.size()) {
            res.emplace_back(cur);
            return;
        }
        for (int i = 1; i <= s.size() - idx; ++i) {
            string substr = s.substr(idx, i);
            if (isPalindrome(substr)) {
                cur.emplace_back(substr);
                backtrack(s, cur, idx + i);
                cur.pop_back();
            }
        }
    }
};
```

优化：预处理每个可能出现的字符串是否为回文串，存储在`IsPalindrom[i][j]`数组中，减少重复判断回文串。

### N 皇后

[51. N 皇后（困难）](https://leetcode.cn/problems/n-queens)

问题：n 皇后问题研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

思路：回溯。从左上方开始，在当前行的每一列尝试放置皇后，放置一个皇后后递归进入下一行，回溯时拿掉当前行的皇后返回上一行。

复杂度分析：

-   时间复杂度：$O(n!)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> res;
        vector<string> cur(n, string(n, '.'));
        backtrack(res, cur, n, 0);
        return res;
    }

private:
    void backtrack(vector<vector<string>>& res, vector<string>& cur, int n, int k) {
        if (k == n) {
            res.emplace_back(cur);
            return;
        }
        for (int i = 0; i < n; ++i) {
            if (!isValid(cur, k, i, n))
                continue;
            cur[k][i] = 'Q';
            backtrack(res, cur, n, k + 1);  // 进入下一行
            cur[k][i] = '.';
        }
    }
    bool isValid(vector<string>& board, int x, int y, int n) {
        for (int i = 0; i < n; ++i)
            if (board[i][y] == 'Q')  // 上方有皇后
                return false;
        for (int i = x - 1, j = y - 1; i >= 0 && j >= 0; --i, --j)
            if (board[i][j] == 'Q')  // 左上方有皇后
                return false;
        for (int i = x - 1, j = y + 1; i >= 0 && j < n; --i, ++j)
            if (board[i][j] == 'Q')  // 右上方有皇后
                return false;
        return true;  // 剪枝：根据放置皇后的顺序，不需要判断左、下、左下、右下是否存在皇后
    }
};
```
