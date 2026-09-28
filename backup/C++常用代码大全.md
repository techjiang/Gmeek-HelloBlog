涵盖基础语法、数据类型、流程控制、函数、数组与字符串、指针与引用、面向对象、STL、文件操作、异常处理、多线程及实用技巧。

---

## 一、基础语法

```cpp
#include <iostream>
using namespace std;

int main() {
    // 输出
    cout << "Hello, World!" << endl;       // endl 换行并刷新缓冲区
    cout << "Hello" << "\n";               // \n 换行（更快）

    // 多值输出
    cout << "Name: " << name << ", Age: " << age << endl;

    // 格式化输出（C 风格）
    printf("Name: %s, Age: %d, PI: %.2f\n", name.c_str(), age, 3.14159);

    // 输入
    int x;
    cin >> x;

    string s;
    getline(cin, s);           // 读取整行（含空格）

    return 0;
}
```

**编译与运行：**
```bash
g++ -o main main.cpp          # 编译
./main                         # 运行
g++ -std=c++17 -O2 -Wall main.cpp -o main   # 推荐编译选项
g++ -std=c++20 main.cpp -o main             # C++20
```

---

## 二、数据类型

### 基本类型

```cpp
// 整型
int a = 10;                    // 通常 4 字节
short b = 100;                 // 2 字节
long c = 100000;               // ≥4 字节
long long d = 10000000000;     // 8 字节
unsigned int e = 42;           // 无符号

// 浮点型
float f = 3.14f;               // 4 字节
double g = 3.14159265358979;   // 8 字节

// 字符型
char ch = 'A';                 // 1 字节
wchar_t wch = L'A';            // 宽字符

// 布尔型
bool flag = true;              // 1 字节

// 自动类型推断（C++11）
auto x = 10;                   // int
auto y = 3.14;                 // double
auto s = "hello";              // const char*
auto v = vector<int>{1, 2, 3}; // vector<int>

// 常量
const int MAX = 100;
constexpr int SIZE = 256;      // 编译期常量（C++11）
#define PI 3.14159             // 宏常量（不推荐）
```

### 类型转换

```cpp
// C++ 风格（推荐）
static_cast<int>(3.14)          // 数值转换
dynamic_cast<Derived*>(base)    // 多态安全转换
const_cast<int*>(ptr)           // 去除 const
reinterpret_cast<void*>(ptr)    // 底层重新解释

// C 风格
(int)3.14
```

---

## 三、流程控制

```cpp
// 条件判断
if (score >= 90) {
    grade = 'A';
} else if (score >= 80) {
    grade = 'B';
} else {
    grade = 'C';
}

// 三元表达式
int result = (score >= 60) ? 1 : 0;

// switch
switch (option) {
    case 1:
        cout << "选项1" << endl;
        break;
    case 2:
        cout << "选项2" << endl;
        break;
    default:
        cout << "未知" << endl;
        break;
}

// for 循环
for (int i = 0; i < 10; i++) {
    cout << i << endl;
}

// for-each（范围 for，C++11）
vector<int> v = {1, 2, 3, 4, 5};
for (int x : v) {
    cout << x << endl;
}

for (auto& item : v) {
    item *= 2;                 // 修改原元素
}

// while 循环
while (condition) {
    // ...
}

// do-while
do {
    // ...
} while (condition);

// break / continue / goto（尽量避免 goto）
for (int i = 0; i < 10; i++) {
    if (i == 5) break;
    if (i % 2 == 0) continue;
    cout << i << endl;
}
```

---

## 四、函数

```cpp
// 基本函数
int add(int a, int b) {
    return a + b;
}

// 默认参数
void greet(string name = "World") {
    cout << "Hello, " << name << endl;
}

// 函数重载
int add(int a, int b) { return a + b; }
double add(double a, double b) { return a + b; }

// 引用参数（避免拷贝）
void swap(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}

// const 引用参数（只读，避免拷贝）
void print(const vector<int>& v) {
    for (int x : v) cout << x << " ";
}

// 指针参数
void modify(int* ptr) {
    *ptr = 100;
}

// 内联函数
inline int square(int x) { return x * x; }

// 递归
int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}

// 函数指针
int (*funcPtr)(int, int) = add;
int result = funcPtr(3, 4);

// Lambda 表达式（C++11）
auto add = [](int a, int b) -> int {
    return a + b;
};
cout << add(1, 2) << endl;

// 捕获外部变量
int x = 10, y = 20;
auto f1 = [x, y]() { return x + y; };       // 值捕获
auto f2 = [&x, &y]() { x += y; };           // 引用捕获
auto f3 = [=]() { return x + y; };          // 捕获所有外部变量（值）
auto f4 = [&]() { x += y; };               // 捕获所有外部变量（引用）
auto f5 = [x, &y]() mutable { x++; return x + y; };  // 可修改值捕获

// 函数对象（仿函数）
struct Adder {
    int operator()(int a, int b) const {
        return a + b;
    }
};
Adder addFunc;
cout << addFunc(1, 2) << endl;

// std::function（C++11）
#include <functional>
function<int(int, int)> func = add;
function<int(int, int)> lambda = [](int a, int b) { return a + b; };
```

---

## 五、数组与字符串

### 数组

