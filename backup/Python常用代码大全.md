涵盖基础语法、数据类型、函数、面向对象、文件操作、异常处理、常用标准库及实用技巧。

---

## 一、基础语法

```python
# 输出
print("Hello, World!")
print("姓名:", name, "年龄:", age)
print(f"姓名: {name}, 年龄: {age}")       # f-string（推荐）
print("姓名: {}, 年龄: {}".format(name, age))

# 输入
name = input("请输入姓名: ")               # 返回字符串
age = int(input("请输入年龄: "))           # 转为整数

# 注释
# 单行注释
"""
多行注释 / 多行字符串
"""

# 变量与类型
x = 10                    # int
pi = 3.14                 # float
name = "Python"           # str
is_ok = True              # bool
data = None               # NoneType

# 类型查看与转换
type(x)                   # 查看类型
int("123")                # 字符串 → 整数
float("3.14")             # 字符串 → 浮点数
str(100)                  # 整数 → 字符串
bool(1)                   # 转布尔值
```

---

## 二、数据类型

### 字符串（str）

```python
s = "Hello, Python"

# 索引与切片
s[0]              # 'H'
s[-1]             # 'n'
s[0:5]            # 'Hello'
s[::2]            # 隔一个取一个
s[::-1]           # 反转字符串

# 常用方法
s.upper()                   # 全大写
s.lower()                   # 全小写
s.strip()                   # 去除首尾空白
s.lstrip()                  # 去除左侧空白
s.rstrip()                  # 去除右侧空白
s.replace("Python", "World")  # 替换
s.split(", ")               # 分割为列表
s.startswith("Hello")       # 是否以...开头
s.endswith("n")             # 是否以...结尾
s.find("Python")            # 查找位置（未找到返回 -1）
s.count("l")                # 统计出现次数
s.isdigit()                 # 是否全为数字
s.isalpha()                 # 是否全为字母
s.zfill(10)                 # 左侧补零
" ".join(["a", "b", "c"])   # 列表拼接为字符串

# 字符串格式化
f"结果是 {value:.2f}"       # 保留两位小数
f"{value:>10}"              # 右对齐，宽度 10
f"{value:<10}"              # 左对齐
f"{value:^10}"              # 居中
f"{value:08.2f}"            # 补零 + 小数
f"{1000000:,}"              # 千位分隔符 → 1,000,000
```

### 列表（list）

```python
lst = [1, 2, 3, 4, 5]

# 增
lst.append(6)               # 末尾添加
lst.insert(0, 0)            # 指定位置插入
lst.extend([7, 8])          # 合并另一个列表

# 删
lst.remove(3)               # 删除第一个匹配值
lst.pop()                   # 弹出末尾元素
lst.pop(0)                  # 弹出指定位置
del lst[0]                  # 删除指定位置
lst.clear()                 # 清空列表

# 改查
lst[0] = 100                # 修改
lst.index(3)                # 查找索引
lst.count(3)                # 统计次数
3 in lst                    # 是否包含

# 排序
lst.sort()                  # 原地排序
lst.sort(reverse=True)      # 降序
sorted(lst)                 # 返回新列表
lst.reverse()               # 反转
lst.copy()                  # 浅拷贝

# 切片
lst[1:3]
lst[-3:]
lst[::2]
lst[::-1]                   # 反转

# 列表推导式
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
matrix = [[i*j for j in range(3)] for i in range(3)]
```

### 元组（tuple）

```python
t = (1, 2, 3, 4, 5)

t[0]              # 索引
t[1:3]            # 切片
t.index(3)        # 查找
t.count(2)        # 统计
len(t)            # 长度

# 解包
a, b, c, d, e = t
first, *rest = t          # first=1, rest=[2,3,4,5]
first, *mid, last = t     # first=1, mid=[2,3,4], last=5

# 元组是不可变的，不能修改
```

### 字典（dict）

