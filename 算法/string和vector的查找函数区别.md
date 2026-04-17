
| **需求** &nbsp; &nbsp; &nbsp; &nbsp; <br> &nbsp; &nbsp; &nbsp;<br> | **对于 string 怎么写？** &nbsp; &nbsp; &nbsp; | **对于 vector 怎么写？**                                        |
| ---------------------------------------------------------------- | --------------------------------------- | --------------------------------------------------------- |
| **找单个元素** <br> <br>                                              | `s.find('a')`                           | `std::find(v.begin(), v.end(), 5)`                        |
| **找一串元素** <br> <br>                                              | `s.find("abc")`                         | `std::search(v.begin(), v.end(), sub.begin(), sub.end())` |
| **返回值**<br>  <br>                                                | 下标 (`size_t`)                           | 迭代器                                                       |
### 1. 返回值到底是什么？(Return Values)

最核心的区别在于：`string` 操作的是**下标**，而 `vector`（通过 `<algorithm>`）操作的是**迭代器**。

- **`string::find` (亲儿子专属)**
    
    - **返回类型**：`size_t`（无符号大整数，代表物理下标位置）。
        
    - **能用 `auto` 吗**：✅ **必须可以，且极度推荐。** `auto pos = s.find(...);` 编译器会自动推导为 `size_t`。
        
- **`std::find` (找单个元素)**
    
    - **返回类型**：迭代器（Iterator）。具体来说是 `vector<T>::iterator`。
        
    - **能用 `auto` 吗**：✅ **必须可以，且极度推荐。** 因为手写 `vector<int>::iterator it = ...` 实在太长了，`auto it = std::find(...);` 是最标准的神仙写法。
        
- **`std::search` (找一段序列)**
    
    - **返回类型**：迭代器（Iterator）。同样是 `vector<T>::iterator`。
        
    - **能用 `auto` 吗**：✅ **同上，强烈推荐使用 `auto`。**
        

---

### 2. 如何判断“没找到”？(Not Found Checks)

判断没找到的逻辑，同样分为“下标派”和“迭代器派”。

- **`string::find` (下标派)**
    
    - **标志**：`string::npos` (一个巨大的无符号整数常量)。
        
    - **判断语法**：
```
        auto pos = s.find("abc");
        if (pos == string::npos) {
            // 没找到
        }
```

- **`std::find` & `std::search` (迭代器派)**
    
    - **标志**：传入的**结束迭代器**。在绝大多数情况下，就是容器的 `.end()`。
        
    - **判断语法**：

    ```
        auto it = std::find(v.begin(), v.end(), 5);
        // 如果迭代器一路狂奔到了终点都没停下，说明没找到
        if (it == v.end()) { 
            // 没找到
        }
    ```


---

### 3. 如何接着向下找？(Continuous Searching)

这是算法题中最容易写错死循环的地方！**“接着找”的核心逻辑是：更新起点。**

#### A. `string::find` 接着找 (通过更新下标起点)

`string::find` 有第二个参数，用来指定“从哪个下标开始找”。

**核心公式**：`下一次寻找的起点 = 这一次找到的下标 + 目标的长度`

```
#include <iostream>
#include <string>
using namespace std;

int main() {
    string txt = "AABB AABB AABB";
    string target = "AABB";
    
    auto pos = txt.find(target); // 第一次找（默认从 0 开始）
    
    while (pos != string::npos) {
        cout << "找到了！位置在: " << pos << "\n";
        
        // 关键一步：更新起点！
        // 如果不加 target.length()，每次都会在同一个地方找到，瞬间死循环！
        pos = txt.find(target, pos + target.length()); 
    }
    return 0;
}
```

#### B. `std::find` 接着找 (通过更新迭代器起点)

`std::find` 接收一个范围 `[起点, 终点)`。我们要更新的，就是下一次搜索的起点迭代器。

**核心公式**：`下一次寻找的起点迭代器 = 这一次找到的迭代器 + 1` (因为找的是单个元素，所以只往后挪一步)。

```
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> v = {1, 2, 5, 3, 5, 4, 5};
    int target = 5;
    
    auto it = std::find(v.begin(), v.end(), target); // 第一次找
    
    while (it != v.end()) {
        cout << "找到了！下标在: " << it - v.begin() << "\n";
        
        // 关键一步：更新起点迭代器！
        // 让新的起点从上一次找到的位置往后走一步
        it = std::find(it + 1, v.end(), target); 
    }
    return 0;
}
```

#### C. `std::search` 接着找 (通过更新迭代器起点)

逻辑和 `std::find` 一模一样，唯一的区别是跨越的步长。

**核心公式**：`下一次寻找的起点迭代器 = 这一次找到的迭代器 + 子序列的长度`。

```
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> haystack = {1, 2, 3,  9,  1, 2, 3,  8};
    vector<int> needle = {1, 2, 3};
    
    auto it = std::search(haystack.begin(), haystack.end(), needle.begin(), needle.end());
    
    while (it != haystack.end()) {
        cout << "找到子序列！起始下标在: " << it - haystack.begin() << "\n";
        
        // 关键一步：更新起点迭代器！
        // 跨过整个刚找到的子序列
        it = std::search(it + needle.size(), haystack.end(), needle.begin(), needle.end()); 
    }
    return 0;
}
```

### 🌟 总结必背口诀

1. **返回值怎么接？** 别管三七二十一，**无脑用 `auto` 接！** 编译器比你懂。
    
2. **怎么判断没找到？** `string` 认准 `== string::npos`；`vector` 认准 `== v.end()`。
    
3. **怎么继续找？** 永远记住**“在旧位置的基础上跨过去”**，切忌原地踏步导致死循环！