```cpp
// 静态数组
int arr[5] = {1, 2, 3, 4, 5};
int arr2[] = {1, 2, 3};               // 自动确定大小
int arr3[5] = {0};                    // 全部初始化为 0

// 访问
arr[0] = 10;
int len = sizeof(arr) / sizeof(arr[0]);   // 数组长度

// 多维数组
int matrix[3][4] = {
    {1, 2, 3, 4},
    {5, 6, 7, 8},
    {9, 10, 11, 12}
};
matrix[1][2] = 100;

// 动态数组（C++11，推荐）
int* dynamicArr = new int[5];
delete[] dynamicArr;

// 推荐使用 vector
#include <vector>
vector<int> vec = {1, 2, 3, 4, 5};
```

### 字符串

```cpp
#include <string>

// 创建
string s1 = "Hello";
string s2("World");
string s3 = s1 + ", " + s2;       // 拼接

// 常用操作
s1.size();               // 长度
s1.length();             // 长度（同上）
s1.empty();              // 是否为空
s1[0];                   // 索引访问
s1.at(0);                // 带边界检查的访问
s1.front();              // 首字符
s1.back();               // 末字符

// 修改
s1.append(" C++");       // 追加
s1 += "!!!";             // 追加
s1.insert(5, " World");  // 插入
s1.erase(5, 6);          // 删除（位置，长度）
s1.replace(0, 5, "Hi");  // 替换
s1.clear();              // 清空

// 查找
s1.find("World");        // 查找子串（返回位置，未找到返回 string::npos）
s1.rfind("o");           // 从后查找
s1.find_first_of("aeiou");   // 查找任一字符
s1.substr(0, 5);         // 截取子串

// 比较
s1 == s2;                // 比较
s1.compare(s2);          // 比较（返回 <0, 0, >0）

// 转换
stoi("123");             // string → int
stol("123");             // string → long
stod("3.14");            // string → double
to_string(123);          // int → string

// C 风格字符串
const char* cs = "Hello";
char buf[256];
strcpy(buf, cs);         // 复制
strcat(buf, " World");   // 追加
strlen(cs);              // 长度
strcmp(cs, "Hello");     // 比较
sprintf(buf, "Value: %d", 42);  // 格式化
snprintf(buf, sizeof(buf), "Value: %d", 42);  // 安全格式化

// string ↔ const char*
string str = "Hello";
const char* cstr = str.c_str();
string from_cstr(cstr);

// 遍历
for (char c : s1) {
    cout << c;
}

// 一行读入
string line;
getline(cin, line);
```

---

## 六、指针与引用

```cpp
// 指针
int x = 10;
int* ptr = &x;               // 取地址
int val = *ptr;              // 解引用（val = 10）
*ptr = 20;                   // 修改 x 为 20

// 空指针
int* p1 = nullptr;           // C++11（推荐）
int* p2 = NULL;              // C 风格（不推荐）
int* p3 = 0;                 // 不推荐

// 指针与数组
int arr[5] = {1, 2, 3, 4, 5};
int* p = arr;                // 数组名即首地址
p[0]                         // 等价于 arr[0]
*(p + 2)                     // 等价于 arr[2]

// 指向指针的指针
int** pp = &ptr;

// 动态内存
int* p = new int(42);        // 分配并初始化
delete p;                     // 释放

int* arr = new int[10];      // 分配数组
delete[] arr;                 // 释放数组

// 智能指针（C++11，推荐避免手动管理内存）
#include <memory>

// unique_ptr：独占所有权
unique_ptr<int> uptr = make_unique<int>(42);
unique_ptr<int[]> uarr = make_unique<int[]>(10);

// shared_ptr：共享所有权（引用计数）
shared_ptr<int> sptr = make_shared<int>(42);
shared_ptr<int> sptr2 = sptr;   // 引用计数 +1

// weak_ptr：弱引用（不增加引用计数）
weak_ptr<int> wptr = sptr;

// 引用
int& ref = x;                // 引用（必须初始化，不能改绑）
ref = 30;                    // 等价于 x = 30

// const 引用
const int& cref = 42;        // 可绑定临时值

// 引用 vs 指针
// 引用：必须初始化、不能改绑、无空引用、用 . 访问
// 指针：可为空、可改指、有算术、用 -> 访问
```

---

## 七、结构体与联合体

```cpp
// 结构体
struct Point {
    double x;
    double y;

    // 构造函数
    Point() : x(0), y(0) {}
    Point(double x, double y) : x(x), y(y) {}

    // 方法
    double distance(const Point& other) const {
        double dx = x - other.x;
        double dy = y - other.y;
        return sqrt(dx * dx + dy * dy);
    }

    // 运算符重载
    Point operator+(const Point& other) const {
        return Point(x + other.x, y + other.y);
    }
};

Point p1(1, 2), p2(4, 6);
Point p3 = p1 + p2;

// 联合体（所有成员共享同一块内存）
union Data {
    int i;
    float f;
    char str[20];
};

// 枚举
enum Color { RED, GREEN, BLUE };               // 旧式
enum class Color : int { Red, Green, Blue };    // 强类型枚举（C++11，推荐）
Color c = Color::Red;

// typedef / using
typedef unsigned long ulong;
using ulong = unsigned long;                   // C++11（推荐）
using VecInt = vector<int>;
```