```python
d = {"name": "Alice", "age": 25, "city": "Beijing"}

# 增改
d["email"] = "alice@example.com"    # 新增
d["age"] = 26                       # 修改
d.update({"phone": "123456"})       # 批量更新
d.setdefault("score", 100)          # 不存在则设置默认值

# 查
d["name"]                  # 获取（不存在会报 KeyError）
d.get("name")              # 获取（不存在返回 None）
d.get("score", 0)          # 获取（不存在返回默认值）
"name" in d                # 判断键是否存在
d.keys()                   # 所有键
d.values()                 # 所有值
d.items()                  # 所有键值对

# 删
d.pop("email")             # 删除并返回
d.popitem()                # 删除最后一个键值对
del d["phone"]
d.clear()

# 遍历
for key, value in d.items():
    print(key, value)

# 字典推导式
squares = {x: x**2 for x in range(5)}
filtered = {k: v for k, v in d.items() if v > 20}

# 嵌套字典
users = {
    "alice": {"age": 25, "city": "Beijing"},
    "bob": {"age": 30, "city": "Shanghai"},
}
users["alice"]["age"]
```

### 集合（set）

```python
s = {1, 2, 3, 4, 5}
s2 = {4, 5, 6, 7, 8}

s.add(6)                # 添加
s.remove(3)             # 删除（不存在报错）
s.discard(3)            # 删除（不存在不报错）
s.pop()                 # 随机弹出

# 集合运算
s & s2                  # 交集
s | s2                  # 并集
s - s2                  # 差集
s ^ s2                  # 对称差集
s.issubset(s2)          # 是否为子集
s.issuperset(s2)        # 是否为超集

# 去重
unique = list(set([1, 2, 2, 3, 3, 3]))   # [1, 2, 3]

# 集合推导式
squares = {x**2 for x in range(10)}
```

---

## 三、流程控制

```python
# 条件判断
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 60:
    grade = "C"
else:
    grade = "D"

# 三元表达式
result = "及格" if score >= 60 else "不及格"

# match-case（Python 3.10+）
match command:
    case "start":
        print("启动")
    case "stop":
        print("停止")
    case _:
        print("未知命令")

# for 循环
for i in range(10):              # 0-9
    print(i)

for i in range(0, 10, 2):        # 0,2,4,6,8
    print(i)

for idx, val in enumerate(["a", "b", "c"]):
    print(idx, val)              # 0 a / 1 b / 2 c

for k, v in {"x": 1, "y": 2}.items():
    print(k, v)

for a, b in zip([1, 2, 3], ["a", "b", "c"]):
    print(a, b)                  # 1 a / 2 b / 3 c

# while 循环
while condition:
    pass

# break / continue / else
for i in range(10):
    if i == 5:
        break
    if i % 2 == 0:
        continue
    print(i)
else:
    print("循环正常结束（未 break）")
```

---

## 四、函数

```python
# 基本函数
def greet(name):
    return f"Hello, {name}!"

# 默认参数
def greet(name="World"):
    return f"Hello, {name}!"

# 可变参数
def func(*args, **kwargs):
    print(args)        # 元组
    print(kwargs)      # 字典

func(1, 2, 3, name="Alice", age=25)

# 仅关键字参数
def func(a, b, *, key1=None, key2=None):
    pass

# Lambda 匿名函数
square = lambda x: x**2
add = lambda x, y: x + y
sorted(lst, key=lambda x: x["age"])

# 返回多个值
def get_info():
    return "Alice", 25, "Beijing"

name, age, city = get_info()

# 闭包
def counter():
    count = 0
    def increment():
        nonlocal count
        count += 1
        return count
    return increment

# 装饰器
import functools

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        import time
        start = time.time()
        result = func(*args, **kwargs)
        print(f"耗时: {time.time() - start:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    import time
    time.sleep(1)

# 类型提示（Type Hints）
def add(a: int, b: int) -> int:
    return a + b

def greet(name: str, age: int = 0) -> str:
    return f"{name} is {age} years old"
```

---

## 五、面向对象编程

