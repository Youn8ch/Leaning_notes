
哈希表（Hash Table）是一种用于实现**键值对存储**的数据结构, 牺牲了空间换取了时间。

它允许在常数时间内进行插入、删除和查找操作。哈希表的核心思想是通过哈希函数将键映射到数组中的一个位置, 从而实现高效的查找。

<hr>

# :memo: 240703 

### 在C++中, 常见的数据结构 >> 数组 set map, 其底层实现以及优劣如下表所示：

<img src="https://github.com/Youn8ch/Leaning_notes/blob/master/imgsource/img.png" alt="img" style="float: left;" />
   
   * 需要保证元素唯一性时使用 std::set 或 std::unordered_set
   * 允许元素重复但需要保持顺序时使用 std::multiset
   * 不关心顺序, 但需要高效查找、插入和删除时使用 std::unordered_set

<img src="https://github.com/Youn8ch/Leaning_notes/blob/master/imgsource/img_1.png" alt="img" style="float: left;" />

   * 需要键值对按键排序且键唯一时使用 std::map
   * 需要键值对按键排序但允许键重复时使用 std::multimap
   * 不关心键顺序, 但需要高效查找、插入和删除时使用 std::unordered_map

### 哈希表能解决什么问题？一般什么时候想到使用哈希表?

一般哈希表都是用来快速判断一个元素是否出现集合里:

1. 快速查找：需要在大量数据中快速查找某个特定元素 
   * std::unordered_map 或 std::unordered_set 查找操作是O(1)时间复杂度
2. 快速插入和删除: 需要频繁地进行数据的插入和删除操作 
   * std::unordered_map 或 std::unordered_set 插入和删除操作也是O(1)时间复杂度
3. 键值对关联：需要将数据以键值对的形式存储和访问  
   * std::unordered_map 提供了高效的键值对存储机制

```cpp
// 创建一个unordered_map, KEY键为std::string类型, VALUE值为int类型
std::unordered_map<std::string, int> wordCount;

// 插入一些键值对
wordCount["apple"] = 2; 
wordCount["banana"] = 3; 
wordCount["orange"] = 5; 

std::string key = "apple"; 
// 查找一个键的值
if (wordCount.find(key) != wordCount.end()) {
    // 找到键
}
```
4. 数据去重：需要检查并确保数据集合中的元素唯一 
   * std::unordered_set 可以快速去除重复元素, 保持集合中的元素唯一性
```cpp
   std::vector<int> nums = {1, 2, 3, 1, 4, 2, 5, 6, 3, 7};
   std::unordered_set<int> uniqueNums(nums.begin(), nums.end()); // 自动去除重复元素, 所以uniqueNums中只会包含唯一的元素
   std::vector<int> result(uniqueNums.begin(), uniqueNums.end());
```
5. 频率统计：需要统计元素出现的频率, 例如统计单词出现的次数
   * 键为元素, 值为出现次数, 可以高效地完成频率统计
```cpp
    for (char ch : text) { 
         if (ch != ' ') frequency[ch]++; // 遍历字符串, 统计每个字符的出现次数
    } 
```

<hr>

# :memo: 240705 

### Leetcode 15 三数之和

<img src="https://github.com/Youn8ch/Leaning_notes/blob/master/imgsource/img_2.png" alt="img" style="float: left;" />
<img src="https://github.com/Youn8ch/Leaning_notes/blob/master/imgsource/img_3.png" alt="img" style="float: left;" />

### 思路 ：常规 哈希表

比方说a+b+c=0, 两层for循环就确定 a 和 b 的数值, 可以使用哈希法来确定 0-(a+b) 是否在数组里出现过。但是有一个问题, 题目要求**不可以包含重复的三元组**

把符合条件的三元组放进vector中, 然后再去重。很难处理, 并且放入的三元组是无序的 {1, 2, -3} {2, 1, -3} {2, -3, 1}

### 思路 ： 因此尝试新方法 >> 一维数组, 三个参量 a + b + c, 固定 a 尝试 b c 组合 >> 双指针

满足 nums[i] + nums[j] + nums[k] == 0, [nums[i], nums[j], nums[k]] 满足 i != j、i != k、j != k, 并且不可以包含重复的三元组

分析题目：三个参数求和后为0, 且i j k各不相同, 主要问题是不可以包含重复的三元组

三个参量 >> 固定一端 i, 初始化 j k 位置  

求和 >> 排序后 j k 初始化至两端, 根据 sum 情况向内逼近

i j k 不同, 且不含重复三元组 >> 更新初始化固定端 i, 以及更新 j k 时检查是否重复项

框架步骤：固定端 + 常规双指针

1. 初始化固定端：i 至最左, 从左到右扫描
2. 初始化指针位置：
   * i j k 三个参量, 固定一个在一端, 尝试组合 j k, 确定双指针是指向 j k 的信息
   * 满足 i != j、i != k 且 j != k
3. 移动指针的条件：决定何时以及如何移动指针
   * 判断sum情况, >0, <0, =0, 什么时候检查是否重复项
   * sum = 0, push 该三元组, 不可以包含重复的三元组, 若 j+1==j || k-1==k, 则重复记录 >> 左右两指针向内缩进至无重复项(不能缩过头 >> j<k), j++ k--
   * sum > 0, k-- ; sum < 0, j++;
4. 终止条件：确定循环终止的条件。通常是当两个指针相遇或者交错时终止
   * i j k各不相同 >> while(j<k)
5. 边界条件：检查边界情况 >> 缩进时需满足 j<k 防止缩进过头
6. 重新初始化固定端：i++, 从左向右扫描。不可以包含重复的三元组 >> 下一个nums[i]相同则跳过, j k在远端满足条件时, nums[i]和nums[i-1]重复

严格按照框架, 代码如下：

```cpp
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        int n = nums.size();
        int l; int r; int sum;
        for (int i = 0; i < n - 2; i++) {
            if (i > 0 && nums[i] == nums[i-1]) continue;
            if (nums[i] > 0) break;
            l = i+1;
            r = n-1;
            while (l<r) {
                sum = nums[i] + nums[l] + nums[r];
                if (sum > 0) r--;
                else if (sum < 0) l++;
                else
                {
                    ans.push_back({nums[i], nums[l], nums[r]});
                    while (l < r && nums[l] == nums[l+1]) l++;  
                    while (l < r && nums[r] == nums[r-1]) r--;  
                    l++;
                    r--;
                }
            }
        }
        return ans;
```