---

## 八、面向对象编程

```cpp
#include <iostream>
#include <string>
using namespace std;

// 基类
class Animal {
protected:                        // 受保护成员
    string name;
    int age;

public:
    // 构造函数
    Animal(const string& name, int age) : name(name), age(age) {
        cout << "Animal created" << endl;
    }

    // 虚析构函数（基类必须有）
    virtual ~Animal() {
        cout << "Animal destroyed" << endl;
    }

    // 拷贝构造
    Animal(const Animal& other) : name(other.name), age(other.age) {}

    // 移动构造（C++11）
    Animal(Animal&& other) noexcept : name(move(other.name)), age(other.age) {
        other.age = 0;
    }

    // 拷贝赋值
    Animal& operator=(const Animal& other) {
        if (this != &other) {
            name = other.name;
            age = other.age;
        }
        return *this;
    }

    // 虚函数（支持多态）
    virtual void speak() const {
        cout << name << " makes a sound" << endl;
    }

    // 纯虚函数（抽象方法）
    // virtual void move() const = 0;

    // 普通方法
    string getName() const { return name; }
    int getAge() const { return age; }

    // 友元函数
    friend ostream& operator<<(ostream& os, const Animal& a);
};

// 友元函数实现
ostream& operator<<(ostream& os, const Animal& a) {
    os << "Animal(" << a.name << ", " << a.age << ")";
    return os;
}

// 派生类（继承）
class Dog : public Animal {
private:
    string breed;

public:
    Dog(const string& name, int age, const string& breed)
        : Animal(name, age), breed(breed) {}

    // 重写虚函数
    void speak() const override {
        cout << name << " barks!" << endl;
    }

    void fetch() const {
        cout << name << " fetches the ball" << endl;
    }
};

class Cat : public Animal {
public:
    Cat(const string& name, int age) : Animal(name, age) {}

    void speak() const override {
        cout << name << " meows!" << endl;
    }
};

// 多态
int main() {
    Animal* animals[] = {
        new Dog("Buddy", 5, "Labrador"),
        new Cat("Whiskers", 3)
    };

    for (const auto& a : animals) {
        a->speak();               // 调用各自版本
    }

    for (const auto& a : animals) {
        delete a;
    }

    return 0;
}

// 抽象类
class Shape {
public:
    virtual double area() const = 0;           // 纯虚函数
    virtual double perimeter() const = 0;
    virtual ~Shape() = default;
};

class Circle : public Shape {
    double radius;
public:
    Circle(double r) : radius(r) {}
    double area() const override { return 3.14159 * radius * radius; }
    double perimeter() const override { return 2 * 3.14159 * radius; }
};

// 运算符重载
class Vector2D {
public:
    double x, y;

    Vector2D(double x = 0, double y = 0) : x(x), y(y) {}

    Vector2D operator+(const Vector2D& v) const {
        return Vector2D(x + v.x, y + v.y);
    }

    Vector2D operator-(const Vector2D& v) const {
        return Vector2D(x - v.x, y - v.y);
    }

    bool operator==(const Vector2D& v) const {
        return x == v.x && y == v.y;
    }

    Vector2D& operator+=(const Vector2D& v) {
        x += v.x;
        y += v.y;
        return *this;
    }

    double operator*(const Vector2D& v) const {   // 点积
        return x * v.x + y * v.y;
    }

    friend ostream& operator<<(ostream& os, const Vector2D& v) {
        os << "(" << v.x << ", " << v.y << ")";
        return os;
    }
};

// 模板类
template<typename T>
class Container {
private:
    vector<T> data;
public:
    void add(const T& item) { data.push_back(item); }
    T get(int index) const { return data[index]; }
    int size() const { return data.size(); }
};

// 静态成员
class Counter {
public:
    static int count;
    Counter() { count++; }
    ~Counter() { count--; }
    static int getCount() { return count; }
};
int Counter::count = 0;
```

---

## 九、模板

```cpp
// 函数模板
template<typename T>
T maximum(T a, T b) {
    return (a > b) ? a : b;
}

// 调用
maximum(3, 7);            // int
maximum(3.14, 2.72);      // double

// 多参数模板
template<typename T, typename U>
auto add(T a, U b) -> decltype(a + b) {
    return a + b;
}

// C++14 简化
template<typename T, typename U>
auto add(T a, U b) {
    return a + b;
}

// 类模板
template<typename T, int N>
class FixedArray {
private:
    T data[N];
public:
    T& operator[](int i) { return data[i]; }
    int size() const { return N; }
};

FixedArray<int, 10> arr;

// 可变参数模板（C++11）
template<typename... Args>
void print(Args... args) {
    (cout << ... << args) << endl;    // 折叠表达式（C++17）
}

// 模板特化
template<>
class Container<bool> {
    // bool 特化版本
};

// 类型萃取
template<typename T>
void process(T value) {
    if constexpr (is_integral_v<T>) {       // C++17
        cout << "Integer: " << value << endl;
    } else {
        cout << "Other: " << value << endl;
    }
}
```

---

## 十、STL（标准模板库）

### 容器总览