```python
# 类定义
class Animal:
    # 类属性
    kingdom = "Animalia"

    def __init__(self, name, age):
        self.name = name        # 实例属性
        self.age = age

    def speak(self):
        return f"{self.name} makes a sound"

    def __str__(self):
        return f"Animal({self.name}, {self.age})"

    def __repr__(self):
        return f"Animal(name='{self.name}', age={self.age})"

    def __len__(self):
        return self.age

    @property
    def info(self):
        return f"{self.name}, {self.age} years old"

    @staticmethod
    def class_method():
        return "静态方法"

    @classmethod
    def create_default(cls):
        return cls("Default", 0)


# 继承
class Dog(Animal):
    def __init__(self, name, age, breed):
        super().__init__(name, age)
        self.breed = breed

    def speak(self):
        return f"{self.name} barks"

    def fetch(self):
        return f"{self.name} fetches the ball"


# 多态
animals = [Dog("Buddy", 5, "Labrador"), Animal("Generic", 3)]
for animal in animals:
    print(animal.speak())       # 不同类型调用同一方法，行为不同


# 抽象类
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        import math
        return math.pi * self.radius ** 2

    def perimeter(self):
        import math
        return 2 * math.pi * self.radius


# 数据类（Python 3.7+）
from dataclasses import dataclass, field

@dataclass
class Point:
    x: float
    y: float
    label: str = "P"

    def distance(self, other):
        return ((self.x - other.x)**2 + (self.y - other.y)**2) ** 0.5

p1 = Point(1, 2)
p2 = Point(4, 6)
print(p1)                # Point(x=1, y=2, label='P')
print(p1.distance(p2))   # 5.0
```

---

## 六、文件操作

```python
# 读取文件
with open("file.txt", "r", encoding="utf-8") as f:
    content = f.read()               # 读取全部
    # content = f.readline()         # 读取一行
    # lines = f.readlines()          # 读取所有行（列表）

# 逐行读取（推荐，省内存）
with open("file.txt", "r", encoding="utf-8") as f:
    for line in f:
        print(line.strip())

# 写入文件
with open("file.txt", "w", encoding="utf-8") as f:
    f.write("Hello\n")
    f.write("World\n")
    f.writelines(["line1\n", "line2\n"])

# 追加文件
with open("file.txt", "a", encoding="utf-8") as f:
    f.write("appended line\n")

# 二进制文件
with open("image.png", "rb") as f:
    data = f.read()

with open("copy.png", "wb") as f:
    f.write(data)

# 常用文件操作模式
# "r"  只读（默认）
# "w"  写入（覆盖）
# "a"  追加
# "x"  创建（已存在则报错）
# "b"  二进制模式
# "t"  文本模式（默认）
# "+"  读写
```

---

## 七、OS 与文件系统

```python
import os
import shutil
from pathlib import Path

# os 模块
os.getcwd()                        # 当前工作目录
os.chdir("/tmp")                   # 切换目录
os.listdir(".")                    # 列出目录内容
os.mkdir("newdir")                 # 创建目录
os.makedirs("a/b/c", exist_ok=True)  # 递归创建目录
os.remove("file.txt")              # 删除文件
os.rmdir("dir")                    # 删除空目录
shutil.rmtree("dir")               # 递归删除目录
os.rename("old.txt", "new.txt")    # 重命名/移动
os.path.exists("path")             # 是否存在
os.path.isfile("path")             # 是否为文件
os.path.isdir("path")              # 是否为目录
os.path.join("a", "b", "c.txt")    # 路径拼接
os.path.basename("/a/b/c.txt")     # 文件名 → c.txt
os.path.dirname("/a/b/c.txt")      # 目录名 → /a/b
os.path.splitext("file.txt")       # 分离扩展名 → ('file', '.txt')
os.path.getsize("file.txt")        # 文件大小（字节）
os.environ.get("HOME")             # 获取环境变量

# pathlib（推荐，更现代）
p = Path("/home/user/file.txt")
p.name               # 'file.txt'
p.stem               # 'file'
p.suffix             # '.txt'
p.parent             # /home/user
p.exists()           # 是否存在
p.is_file()          # 是否为文件
p.is_dir()           # 是否为目录
p.stat().st_size     # 文件大小
p.read_text()        # 读取文本
p.write_text("hi")   # 写入文本
p.with_suffix(".md") # 更换扩展名
p / "subdir"         # 路径拼接

# 遍历目录
for root, dirs, files in os.walk("/path"):
    for f in files:
        print(os.path.join(root, f))

# pathlib 遍历
for f in Path("/path").rglob("*.txt"):
    print(f)
```

