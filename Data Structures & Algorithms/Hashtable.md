
哈希表（Hash Table）是一种用于实现**键值对存储**的数据结构，牺牲了空间换取了时间。

它允许在常数时间内进行插入、删除和查找操作。哈希表的核心思想是通过哈希函数将键映射到数组中的一个位置，从而实现高效的查找。

<hr>

# :memo: 240703 

### 在C++中,常见的数据结构 >> 数组 set map，其底层实现以及优劣如下表所示：

<img src="https://github.com/Youn8ch/Leaning_notes/blob/master/imgsource/img.png" alt="img" style="float: left;" />
<img src="https://github.com/Youn8ch/Leaning_notes/blob/master/imgsource/img_1.png" alt="img" style="float: left;" />

### 哈希表能解决什么问题？一般什么时候想到使用哈希表?

一般哈希表都是用来快速判断一个元素是否出现集合里:

1. 快速查找：需要在大量数据中快速查找某个特定元素 
   * std::unordered_map 或 std::unordered_set 查找操作是O(1)时间复杂度
2. 快速插入和删除: 需要频繁地进行数据的插入和删除操作 
   * std::unordered_map 或 std::unordered_set 插入和删除操作也是O(1)时间复杂度
3. 键值对关联：需要将数据以键值对的形式存储和访问  
   * std::unordered_map 提供了高效的键值对存储机制
   * ====================================
   * std::unordered_map<std::string, int> wordCount; // 创建一个unordered_map，KEY键为std::string类型，VALUE值为int类型
   * wordCount["apple"] = 2; wordCount["banana"] = 3; wordCount["orange"] = 5; // 插入一些键值对
   * std::string key = "apple"; if (wordCount.find(key) != wordCount.end()) {} // 查找一个键的值
   * ====================================
4. 数据去重：需要检查并确保数据集合中的元素唯一 
   * std::unordered_set 可以快速去除重复元素，保持集合中的元素唯一性
   * ====================================
   * std::vector<int> nums = {1, 2, 3, 1, 4, 2, 5, 6, 3, 7};
   * std::unordered_set<int> uniqueNums(nums.begin(), nums.end()); // 自动去除重复元素，所以uniqueNums中只会包含唯一的元素
   * std::vector<int> result(uniqueNums.begin(), uniqueNums.end());
   * ====================================
5. 频率统计：需要统计元素出现的频率，例如统计单词出现的次数
   * 键为元素，值为出现次数，可以高效地完成频率统计
   * ====================================
   *  for (char ch : text) { if (ch != ' ') frequency[ch]++; } // 遍历字符串，统计每个字符的出现次数
   * ====================================