| 容器 | 头文件 | 特点 |
|------|--------|------|
| `vector` | `<vector>` | 动态数组，随机访问 O(1) |
| `list` | `<list>` | 双向链表，插入/删除 O(1) |
| `deque` | `<deque>` | 双端队列，两端操作 O(1) |
| `array` | `<array>` | 固定大小数组（C++11） |
| `set` / `multiset` | `<set>` | 有序集合（红黑树） |
| `map` / `multimap` | `<map>` | 有序键值对（红黑树） |
| `unordered_set` | `<unordered_set>` | 哈希集合（C++11） |
| `unordered_map` | `<unordered_map>` | 哈希键值对（C++11） |
| `stack` | `<stack>` | 栈（LIFO） |
| `queue` | `<queue>` | 队列（FIFO） |
| `priority_queue` | `<queue>` | 优先队列（最大堆） |

### vector（动态数组）

```cpp
#include <vector>
vector<int> v = {1, 2, 3, 4, 5};

// 增
v.push_back(6);               // 尾部添加
v.emplace_back(7);            // 就地构造（更高效）
v.insert(v.begin() + 2, 99);  // 指定位置插入

// 删
v.pop_back();                 // 尾部删除
v.erase(v.begin());           // 删除指定位置
v.erase(v.begin(), v.begin() + 2);   // 删除范围
v.clear();                    // 清空

// 查
v[0];                         // 索引访问
v.at(0);                      // 带边界检查
v.front();                    // 首元素
v.back();                     // 末元素
v.size();                     // 大小
v.empty();                    // 是否为空
v.capacity();                 // 容量

// 遍历
for (int x : v) cout << x << " ";
for (auto it = v.begin(); it != v.end(); ++it) cout << *it << " ";
for (auto it = v.rbegin(); it != v.rend(); ++it) cout << *it << " ";

// 排序与操作
#include <algorithm>
sort(v.begin(), v.end());
sort(v.begin(), v.end(), greater<int>());       // 降序
reverse(v.begin(), v.end());
unique(v.begin(), v.end());     // 相邻去重（先排序）
find(v.begin(), v.end(), 3);    // 查找
count(v.begin(), v.end(), 3);   // 计数
max_element(v.begin(), v.end());
min_element(v.begin(), v.end());
accumulate(v.begin(), v.end(), 0);   // 求和（<numeric>）
fill(v.begin(), v.end(), 0);
```

### string 与其他容器

```cpp
// array（固定大小）
#include <array>
array<int, 5> arr = {1, 2, 3, 4, 5};
arr.size();                   // 编译期大小

// list（链表）
#include <list>
list<int> lst = {1, 2, 3};
lst.push_front(0);
lst.push_back(4);
lst.sort();
lst.unique();

// deque（双端队列）
#include <deque>
deque<int> dq = {2, 3, 4};
dq.push_front(1);
dq.push_back(5);
```

### map 与 set

```cpp
#include <map>
#include <set>

// map（有序键值对）
map<string, int> m;
m["apple"] = 3;
m["banana"] = 5;
m.insert({"cherry", 7});
m.emplace("date", 10);

m["apple"];                   // 访问（不存在会创建）
m.at("apple");                // 访问（不存在抛异常）
m.count("apple");             // 0 或 1
m.find("apple");              // 迭代器
m.erase("apple");
m.size();

// 遍历
for (const auto& [key, value] : m) {    // 结构化绑定（C++17）
    cout << key << ": " << value << endl;
}

// set（有序集合，自动去重）
set<int> s = {3, 1, 4, 1, 5, 9, 2, 6};
s.insert(7);
s.erase(3);
s.count(4);                   // 0 或 1
s.find(5);

// multiset（允许重复）
multiset<int> ms = {1, 1, 2, 3, 3};
ms.count(1);                  // 2

// unordered_map（哈希表，O(1) 查找）
#include <unordered_map>
unordered_map<string, int> um;
um["key"] = 42;

// unordered_set
#include <unordered_set>
unordered_set<int> us = {1, 2, 3};
```

### stack / queue / priority_queue

```cpp
#include <stack>
#include <queue>

// stack（栈，LIFO）
stack<int> st;
st.push(1);
st.push(2);
st.top();                     // 2
st.pop();
st.size();
st.empty();

// queue（队列，FIFO）
queue<int> q;
q.push(1);
q.push(2);
q.front();                    // 1
q.back();                     // 2
q.pop();
q.size();

// priority_queue（优先队列，默认最大堆）
priority_queue<int> pq;                    // 最大堆
priority_queue<int, vector<int>, greater<int>> minPQ;   // 最小堆

pq.push(3);
pq.push(1);
pq.push(4);
pq.top();                     // 4（最大值）
pq.pop();

// 自定义优先级
struct Task {
    int priority;
    string name;
};

struct Compare {
    bool operator()(const Task& a, const Task& b) {
        return a.priority < b.priority;    // 大的优先
    }
};
priority_queue<Task, vector<Task>, Compare> taskPQ;
```

### 常用算法（<algorithm>）