---

## 八、异常处理

```python
# 基本异常处理
try:
    result = 10 / 0
except ZeroDivisionError:
    print("不能除以零")
except (ValueError, TypeError) as e:
    print(f"错误: {e}")
except Exception as e:
    print(f"未知错误: {e}")
else:
    print("没有异常时执行")
finally:
    print("无论如何都执行")

# 自定义异常
class MyError(Exception):
    def __init__(self, message, code=500):
        self.message = message
        self.code = code
        super().__init__(self.message)

# 抛出异常
raise ValueError("无效的值")
raise MyError("自定义错误", code=404)

# 断言
assert age > 0, "年龄必须大于0"

# 捕获并重新抛出
try:
    risky_operation()
except Exception as e:
    logger.error(f"出错了: {e}")
    raise                    # 重新抛出当前异常

# 上下文管理器
class MyContext:
    def __enter__(self):
        print("进入")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("退出")
        return False         # 不吞异常

with MyContext() as ctx:
    print("执行中")
```

---

## 九、常用内置函数

```python
# 类型与转换
type(x)              # 类型
isinstance(x, int)   # 类型判断
int("123")           # 转整数
float("3.14")        # 转浮点数
str(100)             # 转字符串
bool(1)              # 转布尔
list(range(5))       # 转列表
tuple([1, 2, 3])     # 转元组
set([1, 2, 2, 3])    # 转集合（去重）
dict(a=1, b=2)       # 创建字典

# 数学
abs(-5)              # 绝对值
round(3.14159, 2)    # 四舍五入
min(1, 2, 3)         # 最小值
max(1, 2, 3)         # 最大值
sum([1, 2, 3])       # 求和
pow(2, 10)           # 幂运算
divmod(10, 3)        # (商, 余数)

# 序列
len([1, 2, 3])       # 长度
sorted([3, 1, 2])    # 排序
reversed([1, 2, 3])  # 反转迭代器
zip([1, 2], ["a", "b"])    # 打包
enumerate(["a", "b"])      # 带索引遍历
any([False, True])         # 任一为真
all([True, True])          # 全部为真
filter(lambda x: x > 2, [1, 2, 3])    # 过滤
map(lambda x: x**2, [1, 2, 3])        # 映射

# 输入输出
print(...)
input("提示: ")

# 其他
id(x)                # 对象内存地址
hash(x)              # 哈希值
dir(x)               # 属性和方法列表
help(x)              # 帮助文档
callable(x)          # 是否可调用
chr(65)              # ASCII → 字符
ord("A")             # 字符 → ASCII
```

---

## 十、常用标准库

### os / sys

```python
import sys

sys.argv                    # 命令行参数
sys.path                    # 模块搜索路径
sys.version                 # Python 版本
sys.platform                # 平台
sys.exit(0)                 # 退出程序
sys.maxsize                 # 最大整数
sys.stdout.write("hi\n")    # 标准输出
```

### datetime

