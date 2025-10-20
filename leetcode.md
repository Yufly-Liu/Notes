#### 1.string s中的s.substr(pos, len) 的第二个参数是长度（length）；

![image-20251013101753688](D:\LIUYUFEI\code\Notes\assets\image-20251013101753688.png)

---



#### 2.`isdigit()` 是一个简单但非常有用的函数，用于判断单个字符是否为 0-9 的数字。使用时注意：

- 包含头文件 `<ctype.h>`（C）或 `<cctype>`（C++）。
- 处理有符号字符时使用 `(unsigned char)` 转换。
- 它只判断单个字符，不解析整个数字字符串。

---



#### 3.string s字符串可以做压入操作，s.push_back('(')代表着向字符串s中压入字符‘(’;

---



#### 4.string count(26, 0);

- 这是 `std::string` 的构造函数：`string(size_t n, char c)`
  - `n`: 要创建的字符个数
  - `c`: 要填充的字符
- 你传入的是 `0`，这是一个 **整数字面量**，会被当作 `char` 类型的 `0`，即 `'\0'`（空字符），**不是字符 `'0'`**。

所以这行代码创建的是：

```
"\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"
```

一个包含 26 个空字符的字符串，长度为 26，但打印出来是空的。

---



#### 5.leetcode49

给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。

 

**示例 1:**

**输入:** strs = ["eat", "tea", "tan", "ate", "nat", "bat"]

**输出:** [["bat"],["nat","tan"],["ate","eat","tea"]]

**解释：**

- 在 strs 中没有字符串可以通过重新排列来形成 `"bat"`。
- 字符串 `"nat"` 和 `"tan"` 是字母异位词，因为它们可以重新排列以形成彼此。
- 字符串 `"ate"` ，`"eat"` 和 `"tea"` 是字母异位词，因为它们可以重新排列以形成彼此。

**示例 2:**

**输入:** strs = [""]

**输出:** [[""]]

**示例 3:**

**输入:** strs = ["a"]

**输出:** [["a"]]

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map<string,vector<string>> U_map;
        for(auto& str:strs){
            string count(26,0);
            for(auto& c : str){
                count[c - 'a']++;
            }
            U_map[count].push_back(str);
        }
        for(auto& pair:U_map){
            res.push_back(pair.second);
        }
        return res;
    }
};
```

---



#### 6.**C++ 中使用迭代器构造 vector 的正确语法**

```c++
vector<int> left_pre(preorder.begin()+1, preorder.begin()+index+1);
```

这行代码的作用是：

> 从 `preorder` 向量中，**复制一个子区间**，创建一个新的 `vector<int>`，命名为 `left_pre`。

#### 🧩 参数说明（vector 构造函数）

`vector(first, last)`：用迭代器 `[first, last)` 范围内的元素初始化新 vector。

- **`first`**: 起始位置（包含）
- **`last`**: 结束位置（**不包含**），左闭右开区间 `[first, last)`

#### 📌 具体分析

| 表达式                         | 含义                                              |
| ------------------------------ | ------------------------------------------------- |
| `preorder.begin()`             | 指向 `preorder` 第一个元素                        |
| `preorder.begin() + 1`         | 指向**第二个元素**（跳过根节点）                  |
| `preorder.begin() + index + 1` | 指向中序遍历中根节点位置 `index` 的**下一个位置** |



---



#### 7.`merged` 是一个 `vector<vector<int>>` 或 `vector<pair<int,int>>`

##### （1） `merged.back()`

- 返回 `merged` 中**最后一个区间**（即最近合并的区间）。
- 例如：如果 `merged = [[1,3], [4,6]]`，那么 `merged.back() = [4,6]`

##### （2） `merged.back()[1]`

- 取出最后一个区间的**右端点**。
- 上例中：`merged.back()[1] = 6`

##### （3）lambda表达式

```c++
sort(intervals.begin(), intervals.end(), [](const vector<int>& a, const vector<int>& b) {
    return a[0] < b[0]; // 按左端点排序
});
```

###### 1. `sort(..., ..., comparator)`

- 这是 `std::sort` 的三参数版本：
  - `first`: 起始迭代器
  - `last`: 结束迭代器
  - `comparator`: 自定义比较函数（或 lambda）

###### 2. `[](const vector<int>& a, const vector<int>& b) { ... }`

- 这是一个 **Lambda 表达式**，定义了一个匿名函数。
- `const vector<int>&`：使用常量引用，避免拷贝，提高效率。
- `a` 和 `b` 是两个区间（每个是 `vector<int>{left, right}`）。

###### 3. `return a[0] < b[0];`

- 比较两个区间的**左端点**。
- 如果 `a` 的左端点小于 `b` 的左端点，则 `a` 排在 `b` 前面。

----



#### 8.res.insert(res.begin() + person[1], person);

这是 **“根据身高重建队列”**（LeetCode 406）问题中的**核心操作**，它实现了“**插队**”的逻辑。

我们来一步步拆解它的含义、作用和背后的算法思想。

### 📌 语境回顾

- `res`：正在构建的队列（初始为空）
- `person`：当前处理的人，格式为 `[h, k]`
  - `h`：身高
  - `k`：前面有 `k` 个身高 ≥ `h` 的人

在执行这行代码前，`people` 数组已经按以下规则排序：

1. 身高从高到低
2. 身高相同时，`k` 从小到大