```cpp
#include <algorithm>
#include <numeric>

vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};

// 查找
find(v.begin(), v.end(), 5);
find_if(v.begin(), v.end(), [](int x) { return x > 3; });
binary_search(v.begin(), v.end(), 5);    // 需先排序
lower_bound(v.begin(), v.end(), 3);      // 第一个 ≥ 3 的位置
upper_bound(v.begin(), v.end(), 3);      // 第一个 > 3 的位置

// 排序
sort(v.begin(), v.end());
sort(v.begin(), v.end(), greater<int>());
sort(v.begin(), v.end(), [](int a, int b) { return a > b; });
partial_sort(v.begin(), v.begin() + 3, v.end());   // 部分排序
stable_sort(v.begin(), v.end());                    // 稳定排序
nth_element(v.begin(), v.begin() + 3, v.end());    // 第 n 小

// 修改
reverse(v.begin(), v.end());
rotate(v.begin(), v.begin() + 2, v.end());   // 旋转
fill(v.begin(), v.end(), 0);
generate(v.begin(), v.end(), rand);
transform(v.begin(), v.end(), v.begin(), [](int x) { return x * 2; });
replace(v.begin(), v.end(), 1, 100);
remove_if(v.begin(), v.end(), [](int x) { return x < 3; });

// 集合操作（需有序）
vector<int> a = {1, 2, 3, 4}, b = {3, 4, 5, 6};
vector<int> result;
set_union(a.begin(), a.end(), b.begin(), b.end(), back_inserter(result));
set_intersection(a.begin(), a.end(), b.begin(), b.end(), back_inserter(result));

// 数值
accumulate(v.begin(), v.end(), 0);           // 求和
adjacent_difference(v.begin(), v.end(), result.begin());
inner_product(a.begin(), a.end(), b.begin(), 0);

// 其他
min_element(v.begin(), v.end());
max_element(v.begin(), v.end());
count(v.begin(), v.end(), 1);
count_if(v.begin(), v.end(), [](int x) { return x > 3; });
all_of(v.begin(), v.end(), [](int x) { return x > 0; });
any_of(v.begin(), v.end(), [](int x) { return x > 5; });
none_of(v.begin(), v.end(), [](int x) { return x < 0; });
for_each(v.begin(), v.end(), [](int x) { cout << x << " "; });
```

---

## 十一、文件操作（<fstream>）

```cpp
#include <fstream>
#include <string>
#include <iostream>
using namespace std;

// 写入文件
ofstream outFile("data.txt");           // 覆盖模式
if (outFile.is_open()) {
    outFile << "Hello, World!" << endl;
    outFile << "Line 2" << endl;
    outFile.close();
}

// 追加文件
ofstream appendFile("data.txt", ios::app);
appendFile << "Appended line" << endl;
appendFile.close();

// 读取文件
ifstream inFile("data.txt");
if (inFile.is_open()) {
    string line;
    while (getline(inFile, line)) {
        cout << line << endl;
    }
    inFile.close();
}

// 逐词读取
ifstream inFile("data.txt");
string word;
while (inFile >> word) {
    cout << word << endl;
}

// 读取全部内容
ifstream inFile("data.txt");
string content((istreambuf_iterator<char>(inFile)),
                istreambuf_iterator<char>());

// 二进制文件
ofstream binOut("data.bin", ios::binary);
int numbers[] = {1, 2, 3, 4, 5};
binOut.write(reinterpret_cast<char*>(numbers), sizeof(numbers));
binOut.close();

ifstream binIn("data.bin", ios::binary);
int readNumbers[5];
binIn.read(reinterpret_cast<char*>(readNumbers), sizeof(readNumbers));
binIn.close();

// 文件状态检查
ifstream f("test.txt");
if (!f.is_open()) {
    cerr << "无法打开文件" << endl;
}

f.good();            // 状态是否正常
f.eof();             // 是否到末尾
f.fail();            // 是否出错
f.bad();             // 是否严重错误

// 文件系统（C++17）
#include <filesystem>
namespace fs = filesystem;

fs::exists("file.txt");
fs::is_directory("dir");
fs::is_regular_file("file.txt");
fs::file_size("file.txt");
fs::create_directory("newdir");
fs::create_directories("a/b/c");
fs::remove("file.txt");
fs::remove_all("dir");
fs::rename("old.txt", "new.txt");
fs::copy("src.txt", "dst.txt");

// 遍历目录
for (const auto& entry : fs::directory_iterator("/path")) {
    cout << entry.path() << endl;
}
```

---

## 十二、异常处理

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

// 基本异常处理
try {
    int x = -1;
    if (x < 0) {
        throw invalid_argument("数值不能为负");
    }
} catch (const invalid_argument& e) {
    cerr << "参数错误: " << e.what() << endl;
} catch (const exception& e) {
    cerr << "异常: " << e.what() << endl;
} catch (...) {
    cerr << "未知异常" << endl;
}

// 自定义异常
class MyException : public exception {
private:
    string message;
    int code;
public:
    MyException(const string& msg, int code = 500)
        : message(msg), code(code) {}

    const char* what() const noexcept override {
        return message.c_str();
    }

    int getCode() const noexcept { return code; }
};

// 抛出与捕获
try {
    throw MyException("自定义错误", 404);
} catch (const MyException& e) {
    cerr << e.what() << " (code: " << e.getCode() << ")" << endl;
}

// 常见标准异常
// invalid_argument    参数无效
// out_of_range        越界
// length_error        长度超限
// runtime_error       运行时错误
// logic_error         逻辑错误
// bad_alloc           内存分配失败
// bad_cast            类型转换失败