```python
from datetime import datetime, timedelta, date

now = datetime.now()                     # 当前时间
today = date.today()                     # 今天日期
dt = datetime(2024, 1, 15, 10, 30, 0)   # 指定时间

# 格式化
now.strftime("%Y-%m-%d %H:%M:%S")        # → '2024-01-15 10:30:00'
now.strftime("%Y年%m月%d日")              # → '2024年01月15日'

# 解析
dt = datetime.strptime("2024-01-15", "%Y-%m-%d")

# 时间运算
tomorrow = now + timedelta(days=1)
last_week = now - timedelta(weeks=1)
diff = datetime(2024, 12, 31) - datetime(2024, 1, 1)
print(diff.days)                         # 365

# 时间戳
timestamp = now.timestamp()              # datetime → 时间戳
dt = datetime.fromtimestamp(1700000000)  # 时间戳 → datetime
```

### json

```python
import json

# 序列化
data = {"name": "Alice", "age": 25, "scores": [90, 85, 92]}
json_str = json.dumps(data, ensure_ascii=False, indent=2)

# 反序列化
data = json.loads('{"name": "Alice"}')

# 文件读写
with open("data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, ensure_ascii=False, indent=2)

with open("data.json", "r", encoding="utf-8") as f:
    data = json.load(f)
```

### re（正则表达式）

```python
import re

text = "My phone is 138-1234-5678 and email is test@example.com"

# 查找
re.findall(r"\d+", text)                  # ['138', '1234', '5678']
re.search(r"\d{3}-\d{4}-\d{4}", text)     # 首次匹配
re.match(r"My", text)                      # 从头匹配

# 替换
re.sub(r"\d+", "XXX", text)               # 替换所有匹配
re.sub(r"(\d{3})-(\d{4})-(\d{4})", r"\1****\3", text)   # 分组替换

# 分割
re.split(r"\s+", "hello   world  foo")

# 编译（多次使用时效率更高）
pattern = re.compile(r"\b\w+@\w+\.\w+\b")
pattern.findall(text)

# 常用模式
# \d  数字   \w  字母数字下划线   \s  空白
# .   任意字符  ^  开头  $  结尾
# *   0次或多次  +  1次或多次  ?  0次或一次
# {n,m} n到m次  [...] 字符集  (...) 分组
```

### random

```python
import random

random.random()                    # 0-1 随机浮点数
random.randint(1, 100)             # 1-100 随机整数（含两端）
random.uniform(1.0, 10.0)          # 随机浮点数
random.choice(["a", "b", "c"])     # 随机选一个
random.choices([1, 2, 3], k=5)     # 随机选 k 个（可重复）
random.sample([1, 2, 3, 4, 5], 3)  # 随机选 3 个（不重复）
random.shuffle(lst)                # 打乱列表（原地）
random.seed(42)                    # 固定随机种子（可复现）
```

### collections

```python
from collections import Counter, defaultdict, OrderedDict, namedtuple, deque

# Counter - 计数器
c = Counter("aabbbccddd")
c.most_common(2)              # [('d', 3), ('b', 3)]
c["a"]                        # 2

# defaultdict - 带默认值的字典
dd = defaultdict(list)
dd["key"].append(1)           # 自动创建空列表

# namedtuple - 命名元组
Point = namedtuple("Point", ["x", "y"])
p = Point(1, 2)
p.x                           # 1

# deque - 双端队列
dq = deque([1, 2, 3])
dq.appendleft(0)              # 左侧添加
dq.append(4)                  # 右侧添加
dq.popleft()                  # 左侧弹出
dq.pop()                      # 右侧弹出
```

### itertools

```python
from itertools import chain, combinations, groupby, product, count, islice

# 链接多个可迭代对象
list(chain([1, 2], [3, 4]))           # [1, 2, 3, 4]

# 排列组合
list(combinations("ABC", 2))          # [('A','B'), ('A','C'), ('B','C')]
list(product([1, 2], ["a", "b"]))     # 笛卡尔积

# 分组
data = [("A", 1), ("A", 2), ("B", 3)]
for key, group in groupby(data, key=lambda x: x[0]):
    print(key, list(group))

# 无限迭代
for i in islice(count(10), 5):        # 10, 11, 12, 13, 14
    print(i)
```

### functools

