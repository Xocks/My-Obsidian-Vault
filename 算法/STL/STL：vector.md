### 1. 声明与初始化 (Initialization)

在竞赛中，快速且准确地初始化容器是第一步。

- **基本声明：** `vector<int> v;` (空容器)
    
- **指定大小：** `vector<int> v(n);` (创建长度为 `n` 的数组，默认初值为 0)
    
- **指定大小并赋初值：** `vector<int> v(n, -1);` (长度为 `n`，全初始化为 -1)
    
- **复制初始化：** `vector<int> v2(v1);` (将 `v1` 的内容完整拷贝给 `v2`)
    
- **区间初始化：** `vector<int> v(a, a + n);` (将普通数组 `a` 的前 `n` 个元素放入 vector)
    
- **二维动态数组：** `vector<vector<int>> g(n, vector<int>(m, 0));` (创建 $n \times m$ 且初始全为 0 的矩阵)
    

### 2. 元素访问与状态检查 (Access & Capacity)

- **下标访问：** `v[i]` (最常用，不检查越界，速度最快)
    
- **首尾元素：** `v.front()` 和 `v.back()` (返回引用，常用 `v.back()` 获取最后一个数)
    
- **获取大小：** `v.size()` (返回 `size_t`，注意在做 `v.size() - 1` 计算时，若 size 为 0 会产生巨大的正数，建议强转 `(int)v.size()`)
    
- **判空：** `v.empty()` (比 `v.size() == 0` 更规范)
    

### 3. 修改操作 (Modification)

- **尾部增删：** `push_back(x)` 和 `pop_back()` (时间复杂度 $O(1)$，竞赛最常用)
    
- **插入：** `v.insert(it, x)` (在迭代器 `it` 处插入 `x`，后面元素右移，$O(N)$)
    
- **删除：** * `v.erase(it)` (删除 `it` 处的元素，$O(N)$)
    
    - `v.erase(first, last)` (删除区间 `[first, last)` 内的元素)
        
- **清空：** `v.clear()` (清空所有元素，但**不会**释放内存，容量 `capacity` 不变)
    
- **重置：** `v.assign(n, val)` (将内容替换为 `n` 个 `val`)
    

### 4. 竞赛进阶语法 (Optimization & Advanced)

- **空间预留：** `v.reserve(n)`
    
    - **核心痛点：** `vector` 扩容时会申请双倍空间并搬运数据。如果你已知数据量，先 `reserve(n)` 可以避免多次扩容，显著减少运行时间。
        
- **大小调整：** `v.resize(n, val)`
    
    - 改变 `size()`。如果新 `n` 比原来大，会填充 `val`；如果小，会截断。
        
- **快速释放内存：** `vector<int>().swap(v);` (利用一个临时的空 vector 与 `v` 交换，彻底释放 `v` 占用的内存。在内存限制极严的题目中处理大型全局变量时有用)
    

### 5. 配合 STL 算法 (The "Magic" of STL)

`vector` 的强大在于它是很多算法的载体：

- **排序：** `sort(v.begin(), v.end());`
    
- **去重：** ```sort(v.begin(), v.end());
    
	 v.erase(unique(v.begin(), v.end()), v.end());
    
- **查找：** `lower_bound(v.begin(), v.end(), x)` (返回第一个 $\ge x$ 的迭代器)
    
- **反转：** `reverse(v.begin(), v.end());`