// noexcept（标记不抛异常）
void safeFunction() noexcept {
    // 此函数不抛异常
}
```

---

## 十三、多线程（C++11）

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <future>
#include <atomic>
using namespace std;

// 创建线程
void task(int id) {
    cout << "Thread " << id << " running" << endl;
}

int main() {
    thread t1(task, 1);
    thread t2(task, 2);

    t1.join();                 // 等待线程完成
    t2.join();

    return 0;
}

// Lambda 线程
thread t([]() {
    cout << "Lambda thread" << endl;
});
t.join();

// 互斥锁
mutex mtx;

void safeIncrement(int& counter) {
    lock_guard<mutex> lock(mtx);       // 自动加锁/解锁（推荐）
    counter++;
}

// 可手动控制的锁
unique_lock<mutex> lock(mtx);
// ... 操作 ...
lock.unlock();                         // 提前解锁

// 条件变量
mutex mtx;
condition_variable cv;
bool ready = false;

// 生产者
{
    lock_guard<mutex> lock(mtx);
    ready = true;
    cv.notify_one();
}

// 消费者
{
    unique_lock<mutex> lock(mtx);
    cv.wait(lock, [] { return ready; });   // 等待条件满足
}

// 原子变量
#include <atomic>
atomic<int> counter(0);
counter++;
counter += 5;
counter.load();
counter.store(10);

// 异步任务
#include <future>

// async
auto future = async(launch::async, []() {
    return 42;
});
int result = future.get();     // 等待并获取结果

// packaged_task
packaged_task<int(int, int)> task([](int a, int b) {
    return a + b;
});
auto fut = task.get_future();
task(3, 4);
cout << fut.get() << endl;    // 7

// 线程安全的生产者-消费者
queue<int> q;
mutex mtx;
condition_variable cv;
bool done = false;

// 生产者
for (int i = 0; i < 10; i++) {
    {
        lock_guard<mutex> lock(mtx);
        q.push(i);
    }
    cv.notify_one();
}
{
    lock_guard<mutex> lock(mtx);
    done = true;
}
cv.notify_all();
```

---

## 十四、智能指针详解

```cpp
#include <memory>
#include <iostream>
using namespace std;

// unique_ptr（独占所有权）
{
    auto ptr = make_unique<int>(42);
    cout << *ptr << endl;

    // 转移所有权
    auto ptr2 = move(ptr);
    // ptr 此时为空
}

// unique_ptr 管理数组
{
    auto arr = make_unique<int[]>(10);
    arr[0] = 100;
}

// shared_ptr（共享所有权，引用计数）
{
    auto sp1 = make_shared<int>(42);
    cout << "计数: " << sp1.use_count() << endl;   // 1

    {
        auto sp2 = sp1;
        cout << "计数: " << sp1.use_count() << endl;   // 2
    }

    cout << "计数: " << sp1.use_count() << endl;   // 1
}

// weak_ptr（弱引用，解决循环引用）
class Node {
public:
    shared_ptr<Node> next;
    weak_ptr<Node> prev;        // 用 weak_ptr 避免循环引用
    int value;

    Node(int v) : value(v) {}
    ~Node() { cout << "Node " << value << " destroyed" << endl; }
};

// 自定义删除器
auto deleter = [](FILE* fp) {
    if (fp) fclose(fp);
};
unique_ptr<FILE, decltype(deleter)> file(fopen("test.txt", "r"), deleter);
```

---

## 十五、预处理器与编译

```cpp
// 头文件包含
#include <iostream>          // 系统头文件
#include "myheader.h"        // 本地头文件

// 条件编译
#ifndef MY_HEADER_H
#define MY_HEADER_H

// 头文件内容

#endif                       // MY_HEADER_H

#pragma once                 // 等价于上面的 include guard（编译器扩展）

// 宏定义
#define MAX_SIZE 100
#define SQUARE(x) ((x) * (x))         // 注意括号
#define LOG(msg) cout << "[LOG] " << msg << endl

// 条件编译
#ifdef DEBUG
    cout << "调试模式" << endl;
#else
    cout << "发布模式" << endl;
#endif

// 预定义宏
__FILE__        // 当前文件名
__LINE__        // 当前行号
__FUNCTION__    // 当前函数名
__DATE__        // 编译日期
__TIME__        // 编译时间
```

**头文件保护示例（myheader.h）：**
```cpp
#ifndef MY_HEADER_H
#define MY_HEADER_H

#include <string>

class MyClass {
public:
    void doSomething();
};

#endif // MY_HEADER_H
```

**多文件编译：**
```bash
# 分别编译
g++ -c main.cpp -o main.o
g++ -c utils.cpp -o utils.o
g++ main.o utils.o -o program

# 一步编译
g++ main.cpp utils.cpp -o program

# 使用 Makefile
make

# CMake（跨平台构建）
mkdir build && cd build
cmake ..
make
```

**简单 Makefile：**
```makefile
CC = g++
CFLAGS = -std=c++17 -O2 -Wall
TARGET = program
SRCS = main.cpp utils.cpp
OBJS = $(SRCS:.cpp=.o)

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $(TARGET) $(OBJS)

%.o: %.cpp
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS) $(TARGET)
```

---

## 十六、C++11/14/17/20 新特性速查