```python
from functools import lru_cache, partial, reduce

# 缓存（记忆化）
@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# 偏函数
def power(base, exp):
    return base ** exp

square = partial(power, exp=2)
cube = partial(power, exp=3)

# reduce
from functools import reduce
result = reduce(lambda x, y: x + y, [1, 2, 3, 4, 5])    # 15
```

### logging

```python
import logging

# 基本配置
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(name)s: %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S",
    handlers=[
        logging.FileHandler("app.log", encoding="utf-8"),
        logging.StreamHandler()
    ]
)

logger = logging.getLogger(__name__)

logger.debug("调试信息")
logger.info("普通信息")
logger.warning("警告")
logger.error("错误")
logger.critical("严重错误")
```

### argparse（命令行参数解析）

```python
import argparse

parser = argparse.ArgumentParser(description="示例程序")
parser.add_argument("name", help="姓名")
parser.add_argument("-a", "--age", type=int, default=0, help="年龄")
parser.add_argument("-v", "--verbose", action="store_true", help="详细输出")

args = parser.parse_args()
print(f"Name: {args.name}, Age: {args.age}, Verbose: {args.verbose}")

# 运行: python script.py Alice --age 25 --verbose
```

---

## 十一、并发编程

### 多线程

```python
import threading
import time

def worker(name, delay):
    for i in range(3):
        print(f"{name}: {i}")
        time.sleep(delay)

# 创建线程
t1 = threading.Thread(target=worker, args=("Thread-1", 1))
t2 = threading.Thread(target=worker, args=("Thread-2", 0.5))

t1.start()
t2.start()

t1.join()          # 等待线程结束
t2.join()

# 线程锁
lock = threading.Lock()

def safe_increment(counter):
    with lock:
        counter["value"] += 1

# 线程池
from concurrent.futures import ThreadPoolExecutor

with ThreadPoolExecutor(max_workers=4) as executor:
    futures = [executor.submit(worker, f"T{i}", 1) for i in range(4)]
    for future in futures:
        print(future.result())
```

### 多进程

```python
from multiprocessing import Process, Pool, Queue
import os

def task(name):
    print(f"Process {name}: PID={os.getpid()}")

# 创建进程
p = Process(target=task, args=("Worker",))
p.start()
p.join()

# 进程池
with Pool(processes=4) as pool:
    results = pool.map(task, ["A", "B", "C", "D"])

# 进程间通信
q = Queue()
```

### 异步编程（asyncio）

```python
import asyncio

async def fetch(url):
    print(f"开始请求: {url}")
    await asyncio.sleep(1)          # 模拟 IO 操作
    return f"响应: {url}"

async def main():
    # 并发执行多个协程
    tasks = [fetch(f"http://example.com/{i}") for i in range(5)]
    results = await asyncio.gather(*tasks)
    for r in results:
        print(r)

asyncio.run(main())

# 异步 HTTP 请求（需安装 aiohttp）
# import aiohttp
# async def fetch_all(urls):
#     async with aiohttp.ClientSession() as session:
#         tasks = [session.get(url) for url in urls]
#         return await asyncio.gather(*tasks)
```

---

## 十二、虚拟环境与包管理

```bash
# 创建虚拟环境
python -m venv venv

# 激活虚拟环境
source venv/bin/activate        # Linux / macOS
venv\Scripts\activate           # Windows

# 退出虚拟环境
deactivate

# 安装包
pip install requests
pip install requests==2.31.0    # 指定版本
pip install -r requirements.txt # 从文件安装
pip install --upgrade pip       # 升级 pip

# 导出/安装依赖
pip freeze > requirements.txt

# 常用 pip 命令
pip list                        # 已安装包列表
pip show requests               # 查看包详情
pip uninstall requests          # 卸载
pip install -e .                # 安装本地包（开发模式）
```

**requirements.txt 示例：**
```
requests==2.31.0
flask>=2.0
numpy
pandas~=2.0
```

---

## 十三、常用开发技巧

