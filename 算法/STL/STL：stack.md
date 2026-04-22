
```
#include <iostream>
#include <stack>
using namespace std;

int main() {
    // 1. 声明一个存数字的薯片筒
    stack<int> s; 

    // 2. 塞薯片 (push) - 只能放在最上面
    s.push(10); 
    s.push(20); 
    // 现在筒里从下到上是：10, 20

    // 3. 看一眼最上面的薯片是谁 (top)
    // 注意：top 只是看一眼，不会把它拿走！
    cout << s.top() << endl; // 输出 20

    // 4. 拿走最上面的薯片 (pop)
    // 注意：pop 是一个动作，它没有返回值，仅仅是把最上面的扔掉！
    s.pop(); 
    // 刚才的 20 被扔掉了，现在筒里只剩下 10

    // 5. 看看筒里还有几片薯片 (size) 和 是不是空了 (empty)
    cout << s.size() << endl; // 输出 1 (里面只有 10)
    
    if (s.empty()) {
        cout << "薯片筒空了！" << endl;
    }
    
    return 0;
}
```