```cpp
// ===== C++11 =====
auto x = 10;                              // 自动类型推断
vector<int> v = {1, 2, 3};               // 初始化列表
for (auto& item : v) {}                   // 范围 for
int& ref = x;                             // 引用
nullptr;                                  // 空指针
unique_ptr<int> p = make_unique<int>(1);  // 智能指针
thread t(func);                           // 多线程
lambda: auto f = [](int x) { return x; }; // Lambda
enum class Color { Red, Green };          // 强类型枚举
move(x);                                  // 移动语义
constexpr int N = 10;                     // 编译期常量
static_assert(N > 0, "N must be positive"); // 静态断言
decltype(x) y = 10;                       // 获取类型
tuple<int, string, double> t(1, "a", 2.0); // 元组
unordered_map<string, int> um;            // 哈希表

// ===== C++14 =====
auto add = [](auto a, auto b) { return a + b; };  // 泛型 Lambda
auto func() { return 42; }                        // 自动返回类型
make_unique<int>(42);                             // make_unique
shared_lock<mutex> lock(mtx);                     // 共享锁

// ===== C++17 =====
if (auto it = m.find(key); it != m.end()) {}      // if 初始化语句
if constexpr (is_integral_v<T>) {}                // 编译期 if
auto [x, y, z] = getTuple();                      // 结构化绑定
string_view sv = "Hello";                         // 字符串视图
optional<int> opt = 42;                           // 可选值
any a = 42;                                       // 任意类型
variant<int, string> var = "hello";               // 类型安全联合
filesystem::exists("file.txt");                   // 文件系统
reduce(v.begin(), v.end());                       // 并行归约
clamp(x, 0, 100);                                 // 值限制
[[nodiscard]] int func();                         // 属性

// ===== C++20 =====
#include <ranges>
for (auto x : views::iota(1, 10) | views::filter([](int n) { return n % 2 == 0; })) {}

#include <concepts>
template<typename T>
concept Numeric = is_arithmetic_v<T>;
template<Numeric T>
T add(T a, T b) { return a + b; }

// 协程
generator<int> fibonacci() {
    int a = 0, b = 1;
    while (true) {
        co_yield a;
        tie(a, b) = pair{b, a + b};
    }
}

#include <format>
string s = format("Name: {}, Age: {}", name, age);   // 格式化

#include <span>
void process(span<int> data) { ... }                 // 数组视图

consteval int factorial(int n) { ... }               // 编译期函数
three_way_comparison: a <=> b                        // 太空船运算符
```

---

## 十七、实用技巧与最佳实践

```cpp
// 1. 使用 auto 减少冗长类型
auto it = v.begin();
auto result = func();

// 2. 使用 emplace_back 代替 push_back（减少拷贝）
v.emplace_back(1, 2, 3);      // 直接构造

// 3. 使用 emplace 代替 insert / map[]
m.emplace("key", 42);

// 4. 用 range-for 遍历
for (const auto& item : vec) { }

// 5. 用 unique_ptr / shared_ptr 代替裸指针
auto ptr = make_unique<MyClass>();

// 6. 用 string_view 避免不必要的字符串拷贝（C++17）
void print(string_view sv) { }

// 7. 用 optional 处理可能缺失的值（C++17）
optional<int> find(const string& key) {
    if (found) return 42;
    return nullopt;
}

// 8. 用 constexpr 编译期计算
constexpr int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}
constexpr int f10 = factorial(10);   // 编译期计算

// 9. 用结构化绑定（C++17）
auto [key, value] = *m.begin();

// 10. 用 if constexpr 处理编译期分支
template<typename T>
auto serialize(T value) {
    if constexpr (is_integral_v<T>) {
        return to_string(value);
    } else {
        return value;
    }
}

// 11. 用 std::array 代替 C 数组
array<int, 5> arr = {1, 2, 3, 4, 5};

// 12. 用 delegate 构造函数减少重复
class Foo {
    Foo() : Foo(0, "") {}
    Foo(int a, string b) : x(a), y(b) {}
};

// 13. 用 nullptr 代替 NULL / 0
int* p = nullptr;

// 14. 用 enum class 代替 enum
enum class Status { Active, Inactive, Deleted };

// 15. 用 override / final 标记虚函数
class Base {
    virtual void foo() {}
    virtual void bar() final {}
};
class Derived : public Base {
    void foo() override {}     // 明确重写
};

// 16. 用 noexcept 标记不抛异常的函数
void swap(int& a, int& b) noexcept { ... }

// 17. 移动语义（避免不必要的拷贝）
vector<int> createData() {
    vector<int> result = {1, 2, 3};
    return result;              // RVO / 移动构造
}
auto data = createData();
auto newData = move(data);      // 转移所有权

// 18. RAII（资源获取即初始化）
{
    lock_guard<mutex> lock(mtx);     // 自动加锁/解锁
    // 临界区
}   // 自动解锁

// 19. 编译警告
// g++ -Wall -Wextra -Werror
```

---

## 十八、常见报错与解决