```python
# 1. 交换变量
a, b = b, a

# 2. 链式比较
if 1 < x < 10:
    pass

# 3. 字符串翻转
s[::-1]

# 4. 列表展平
from itertools import chain
flat = list(chain.from_iterable([[1, 2], [3, 4], [5, 6]]))

# 5. 字典合并（Python 3.9+）
merged = d1 | d2

# 6. 获取最大/最小值的索引
max_idx = max(range(len(lst)), key=lambda i: lst[i])

# 7. 列表去重（保持顺序）
seen = set()
unique = [x for x in lst if not (x in seen or seen.add(x))]

# 8. 反转字典
inverted = {v: k for k, v in d.items()}

# 9. 打印带颜色的输出
print("\033[91m红色文字\033[0m")
print("\033[92m绿色文字\033[0m")
print("\033[93m黄色文字\033[0m")

# 10. 占位符
todo = ...            # Ellipsis，常用于占位
pass                  # 空语句

# 11. 测量执行时间
import time
start = time.perf_counter()
# ... 代码 ...
print(f"耗时: {time.perf_counter() - start:.6f}s")

# 12. 链式安全取值
from functools import reduce
value = reduce(lambda d, k: d.get(k, {}), ["a", "b", "c"], data)

# 13. 枚举遍历（带起始值）
for i, val in enumerate(lst, start=1):
    print(i, val)

# 14. 使用 dataclass 替代普通类
from dataclasses import dataclass

@dataclass
class User:
    name: str
    age: int
    email: str = ""
```

---

## 十四、第三方常用库速查

| 库 | 用途 | 安装 |
|------|------|------|
| `requests` | HTTP 请求 | `pip install requests` |
| `flask` | 轻量 Web 框架 | `pip install flask` |
| `django` | 全功能 Web 框架 | `pip install django` |
| `fastapi` | 高性能 API 框架 | `pip install fastapi uvicorn` |
| `numpy` | 数值计算 | `pip install numpy` |
| `pandas` | 数据分析 | `pip install pandas` |
| `matplotlib` | 数据可视化 | `pip install matplotlib` |
| `pillow` | 图片处理 | `pip install pillow` |
| `beautifulsoup4` | HTML 解析 | `pip install beautifulsoup4` |
| `scrapy` | 爬虫框架 | `pip install scrapy` |
| `sqlalchemy` | ORM 数据库 | `pip install sqlalchemy` |
| `celery` | 异步任务队列 | `pip install celery` |
| `redis` | Redis 客户端 | `pip install redis` |
| `pymysql` | MySQL 驱动 | `pip install pymysql` |
| `pydantic` | 数据校验 | `pip install pydantic` |
| `click` | 命令行工具 | `pip install click` |
| `rich` | 终端美化输出 | `pip install rich` |
| `pytest` | 测试框架 | `pip install pytest` |

---

## 十五、HTTP 请求示例（requests）

```python
import requests

# GET 请求
resp = requests.get("https://api.example.com/users", params={"page": 1})
print(resp.status_code)         # 200
print(resp.json())              # 解析 JSON
print(resp.text)                # 文本内容
print(resp.headers)             # 响应头

# POST 请求
resp = requests.post(
    "https://api.example.com/users",
    json={"name": "Alice", "age": 25},
    headers={"Authorization": "Bearer token123"}
)

# 文件上传
with open("file.png", "rb") as f:
    resp = requests.post("https://api.example.com/upload", files={"file": f})

# 超时与异常
try:
    resp = requests.get("https://api.example.com", timeout=5)
    resp.raise_for_status()
except requests.exceptions.Timeout:
    print("请求超时")
except requests.exceptions.RequestException as e:
    print(f"请求错误: {e}")

# Session（保持 Cookie、连接复用）
session = requests.Session()
session.headers.update({"Authorization": "Bearer token123"})
resp1 = session.get("https://api.example.com/user")
resp2 = session.get("https://api.example.com/orders")

# 下载文件
resp = requests.get("https://example.com/large_file.zip", stream=True)
with open("file.zip", "wb") as f:
    for chunk in resp.iter_content(chunk_size=8192):
        f.write(chunk)
```

