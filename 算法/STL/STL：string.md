### 1. 声明与初始化 (Initialization)

在竞赛中，`string` 的初始化非常灵活。

- **基本声明：** `string s;` (空字符串)
    
- **直接赋值：** `string s = "hello";`
    
- **字符填充：** `string s(5, 'a');` (生成 `"aaaaa"`，⚠️ 注意：单引号才是字符，不能写成双引号)
    
- **复制初始化：** `string s2(s1);`
    
- **截取初始化：** `string s3(s1, 2, 3);` (从 `s1` 的下标 2 开始，截取 3 个字符复制给 `s3`)
    

### 2. 字符串比较 (Comparison) 

竞赛中经常需要比较字典序，`string` 直接重载了关系运算符，这是它比 C 语言 `strcmp` 好用一万倍的地方。

- **直接比较：** `==` 和 `!=` (判断两字符串内容是否完全相同)
    
- **字典序比较：** `<`, `<=`, `>`, `>=` (按照字典序逐个字符比较，常用于排序)

```
string a = "apple";
string b = "banana";
if (a < b) {
    // a 的字典序在 b 前面，条件成立
}
```

### 3. 核心操作：增、删、改、查 (CRUD)

> **💡 核心认知：** 抛弃 `<algorithm>`！`string` 的增删改查绝大多数依靠自带的成员函数，且参数风格通常是 `(起始下标, 作用长度)`。

#### 3.1 增 (Add)

- **尾部追加 (最常用)：** `s += " world";` 或 `s += '!';` (重载了 `+` 号，支持接字符串或单字符，均摊时间复杂度 $O(1)$)
    
- **尾部追加单字符：** `s.push_back('a');`
    
- **指定位置插入：** `s.insert(pos, "str");` (在下标 `pos` 处插入整个字符串 `"str"`)
    
- **多字符插入：** `s.insert(pos, 3, 'a');` (在下标 `pos` 处插入 3 个字符 `'a'`)
    

#### 3.2 删 (Delete)

- **尾部删除：** `s.pop_back();` (删除最后一个字符，C++11 引入，$O(1)$)
    
- **指定位置删除 (erase)：** ⚠️ **避坑：** `vector` 的 `erase` 是传迭代器，而 `string` 是传 `(起始下标, 删除长度)`！
    
    - `s.erase(pos, len);` (从下标 `pos` 开始，往后删除 `len` 个字符)
        
    - `s.erase(pos);` (如果不写长度，默认从 `pos` 一直删到字符串结尾)
        
- **清空：** `s.clear();`
    

#### 3.3 改 (Update / Modify)

- **单点修改：** `s[i] = 'X';` (直接通过下标覆盖，速度最快)
    
- **区间替换 (replace)：** `s.replace(pos, len, "new_str");` (从下标 `pos` 开始，把接下来的 `len` 个字符替换成 `"new_str"`)
    
    - **注意：** 替换的字符串长度不需要和原来的 `len` 相同，`string` 会自动帮你调整容量和后续字符的位置。
        
- **💡 占位符技巧：** 如果执行大量删改，`erase` 和 `replace` 的底层平移耗时极高。可以使用特殊字符（如 `#` 或 `\1`）作为懒惰删除（Lazy Deletion）的标记。
```
string s = "abcde";
// 不真正删除 "bcd"，而是打上占位标记避免后续字符移动
s.replace(1, 3, "###"); 
// 输出时遇到 '#' 也就是原先被替代/删除的部分，直接跳过即可
```

#### 3.4 查 (Query / Read)

不要用 `std::find`！用 `string` 自带的查找。

- **寻找子串 (find)：**

```
string s = "hello world";
size_t pos = s.find("world"); 
if (pos != string::npos) { 
    // ⚠️ 必须用 string::npos 判断是否找到，npos 代表一个不存在的巨大正数
    cout << "找到了，起点下标是: " << pos << "\n";
}
```

- **从指定位置往后找：** `s.find("o", 5);` (从下标 5 开始向右找字母 `'o'`)
    
- **逆向寻找 (rfind)：** `s.rfind("o");` (从右往左找，返回最后一次出现的下标)
    

### 4. 特殊输入输出与流处理 (I/O & Stream) 

- **读入遇到空格停止：** `cin >> s;` (最基础的读入，但遇到空格或换行会断开)
    
- **读入整行 (包含空格)：** `getline(cin, s);` (竞赛处理带空格的句子必备)
    
- **字符串切割 (stringstream)：** 包含 `#include <sstream>`，可以像 `cin` 一样把字符串里的单词按空格切分出来，非常强大。

```
string s = "apple banana orange";
stringstream ss(s);
string word;
while (ss >> word) {
    cout << word << "\n"; // 依次输出三个单词
}
```

### 5. 元素访问与状态检查 (Access & State)

- **获取长度：** `s.length()` 或 `s.size()` (两者完全等价，为了和 vector 统一，常敲 `s.size()`)
    
- **下标访问：** `s[i]`
    
- **首尾字符：** `s.front()` 和 `s.back()`
    
- **判空：** `s.empty()`
    
- **与 C 语言交互：** `s.c_str()` (很多老式函数或 `printf("%s")` 只认识 C 风格的字符数组，必须用它转换)
    

### 6. 字符串专属神技 (String Specific Magic)

这部分是 `vector` 没有的，也是刷题时最能救命的 API。

- **提取子串 (substr)：** * `s.substr(pos, len);` (从 `pos` 开始提取长度为 `len` 的子串，**返回一个新的 string**，不改变原串)
    
    - `s.substr(pos);` (只给起点，提取从 `pos` 到末尾的所有内容)
        
- **字符串与数字互转 (C++11)：** * `int num = stoi(s);` (String TO Int)
    
    - `long long num = stoll(s);` (String TO Long Long)
        
    - `double num = stod(s);`
        
    - `string s2 = to_string(12345);` (将数字直接变成字符串)
        

### 7. 配合 STL 算法神技 (The "Magic" of STL)

虽然增删改查不用 `<algorithm>`，但因为 `string` 支持迭代器 `s.begin()` 和 `s.end()`，所以大部分泛型算法对它都有效。

- **翻转字符串 (判断回文串必备)：** `reverse(s.begin(), s.end());`
    
- **对字符串里的字符排序：** `sort(s.begin(), s.end());` (按字典序重排字符，可用于判断异位词)
    
- **统计特定字符出现次数：** `int cnt = count(s.begin(), s.end(), 'a');`
    
- **全转大写/小写：** ```cpp
    
    // 语法：transform(起点, 终点, 写入位置, 操作操作)
    
    transform(s.begin(), s.end(), s.begin(), ::tolower); // 全转小写
    
    transform(s.begin(), s.end(), s.begin(), ::toupper); // 全转大写