
哈希表（Hash Table）是一种用于实现**键值对存储**的数据结构。它允许在常数时间内进行插入、删除和查找操作。哈希表的核心思想是通过哈希函数将键映射到数组中的一个位置，从而实现高效的查找。

<hr>

## :memo: Data Structures & Algorithms :black_nib:

### 哈希表能解决什么问题？一般什么时候想到使用哈希表?

一般哈希表都是用来快速判断一个元素是否出现集合里:

1. 快速查找：需要在大量数据中快速查找某个特定元素 >> std::unordered_map 或 std::unordered_set
2. 快速插入和删除: 需要频繁地进行数据的插入和删除操作 >> 哈希表在插入和删除操作上也有常数时间复杂度
3. 键值对存储：需要将数据以键值对的形式存储和访问 >> std::unordered_map 提供了高效的键值对存储机制
4. 数据去重：需要检查并确保数据集合中的元素唯一 >> 可以快速去除重复元素，保持集合中的元素唯一性

### 在C++中,常见的数据结构 >> 数组 set map

<img src="https://github.com/Youn8ch/Leaning_notes/blob/master/imgsource/img.png" alt="img" style="float: left;" />
<img src="https://github.com/Youn8ch/Leaning_notes/blob/master/imgsource/img_1.png" alt="img" style="float: left;" />