---

## 十六、数据库操作示例

### SQLite

```python
import sqlite3

conn = sqlite3.connect("mydb.db")
cursor = conn.cursor()

# 建表
cursor.execute("""
    CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        age INTEGER
    )
""")

# 插入
cursor.execute("INSERT INTO users (name, age) VALUES (?, ?)", ("Alice", 25))
cursor.executemany("INSERT INTO users (name, age) VALUES (?, ?)",
                   [("Bob", 30), ("Charlie", 35)])
conn.commit()

# 查询
cursor.execute("SELECT * FROM users WHERE age > ?", (20,))
rows = cursor.fetchall()
for row in rows:
    print(row)

# 更新与删除
cursor.execute("UPDATE users SET age = ? WHERE name = ?", (26, "Alice"))
cursor.execute("DELETE FROM users WHERE name = ?", ("Charlie",))
conn.commit()

conn.close()

# 推荐用 with 管理连接
with sqlite3.connect("mydb.db") as conn:
    conn.execute("INSERT INTO users (name, age) VALUES (?, ?)", ("Dave", 28))
```

### MySQL（pymysql）

```python
import pymysql

conn = pymysql.connect(
    host="localhost",
    user="root",
    password="password",
    database="mydb",
    charset="utf8mb4"
)

with conn.cursor() as cursor:
    cursor.execute("SELECT * FROM users WHERE age > %s", (20,))
    results = cursor.fetchall()
    for row in results:
        print(row)

conn.close()
```

---

## 十七、测试（pytest）

```python
# test_calc.py
def add(a, b):
    return a + b

def test_add():
    assert add(1, 2) == 3
    assert add(-1, 1) == 0

def test_add_float():
    assert add(0.1, 0.2) == pytest.approx(0.3)

# fixture
import pytest

@pytest.fixture
def sample_data():
    return [1, 2, 3, 4, 5]

def test_with_fixture(sample_data):
    assert len(sample_data) == 5

# 参数化
@pytest.mark.parametrize("input,expected", [
    (1, 2),
    (2, 4),
    (3, 6),
])
def test_double(input, expected):
    assert input * 2 == expected

# 运行测试
# pytest test_calc.py -v
# pytest -v --tb=short
# pytest -k "test_add"
```

---

## 十八、常见报错与解决

| 错误 | 原因 | 解决 |
|------|------|------|
| `SyntaxError` | 语法错误 | 检查括号、缩进、引号 |
| `IndentationError` | 缩进不一致 | 统一用 4 个空格 |
| `TypeError` | 类型不匹配 | 检查变量类型 |
| `ValueError` | 值不合法 | 检查传入值 |
| `KeyError` | 字典键不存在 | 用 `d.get(key)` |
| `IndexError` | 索引越界 | 检查列表长度 |
| `AttributeError` | 属性不存在 | 检查对象类型 |
| `ImportError` | 模块未安装 | `pip install 模块名` |
| `FileNotFoundError` | 文件不存在 | 检查路径 |
| `ZeroDivisionError` | 除以零 | 添加判断 |
| `RecursionError` | 递归过深 | 改用循环或增加递归限制 |
| `UnicodeDecodeError` | 编码错误 | 指定 `encoding="utf-8"` |

---

## 十九、Python 版本新特性速查

```python
# Python 3.8
:= (海象运算符)
if (n := len(data)) > 10:
    print(f"too long: {n}")

# Python 3.9
d1 | d2                    # 字典合并
d |= {"key": "value"}      # 字典更新
list[str]                  # 类型提示（无需 typing）

# Python 3.10
match-case                 # 模式匹配
x: int | float             # 联合类型

# Python 3.11
ExceptionGroup             # 异常组
tomllib                    # 读取 TOML 文件

# Python 3.12
type aliases               # 类型别名
f-string 改进               # 嵌套引号
```