| 错误 | 原因 | 解决 |
|------|------|------|
| `undefined reference` | 链接错误 | 检查函数声明与定义，添加 `-l` 链接库 |
| `segmentation fault` | 非法内存访问 | 检查指针、数组越界 |
| `no matching function` | 参数类型不匹配 | 检查函数签名 |
| `use of undeclared identifier` | 未声明 | 添加头文件 / 声明 |
| `expected ';'` | 缺少分号 | 检查上一行 |
| `redefinition of` | 重复定义 | 添加 `#pragma once` / include guard |
| `cannot bind non-const lvalue` | 引用绑定问题 | 使用 `const&` 或右值引用 |
| `pure virtual method called` | 调用纯虚函数 | 检查析构函数、继承链 |
| `stack overflow` | 递归过深 / 栈变量过大 | 改用迭代 / 堆分配 |
| `memory leak` | 内存未释放 | 使用智能指针 / valgrind 检测 |
| `double free` | 重复释放 | 检查指针管理 |

**调试工具：**
```bash
# GDB 调试
g++ -g -O0 main.cpp -o main
gdb ./main
(gdb) run
(gdb) backtrace
(gdb) break main
(gdb) watch x
(gdb) print x

# Valgrind 内存检测
valgrind --leak-check=full ./main

# AddressSanitizer（编译时检测）
g++ -fsanitize=address -g main.cpp -o main

# 静态分析
cppcheck main.cpp
clang-tidy main.cpp
```

---

## 十九、常用标准库速查

| 头文件 | 用途 |
|--------|------|
| `<iostream>` | 输入输出 |
| `<string>` | 字符串 |
| `<vector>` | 动态数组 |
| `<map>` | 有序键值对 |
| `<unordered_map>` | 哈希键值对 |
| `<set>` | 有序集合 |
| `<algorithm>` | 算法（排序、查找等） |
| `<numeric>` | 数值算法（求和等） |
| `<cmath>` | 数学函数 |
| `<ctime>` | 时间 |
| `<fstream>` | 文件操作 |
| `<sstream>` | 字符串流 |
| `<memory>` | 智能指针 |
| `<thread>` | 多线程 |
| `<mutex>` | 互斥锁 |
| `<future>` | 异步任务 |
| `<functional>` | 函数对象、bind |
| `<regex>` | 正则表达式 |
| `<tuple>` | 元组 |
| `<optional>` | 可选值（C++17） |
| `<variant>` | 类型安全联合（C++17） |
| `<any>` | 任意类型（C++17） |
| `<filesystem>` | 文件系统（C++17） |
| `<format>` | 格式化（C++20） |
| `<ranges>` | 范围库（C++20） |
| `<concepts>` | 概念（C++20） |

---

## 二十、完整示例：学生管理系统

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
#include <fstream>
#include <sstream>
#include <iomanip>
using namespace std;

class Student {
private:
    int id;
    string name;
    double score;

public:
    Student(int id, const string& name, double score)
        : id(id), name(name), score(score) {}

    int getId() const { return id; }
    string getName() const { return name; }
    double getScore() const { return score; }

    void setScore(double s) { score = s; }

    void display() const {
        cout << setw(6) << id
             << setw(12) << name
             << setw(8) << fixed << setprecision(1) << score
             << endl;
    }

    string toCSV() const {
        return to_string(id) + "," + name + "," + to_string(score);
    }
};

class StudentManager {
private:
    vector<Student> students;

public:
    void add(int id, const string& name, double score) {
        students.emplace_back(id, name, score);
    }

    bool remove(int id) {
        auto it = remove_if(students.begin(), students.end(),
            [id](const Student& s) { return s.getId() == id; });
        if (it != students.end()) {
            students.erase(it, students.end());
            return true;
        }
        return false;
    }

    Student* findById(int id) {
        for (auto& s : students) {
            if (s.getId() == id) return &s;
        }
        return nullptr;
    }

    void sortByScore(bool ascending = true) {
        if (ascending) {
            sort(students.begin(), students.end(),
                [](const Student& a, const Student& b) {
                    return a.getScore() < b.getScore();
                });
        } else {
            sort(students.begin(), students.end(),
                [](const Student& a, const Student& b) {
                    return a.getScore() > b.getScore();
                });
        }
    }

    void displayAll() const {
        cout << setw(6) << "ID"
             << setw(12) << "Name"
             << setw(8) << "Score" << endl;
        cout << string(26, '-') << endl;
        for (const auto& s : students) {
            s.display();
        }
    }

    void saveToFile(const string& filename) const {
        ofstream ofs(filename);
        for (const auto& s : students) {
            ofs << s.toCSV() << endl;
        }
    }

    void loadFromFile(const string& filename) {
        ifstream ifs(filename);
        string line;
        while (getline(ifs, line)) {
            stringstream ss(line);
            string token;
            int id;
            string name;
            double score;

            getline(ss, token, ',');
            id = stoi(token);
            getline(ss, name, ',');
            getline(ss, token, ',');
            score = stod(token);

            students.emplace_back(id, name, score);
        }
    }
};

int main() {
    StudentManager mgr;

    mgr.add(1, "Alice", 92.5);
    mgr.add(2, "Bob", 85.0);
    mgr.add(3, "Charlie", 97.8);

    cout << "=== 所有学生 ===" << endl;
    mgr.displayAll();

    cout << "\n=== 按成绩排序（降序） ===" << endl;
    mgr.sortByScore(false);
    mgr.displayAll();

    mgr.saveToFile("students.csv");
    cout << "\n已保存到 students.csv" << endl;

    return 0;
}
```