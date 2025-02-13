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

### 单词规律

[290. 单词规律（简单）](https://leetcode.cn/problems/word-pattern/)

问题：给定一种规律 `pattern` 和一个字符串 `s` ，判断 `s` 是否遵循相同的规律。

思路：在集合论中，这种关系被称为「双射」。可以利用哈希表记录每一个字符对应的字符串，以及每一个字符串对应的字符。

_提示：使用 `istringstream` 将字符串转为流，可以提取出空格分割的单词。_

复杂度分析：

-   时间复杂度：$O(m+n)$
-   空间复杂度：$O(m+n)$

实现：

```cpp
class Solution {
public:
    bool wordPattern(string pattern, string s) {
        unordered_map<char, string> ctw;
        unordered_map<string, char> wtc;
        istringstream iss(s);
        string word;
        for (char c : pattern) {
            if (!(iss >> word) || (ctw.count(c) && ctw[c] != word) ||
                (wtc.count(word) && wtc[word] != c))
                return false;
            ctw[c] = word;
            wtc[word] = c;
        }
        return !(iss >> word);
    }
};
```

### O(1) 时间插入、删除和获取随机元素

[380. O(1) 时间插入、删除和获取随机元素（中等）](https://leetcode.cn/problems/insert-delete-getrandom-o1/)

问题：实现一个类，能 O(1) 时间插入、删除和获取随机元素。

思路：哈希表 + 变长数组。哈希表存储元素在数组中的下标，删除元素时，将最后一个元素移动到要删除的元素位置，然后删除数组的最后一个元素。

复杂度分析：

-   时间复杂度：$O(1)$
-   空间复杂度：$O(n)$

实现：

```cpp
class RandomizedSet {
public:
    RandomizedSet() {
        srand((unsigned)time(NULL));
    }

    bool insert(int val) {
        if (indices.count(val)) {
            return false;
        }
        int index = nums.size();
        nums.emplace_back(val);
        indices[val] = index;
        return true;
    }

    bool remove(int val) {
        if (!indices.count(val)) {
            return false;
        }
        int index = indices[val];
        int last = nums.back();
        nums[index] = last;
        indices[last] = index;
        nums.pop_back();
        indices.erase(val);
        return true;
    }

    int getRandom() {
        int randomIndex = rand()%nums.size();
        return nums[randomIndex];
    }
private:
    vector<int> nums;
    unordered_map<int, int> indices;
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

### 颜色分类

[75. 颜色分类（中等）](https://leetcode.cn/problems/sort-colors/)

问题：给定一个包含红色、白色和蓝色、共 `n` 个元素的数组 `nums` ，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

思路：双指针。遍历数组，将`0`交换到左边，将`2`交换到右边。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int p = 0, q = nums.size() - 1;
        for (int i = 0; i <= q; ++i) {
            if (nums[i] == 0) {
                swap(nums[i], nums[p++]);  // 剪枝：已经遍历过的元素是有序的，不可能存在2
            }
            if (nums[i] == 2) {
                swap(nums[i], nums[q--]);
                if (nums[i] != 1)
                    --i;  // 考虑从后面交换来的元素可能为0或2，需要重新判断
            }
        }
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

实现（背诵）：

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

### 合并两个有序数组

[88. 合并两个有序数组（简单）](https://leetcode.cn/problems/merge-sorted-array/)

问题：给你两个按非递减顺序排列的整数数组 `nums1` 和 `nums2`，另有两个整数 `m` 和 `n` ，分别表示 `nums1` 和 `nums2` 中的元素数目。请你合并`nums2` 到 `nums1` 中，使合并后的数组同样按非递减顺序排列。

思路：逆向双指针。从后往前更新数组，避免了覆盖原数组中前面的元素。

复杂度分析：

-   时间复杂度：$O(m+n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int p1 = m - 1, p2 = n - 1, i = m + n - 1;
        while (p1 >= 0 && p2 >= 0) {
            nums1[i--] = nums1[p1] > nums2[p2] ? nums1[p1--] : nums2[p2--];
        }
        while (p2 >= 0) {
            nums1[i--] = nums2[p2--];
        }
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

### 串联所有单词的子串

[30. 串联所有单词的子串（困难）](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)

问题：给定一个字符串 `s` 和一个字符串数组 `words`**。** `words` 中所有字符串长度相同。返回所有串联子串在 `s` 中的开始索引。

思路：滑动窗口。

复杂度分析：

-   时间复杂度：$O(l*n)$
-   空间复杂度：$O(m*n)$

实现：

```cpp
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        int wordSize = words[0].size();  //单词大小
        int wordNum = words.size();  //单词数量
        vector<int> res;
        for (int i = 0; i < wordSize && i + wordSize * wordNum <= s.size();i++)
        {
            int left = i;  //初始化滑动窗口左边界
            int right = i + wordSize * wordNum;  //右边界
            unordered_map<string, int> wordsMap;  //保存当前窗口与words里单词的次数关系
            //初始化滑动窗口内单词计数
            for (int j = left; j < right; j += wordSize)
                wordsMap[s.substr(j, wordSize)]++;
            //减去words里出现的次数
            for (string& word : words)
            {
                wordsMap[word]--;
                if (wordsMap[word] == 0)
                    wordsMap.erase(word);
            }
            //判断初始状态是否满足
            if (wordsMap.empty())
                res.push_back(left);
            while (right < s.size())
            {
                //滑动一次，从左边界开始的一个单词需要删除，右边界开始的单词需要加入
                const string& deleteWord = s.substr(left, wordSize);
                const string& joinWord = s.substr(right, wordSize);
                if (--wordsMap[deleteWord] == 0)
                    wordsMap.erase(deleteWord);
                if (++wordsMap[joinWord] == 0)
                    wordsMap.erase(joinWord);

                //滑动之后更新边界，并判断是否满足
                right += wordSize;
                left += wordSize;
                if (wordsMap.empty())
                    res.push_back(left);
            }
        }
        return res;
    }
};
```

### 长度最小的子数组

[209. 长度最小的子数组（中等）](https://leetcode.cn/problems/minimum-size-subarray-sum/)

问题：给定一个含有 `n` 个正整数的数组和一个正整数 `target` 。找出该数组中满足其总和大于等于 `target` 的长度最小的子数组`[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。

思路：使用滑动窗口遍历字符串。当窗口中累加和大于等于 `target` 时，右移窗口左边界并更新最小窗口大小；否则右移窗口右边界。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int n = nums.size();
        if (n == 0) {
            return 0;
        }
        int ans = INT_MAX;
        int start = 0, end = 0;
        int sum = 0;
        while (end < n) {
            sum += nums[end];
            while (sum >= s) {
                ans = min(ans, end - start + 1);
                sum -= nums[start];
                start++;
            }
            end++;
        }
        return ans == INT_MAX ? 0 : ans;
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

### 找出字符串中第一个匹配项的下标

[28. 找出字符串中第一个匹配项的下标（简单）](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

问题：给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 0 开始）。如果 `needle` 不是 `haystack` 的一部分，则返回 `-1` 。

思路：[KMP 算法](https://blog.csdn.net/v_july_v/article/details/7041827)。构建一个被称为部分匹配表(Partial Match Table)的数组，PMT 中的值是字符串的前缀集合与后缀集合的交集中最长元素的长度。为了编程的方便， 我们不直接使用 PMT 数组，而是将 PMT 数组向后偏移一位，把新得到的这个数组称为 next 数组。利用部分匹配的原理，失配时只需要将指针左移 next[j] 位即可。关键在于 next 数组的求法。

复杂度分析：

-   时间复杂度：$O(m+n)$
-   空间复杂度：$O(m)$

实现（背诵）：

```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.size(), m = needle.size();
        vector<int> next(m);
        for (int i = 1, j = 0; i < m; ++i) {
            while (j > 0 && needle[i] != needle[j])
                j = next[j - 1];
            if (needle[i] == needle[j])
                ++j;
            next[i] = j;
        }
        for (int i = 0, j = 0; i < n; ++i) {
            while (j > 0 && haystack[i] != needle[j])
                j = next[j - 1];
            if (haystack[i] == needle[j])
                ++j;
            if (j == m)
                return i - m + 1;
        }
        return -1;
    }
};
```

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

### 环形子数组的最大和

[918. 环形子数组的最大和（中等）](https://leetcode.cn/problems/maximum-sum-circular-subarray/)

问题：给定一个长度为 `n` 的**环形整数数组** `nums` ，返回`nums` 的非空 **子数组** 的最大可能和。

思路：考虑两种情况：

1. 子数组没有越过环形边界，此时只需计算最大子数组和`max_s`；
2. 子数组越过了环形边界，此时子数组在数组的两边，中间的子数组和越小，两边的和就越大，等价于`sum - min_s`。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int maxSubarraySumCircular(vector<int>& nums) {
        int max_s = INT_MIN, max_f = INT_MIN, min_s = INT_MAX, min_f = INT_MAX,
            sum = 0;
        for (auto& num : nums) {
            max_f = max(max_f, 0) + num;
            max_s = max(max_s, max_f);
            min_f = min(min_f, 0) + num;
            min_s = min(min_s, min_f);
            sum += num;
        }
        // 考虑题目不允许空数组的特殊情况
        return min_s == sum ? max_s : max(max_s, sum - min_s);
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

### 旋转链表

[61. 旋转链表（中等）](https://leetcode.cn/problems/rotate-list/)

问题：给你一个链表的头节点 `head` ，旋转链表，将链表每个节点向右移动 `k` 个位置。

思路：先将给定的链表连接成环，然后将指定位置断开。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        if (k == 0 || head == nullptr || head->next == nullptr) {
            return head;
        }
        ListNode *p = head;
        int length = 1;
        while (p->next) {
            ++length;
            p = p->next;
        }
        int step = length - k % length;
        if (step == length) {
            return head;
        }
        p->next = head;
        for (int i = 1; i < step; ++i) {
            head = head->next;
        }
        ListNode* pre = head;
        head = head->next;
        pre->next = nullptr;
        return head;
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
        cur->next = p ? p : q;
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

### 完全二叉树的节点个数

[222. 完全二叉树的节点个数（简单）](https://leetcode.cn/problems/count-complete-tree-nodes/)

问题：给你一棵完全二叉树的根节点 `root` ，求出该树的节点个数。

思路：二分查找 + 位运算。对于最大层数为 h 的完全二叉树，节点个数一定在 $[2^h,2^(h+1)−1]$ 的范围内，可以在该范围内通过二分查找的方式得到完全二叉树的节点个数。如何判断第 k 个节点是否存在呢？如果第 k 个节点位于第 h 层，则 k 的二进制表示包含 h+1 位，其中最高位是 1，其余各位从高到低表示从根节点到第 k 个节点的路径，0 表示移动到左子节点，1 表示移动到右子节点。通过位运算得到第 k 个节点对应的路径，判断该路径对应的节点是否存在，即可判断第 k 个节点是否存在。

复杂度分析：

-   时间复杂度：$O(\logn^2)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int countNodes(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }
        int level = 0;
        TreeNode* node = root;
        while (node->left != nullptr) {
            level++;
            node = node->left;
        }
        int low = 1 << level, high = (1 << (level + 1)) - 1;
        while (low < high) {
            int mid = (high - low + 1) / 2 + low;
            if (exists(root, level, mid)) {
                low = mid;
            } else {
                high = mid - 1;
            }
        }
        return low;
    }

    bool exists(TreeNode* root, int level, int k) {
        int bits = 1 << (level - 1);
        TreeNode* node = root;
        while (node != nullptr && bits > 0) {
            if (!(bits & k)) {
                node = node->left;
            } else {
                node = node->right;
            }
            bits >>= 1;
        }
        return node != nullptr;
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

### 填充每个节点的下一个右侧节点指针 II

[117. 填充每个节点的下一个右侧节点指针 II（中等）](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node-ii/)

问题：给定一个二叉树，填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL` 。

思路：考虑使用队列进行层序遍历的方法。一旦在某层的节点之间建立了 next 指针，那这层节点实际上形成了一个链表。因此，如果先去建立某一层的 next 指针，再去遍历这一层，就无需再使用队列了。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    Node* connect(Node* root) {
        if (!root) {
            return nullptr;
        }
        Node *head = root, *dummy = new Node(), *cur;
        while (head) {
            dummy->next = nullptr, cur = dummy;
            for (Node* p = head; p; p = p->next) {
                if (p->left) {
                    cur->next = p->left;
                    cur = cur->next;
                }
                if (p->right) {
                    cur->next = p->right;
                    cur = cur->next;
                }
            }
            head = dummy->next;
        }
        delete (dummy);
        return root;
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

### 从中序与后序遍历序列构造二叉树

[106. 从中序与后序遍历序列构造二叉树（中等）](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

问题：给定两个整数数组 `inorder` 和 `postorder` ，其中 `inorder` 是二叉树的中序遍历， `postorder` 是同一棵树的后序遍历，请你构造并返回这颗二叉树。

思路：根据后序遍历逻辑，递归创建右子树和左子树。建立一个（元素，下标）键值对的哈希表，根据根节点的下标将中序数组分为左子树和右子树两部分。注意这里有需要先创建右子树，再创建左子树的依赖关系。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        post_idx = postorder.size() - 1;
        for (int i = 0; i < inorder.size(); ++i) {
            idx_map[inorder[i]] = i;
        }
        return buildNode(0, post_idx, inorder, postorder);
    }

private:
    int post_idx;
    unordered_map<int, int> idx_map;
    TreeNode* buildNode(int in_left, int in_right, vector<int>& inorder,
                        vector<int>& postorder) {
        if (in_left > in_right) {
            return nullptr;
        }
        int root_val = postorder[post_idx];
        TreeNode* root = new TreeNode(root_val);
        int index = idx_map[root_val];
        --post_idx;
        // 先创建右子树，再创建左子树
        root->right = buildNode(index + 1, in_right, inorder, postorder);
        root->left = buildNode(in_left, index - 1, inorder, postorder);
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

拓扑排序：课程表

并查集：除法求值

### 克隆图

[133. 克隆图（中等）](https://leetcode.cn/problems/clone-graph/)

问题：给你无向连通图中一个节点的引用，请你返回该图的深拷贝（克隆）。

思路：深度优先搜索，用哈希表记录所有已被访问和克隆的节点，防止死循环。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
    unordered_map<Node*, Node*> visited;

public:
    Node* cloneGraph(Node* node) {
        if (!node) {
            return nullptr;
        }
        if (visited.find(node) != visited.end()) {
            return visited[node];
        }
        // 克隆节点并用哈希表存储
        Node* cloneNode = new Node(node->val);
        visited[node] = cloneNode;
        // 遍历该节点的邻居并更新克隆节点的邻居列表
        for (Node* neighbor : node->neighbors) {
            cloneNode->neighbors.emplace_back(cloneGraph(neighbor));
        }
        return cloneNode;
    }
};
```

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

### 除法求值

[399. 除法求值（中等）](https://leetcode.cn/problems/evaluate-division/)

问题：给你一个变量对数组 `equations` 和一个实数值数组 `values` 作为已知条件，其中 `equations[i] = [Ai, Bi]` 和 `values[i]` 共同表示等式 `Ai / Bi = values[i]` 。每个 `Ai` 或 `Bi` 是一个表示单个变量的字符串。另有一些以数组 `queries` 表示的问题，其中 `queries[j] = [Cj, Dj]` 表示第 `j` 个问题，请你根据已知条件找出 `Cj / Dj = ?` 的结果作为答案。

思路：带权并查集。

复杂度分析：

-   时间复杂度：$O(n*m)$
-   空间复杂度：$O(n*m)$

实现：

```cpp
class Solution {
    int findf(vector<int>& f, vector<double>& w, int x) {
        if (f[x] != x) {
            int father = findf(f, w, f[x]);
            w[x] = w[x] * w[f[x]];
            f[x] = father;
        }
        return f[x];
    }
    void merge(vector<int>& f, vector<double>& w, int x, int y, double val) {
        int fx = findf(f, w, x);
        int fy = findf(f, w, y);
        f[fx] = fy;
        w[fx] = val * w[y] / w[x];
    }

public:
    vector<double> calcEquation(vector<vector<string>>& equations,
                                vector<double>& values,
                                vector<vector<string>>& queries) {
        int nvars = 0;
        unordered_map<string, int> variables;

        int n = equations.size();
        for (int i = 0; i < n; ++i) {
            if (variables.count(equations[i][0])) {
                variables[equations[i][0]] = nvars++;
            }
            if (variables.count(equations[i][1])) {
                variables[equations[i][1]] = nvars++;
            }
        }
        vector<int> f(nvars);
        vector<double> w(nvars, 1.0);
        for (int i = 0; i < nvars; ++i) {
            f[i] = i;
        }
        for (int i = 0; i < n; ++i) {
            int va = variables[equations[i][0]],
                vb = variables[equations[i][1]];
            merge(f, w, va, vb, values[i]);
        }
        vector<double> ret;
        for (const auto& q : queries) {
            double result = -1.0;
            if (variables.count(q[0]) && variables.count(q[1])) {
                int ia = variables[q[0]], ib = variables[q[1]];
                int fa = findf(f, w, ia), fb = findf(f, w, ib);
                if (fa == fb) {
                    result = w[ia] / w[ib];
                }
            }
            ret.push_back(result);
        }
        return ret;
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

## 二分查找

### 搜索插入位置

[35. 搜索插入位置（简单）](https://leetcode.cn/problems/search-insert-position)

问题：给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

思路：二分查找。如果没有找到，返回 `left` （找规律）。

复杂度分析：

-   时间复杂度：$O(\log n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] == target)
                return mid;
            else if (nums[mid] < target)
                left = mid + 1;
            else
                right = mid - 1;
        }
        return left;
    }
};
```

### 搜索二维矩阵

[74. 搜索二维矩阵（中等）](https://leetcode.cn/problems/search-a-2d-matrix)

问题：给你一个整数 `target` ，如果 `target` 在矩阵中，返回 `true` ；否则，返回 `false` 。每行中的整数从左到右按非严格递增顺序排列，每行的第一个整数大于前一行的最后一个整数。

思路：二分查找。先查找 target 可能存在的行，在进入该行搜索对应的列。

复杂度分析：

-   时间复杂度：$O(\log n + \log m)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>> matrix, int target) {
        auto row = upper_bound(matrix.begin(), matrix.end(), target, [](const int b, const vector<int> &a) {
            return b < a[0];
        });
        if (row == matrix.begin()) {
            return false;
        }
        --row;
        return binary_search(row->begin(), row->end(), target);
    }
};
```

### 在排序数组中查找元素的第一个和最后一个位置

[34. 在排序数组中查找元素的第一个和最后一个位置（中等）](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

问题：给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

思路：二分查找。寻找`target`的开始位置，结束位置即为`target+1`的开始位置-1。

复杂度分析：

-   时间复杂度：$O(\log n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
    // lower_bound 返回最小的满足 nums[i] >= target 的 i
    // 如果数组为空，或者所有数都 < target，则返回 nums.size()
    int lower_bound(vector<int> &nums, int target) {
        int left = 0, right = (int) nums.size() - 1;
        while (left <= right) {
            // 循环不变量：
            // nums[left-1] < target
            // nums[right+1] >= target
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1; // 范围缩小到 [mid+1, right]
            } else {
                right = mid - 1; // 范围缩小到 [left, mid-1]
            }
        }
        return left;
    }

public:
    vector<int> searchRange(vector<int> &nums, int target) {
        int start = lower_bound(nums, target);
        if (start == nums.size() || nums[start] != target) {
            return {-1, -1};
        }
        int end = lower_bound(nums, target + 1) - 1;
        return {start, end};
    }
};
```

### 搜索旋转排列数组

[33. 搜索旋转排序数组（中等）](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

问题：整数数组 `nums` 按升序排列，数组中的值互不相同。在传递给函数之前，`nums` 在预先未知的某个下标 `k`（`0 <= k < nums.length`）上进行了旋转，使数组变为 `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]`（下标从 0 开始计数）。例如， `[0,1,2,4,5,6,7]` 在下标 `3` 处经旋转后可能变为 `[4,5,6,7,0,1,2]` 。给你旋转后的数组 `nums` 和一个整数 `target` ，如果 `nums` 中存在这个目标值 `target` ，则返回它的下标，否则返回 `-1` 。

思路：二分查找。每次查看以当前 `mid` 为界分割出来的两个部分，`[l, mid]`和`[mid+1, r]`中至少有一个是有序的。通过有序的那部分的左右边界，可以判断出`target`是否在那部分中。

复杂度分析：

-   时间复杂度：$O(\log n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = (int)nums.size();
        if (!n) {
            return -1;
        }
        if (n == 1) {
            return nums[0] == target ? 0 : -1;
        }
        int l = 0, r = n - 1;
        while (l <= r) {
            int mid = (l + r) / 2;
            if (nums[mid] == target) return mid;
            if (nums[0] <= nums[mid]) {
                if (nums[0] <= target && target < nums[mid]) {
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            } else {
                if (nums[mid] < target && target <= nums[n - 1]) {
                    l = mid + 1;
                } else {
                    r = mid - 1;
                }
            }
        }
        return -1;
    }
};
```

### 寻找旋转排序数组中的最小值

[153. 寻找旋转排序数组中的最小值（中等）](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

问题：给你一个元素值互不相同的数组 `nums` ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的最小元素 。

思路：二分查找。如果`nums[mid] < nums[right]`，那么最小值就在区间`[left, mid]`内；如果`nums[mid] >= nums[right]`，那么最小值一定在`(mid, right]`中。当`left == right`，此时最小值就是`nums[left]`。

复杂度分析：

-   时间复杂度：$O(\log n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0, right = nums.size() - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < nums[right])
                right = mid;  // 注意边界条件
            else
                left = mid + 1;
        }
        return nums[left];
    }
};
```

### 寻找两个正序数组的中位数

[4. 寻找两个正序数组的中位数（困难）](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

问题：给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的中位数。

思路：将问题转化为找到两个数组中第 k 小的数字。要找到第 k (k>1) 小的元素，那么就取 pivot1 = nums1[k/2-1] 和 pivot2 = nums2[k/2-1] 进行比较。nums1 中小于等于 pivot1 的元素有 k/2-1 个，nums2 中小于等于 pivot2 的元素有 k/2-1 个。取 pivot = min(pivot1, pivot2)，两个数组中小于等于 pivot 的元素共计不会超过 (k/2-1) + (k/2-1) <= k-2 个，因此较小的那一边的左边不可能是第 k 小的元素，左边界可以右移。

复杂度分析：

-   时间复杂度：$O(\log {m+n})$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int totalLength = nums1.size() + nums2.size();
        if (totalLength % 2 == 1) {
            return getKthElement(nums1, nums2, (totalLength + 1) / 2);
        } else {
            return (getKthElement(nums1, nums2, totalLength / 2) +
                    getKthElement(nums1, nums2, totalLength / 2 + 1)) /
                   2.0;
        }
    }

private:
    int getKthElement(const vector<int>& nums1, const vector<int>& nums2,
                      int k) {
        int m = nums1.size(), n = nums2.size(), offset1 = 0, offset2 = 0;
        while (true) {
            if (offset1 == m) {
                return nums2[offset2 + k - 1];
            }
            if (offset2 == n) {
                return nums1[offset1 + k - 1];
            }
            if (k == 1) {
                return min(nums1[offset1], nums2[offset2]);
            }
            int mid1 = min(offset1 + k / 2 - 1, m - 1),
                mid2 = min(offset2 + k / 2 - 1, n - 1);
            if (nums1[mid1] <= nums2[mid2]) {
                k -= mid1 - offset1 + 1;
                offset1 = mid1 + 1;
            } else {
                k -= mid2 - offset2 + 1;
                offset2 = mid2 + 1;
            }
        }
    }
};
```

## 栈

### 有效的括号

[20. 有效的括号（简单）](https://leetcode.cn/problems/valid-parentheses/)

问题：给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

思路：用栈解决。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n+|\Sigma|)$

实现：

```cpp
class Solution {
public:
    bool isValid(string s) {
        int n = s.size();
        if (n % 2 == 1) {
            return false;
        }

        unordered_map<char, char> pairs = {
            {')', '('},
            {']', '['},
            {'}', '{'}
        };
        stack<char> stk;
        for (char ch: s) {
            if (pairs.count(ch)) {
                if (stk.empty() || stk.top() != pairs[ch]) {
                    return false;
                }
                stk.pop();
            }
            else {
                stk.push(ch);
            }
        }
        return stk.empty();
    }
};
```

### 简化路径

[71. 简化路径（中等）](https://leetcode.cn/problems/simplify-path/)

问题：给你一个字符串 `path` ，表示指向某一文件或目录的 Unix 风格绝对路径（以 `'/'` 开头），请你将其转化为更加简洁的规范路径。

思路：用栈维护路径。将字符串用 '/' 分割为子串，当遇到合法名字时压入栈顶；遇到 ".." 时弹出栈顶元素。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    string simplifyPath(string path) {
        deque<string> dq{};
        int n = path.size();
        for (int i = 0; i < n; ++i) {
            if (path[i] != '/') {
                int j = i + 1;
                while (j < n && path[j] != '/')
                    ++j;
                string str = path.substr(i, j - i);
                if (str == ".") {
                } else if (str == "..") {
                    if (!dq.empty())
                        dq.pop_back();
                } else {
                    dq.emplace_back(str);
                }
                i = j;
            }
        }
        if (dq.empty())
            return {'/'};
        string ans{};
        for (auto it = dq.begin(); it != dq.end(); ++it) {
            ans.push_back('/');
            ans.append(*it);
        }
        return ans;
    }
};
```

### 最小栈

[155. 最小栈（中等）](https://leetcode.cn/problems/min-stack/)

问题：设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈。

思路：用一个辅助栈存储当前栈顶元素入栈时的最小值。

复杂度分析：

-   时间复杂度：$O(1)$
-   空间复杂度：$O(n)$

实现：

```cpp
class MinStack {
    stack<int> x_stack;
    stack<int> min_stack;
public:
    MinStack() {
        min_stack.push(INT_MAX);
    }

    void push(int x) {
        x_stack.push(x);
        min_stack.push(min(min_stack.top(), x));
    }

    void pop() {
        x_stack.pop();
        min_stack.pop();
    }

    int top() {
        return x_stack.top();
    }

    int getMin() {
        return min_stack.top();
    }
};
```

思路：用单链表 + 头插法实现栈。每个节点维护当前节点入栈时的最小值。

实现：

```cpp
struct ListNode {
    int val;
    int min;
    ListNode* next;
    ListNode(int val, int min, ListNode* next)
        : val(val), min(min), next(next) {}
    ListNode() : ListNode(0, 0, nullptr) {}
};

class MinStack {
public:
    MinStack() { dummy = new ListNode(); }

    void push(int val) {
        int min = dummy->next ? dummy->next->min : val;
        if (val < min)
            min = val;
        ListNode* tmp = new ListNode(val, min, dummy->next);
        dummy->next = tmp;
    }

    void pop() {
        ListNode* tmp = dummy->next;
        if (tmp == nullptr)
            return;
        dummy->next = dummy->next->next;
        delete (tmp);
    }

    int top() { return dummy->next->val; }

    int getMin() { return dummy->next->min; }

private:
    ListNode* dummy;
};
```

### 基本计算器

[224. 基本计算器（困难）](https://leetcode.cn/problems/basic-calculator/)

问题：给你一个字符串表达式 `s` ，请你实现一个基本计算器来计算并返回它的值。

思路：括号展开 + 栈。如果展开表达式中所有的括号，则得到的新表达式中，数字本身不会发生变化，只是每个数字前面的符号会发生变化。为此需要维护一个栈，其中栈顶元素记录了当前位置所处的每个括号所「共同形成」的符号。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int calculate(string s) {
        stack<int> ops;
        ops.push(1);
        int sign = 1;
        int res = 0;
        int n = s.size();
        for (int i = 0; i < n; ++i) {
            switch (s[i]) {
            case '+':
                sign = ops.top();
                break;
            case '-':
                sign = -ops.top();
                break;
            case '(':
                ops.push(sign);
                break;
            case ')':
                ops.pop();
                break;
            case ' ':
                break;
            default:
                long num = 0;
                while (i < n && isdigit(s[i])) {
                    num = num * 10 + s[i++] - '0';
                }
                res += sign * num;
                --i;
            }
        }
        return res;
    }
};
```

### 字符串解码

[394. 字符串解码（中等）](https://leetcode.cn/problems/decode-string/)

问题：给定一个经过编码的字符串，返回它解码后的字符串。编码规则为: `k[encoded_string]`，表示其中方括号内部的 `encoded_string` 正好重复 `k` 次。

思路：构建辅助栈。一个辅助栈用于记录该层`[]`中元素重复的次数，一个辅助栈用于记录进入该层`[]`之前的字符串。当遇到`[`时进行记录；遇到`]`时进行解码。

复杂度分析：

-   时间复杂度：$O(|S|)$
-   空间复杂度：$O(|S|)$

实现：

```cpp
class Solution {
public:
    string decodeString(string s) {
        stack<int> countStack{};
        stack<string> strStack{};
        string cur{};
        int k = 0;
        for (const char& c : s) {
            if (c >= '0' && c <= '9') {
                k = k * 10 + (c - '0');  // 处理多位数
            } else if (c == '[') {
                // 遇到 '['，将当前的字符串和数字推入各自的栈
                countStack.push(k);
                strStack.push(cur);
                cur = "";
                k = 0;
            } else if (c == ']') {
                // 遇到 ']'，解码
                string tmp = strStack.top();
                strStack.pop();
                int repeat = countStack.top();
                countStack.pop();
                for (int i = 0; i < repeat; ++i) {
                    tmp.append(cur);  // 重复当前字符串
                }
                cur = tmp;  // 更新当前字符串
            } else {
                // 如果是字母，直接加到当前字符串
                cur.push_back(c);
            }
        }
        return cur;
    }
};
```

### 每日温度

[739. 每日温度（中等）](https://leetcode.cn/problems/daily-temperatures/)

问题：给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

思路：单调栈。单调栈通常用于求出数组中各个元素右侧第一个更大的元素及其下标，然后一并得到其他信息。单调栈的实现在于每遍历到一个新的元素，就将小于（或大于）该元素的栈顶全部弹出。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        int n = temperatures.size();
        stack<int> idxStack{};
        vector<int> ans(n, 0);
        for (int i = 0; i < n; ++i) {
            while (!idxStack.empty() && temperatures[i] > temperatures[idxStack.top()]) {
                int idx = idxStack.top();
                ans[idx] = i - idx;
                idxStack.pop();
            }
            idxStack.push(i);
        }
        return ans;
    }
};
```

### 柱状图中最大的矩形

[84. 柱状图中最大的矩形（困难）](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

问题：给定 _n_ 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。求在该柱状图中，能够勾勒出来的矩形的最大面积。

思路：单调栈。在一维数组中对于每一个数，找到左边和右边第一个比自己小的数的下标。以这个数为高度的柱子的底就被两个下标包围。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int n = heights.size();
        vector<int> left(n), right(n, n);
        stack<int> mono_stack;
        for (int i = 0; i < n; ++i) {
            while (!mono_stack.empty() && heights[mono_stack.top()] >= heights[i]) {
                right[mono_stack.top()] = i;  // 记录右侧第一个小于自身的下标
                mono_stack.pop();
            }
            left[i] = (mono_stack.empty() ? -1 : mono_stack.top());  // 记录左侧第一个小于自身的下标
            mono_stack.push(i);
        }

        int ans = 0;
        for (int i = 0; i < n; ++i) {
            // 以height[i]为高度的柱形，底在两侧第一个小于自身的坐标内侧
            ans = max(ans, (right[i] - left[i] - 1) * heights[i]);
        }
        return ans;
    }
};
```

## 堆

在需要寻找第 k 大或前 k 个元素时，通常会用到堆和堆排序。建堆的时间为 $O(n)$ ，排序的时间为 $O(n \log n)$ 。

_提示：寻找第 k 大的元素时，可以建立大根堆，调整 k-1 次，堆顶元素即为所求；寻找前 k 大的元素时（不要求顺序），可以建立大小为 k 的小根堆，每次将新元素与堆顶比较，若更大则交换并调整。_

### 数组中的第 K 个最大元素

[215. 数组中的第 K 个最大元素（中等）](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

问题：给定整数数组 `nums` 和整数 `k`，请返回数组中第 `k` 个最大的元素。

思路：建立大根堆，进行 `k-1` 次排序操作后，堆顶元素就是答案。

复杂度分析：

-   时间复杂度：$O(n \log n)$。建堆的时间复杂度为$O(n)$，最坏情况下排序时间复杂度为$O(n \log n)$。
-   空间复杂度：$O(\log n)$

实现（背诵）：

```cpp
class Solution {
public:
    void maxHeapify(vector<int>& a, int i, int heapSize) {
        int l = i * 2 + 1, r = i * 2 + 2, largest = i;  // 左孩子下标为2*i+1; 右孩子下标为2*i+2
        if (l < heapSize && a[l] > a[largest]) {
            largest = l;
        }
        if (r < heapSize && a[r] > a[largest]) {
            largest = r;
        }
        if (largest != i) {
            swap(a[i], a[largest]);
            maxHeapify(a, largest, heapSize);  // 如果进行了交换，需要向下调整
        }
    }

    void buildMaxHeap(vector<int>& a, int heapSize) {
        // 从最后一个非叶子节点开始往前调整
        for (int i = heapSize / 2 - 1; i >= 0; --i) {
            maxHeapify(a, i, heapSize);
        }
    }

    int findKthLargest(vector<int>& nums, int k) {
        int heapSize = nums.size();
        buildMaxHeap(nums, heapSize);
        for (int i = 0; i < k - 1; ++i) {
            swap(nums[0], nums[--heapSize]);  // 将堆顶元素（最大值）和倒数第i个元素交换，后i个元素已经完成排序
            maxHeapify(nums, 0, heapSize);  // 从堆顶开始调整
        }
        return nums[0];  // 排序k-1次后，大根堆顶是第k大的元素
    }
};
```

### 前 K 个高频元素

[347. 前 K 个高频元素（中等）](https://leetcode.cn/problems/top-k-frequent-elements/)

问题：给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按任意顺序返回答案。

思路：先遍历数组，将每个元素出现的次数存储在 map 中。再利用 map 中的元素建立大根堆，进行 k-1 次调整。

优化：建立大小为 k 的小根堆，比较新元素和堆顶元素的大小，如果大于堆顶元素则进行交换和调整。

复杂度分析：

-   时间复杂度：$O(n \log n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    static bool cmp(pair<int, int>& m, pair<int, int>& n) {
        return m.second > n.second;
    }

    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> occurrences;
        for (auto& v : nums) {
            occurrences[v]++;
        }

        // pair 的第一个元素代表数组的值，第二个元素代表了该值出现的次数
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(&cmp)> q(cmp);
        for (auto& [num, count] : occurrences) {
            if (q.size() == k) {
                if (q.top().second < count) {
                    q.pop();
                    q.emplace(num, count);
                }
            } else {
                q.emplace(num, count);
            }
        }
        vector<int> ret;
        while (!q.empty()) {
            ret.emplace_back(q.top().first);
            q.pop();
        }
        return ret;
    }
};
```

### 查找和最小的 K 对数字
[373. 查找和最小的 K 对数字（中等）](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/)

问题：给定两个以 **非递减顺序排列** 的整数数组 `nums1` 和 `nums2` , 以及一个整数 `k` 。定义一对值 `(u,v)`，其中第一个元素来自 `nums1`，第二个元素来自 `nums2` 。请找到和最小的 `k` 个数对 `(u1,v1)`, ` (u2,v2)` ...  `(uk,vk)` 。

思路：借助最小堆。

复杂度分析：

-   时间复杂度：$O(k \log min(n, k))$
-   空间复杂度：$O(min(n, k))$

实现：

```cpp
class Solution {
public:
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        int n = nums1.size(), m = nums2.size();
        vector<vector<int>> ans;
        priority_queue<tuple<int, int, int>> pq;
        for (int i = 0; i < min(n, k); i++) { // 至多 k 个
            pq.emplace(-nums1[i] - nums2[0], i, 0); // 取相反数变成小顶堆
        }
        while (ans.size() < k) {
            auto [_, i, j] = pq.top();
            pq.pop();
            ans.push_back({nums1[i], nums2[j]});
            if (j + 1 < m) {
                pq.emplace(-nums1[i] - nums2[j + 1], i, j + 1);
            }
        }
        return ans;
    }
};
```

### 数据流的中位数

[295. 数据流的中位数（困难）](https://leetcode.cn/problems/find-median-from-data-stream/)

问题：实现 MedianFinder 类。要求在数据流中找到中位数。

思路：将数据分为左右两边，一边以最大堆的形式实现，可以快速获得左侧最大数， 另一边则以最小堆的形式实现。需要注意，左右侧数据的长度差不能超过 1。堆可以使用优先队列实现。

优化：每次插入元素不用比较大小，只要先插入较小的堆，然后经过堆调整，再将堆顶元素推入另一个堆。

复杂度分析：

-   时间复杂度：$O(\log n)$
-   空间复杂度：$O(n)$

实现（背诵）：

```cpp
class MedianFinder {
public:
    priority_queue<int, vector<int>, greater<int>> A; // 小顶堆，保存较大的一半
    priority_queue<int, vector<int>, less<int>> B; // 大顶堆，保存较小的一半
    MedianFinder() { }
    void addNum(int num) {
        if (A.size() != B.size()) {
            A.push(num);
            B.push(A.top());
            A.pop();
        } else {
            B.push(num);
            A.push(B.top());
            B.pop();
        }
    }
    double findMedian() {
        return A.size() != B.size() ? A.top() : (A.top() + B.top()) / 2.0;
    }
};
```

### IPO

[502. IPO（困难）](https://leetcode.cn/problems/ipo/)

问题：给你 `n` 个项目。对于每个项目 `i` ，它都有一个纯利润 `profits[i]` ，和启动该项目需要的最小资本 `capital[i]` 。从给定项目中选择 **最多** `k` 个不同项目的列表，以 **最大化最终资本** ，并输出最终可获得的最多资本。

思路：利用堆的贪心算法。利用大根堆的特性，将所有能够投资的项目的利润全部压入到堆中，每次从堆中取出最大值，然后更新手中持有的资本，同时将待选的项目利润进入堆，不断重复上述操作。

复杂度分析：

-   时间复杂度：$O((n+k)\log n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int findMaximizedCapital(int k, int w, vector<int>& profits,
                             vector<int>& capital) {
        int n = profits.size();
        priority_queue<int, vector<int>, less<int>> pq;
        vector<pair<int, int>> arr;
        for (int i = 0; i < n; ++i) {
            arr.push_back({capital[i], profits[i]});
        }
        sort(arr.begin(), arr.end(),
             [](const pair<int, int>& a, const pair<int, int>& b) {
                 return a.first < b.first;
             });
        int idx = 0;
        for (int i = 0; i < k; ++i) {
            while (idx < n && arr[idx].first <= w) {
                pq.push(arr[idx++].second);
            }
            if (!pq.empty()) {
                w += pq.top();
                pq.pop();
            } else {
                break;
            }
        }
        return w;
    }
};
```

## 贪心算法

### 买卖股票的最佳时机

[121. 买卖股票的最佳时机（简单）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)

问题：给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。你只能选择某一天买入这只股票，并选择在未来的某一个不同的日子卖出该股票。设计一个算法来计算你所能获取的最大利润。

思路：遍历数组，维护当前天数之前最低价格，当天利润 = 当天股价 - 历史低价。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int cost = INT_MAX, profit = 0;
        for (int price : prices) {
            cost = min(cost, price);
            profit = max(profit, price - cost);
        }
        return profit;
    }
};
```

### 跳跃游戏

[55. 跳跃游戏（中等）](https://leetcode.cn/problems/jump-game/)

问题：给你一个非负整数数组 `nums` ，你最初位于数组的第一个下标。数组中的每个元素代表你在该位置可以跳跃的最大长度。判断你是否能够到达最后一个下标。

思路：贪心算法。遍历数组中的每一个位置，并实时维护最远可以到达的位置`rightmax`。对于当前遍历到的位置 i，如果它可达，即`i<=rightmax`，就可以用 `i+nums[i]` 更新最远可以到达的位置。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        int rightmax = 0;
        for (int i = 0; i < n; ++i) {
            if (i <= rightmax) {
                rightmax = max(rightmost, i + nums[i]);
                if (rightmax >= n - 1) {
                    return true;
                }
            }
        }
        return false;
    }
};
```

### 跳跃游戏 II

[45. 跳跃游戏 II（中等）](https://leetcode.cn/problems/jump-game-ii/)

问题：给定一个长度为 `n` 的整数数组 `nums`。每个元素 `nums[i]` 表示从索引 `i` 向前跳转的最大长度。返回到达 `nums[n - 1]` 的最小跳跃次数。

思路：贪心算法。维护当前能够到达的最大下标位置，记为边界。从左到右遍历数组，到达边界时，更新边界并将跳跃次数增加 1。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        int end = 0, maxPos = 0, step = 0, n = nums.size();
        for (int i = 0; i < n - 1; ++i) {
            maxPos = max(maxPos, nums[i] + i);  // 维护当前能跳到的最远位置
            if (i == end) {  // 到达最右起跳点
                end = maxPos;  // 进行下一次跳跃
                ++step;
            }
        }
        return step;
    }
};
```

### 划分字母区间

[763. 划分字母区间（中等）](https://leetcode.cn/problems/partition-labels/)

问题：给你一个字符串 `s` 。我们要把这个字符串划分为尽可能多的片段，同一字母最多出现在一个片段中。

思路：贪心算法。先遍历一次字符串，记录每个字母最后一次出现的下标。再次遍历字符串，维护当前片段中所有字母最右的下标，当遍历到该下标，该片段结束，开始下一个片段。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    vector<int> partitionLabels(string s) {
        vector<int> words(26);
        int n = s.size();
        for (int i = 0; i < n; ++i) {
            words[s[i] - 'a'] = i;
        }
        int idx = 0, start = 0, end = 0;
        vector<int> ans;
        for (int i = 0; i < n; ++i) {
            end = max(end, words[s[i] - 'a']);
            if (i == end) {
                ans.push_back(end - start + 1);
                start = end + 1;
            }
        }
        return ans;
    }
};
```

## 动态规划

### 爬楼梯

[70. 爬楼梯（简单）](https://leetcode.cn/problems/climbing-stairs/)

问题：假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。每次你可以爬 `1` 或 `2` 个台阶。你有多少种不同的方法可以爬到楼顶呢？

思路：动态规划。考虑最后一步可能跨了一级台阶，也可能跨了两级台阶，它意味着爬到第 _x_ 级台阶的方案数是爬到第 *x*−1 级台阶的方案数和爬到第 *x*−2 级台阶的方案数的和。

优化：使用滚动数组，优化空间复杂度。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int climbStairs(int n) {
        int p = 0, q = 0, r = 1;
        for (int i = 1; i <= n; ++i) {
            p = q;
            q = r;
            r = p + q;
        }
        return r;
    }
};
```

### 杨辉三角

[118. 杨辉三角（简单）](https://leetcode.cn/problems/pascals-triangle/)

问题：给定一个非负整数`numRows`，生成「杨辉三角」的前`numRows`行。

思路：动态规划。

复杂度分析：

-   时间复杂度：$O(numRows^2)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ans;
        for (int i = 0; i < numRows; ++i) {
            vector<int> row(i + 1, 1);
            for (int j = 1; j < i; ++j) {
                row[j] = ans[i - 1][j - 1] + ans[i - 1][j];
            }
            ans.emplace_back(row);
        }
        return ans;
    }
};
```

### 打家劫舍

[198. 打家劫舍（中等）](https://leetcode.cn/problems/house-robber/)

问题：每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。给定一个代表每个房屋存放金额的非负整数数组，计算你不触动警报装置的情况下，一夜之内能够偷窃到的最高金额。

思路：动态规划。到第 i 间房屋时，有偷和不偷两个选项。因此，前 i 间房屋能偷盗的最大金额 $dp[i]=\max (dp[i-2]+nums[i],dp[i-1])$，边界条件为前两间房屋选择金额较高的一间。

优化：用滚动数组优化空间复杂度。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        int size = nums.size();
        if (size == 1) {
            return nums[0];
        }
        int first = nums[0], second = max(nums[0], nums[1]);
        for (int i = 2; i < size; i++) {
            int temp = second;
            second = max(first + nums[i], second);
            first = temp;
        }
        return second;
    }
};
```

### 完全平方数

[279. 完全平方数（中等）](https://leetcode.cn/problems/perfect-squares/)

问题：给你一个整数 `n` ，返回和为 `n` 的完全平方数的最少数量。

思路：动态规划。

复杂度分析：

-   时间复杂度：$O(n\sqrt n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int numSquares(int n) {
        vector<int> f(n + 1);
        for (int i = 1; i <= n; i++) {
            int minn = INT_MAX;
            for (int j = 1; j * j <= i; j++) {
                minn = min(minn, f[i - j * j]);
            }
            f[i] = minn + 1;
        }
        return f[n];
    }
};
```

### 零钱兑换

[322. 零钱兑换（中等）](https://leetcode.cn/problems/coin-change/)

问题：给你一个整数数组 `coins` ，表示不同面额的硬币；以及一个整数 `amount` ，表示总金额。计算并返回可以凑成总金额所需的最少的硬币个数。

思路：动态规划。

复杂度分析：

-   时间复杂度：$O(Sn)$
-   空间复杂度：$O(S)$

实现：

```cpp
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount + 1, amount + 1);  // MAX如果取INT_MAX会导致+1后溢出
        dp[0] = 0;
        for (int i = 1; i <= amount; ++i) {
            for (const int& coin : coins) {
                if (i - coin < 0)
                    continue;
                dp[i] = min(dp[i], dp[i - coin] + 1);
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }
};
```

### 单词拆分

[139. 单词拆分（中等）](https://leetcode.cn/problems/word-break/)

问题：给你一个字符串 `s` 和一个字符串列表 `wordDict` 作为字典。如果可以利用字典中出现的一个或多个单词拼接出 `s` 则返回 `true`。

思路：动态规划。从前往后计算考虑转移方程，每次转移的时候需要枚举每一个单词，检查起始点是否合法及子串是否等于该单词。

复杂度分析：

-   时间复杂度：$O(Sn)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        int n = s.size();
        vector<int> dp(n + 1, 0);
        dp[0] = 1;
        for (int i = 1; i <= n; ++i) {
            for (const string& str : wordDict) {
                int k = str.size();
                if (k <= i) {
                    if (dp[i - k] && s.substr(i - k, k) == str) {
                        dp[i] = 1;
                        break;
                    }
                }
            }
        }
        return dp[n];
    }
};
```

### 最长递增子序列

[300. 最长递增子序列（中等）](https://leetcode.cn/problems/longest-increasing-subsequence/)

问题：给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。

思路：动态规划。规定`dp[i]`为第 i 个元素结尾的递增子序列的长度，每次遍历前面所有元素进行更新。

复杂度分析：

-   时间复杂度：$O(n^2)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = (int)nums.size();
        if (n == 0) {
            return 0;
        }
        vector<int> dp(n, 0);
        for (int i = 0; i < n; ++i) {
            dp[i] = 1;
            for (int j = 0; j < i; ++j) {
                if (nums[j] < nums[i]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
        }
        return *max_element(dp.begin(), dp.end());
    }
};
```

### 乘积最大子数组

[152. 乘积最大子数组（中等）](https://leetcode.cn/problems/maximum-product-subarray/)

问题：给你一个整数数组 `nums` ，请你找出数组中乘积最大的非空连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

思路：动态规划。维护两个动态数组$f_{max}$和$f_{min}$，有状态转移方程:

$$
f_{max}(i)=\max(f_{max}(i-1)*a(i),f_{min}(i-1)*a(i),a(i))\\
f_{min}(i)=\min(f_{max}(i-1)*a(i),f_{min}(i-1)*a(i),a(i))
$$

优化：使用滚动数组优化空间复杂度。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int maxF = nums[0], minF = nums[0], ans = nums[0];
        for (int i = 1; i < nums.size(); ++i) {
            int tmpmax = maxF, tmpmin = minF;
            maxF = max(tmpmax * nums[i], max(tmpmin * nums[i], nums[i]));
            minF = min(tmpmax * nums[i], min(tmpmin * nums[i], nums[i]));
            ans = max(ans, maxF);
        }
        return ans;
    }
};
```

### 分割等和子集

[416. 分割等和子集（中等）](https://leetcode.cn/problems/partition-equal-subset-sum/)

问题：给你一个只包含正整数的非空数组 `nums` 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

思路：将问题转化为 `0-1背包` 问题，定义 dp[i, target] 为“能否从 nums[0] 到 nums[i] 中选出和为 target 的子集”，有状态转移方程：

$$
dp(i,target) = \begin{cases}
dp(i-1,target) \lor dp(i-1,target-nums(i)), & target \geq nums(i) \\
dp(i-1,target), & target < nums(i)
\end{cases}
$$

优化：可以发现在计算 dp 的过程中，每一行的 dp 值都只与上一行的 dp 值有关，因此只需要一个一维数组即可将空间复杂度降到 $O(target)$。此时状态转移方程为

$$
dp(target)=dp(target) \lor dp(target-nums(i))
$$

复杂度分析：

-   时间复杂度：$O(n*target)$
-   空间复杂度：$O(target)$

实现：

```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int n = nums.size();
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if (sum & 1)
            return false;
        int target = sum / 2;
        if (target < *max_element(nums.begin(), nums.end()))
            return false;

        vector<int> dp(target + 1, 0);
        dp[0] = 1;
        for (int i = 0; i < n; ++i) {
            int num = nums[i];
            for (int j = target; j >= num; --j)  // 从大到小计算，否则dp[j - num]就不是上一层的状态了
                dp[j] |= dp[j - num];
        }
        return dp[target];
    }
};
```

### 最长有效括号

[32. 最长有效括号（困难）](https://leetcode.cn/problems/longest-valid-parentheses/)

问题：给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

思路：定义 dp[i] 为以下标 i 字符为结尾的最长有效括号的长度，有状态转移方程：

$$
dp(i) = \begin{cases}
dp(i-2)+2, & s(i)=')' \and s(i-1)='(' \\
dp(i-1)+dp(i-dp(i-1)-2)+2, & s(i)=')' \and s(i-1)=')' \and s(i-dp(i-1)-1)='('\\
0, & else
\end{cases}
$$

优化：用一个额外空间 dp[0] 来减少边界条件的判断。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        int n = s.size();
        vector<int> dp(n + 1, 0);
        for (int i = 1; i < n; ++i) {
            if (s[i] == ')') {
                if (s[i - 1] == '(') {
                    dp[i + 1] = dp[i - 1] + 2;
                } else if (i - dp[i] > 0 && s[i - dp[i] - 1] == '(') {
                    dp[i + 1] = dp[i] + dp[i - dp[i] - 1] + 2;
                }
            }
        }
        return *max_element(dp.begin(), dp.end());
    }
};
```

## 多维动态规划

### 不同路径

[62. 不同路径（中等）](https://leetcode.cn/problems/unique-paths/)

问题：一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为 “Start” ）。机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。问总共有多少条不同的路径？

思路：定义`dp[i][j]`为第 i 行第 j 列的走法，有状态转移方程：

$$
dp(i,j)=dp(i-1,j)+dp(i,j-1)
$$

优化：使用滚动数组优化空间复杂度。

复杂度分析：

-   时间复杂度：$O(n*m)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<int> dp(n + 1, 0);
        dp[1] = 1;
        for (int i = 1; i <= m; ++i)
            for (int j = 1; j <= n; ++j)
                dp[j] += dp[j - 1];
        return dp[n];
    }
};
```

### 最小路径和

[64. 最小路径和（中等）](https://leetcode.cn/problems/minimum-path-sum/)

问题：给定一个包含非负整数的 `m x n` 网格 `grid` ，每次只能向下或者向右移动一步。请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

思路：定义`dp[i][j]`为第 i 行第 j 列的路径和，有状态转移方程：

$$
dp(i,j)=\min(dp(i-1,j),dp(i,j-1))+grid(i,j)
$$

优化：使用滚动数组优化空间复杂度。

复杂度分析：

-   时间复杂度：$O(n*m)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<int> dp(n, 0);
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j) {
                if (j == 0)
                    dp[j] += grid[i][j];
                else if (i == 0)
                    dp[j] = dp[j - 1] + grid[i][j];
                else
                    dp[j] = min(dp[j], dp[j - 1]) + grid[i][j];
            }
        return dp[n - 1];
    }
};
```

### 最长回文子串

[5. 最长回文子串（中等）](https://leetcode.cn/problems/longest-palindromic-substring/)

问题：给你一个字符串 `s`，找到 `s` 中最长的回文子串。

思路：动态规划。定义 P(i, j) 为第 i 个到第 j 个字符是否为回文串，有状态转移方程：

$$
P(i,j)=P(i+1,j-1) \and (S_i==S_j)
$$

复杂度分析：

-   时间复杂度：$O(n^2)$
-   空间复杂度：$O(n^2)$

思路：中心扩展算法。枚举所有的回文中心（包括长度为 1 和长度为 2 的中心），向两边扩展。

复杂度分析：

-   时间复杂度：$O(n^2)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int begin = 0, length = 0;
        for (int i = 0; i < s.size(); ++i) {
            auto [begin1, length1] = expandAroundCenter(s, i, i);
            auto [begin2, length2] = expandAroundCenter(s, i, i + 1);
            if (length1 > length) {
                begin = begin1;
                length = length1;
            }
            if (length2 > length) {
                begin = begin2;
                length = length2;
            }
        }
        return s.substr(begin, length);
    }

private:
    pair<int, int> expandAroundCenter(const string& s, int left, int right) {
        while (left >= 0 && right < s.size() && s[left] == s[right]) {
            left--;
            right++;
        }
        return {left + 1, right - left - 1};
    }
};
```

### 最大正方形

[221. 最大正方形（中等）](https://leetcode.cn/problems/maximal-square/)

问题：在一个由 `'0'` 和 `'1'` 组成的二维矩阵内，找到只包含 `'1'` 的最大正方形，并返回其面积。

思路：动态规划。定义 dp(i, j) 为以 (i, j) 为右下角，且只包含 1 的正方形的边长最大值，有状态转移方程：

$$
dp[i][j] = \begin{cases}
min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1])+1, &matrix[i][j]=='1'\\
0, &otherwise
\end{cases}
$$

复杂度分析：

-   时间复杂度：$O(mn)$
-   空间复杂度：$O(mn)$

实现：

```cpp
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        if (matrix.size() == 0 || matrix[0].size() == 0) {
            return 0;
        }
        int maxSide = 0;
        int rows = matrix.size(), columns = matrix[0].size();
        vector<vector<int>> dp(rows, vector<int>(columns));
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = min(min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                    }
                    maxSide = max(maxSide, dp[i][j]);
                }
            }
        }
        int maxSquare = maxSide * maxSide;
        return maxSquare;
    }
};
```

### 最长公共子序列

[1143. 最长公共子序列（中等）](https://leetcode.cn/problems/longest-common-subsequence/)

问题：给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长公共子序列的长度。如果不存在公共子序列，返回 `0` 。

思路：动态规划。定义 P(i, j) 为 text1 [0, i] 和 text2 [0, j] 的最长公共子序列长度，有状态转移方程：

$$
dp(i，j) = \begin{cases}
dp(i-1,j-1)+1, & text1[i]=text2[j]\\
\max(dp(i-1,j),dp(i,j-1)), & otherwise
\end{cases}
$$

复杂度分析：

-   时间复杂度：$O(mn)$
-   空间复杂度：$O(mn)$

实现：

```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int m = text1.size(), n = text2.size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (text1[i - 1] == text2[j - 1])
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                else
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return dp[m][n];
    }
};
```

### 编辑距离

[72. 编辑距离（困难）](https://leetcode.cn/problems/edit-distance/)

问题：给你两个单词 `word1` 和 `word2`，请返回将 `word1` 转换成 `word2` 所使用的最少操作数。

思路：本质不同的操作实际上只有三种：

1. 在单词 `A` 中插入一个字符，即`dp[i][j]=dp[i-1][j]+1`；
2. 在单词 `B` 中插入一个字符，即`dp[i][j]=dp[i][j-1]+1`；
3. 修改单词 `A` 的一个字符。若字母相等，则不用修改，有`dp[i][j]=dp[i-1][j-1]`；若不等，有`dp[i][j]=dp[i-1][j-1]+1`。

定义 dp(i, j) 为 word1 [0, i] 和 word2 [0, j] 的编辑距离，在三种操作中取操作数最少的一种，有状态转移方程：

$$
dp(i，j) = \begin{cases}
1+min(dp(i,j-1),dp(i-1,j),dp(i-1,j-1)-1), & word1[i]=word2[j]\\
1+min(dp(i,j-1),dp(i-1,j),dp(i-1,j-1)), & word1[i]\neq word2[j]
\end{cases}
$$

复杂度分析：

-   时间复杂度：$O(mn)$
-   空间复杂度：$O(mn)$

实现：

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size(), n = word2.size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
        // 初始化
        for (int i = 0; i <= m; ++i)
            dp[i][0] = i;
        for (int j = 0; j <= n; ++j)
            dp[0][j] = j;
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                dp[i][j] =
                    word1[i - 1] == word2[j - 1]
                        ? 1 + min(dp[i][j - 1],
                                  min(dp[i - 1][j], dp[i - 1][j - 1] - 1))
                        : 1 + min(dp[i][j - 1],
                                  min(dp[i - 1][j], dp[i - 1][j - 1]));
            }
        }
        return dp[m][n];
    }
};
```

## 模拟

### 罗马数字转整数

[13. 罗马数字转整数（简单）](https://leetcode.cn/problems/roman-to-integer/)

问题：给定一个罗马数字，将其转换成整数。

思路：通常情况下，罗马数字中小的数字在大的数字的右边。若输入的字符串满足该情况，那么可以将每个字符视作一个单独的值，累加每个字符对应的数值即可。若存在小的数字在大的数字的左边的情况，根据规则需要减去小的数字。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int romanToInt(string s) {
        int num = 0;
        unordered_map<char, int> map{{'I', 1},   {'V', 5},   {'X', 10},
                                     {'L', 50},  {'C', 100}, {'D', 500},
                                     {'M', 1000}};
        for (int i = 0; i < s.size(); ++i) {
            num += i < s.size() - 1 && map[s[i]] < map[s[i + 1]] ? -map[s[i]]
                                                                 : map[s[i]];
        }
        return num;
    }
};
```

### 数字转罗马数字

[12. 整数转罗马数字（中等）](https://leetcode.cn/problems/integer-to-roman/)

问题：给定一个整数，将其转换成罗马数字。

思路：硬编码数字。

复杂度分析：

-   时间复杂度：$O(1)$
-   空间复杂度：$O(1)$

实现：

```cpp
const string thousands[] = {"", "M", "MM", "MMM"};
const string hundreds[]  = {"", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"};
const string tens[]      = {"", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"};
const string ones[]      = {"", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"};

class Solution {
public:
    string intToRoman(int num) {
        return thousands[num / 1000] + hundreds[num % 1000 / 100] + tens[num % 100 / 10] + ones[num % 10];
    }
};
```

## 位运算

### 只出现一次的数字

[136. 只出现一次的数字（简单）](https://leetcode.cn/problems/single-number/)

问题：给你一个非空整数数组 `nums` ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

思路：数组中的全部元素的**异或**运算结果即为数组中只出现一次的数字。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ret = 0;
        for (auto e: nums) ret ^= e;
        return ret;
    }
};
```

### 只出现一次的数字 II

[137. 只出现一次的数字 II（中等）](https://leetcode.cn/problems/single-number-ii/)

问题：给你一个整数数组 `nums` ，除某个元素仅出现一次外，其余每个元素都恰出现三次。请你找出并返回那个只出现了一次的元素。

思路：数组中的全部元素的异或运算结果即为数组中只出现一次的数字。

复杂度分析：

-   时间复杂度：$O(n\log C)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;
        for (int i = 0; i < 32; ++i) {
            int total = 0;
            for (int num: nums) {
                total += ((num >> i) & 1);
            }
            if (total % 3) {
                ans |= (1 << i);
            }
        }
        return ans;
    }
};
```

### 数字范围按位与

[201. 数字范围按位与（中等）](https://leetcode.cn/problems/bitwise-and-of-numbers-range/)

问题：给你两个整数 `left` 和 `right` ，表示区间 `[left, right]` ，返回此区间内所有数字按位与的结果（包含 `left` 、`right` 端点）。

思路：可以将问题重新表述为：给定两个整数，我们要找到它们对应的二进制字符串的公共前缀。有一个位移相关的算法叫做「Brian Kernighan 算法」，它用于清除二进制串中最右边的 1，可以用它来计算两个二进制字符串的公共前缀。

复杂度分析：

-   时间复杂度：$O(log n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int rangeBitwiseAnd(int m, int n) {
        while (m < n) {
            // 抹去最右边的 1
            n = n & (n - 1);
        }
        return n;
    }
};
```

## 数学

### 直线上最多的点数

[149. 直线上最多的点数（困难）](https://leetcode.cn/problems/max-points-on-a-line/)

问题：给你一个数组 `points` ，其中 `points[i] = [xi, yi]` 表示平面上的一个点。求最多有多少个点在同一条直线上。

思路：`n!` 结果中尾随零的数量只与 n 中因子中有几个 5 有关。

复杂度分析：

-   时间复杂度：$O(n^2 × \log m)$
-   空间复杂度：$O(n)$

实现：

```cpp
class Solution {
public:
    int gcd(int a, int b) {
        return b ? gcd(b, a % b) : a;
    }

    int maxPoints(vector<vector<int>>& points) {
        int n = points.size();
        if (n <= 2) {
            return n;
        }
        int ret = 0;
        for (int i = 0; i < n; i++) {
            if (ret >= n - i || ret > n / 2) {
                break;
            }
            unordered_map<int, int> mp;
            for (int j = i + 1; j < n; j++) {
                int x = points[i][0] - points[j][0];
                int y = points[i][1] - points[j][1];
                if (x == 0) {
                    y = 1;
                } else if (y == 0) {
                    x = 1;
                } else {
                    if (y < 0) {
                        x = -x;
                        y = -y;
                    }
                    int gcdXY = gcd(abs(x), abs(y));
                    x /= gcdXY, y /= gcdXY;
                }
                mp[y + x * 20001]++;
            }
            int maxn = 0;
            for (auto& [_, num] : mp) {
                maxn = max(maxn, num + 1);
            }
            ret = max(ret, maxn);
        }
        return ret;
    }
};
```

### 阶乘后的零

[172. 阶乘后的零（中等）](https://leetcode.cn/problems/factorial-trailing-zeroes/)

问题：给定一个整数 `n` ，返回 `n!` 结果中尾随零的数量。

思路：`n!` 结果中尾随零的数量只与 n 中因子中有几个 5 有关。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int trailingZeroes(int n) {
        int ans = 0;
        while (n) {
            n /= 5;
            ans += n;
        }
        return ans;
    }
};
```

## 技巧

### 多数元素

[169. 多数元素（简单）](https://leetcode.cn/problems/majority-element/)

问题：给定一个大小为 `n` 的数组 `nums` ，返回其中的多数元素。多数元素是指在数组中出现次数大于`⌊ n/2 ⌋` 的元素。

思路：Boyer-Moore 投票算法。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int candidate = -1;
        int count = 0;
        for (int num : nums) {
            if (num == candidate)
                ++count;
            else if (--count < 0) {
                candidate = num;
                count = 1;
            }
        }
        return candidate;
    }
};
```

### 下一个排列

[31. 下一个排列（中等）](https://leetcode.cn/problems/next-permutation/)

问题：整数数组的一个排列就是将其所有成员以序列或线性顺序排列。给你一个整数数组 `nums` ，找出 `nums` 的下一个排列。

思路：将一个左边的「较小数」与一个右边的「较大数」交换，当交换完成后，「较大数」右边的数需要按照升序重新排列。这样可以在保证新排列大于原来排列的情况下，使变大的幅度尽可能小。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int i = nums.size() - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }
        if (i >= 0) {
            int j = nums.size() - 1;
            while (j >= 0 && nums[i] >= nums[j]) {
                j--;
            }
            swap(nums[i], nums[j]);
        }
        reverse(nums.begin() + i + 1, nums.end());
    }
};
```

### Pow(x, n)

[50. Pow(x, n)（中等）](https://leetcode.cn/problems/powx-n/)

问题：实现 pow(x, n)。

思路：使用分治法实现快速幂。

复杂度分析：

-   时间复杂度：$O(\log n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    double quickMul(double x, long long N) {
        double ans = 1.0;
        // 贡献的初始值为 x
        double x_contribute = x;
        // 在对 N 进行二进制拆分的同时计算答案
        while (N > 0) {
            if (N % 2 == 1) {
                // 如果 N 二进制表示的最低位为 1，那么需要计入贡献
                ans *= x_contribute;
            }
            // 将贡献不断地平方
            x_contribute *= x_contribute;
            // 舍弃 N 二进制表示的最低位，这样我们每次只要判断最低位即可
            N /= 2;
        }
        return ans;
    }

    double myPow(double x, int n) {
        long long N = n;
        return N >= 0 ? quickMul(x, N) : 1.0 / quickMul(x, -N);
    }
};
```

### 快乐数

[202. 快乐数（简单）](https://leetcode.cn/problems/happy-number/)

问题：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1 ，那么这个数就是快乐数。编写一个算法来判断一个数 `n` 是不是快乐数。

思路：使用“快慢指针”思想找出循环，判断循环点是否为 1。

复杂度分析：

-   时间复杂度：$O(\log n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    bool isHappy(int n) {
        int slow = n, fast = n;
        do {
            slow = bitSqrSum(slow);
            fast = bitSqrSum(bitSqrSum(fast));
        } while (slow != fast);
        return slow == 1;
    }
    int bitSqrSum(int n) {
        int ans = 0;
        while (n > 0) {
            ans += pow(n % 10, 2);
            n /= 10;
        }
        return ans;
    }
};
```

### 寻找重复数

[287. 寻找重复数（中等）](https://leetcode.cn/problems/find-the-duplicate-number/)

问题：给定一个包含 `n + 1` 个整数的数组 `nums` ，其数字都在 `[1, n]` 范围内（包括 `1` 和 `n`），可知至少存在一个重复的整数。假设只有一个重复的整数，找到这个重复的数。

思路：位运算。统计 `[1, n]` 范围内每一位 1 的总数，再统计 `nums` 每一位 1 的总数。如果后者在某一位的 1 数量更多，则答案中对应位置为 1 。

复杂度分析：

-   时间复杂度：$O(n \log n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int n = nums.size(), bit_max = 31, res = 0;
        while (!((n - 1) >> bit_max)) {
            --bit_max;  // // 确定二进制下最高位是多少
        }
        for (int bit = 0; bit <= bit_max; ++bit) {
            int x = 0, y = 0;
            for (int i = 1; i < n; ++i) {
                x += 1 & (i >> bit);
            }
            for (int a : nums) {
                y += 1 & (a >> bit);
            }
            if (x < y)
                res |= 1 << bit;
        }
        return res;
    }
};
```

思路：快慢指针。对 nums 数组建图，每个位置 i 连一条 i→nums[i] 的边。由于存在的重复的数字 target，因此 target 这个位置一定有起码两条指向它的边，因此整张图一定存在环，且要找到的 target 就是这个环的入口，那么整个问题就等价于找到环的入口。

复杂度分析：

-   时间复杂度：$O(n)$
-   空间复杂度：$O(1)$

实现：

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int slow = 0, fast = 0;
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);
        slow = 0;
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }
};
```
