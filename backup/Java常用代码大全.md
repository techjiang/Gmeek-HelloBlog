涵盖基础语法、数据类型、流程控制、数组与字符串、面向对象、集合框架、泛型、Lambda 与 Stream、异常处理、IO 流、多线程、JDBC、常用工具类及实战示例。

---

## 一、基础语法

```java
// 第一个程序
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");          // 输出并换行
        System.out.print("不换行 ");                   // 输出不换行
        System.out.printf("姓名: %s, 年龄: %d%n", name, age);  // 格式化输出
    }
}

// 输入（Scanner）
import java.util.Scanner;
Scanner scanner = new Scanner(System.in);
String name = scanner.nextLine();          // 读一行
int age = scanner.nextInt();               // 读整数
double score = scanner.nextDouble();       // 读浮点数
boolean flag = scanner.nextBoolean();      // 读布尔
scanner.close();

// 注释
// 单行注释
/* 多行注释 */
/** JavaDoc 文档注释 */

// 编译与运行
// javac HelloWorld.java
// java HelloWorld
```

**编译与运行：**
```bash
javac HelloWorld.java           # 编译（生成 .class 文件）
java HelloWorld                  # 运行
javac -encoding UTF-8 Main.java  # 指定编码
java -cp . Main                  # 指定 classpath

# Java 11+ 直接运行单文件
java HelloWorld.java

# 打包为 JAR
jar cvf app.jar *.class
java -jar app.jar
```

---

## 二、数据类型

### 基本数据类型

```java
// 整型
byte b = 127;                   // 1 字节，-128~127
short s = 32767;                // 2 字节
int i = 2147483647;             // 4 字节（最常用）
long l = 9223372036854775807L;  // 8 字节，注意 L 后缀

// 浮点型
float f = 3.14f;                // 4 字节，注意 f 后缀
double d = 3.14159265358979;    // 8 字节（默认）

// 字符型
char c = 'A';                   // 2 字节，Unicode
char chinese = '中';            // 支持中文字符

// 布尔型
boolean flag = true;            // true / false

// 类型转换
int x = (int) 3.99;            // 强制转换（截断）→ 3
double y = 10;                  // 自动提升 → 10.0
long big = 100L;
```

### 引用数据类型

```java
// 字符串
String name = "Java";

// 数组
int[] nums = new int[5];
String[] names = {"Alice", "Bob", "Charlie"};

// 对象
Object obj = new Object();

// 包装类（自动装箱/拆箱）
Integer intObj = 42;            // 自动装箱
int val = intObj;               // 自动拆箱
Integer a = 100, b = 100;
a == b;                         // true（-128~127 缓存）
Integer c = 200, d = 200;
c == d;                         // false（超出缓存范围）
c.equals(d);                    // true（推荐用 equals）

// 常用包装类
Integer.parseInt("123");         // String → int
Integer.valueOf("123");          // String → Integer
Double.parseDouble("3.14");      // String → double
String.valueOf(123);             // int → String
Integer.toString(123);           // int → String
```

---

## 三、流程控制

```java
// 条件判断
if (score >= 90) {
    grade = "A";
} else if (score >= 80) {
    grade = "B";
} else {
    grade = "C";
}

// 三元表达式
String result = score >= 60 ? "及格" : "不及格";

// switch（Java 12+ 增强）
switch (day) {
    case 1, 2, 3, 4, 5 -> System.out.println("工作日");
    case 6, 7 -> System.out.println("周末");
    default -> System.out.println("无效");
}

// switch 返回值（Java 14+）
String type = switch (code) {
    case 1 -> "管理员";
    case 2 -> "普通用户";
    default -> "游客";
};

// 传统 switch
switch (option) {
    case 1:
        System.out.println("选项1");
        break;
    case 2:
        System.out.println("选项2");
        break;
    default:
        System.out.println("未知");
}

// for 循环
for (int i = 0; i < 10; i++) {
    System.out.println(i);
}

// 增强 for（for-each）
int[] nums = {1, 2, 3, 4, 5};
for (int x : nums) {
    System.out.println(x);
}

List<String> list = List.of("A", "B", "C");
for (String s : list) {
    System.out.println(s);
}

// while 循环
while (condition) {
    // ...
}

// do-while
do {
    // ...
} while (condition);

// break / continue / 标签
outer:
for (int i = 0; i < 5; i++) {
    for (int j = 0; j < 5; j++) {
        if (j == 3) break outer;     // 跳出外层循环
        if (j == 1) continue;
        System.out.println(i + "," + j);
    }
}
```

---

## 四、数组

```java
// 声明与初始化
int[] arr1 = new int[5];               // 默认全 0
int[] arr2 = {1, 2, 3, 4, 5};          // 直接初始化
int[] arr3 = new int[]{1, 2, 3};       // 匿名数组

// 基本操作
arr2[0] = 10;                           // 修改
int len = arr2.length;                  // 长度
System.out.println(arr2[0]);            // 访问

// 多维数组
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
System.out.println(matrix[1][2]);       // 6

// 遍历
for (int i = 0; i < arr2.length; i++) {
    System.out.println(arr2[i]);
}
for (int x : arr2) {
    System.out.println(x);
}

// Arrays 工具类
import java.util.Arrays;

int[] nums = {5, 3, 1, 4, 2};
Arrays.sort(nums);                       // 排序
Arrays.sort(nums, 0, 3);                // 部分排序
Arrays.fill(nums, 0);                   // 填充
Arrays.fill(nums, 0, 3, 99);            // 范围填充
Arrays.copyOf(nums, 10);                // 复制（可扩容）
Arrays.copyOfRange(nums, 1, 3);         // 范围复制
Arrays.equals(nums, nums);              // 比较
Arrays.binarySearch(nums, 3);           // 二分查找（需排序）
Arrays.toString(nums);                  // 转字符串 [5, 3, 1, 4, 2]
Arrays.stream(nums).sum();              // 求和
Arrays.stream(nums).max().orElse(0);    // 最大值
Arrays.stream(nums).average().orElse(0); // 平均值

// 二维数组转字符串
Arrays.deepToString(matrix);

// 数组 → List
List<Integer> list = Arrays.asList(1, 2, 3);
List<String> strList = Arrays.asList("A", "B", "C");
```

---

## 五、字符串

```java
String s = "Hello, Java";

// 创建
String s1 = "Hello";                    // 字符串常量池
String s2 = new String("Hello");        // 新对象
String s3 = String.valueOf(123);        // 数字转字符串
String s4 = String.format("%s-%d", "A", 1);   // 格式化

// 常用方法
s.length();                    // 长度
s.charAt(0);                   // 指定位置字符
s.indexOf("Java");             // 查找（返回位置，未找到 -1）
s.lastIndexOf("a");            // 从后查找
s.contains("Java");            // 是否包含
s.startsWith("Hello");         // 是否以...开头
s.endsWith("ava");             // 是否以...结尾
s.substring(7);                // 截取子串（7 到末尾）
s.substring(0, 5);             // 截取子串（0 到 5，不含 5）
s.toLowerCase();               // 转小写
s.toUpperCase();               // 转大写
s.trim();                      // 去除首尾空白
s.strip();                     // 去除空白（Java 11，更好）
s.replace("Java", "World");    // 替换
s.replaceAll("\\d+", "");      // 正则替换
s.split(", ");                 // 分割为数组
s.isEmpty();                   // 是否为空
s.isBlank();                   // 是否空白（Java 11）
s.repeat(3);                   // 重复 3 次
s.lines();                     // 按行分割（Java 11）
s.chars();                     // 字符流

// 比较
s1.equals(s2);                 // 内容比较
s1.equalsIgnoreCase(s2);       // 忽略大小写比较
s1.compareTo(s2);              // 字典序比较
s1.compareToIgnoreCase(s2);    // 忽略大小写字典序比较

// 类型转换
Integer.parseInt("123");       // String → int
Double.parseDouble("3.14");    // String → double
Long.parseLong("123456789");   // String → long
Boolean.parseBoolean("true");  // String → boolean
String.valueOf(42);            // int → String
String.valueOf(3.14);          // double → String

// StringBuilder（可变字符串，推荐用于拼接）
StringBuilder sb = new StringBuilder();
sb.append("Hello");
sb.append(", ");
sb.append("World");
sb.insert(0, ">> ");
sb.replace(0, 2, "");          // 替换
sb.delete(0, 3);               // 删除
sb.reverse();                  // 反转
String result = sb.toString(); // 转为不可变字符串

// String.join（拼接）
String joined = String.join(", ", "A", "B", "C");   // "A, B, C"
```

---

## 六、面向对象编程

### 类与对象

```java
public class Person {
    // 成员变量
    private String name;
    private int age;
    private String email;

    // 静态变量
    private static int count = 0;

    // 常量
    public static final String SPECIES = "Human";

    // 无参构造
    public Person() {
        this("Unknown", 0);
        count++;
    }

    // 全参构造
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
        count++;
    }

    // Getter / Setter
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) {
        if (age > 0) this.age = age;
    }

    // 实例方法
    public String getInfo() {
        return String.format("Name: %s, Age: %d", name, age);
    }

    // 静态方法
    public static int getCount() {
        return count;
    }

    // toString
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }

    // equals（内容比较）
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }

    // hashCode（与 equals 保持一致）
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}

// 使用
Person p = new Person("Alice", 25);
System.out.println(p.getInfo());
System.out.println(p);
```

### 继承与多态

```java
// 基类
public abstract class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    // 抽象方法（子类必须实现）
    public abstract void speak();

    // 普通方法
    public void sleep() {
        System.out.println(name + " is sleeping");
    }

    // final 方法（不可重写）
    public final void breathe() {
        System.out.println(name + " is breathing");
    }
}

// 派生类
public class Dog extends Animal {
    private String breed;

    public Dog(String name, String breed) {
        super(name);                   // 调用父类构造
        this.breed = breed;
    }

    @Override
    public void speak() {
        System.out.println(name + " barks!");
    }

    public void fetch() {
        System.out.println(name + " fetches the ball");
    }
}

public class Cat extends Animal {
    public Cat(String name) {
        super(name);
    }

    @Override
    public void speak() {
        System.out.println(name + " meows!");
    }
}

// 多态
Animal[] animals = {new Dog("Buddy", "Labrador"), new Cat("Whiskers")};
for (Animal a : animals) {
    a.speak();               // 调用各自版本
    a.sleep();               // 调用父类方法
}

// instanceof 检查
if (animal instanceof Dog) {
    ((Dog) animal).fetch();
}

// 模式匹配 instanceof（Java 16+）
if (animal instanceof Dog dog) {
    dog.fetch();             // 自动转型
}
```

### 接口

```java
// 接口定义
public interface Drawable {
    void draw();                          // 抽象方法

    default void resize() {               // 默认方法（Java 8+）
        System.out.println("Resizing...");
    }

    static void printInfo() {             // 静态方法
        System.out.println("Drawable interface");
    }

    int MAX_SIZE = 100;                   // 常量（public static final）
}

public interface Serializable {
    String serialize();
}

// 实现多个接口
public class Circle implements Drawable, Serializable {
    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public void draw() {
        System.out.println("Drawing circle with radius " + radius);
    }

    @Override
    public String serialize() {
        return "{\"radius\": " + radius + "}";
    }
}

// 函数式接口（只有一个抽象方法）
@FunctionalInterface
public interface MathOperation {
    int operate(int a, int b);
}
```

### 内部类与枚举

```java
// 成员内部类
public class Outer {
    private int x = 10;

    public class Inner {
        public void show() {
            System.out.println("x = " + x);    // 可访问外部类成员
        }
    }

    // 静态内部类
    public static class StaticInner {
        public void show() {
            System.out.println("Static inner class");
        }
    }

    // 局部内部类
    public void method() {
        class LocalInner {
            void show() { System.out.println("Local inner"); }
        }
        new LocalInner().show();
    }

    // 匿名内部类
    Runnable r = new Runnable() {
        @Override
        public void run() {
            System.out.println("Anonymous class");
        }
    };
}

// 枚举
public enum Status {
    ACTIVE, INACTIVE, DELETED
}

// 枚举（带属性和方法）
public enum Season {
    SPRING("春天", 1), SUMMER("夏天", 2),
    AUTUMN("秋天", 3), WINTER("冬天", 4);

    private final String name;
    private final int order;

    Season(String name, int order) {
        this.name = name;
        this.order = order;
    }

    public String getName() { return name; }
    public int getOrder() { return order; }
}

// 使用
Season s = Season.SPRING;
System.out.println(s.getName());     // 春天
System.out.println(s.ordinal());     // 0
System.out.println(s.name());        // SPRING
System.out.println(Season.valueOf("SUMMER"));
System.out.println(Season.values().length);   // 4

// switch 中使用枚举
switch (status) {
    case ACTIVE -> System.out.println("活跃");
    case INACTIVE -> System.out.println("未激活");
    case DELETED -> System.out.println("已删除");
}
```

### 记录类（Record，Java 16+）

```java
// 不可变数据类（自动生成构造、getter、equals、hashCode、toString）
public record Point(double x, double y) {
    // 自定义方法
    public double distance(Point other) {
        return Math.sqrt(Math.pow(x - other.x, 2) + Math.pow(y - other.y, 2));
    }

    // 紧凑构造器（参数校验）
    public Point {
        if (x < 0 || y < 0) {
            throw new IllegalArgumentException("坐标不能为负");
        }
    }
}

Point p1 = new Point(1, 2);
Point p2 = new Point(4, 6);
System.out.println(p1);              // Point[x=1.0, y=2.0]
System.out.println(p1.x());          // 1.0
System.out.println(p1.distance(p2)); // 5.0
```

---

## 七、集合框架

### 总览

| 接口 | 实现类 | 特点 |
|------|--------|------|
| `List` | `ArrayList` | 动态数组，随机访问 O(1) |
| `List` | `LinkedList` | 双向链表，插入/删除 O(1) |
| `Set` | `HashSet` | 哈希集合，无序 |
| `Set` | `TreeSet` | 有序集合（红黑树） |
| `Set` | `LinkedHashSet` | 保持插入顺序 |
| `Map` | `HashMap` | 哈希表，无序 |
| `Map` | `TreeMap` | 有序键值对（红黑树） |
| `Map` | `LinkedHashMap` | 保持插入顺序 |
| `Queue` | `LinkedList` | 队列 |
| `Queue` | `PriorityQueue` | 优先队列（堆） |
| `Deque` | `ArrayDeque` | 双端队列 |

### List

```java
import java.util.*;

// ArrayList（最常用）
List<String> list = new ArrayList<>();
list.add("A");                     // 添加
list.add(0, "B");                  // 指定位置插入
list.addAll(List.of("C", "D"));   // 批量添加
list.remove(0);                    // 按索引删除
list.remove("A");                  // 按对象删除
list.get(0);                       // 获取
list.set(0, "X");                  // 修改
list.indexOf("B");                 // 查找索引
list.contains("C");                // 是否包含
list.size();                       // 大小
list.isEmpty();                    // 是否为空
list.clear();                      // 清空
list.subList(0, 2);               // 子列表

// 遍历
for (String s : list) { System.out.println(s); }
for (int i = 0; i < list.size(); i++) { System.out.println(list.get(i)); }
list.forEach(s -> System.out.println(s));
list.forEach(System.out::println);

// 排序
Collections.sort(list);                          // 自然排序
Collections.sort(list, Comparator.reverseOrder()); // 降序
list.sort(Comparator.comparingInt(String::length)); // 按长度排序
list.sort(Comparator.comparing(Person::getAge)
                    .thenComparing(Person::getName));  // 多字段排序

// 不可变列表（Java 9+）
List<String> immutable = List.of("A", "B", "C");
List<String> mutable = new ArrayList<>(immutable);

// 查找
list.stream().filter(s -> s.startsWith("A")).findFirst();
```

### Map

```java
Map<String, Integer> map = new HashMap<>();
map.put("apple", 3);
map.put("banana", 5);
map.put("cherry", 7);

// 读取
map.get("apple");                  // 3
map.getOrDefault("date", 0);       // 0（不存在返回默认值）
map.containsKey("apple");          // true
map.containsValue(5);              // true
map.size();                        // 3

// 修改
map.put("apple", 10);              // 覆盖
map.putIfAbsent("date", 10);       // 不存在才放入
map.remove("banana");              // 删除
map.replace("apple", 3, 20);       // 条件替换（值匹配才替换）
map.merge("apple", 1, Integer::sum);     // 合并（累加）
map.compute("grape", (k, v) -> (v == null) ? 1 : v + 1);  // 计算
map.computeIfAbsent("melon", k -> 10);   // 不存在则计算
map.computeIfPresent("apple", (k, v) -> v * 2);  // 存在则计算

// 遍历
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
map.forEach((key, value) -> System.out.println(key + ": " + value));
for (String key : map.keySet()) { }
for (Integer value : map.values()) { }

// 排序
map.entrySet().stream()
    .sorted(Map.Entry.comparingByKey())
    .forEach(System.out::println);

map.entrySet().stream()
    .sorted(Map.Entry.comparingByValue(Comparator.reverseOrder()))
    .forEach(System.out::println);

// 统计
map.entrySet().stream()
    .max(Map.Entry.comparingByValue())
    .ifPresent(e -> System.out.println("最大: " + e));

// 不可变 Map（Java 9+）
Map<String, Integer> immutable = Map.of("a", 1, "b", 2);
Map<String, Integer> immutable2 = Map.ofEntries(
    Map.entry("a", 1),
    Map.entry("b", 2)
);

// TreeMap（有序）
Map<String, Integer> sortedMap = new TreeMap<>(map);
Map<String, Integer> reverseMap = new TreeMap<>(Comparator.reverseOrder());
reverseMap.putAll(map);

// LinkedHashMap（保持插入顺序）
Map<String, Integer> ordered = new LinkedHashMap<>(map);

// 计数统计
Map<String, Long> countMap = list.stream()
    .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
```

### Set / Queue / Deque

```java
// Set（自动去重）
Set<String> set = new HashSet<>();
set.add("A");
set.add("B");
set.add("A");              // 重复，不会添加
set.remove("A");
set.contains("B");
set.size();

// TreeSet（有序）
Set<Integer> sortedSet = new TreeSet<>(List.of(3, 1, 4, 1, 5, 9));
// → {1, 3, 4, 5, 9}

// 集合运算
Set<Integer> a = new HashSet<>(List.of(1, 2, 3));
Set<Integer> b = new HashSet<>(List.of(3, 4, 5));

Set<Integer> union = new HashSet<>(a);
union.addAll(b);                         // 并集

Set<Integer> intersection = new HashSet<>(a);
intersection.retainAll(b);               // 交集

Set<Integer> difference = new HashSet<>(a);
difference.removeAll(b);                 // 差集

// Queue（队列）
Queue<String> queue = new LinkedList<>();
queue.offer("A");                        // 入队
queue.offer("B");
queue.peek();                            // 查看队首
queue.poll();                            // 出队
queue.size();

// Deque（双端队列 / 可当栈用）
Deque<String> deque = new ArrayDeque<>();
deque.push("A");                         // 入栈
deque.push("B");
deque.pop();                             // 出栈
deque.offerFirst("X");                   // 头部入队
deque.offerLast("Y");                    // 尾部入队

// PriorityQueue（优先队列 / 堆）
PriorityQueue<Integer> minHeap = new PriorityQueue<>();    // 最小堆
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Comparator.reverseOrder());  // 最大堆
minHeap.offer(3);
minHeap.offer(1);
minHeap.offer(2);
minHeap.peek();          // 1（最小值）

// 自定义优先级
PriorityQueue<Task> taskQueue = new PriorityQueue<>(
    Comparator.comparingInt(Task::getPriority).reversed()
);
```

---

## 八、泛型

```java
// 泛型类
public class Box<T> {
    private T content;

    public void set(T content) { this.content = content; }
    public T get() { return content; }
}

Box<String> strBox = new Box<>();
strBox.set("Hello");
String s = strBox.get();

// 泛型方法
public static <T> void printArray(T[] array) {
    for (T item : array) {
        System.out.println(item);
    }
}

public static <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}

// 多参数泛型
public class Pair<K, V> {
    private K key;
    private V value;
    public Pair(K key, V value) { this.key = key; this.value = value; }
    public K getKey() { return key; }
    public V getValue() { return value; }
}

// 通配符
public void printList(List<?> list) { }              // 任意类型
public void addNumbers(List<? extends Number> list) { }  // 上界（Number 及子类）
public void addIntegers(List<? super Integer> list) { }  // 下界（Integer 及父类）

// 有界泛型
public class NumberBox<T extends Number> {
    private T value;
    public double doubleValue() { return value.doubleValue(); }
}

// 类型擦除
// 泛型只在编译期检查，运行时被擦除为 Object
// List<String> 和 List<Integer> 运行时都是 List
```

---

## 九、Lambda 与 Stream

### Lambda 表达式

```java
import java.util.function.*;

// 基本语法
Runnable r = () -> System.out.println("Hello");
BinaryOperator<Integer> add = (a, b) -> a + b;
Function<String, Integer> toLength = String::length;
Consumer<String> print = System.out::println;

// 函数式接口
Function<String, Integer> f1 = s -> s.length();
BiFunction<String, Integer, String> f2 = (s, n) -> s.substring(0, n);
Supplier<List<String>> f3 = ArrayList::new;
Consumer<String> f4 = s -> System.out.println(s);
Predicate<String> f5 = s -> s.isEmpty();
UnaryOperator<String> f6 = String::toUpperCase;
BiPredicate<String, String> f7 = String::equals;

// 方法引用
Function<String, Integer> length = String::length;          // 静态方法
Supplier<String> upper = "hello"::toUpperCase;               // 实例方法
Function<Integer, String> converter = String::valueOf;       // 构造方法
list.forEach(System.out::println);                           // 方法引用

// Optional（避免空指针）
Optional<String> opt = Optional.ofNullable(mayBeNull);
opt.ifPresent(v -> System.out.println(v));
opt.orElse("default");
opt.orElseGet(() -> computeDefault());
opt.orElseThrow(() -> new RuntimeException("空值"));
opt.map(String::toUpperCase).orElse("EMPTY");
opt.filter(s -> s.length() > 3).isPresent();
```

### Stream API

```java
import java.util.stream.*;
import java.util.*;

List<String> names = List.of("Alice", "Bob", "Charlie", "David", "Eve");

// 创建 Stream
Stream<String> s1 = Stream.of("A", "B", "C");
Stream<String> s2 = list.stream();
IntStream s3 = IntStream.range(1, 11);              // 1-10
IntStream s4 = IntStream.rangeClosed(1, 10);        // 1-10（含 10）
Stream<String> s5 = Arrays.stream(new String[]{"A", "B"});
Stream<String> s6 = Files.lines(Path.of("file.txt"));  // 文件行

// filter（过滤）
List<String> result = names.stream()
    .filter(n -> n.length() > 3)
    .collect(Collectors.toList());

// map（映射）
List<Integer> lengths = names.stream()
    .map(String::length)
    .collect(Collectors.toList());

// sorted（排序）
List<String> sorted = names.stream()
    .sorted()
    .collect(Collectors.toList());

List<String> sortedByLen = names.stream()
    .sorted(Comparator.comparingInt(String::length).reversed())
    .collect(Collectors.toList());

// distinct（去重）
List<Integer> unique = numbers.stream()
    .distinct()
    .collect(Collectors.toList());

// limit / skip
List<String> top3 = names.stream().limit(3).collect(Collectors.toList());
List<String> skip2 = names.stream().skip(2).collect(Collectors.toList());

// anyMatch / allMatch / noneMatch
boolean anyLong = names.stream().anyMatch(n -> n.length() > 5);
boolean allLong = names.stream().allMatch(n -> n.length() > 2);
boolean noneEmpty = names.stream().noneMatch(String::isEmpty);

// findFirst / findAny
Optional<String> first = names.stream().findFirst();
Optional<String> any = names.parallelStream().findAny();

// count / min / max
long count = names.stream().filter(n -> n.length() > 3).count();
Optional<String> longest = names.stream().max(Comparator.comparingInt(String::length));

// forEach
names.stream().forEach(System.out::println);

// reduce（归约）
int sum = numbers.stream().reduce(0, Integer::sum);
Optional<Integer> product = numbers.stream().reduce((a, b) -> a * b);
String joined = names.stream().reduce("", (a, b) -> a.isEmpty() ? b : a + ", " + b);

// Collectors
List<String> list = names.stream().collect(Collectors.toList());
Set<String> set = names.stream().collect(Collectors.toSet());
String joined = names.stream().collect(Collectors.joining(", ", "[", "]"));
Map<Integer, List<String>> groupByLen = names.stream()
    .collect(Collectors.groupingBy(String::length));
Map<String, Integer> toMap = names.stream()
    .collect(Collectors.toMap(Function.identity(), String::length));
IntSummaryStatistics stats = numbers.stream()
    .collect(Collectors.summarizingInt(Integer::intValue));
// stats.getAverage(), stats.getCount(), stats.getMax(), stats.getMin(), stats.getSum()

// flatMap（展平嵌套）
List<List<Integer>> nested = List.of(List.of(1, 2), List.of(3, 4));
List<Integer> flat = nested.stream()
    .flatMap(Collection::stream)
    .collect(Collectors.toList());

// 并行流
long count = list.parallelStream().filter(x -> x > 100).count();

// 综合示例
Map<String, List<Person>> groupByCity = people.stream()
    .filter(p -> p.getAge() > 18)
    .sorted(Comparator.comparing(Person::getName))
    .collect(Collectors.groupingBy(Person::getCity));

Optional<Person> oldest = people.stream()
    .max(Comparator.comparingInt(Person::getAge));
```

---

## 十、异常处理

```java
// 基本异常处理
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.err.println("算术异常: " + e.getMessage());
} catch (Exception e) {
    System.err.println("异常: " + e.getMessage());
    e.printStackTrace();
} finally {
    System.out.println("无论如何都执行");
}

// 多异常捕获（Java 7+）
try {
    // ...
} catch (IOException | SQLException e) {
    System.err.println(e.getMessage());
}

// try-with-resources（自动关闭资源，Java 7+）
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    String line = reader.readLine();
} catch (IOException e) {
    e.printStackTrace();
}

try (var reader = new BufferedReader(new FileReader("file.txt"));
     var writer = new BufferedWriter(new FileWriter("out.txt"))) {
    // 自动关闭
}

// 自定义异常
public class BusinessException extends Exception {
    private int code;

    public BusinessException(String message, int code) {
        super(message);
        this.code = code;
    }

    public int getCode() { return code; }
}

public class ValidationException extends RuntimeException {
    public ValidationException(String message) {
        super(message);
    }
}

// 抛出异常
public void withdraw(double amount) throws BusinessException {
    if (amount > balance) {
        throw new BusinessException("余额不足", 400);
    }
}

// 异常链
try {
    // ...
} catch (IOException e) {
    throw new BusinessException("文件处理失败", 500, e);
}

// 常见异常
// NullPointerException       空指针
// ArrayIndexOutOfBoundsException  数组越界
// ClassCastException          类型转换异常
// IllegalArgumentException   非法参数
// NumberFormatException       数字格式异常
// IOException                IO 异常
// FileNotFoundException     文件未找到
// SQLException              SQL 异常
// ConcurrentModificationException 并发修改异常
// IndexOutOfBoundsException  索引越界
```

---

## 十一、IO 流

### 文件操作

```java
import java.io.*;
import java.nio.file.*;
import java.util.*;

// ===== 字节流 =====
// 写入文件
try (FileOutputStream fos = new FileOutputStream("data.txt")) {
    fos.write("Hello, Java".getBytes());
    fos.write('\n');
}

// 读取文件
try (FileInputStream fis = new FileInputStream("data.txt")) {
    byte[] buffer = new byte[1024];
    int len;
    while ((len = fis.read(buffer)) != -1) {
        System.out.println(new String(buffer, 0, len));
    }
}

// ===== 字符流 =====
// 写入（带缓冲）
try (BufferedWriter writer = new BufferedWriter(new FileWriter("data.txt", true))) {
    writer.write("Hello");
    writer.newLine();
    writer.write("World");
}

// 读取（带缓冲）
try (BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}

// 读取全部行
List<String> lines = Files.readAllLines(Path.of("data.txt"), StandardCharsets.UTF_8);

// 写入全部行
Files.write(Path.of("data.txt"), List.of("Line 1", "Line 2"), StandardCharsets.UTF_8);
Files.write(Path.of("data.txt"), List.of("Line 3"), StandardOpenOption.APPEND);

// 读取全部内容
String content = Files.readString(Path.of("data.txt"));

// 写入字符串
Files.writeString(Path.of("data.txt"), "Hello, World!");

// ===== 对象序列化 =====
// 写入对象
try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("obj.dat"))) {
    oos.writeObject(new Person("Alice", 25));
}

// 读取对象
try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream("obj.dat"))) {
    Person p = (Person) ois.readObject();
}

// 序列化要求：类实现 Serializable 接口
public class Person implements Serializable {
    private static final long serialVersionUID = 1L;
    private String name;
    // ...
}

// ===== NIO（Java 7+，推荐）=====
Path path = Path.of("/home/user/file.txt");
Files.exists(path);
Files.isDirectory(path);
Files.isRegularFile(path);
Files.size(path);
Files.copy(Path.of("src.txt"), Path.of("dst.txt"));
Files.move(Path.of("old.txt"), Path.of("new.txt"));
Files.delete(Path.of("file.txt"));
Files.createDirectories(Path.of("a/b/c"));
Files.createFile(Path.of("new.txt"));
Files.list(Path.of("/dir"));               // 目录流
Files.walk(Path.of("/dir"));               // 递归遍历
Files.walk(Path.of("/dir"))
    .filter(Files::isRegularFile)
    .filter(p -> p.toString().endsWith(".txt"))
    .forEach(System.out::println);

// 路径操作
Path p = Path.of("/home/user/file.txt");
p.getFileName();               // file.txt
p.getParent();                 // /home/user
p.getRoot();                   // /
p.normalize();                 // 规范化路径
Path.of("/home").resolve("user/file.txt");   // 拼接路径

// 文件属性
BasicFileAttributes attrs = Files.readAttributes(path, BasicFileAttributes.class);
attrs.creationTime();
attrs.lastModifiedTime();
attrs.size();
```

---

## 十二、多线程

```java
// ===== 创建线程 =====
// 方式 1：继承 Thread
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running: " + getName());
    }
}
new MyThread().start();

// 方式 2：实现 Runnable（推荐）
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable running");
    }
}
new Thread(new MyRunnable()).start();
new Thread(() -> System.out.println("Lambda thread")).start();

// 方式 3：实现 Callable（有返回值）
Callable<Integer> task = () -> {
    Thread.sleep(1000);
    return 42;
};
ExecutorService executor = Executors.newSingleThreadExecutor();
Future<Integer> future = executor.submit(task);
int result = future.get();     // 阻塞等待结果
executor.shutdown();

// ===== 线程池 =====
// 固定大小线程池
ExecutorService fixedPool = Executors.newFixedThreadPool(5);

// 缓存线程池
ExecutorService cachedPool = Executors.newCachedThreadPool();

// 单线程池
ExecutorService singlePool = Executors.newSingleThreadExecutor();

// 定时线程池
ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(2);
scheduledPool.scheduleAtFixedRate(() -> {
    System.out.println("定时任务");
}, 0, 1, TimeUnit.SECONDS);

// 提交任务
fixedPool.submit(() -> System.out.println("Task 1"));
Future<String> f = fixedPool.submit(() -> "Result");
fixedPool.shutdown();          // 优雅关闭
fixedPool.shutdownNow();       // 立即关闭

// 自定义线程池（推荐生产使用）
ThreadPoolExecutor customPool = new ThreadPoolExecutor(
    2,                          // 核心线程数
    5,                          // 最大线程数
    60L, TimeUnit.SECONDS,      // 空闲线程存活时间
    new LinkedBlockingQueue<>(100),  // 任务队列
    new ThreadPoolExecutor.CallerRunsPolicy()  // 拒绝策略
);

// ===== 线程同步 =====
// synchronized
public class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}

// 同步代码块
synchronized (lock) {
    // 临界区
}

// ReentrantLock（更灵活）
Lock lock = new ReentrantLock();
lock.lock();
try {
    // 临界区
} finally {
    lock.unlock();
}

// 读写锁
ReadWriteLock rwLock = new ReentrantReadWriteLock();
rwLock.readLock().lock();
try { /* 读操作 */ } finally { rwLock.readLock().unlock(); }

rwLock.writeLock().lock();
try { /* 写操作 */ } finally { rwLock.writeLock().unlock(); }

// ===== 线程间通信 =====
// wait / notify
synchronized (lock) {
    while (!condition) {
        lock.wait();                // 等待
    }
    // 处理
}

synchronized (lock) {
    condition = true;
    lock.notify();                  // 唤醒一个
    // lock.notifyAll();            // 唤醒所有
}

// CountDownLatch（倒计时门闩）
CountDownLatch latch = new CountDownLatch(3);
for (int i = 0; i < 3; i++) {
    new Thread(() -> {
        // 执行任务
        latch.countDown();
    }).start();
}
latch.await();                      // 等待所有任务完成

// Semaphore（信号量）
Semaphore semaphore = new Semaphore(3);    // 最多 3 个并发
semaphore.acquire();
try {
    // 受限资源
} finally {
    semaphore.release();
}

// ===== 并发集合 =====
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("key", 1);
map.compute("key", (k, v) -> (v == null) ? 1 : v + 1);
map.computeIfAbsent("newKey", k -> 0);
map.forEach((k, v) -> System.out.println(k + ": " + v));

CopyOnWriteArrayList<String> cowList = new CopyOnWriteArrayList<>();

// ===== 原子类 =====
AtomicInteger atomicInt = new AtomicInteger(0);
atomicInt.incrementAndGet();       // ++i
atomicInt.addAndGet(5);            // += 5
atomicInt.compareAndSet(5, 10);    // CAS
atomicInt.get();

AtomicReference<String> ref = new Reference<>("initial");
AtomicLong atomicLong = new AtomicLong(0);
```

---

## 十三、日期与时间（java.time，Java 8+）

```java
import java.time.*;
import java.time.format.*;

// 当前时间
LocalDate today = LocalDate.now();                 // 2024-01-15
LocalTime now = LocalTime.now();                   // 10:30:45.123
LocalDateTime dateTime = LocalDateTime.now();      // 2024-01-15T10:30:45.123

// 创建
LocalDate date = LocalDate.of(2024, 1, 15);
LocalTime time = LocalTime.of(10, 30, 0);
LocalDateTime dt = LocalDateTime.of(2024, 1, 15, 10, 30, 0);

// 格式化
DateTimeFormatter fmt = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
String formatted = dt.format(fmt);                 // "2024-01-15 10:30:00"

// 解析
LocalDateTime parsed = LocalDateTime.parse("2024-01-15 10:30:00", fmt);
LocalDate parsedDate = LocalDate.parse("2024-01-15");

// 获取
date.getYear();                    // 2024
date.getMonth();                   // JANUARY
date.getMonthValue();              // 1
date.getDayOfMonth();              // 15
date.getDayOfWeek();               // MONDAY
date.getDayOfYear();               // 15
dt.getHour();                      // 10
dt.getMinute();                    // 30

// 计算
date.plusDays(7);                  // 加 7 天
date.plusMonths(1);                // 加 1 月
date.minusDays(3);                 // 减 3 天
date.withYear(2025);               // 修改年份

// 间隔
LocalDate start = LocalDate.of(2024, 1, 1);
LocalDate end = LocalDate.of(2024, 12, 31);
Period period = Period.between(start, end);
period.getYears();                 // 0
period.getMonths();                // 11
period.getDays();                  // 30

Duration duration = Duration.between(startTime, endTime);
duration.toHours();
duration.toMinutes();
duration.getSeconds();

// 时间戳
Instant instant = Instant.now();               // UTC 时间
long millis = instant.toEpochMilli();          // 毫秒时间戳
Instant fromMillis = Instant.ofEpochMilli(1700000000000L);

// 时区
ZonedDateTime zdt = ZonedDateTime.now(ZoneId.of("Asia/Shanghai"));
ZonedDateTime utcTime = ZonedDateTime.now(ZoneId.of("UTC"));

// 常用格式
DateTimeFormatter.ISO_DATE             // 2024-01-15
DateTimeFormatter.ISO_DATE_TIME        // 2024-01-15T10:30:00
DateTimeFormatter.ofPattern("yyyy年MM月dd日")
DateTimeFormatter.ofPattern("HH:mm:ss")
```

---

## 十四、JDBC 数据库操作

```java
import java.sql.*;

// 加载驱动（JDBC 4.0+ 自动加载，无需手动 Class.forName）
String url = "jdbc:mysql://localhost:3306/mydb?useSSL=false&serverTimezone=UTC";
String user = "root";
String password = "password";

// ===== 基本操作 =====
try (Connection conn = DriverManager.getConnection(url, user, password)) {

    // 查询
    String sql = "SELECT id, name, age FROM users WHERE age > ?";
    try (PreparedStatement ps = conn.prepareStatement(sql)) {
        ps.setInt(1, 18);
        try (ResultSet rs = ps.executeQuery()) {
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                int age = rs.getInt("age");
                System.out.println(id + ", " + name + ", " + age);
            }
        }
    }

    // 插入
    String insertSql = "INSERT INTO users (name, age) VALUES (?, ?)";
    try (PreparedStatement ps = conn.prepareStatement(insertSql)) {
        ps.setString(1, "Alice");
        ps.setInt(2, 25);
        int rows = ps.executeUpdate();
        System.out.println("插入 " + rows + " 行");
    }

    // 批量插入
    try (PreparedStatement ps = conn.prepareStatement(insertSql)) {
        for (int i = 0; i < 100; i++) {
            ps.setString(1, "User" + i);
            ps.setInt(2, 20 + i);
            ps.addBatch();
        }
        int[] results = ps.executeBatch();
    }

    // 更新
    String updateSql = "UPDATE users SET age = ? WHERE id = ?";
    try (PreparedStatement ps = conn.prepareStatement(updateSql)) {
        ps.setInt(1, 26);
        ps.setInt(2, 1);
        ps.executeUpdate();
    }

    // 删除
    String deleteSql = "DELETE FROM users WHERE id = ?";
    try (PreparedStatement ps = conn.prepareStatement(deleteSql)) {
        ps.setInt(1, 1);
        ps.executeUpdate();
    }

} catch (SQLException e) {
    e.printStackTrace();
}

// ===== 事务 =====
try (Connection conn = DriverManager.getConnection(url, user, password)) {
    conn.setAutoCommit(false);          // 关闭自动提交
    try {
        // 操作 1
        // 操作 2
        conn.commit();                   // 提交事务
    } catch (SQLException e) {
        conn.rollback();                 // 回滚事务
        throw e;
    } finally {
        conn.setAutoCommit(true);        // 恢复自动提交
    }
}

// ===== 获取自增主键 =====
String sql = "INSERT INTO users (name, age) VALUES (?, ?)";
try (PreparedStatement ps = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {
    ps.setString(1, "Alice");
    ps.setInt(2, 25);
    ps.executeUpdate();
    try (ResultSet keys = ps.getGeneratedKeys()) {
        if (keys.next()) {
            long id = keys.getLong(1);
        }
    }
}
```

---

## 十五、常用工具类

### Objects

```java
import java.util.Objects;

Objects.requireNonNull(obj);                // 非空检查（null 抛异常）
Objects.requireNonNull(obj, "不能为空");    // 自定义消息
Objects.equals(a, b);                      // 安全比较（null 安全）
Objects.isNull(obj);                       // 是否为 null
Objects.nonNull(obj);                      // 是否非 null
Objects.hash(a, b, c);                    // 计算哈希
Objects.toString(obj);                    // 安全转字符串
Objects.toString(obj, "default");          // null 返回默认值
```

### Math / Random

```java
Math.abs(-5);                  // 绝对值
Math.max(1, 2);                // 最大值
Math.min(1, 2);                // 最小值
Math.pow(2, 10);               // 幂
Math.sqrt(16);                 // 平方根
Math.round(3.14);              // 四舍五入
Math.ceil(3.14);               // 向上取整
Math.floor(3.14);              // 向下取整
Math.random();                 // 0-1 随机数

// 随机数
Random rand = new Random();
rand.nextInt(100);             // 0-99
rand.nextDouble();             // 0-1
rand.nextBoolean();
rand.ints(5, 0, 100).boxed().collect(Collectors.toList());  // 5 个随机数

// ThreadLocalRandom（多线程推荐）
ThreadLocalRandom.current().nextInt(1, 101);
```

### Collections 工具类

```java
import java.util.Collections;

List<Integer> list = new ArrayList<>(List.of(3, 1, 4, 1, 5));
Collections.sort(list);                    // 排序
Collections.reverse(list);                 // 反转
Collections.shuffle(list);                 // 随机打乱
Collections.max(list);                     // 最大值
Collections.min(list);                     // 最小值
Collections.frequency(list, 1);            // 出现次数
Collections.replaceAll(list, 1, 100);      // 替换所有
Collections.fill(list, 0);                 // 填充
Collections.unmodifiableList(list);        // 不可变列表
Collections.synchronizedList(list);        // 线程安全列表
Collections.emptyList();                   // 空列表
Collections.singletonList("A");            // 单元素列表
Collections.nCopies(5, "A");              // 重复元素列表
```

### 日志（SLF4J + Logback）

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

private static final Logger log = LoggerFactory.getLogger(MyClass.class);

log.debug("调试信息: {}", value);
log.info("普通信息: user={}", username);
log.warn("警告: cache miss");
log.error("错误: {}", e.getMessage(), e);
```

---

## 十六、JSON 处理

### Jackson

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.core.type.TypeReference;

ObjectMapper mapper = new ObjectMapper();

// 对象 → JSON
String json = mapper.writeValueAsString(person);
String prettyJson = mapper.writerWithDefaultPrettyPrinter()
                          .writeValueAsString(person);

// JSON → 对象
Person person = mapper.readValue(json, Person.class);
List<Person> list = mapper.readValue(json, new TypeReference<List<Person>>() {});
Map<String, Object> map = mapper.readValue(json, new TypeReference<Map<String, Object>>() {});

// 文件读写
mapper.writeValue(new File("data.json"), person);
Person p = mapper.readValue(new File("data.json"), Person.class);

// 常用注解
public class User {
    @JsonProperty("user_name")
    private String userName;

    @JsonIgnore
    private String password;

    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate birthday;

    @JsonInclude(JsonInclude.Include.NON_NULL)
    private String email;
}
```

### Gson

```java
import com.google.gson.Gson;
import com.google.gson.reflect.TypeToken;

Gson gson = new Gson();

String json = gson.toJson(person);
Person person = gson.fromJson(json, Person.class);
List<Person> list = gson.fromJson(json, new TypeToken<List<Person>>(){}.getType());
```

---

## 十七、常用设计模式速查

```java
// ===== 单例模式 =====
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }

    // 枚举单例（最安全）
    // public enum Singleton { INSTANCE; }
}

// ===== 工厂模式 =====
public interface Shape {
    void draw();
}

public class ShapeFactory {
    public static Shape create(String type) {
        return switch (type) {
            case "circle" -> new Circle();
            case "square" -> new Square();
            default -> throw new IllegalArgumentException("Unknown type: " + type);
        };
    }
}

// ===== 建造者模式 =====
public class User {
    private String name;
    private int age;
    private String email;

    private User(Builder builder) {
        this.name = builder.name;
        this.age = builder.age;
        this.email = builder.email;
    }

    public static class Builder {
        private String name;
        private int age;
        private String email;

        public Builder name(String name) { this.name = name; return this; }
        public Builder age(int age) { this.age = age; return this; }
        public Builder email(String email) { this.email = email; return this; }
        public User build() { return new User(this); }
    }
}

User user = new User.Builder()
    .name("Alice")
    .age(25)
    .email("alice@example.com")
    .build();

// ===== 策略模式 =====
@FunctionalInterface
public interface PaymentStrategy {
    void pay(double amount);
}

Map<String, PaymentStrategy> strategies = Map.of(
    "alipay", amount -> System.out.println("支付宝支付: " + amount),
    "wechat", amount -> System.out.println("微信支付: " + amount)
);
strategies.get("alipay").pay(100);

// ===== 观察者模式（Java 内置）=====
// java.util.Observable / Observer（已过时）
// 推荐用 PropertyChangeListener 或自定义事件

// ===== 模板方法 =====
public abstract class AbstractTask {
    public final void execute() {
        init();
        doTask();
        cleanup();
    }

    protected void init() { }
    protected abstract void doTask();
    protected void cleanup() { }
}
```

---

## 十八、实用技巧与最佳实践

```java
// 1. 使用 Optional 避免 NPE
Optional.ofNullable(value).map(String::toUpperCase).orElse("DEFAULT");

// 2. 使用 var（Java 10+，局部变量类型推断）
var list = new ArrayList<String>();
var map = new HashMap<String, Integer>();
var stream = list.stream();

// 3. 使用 List.of / Map.of 创建不可变集合
List<String> list = List.of("A", "B", "C");
Map<String, Integer> map = Map.of("a", 1, "b", 2);

// 4. 使用 Stream 处理集合
list.stream().filter(x -> x > 0).map(String::valueOf).collect(Collectors.toList());

// 5. 使用 String.format / formatted（Java 15+）
String s = "Name: %s, Age: %d".formatted(name, age);

// 6. 使用 switch 表达式（Java 14+）
String type = switch (code) {
    case 1 -> "Admin";
    case 2 -> "User";
    default -> "Guest";
};

// 7. 使用 Text Block（Java 15+）
String json = """
    {
        "name": "Alice",
        "age": 25
    }
    """;

// 8. 使用 record 创建不可变数据类
record Point(double x, double y) {}

// 9. 使用 Sealed Classes（Java 17+）
sealed interface Shape permits Circle, Square {}
record Circle(double r) implements Shape {}
record Square(double s) implements Shape {}

// 10. 使用 try-with-resources
try (var reader = Files.newBufferedReader(path)) { }

// 11. 使用 Comparator 链
list.sort(Comparator.comparing(Person::getAge)
                    .thenComparing(Person::getName)
                    .reversed());

// 12. 使用 Collectors
Collectors.toList(), toSet(), toMap(), joining(), groupingBy(), counting()

// 13. 使用 Map.merge 处理计数
Map<String, Integer> count = new HashMap<>();
words.forEach(w -> count.merge(w, 1, Integer::sum));

// 14. 使用 IntSummaryStatistics
IntSummaryStatistics stats = numbers.stream()
    .mapToInt(Integer::intValue)
    .summaryStatistics();
// stats.getAverage(), stats.getCount(), stats.getMax(), stats.getMin(), stats.getSum()

// 15. 使用 Files.walk 遍历文件
Files.walk(Path.of("/root"))
    .filter(Files::isRegularFile)
    .filter(p -> p.toString().endsWith(".java"))
    .forEach(System.out::println);
```

---

## 十九、Maven 常用命令

```bash
mvn clean                       # 清理 target
mvn compile                     # 编译
mvn test                        # 运行测试
mvn package                     # 打包（生成 jar/war）
mvn install                     # 安装到本地仓库
mvn deploy                      # 部署到远程仓库
mvn clean install               # 清理并安装
mvn clean package               # 清理并打包
mvn dependency:tree             # 查看依赖树
mvn dependency:resolve          # 下载依赖
mvn archetype:generate          # 创建项目
mvn exec:java -Dexec.mainClass="com.example.Main"   # 运行主类
mvn spring-boot:run             # 运行 Spring Boot 项目

# 常用选项
mvn -DskipTests package         # 跳过测试
mvn -Dmaven.test.skip=true package  # 跳过测试编译
mvn -U clean install            # 强制更新快照
mvn -o clean install            # 离线模式
mvn -X clean install            # 调试模式
```

**pom.xml 常用配置：**
```xml
<project>
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.30</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

---

## 二十、常见报错与解决

| 错误 | 原因 | 解决 |
|------|------|------|
| `NullPointerException` | 对象为 null | 用 `Optional` / 判空 |
| `ClassNotFoundException` | 类未找到 | 检查 classpath、依赖 |
| `IndexOutOfBoundsException` | 索引越界 | 检查数组/集合长度 |
| `ClassCastException` | 类型转换失败 | 用 `instanceof` 检查 |
| `NumberFormatException` | 数字格式错误 | 检查输入字符串 |
| `ConcurrentModificationException` | 遍历时修改集合 | 用 `Iterator.remove()` |
| `StackOverflowError` | 递归过深 | 改用循环 |
| `OutOfMemoryError` | 内存溢出 | 调大堆内存 `-Xmx` |
| `SQLException` | SQL 错误 | 检查 SQL 语句、连接 |
| `IOException` | IO 错误 | 检查文件路径、权限 |
| `UnsupportedOperationException` | 集合不可修改 | 用 `new ArrayList<>(list)` |
| `IllegalArgumentException` | 非法参数 | 检查传入值 |

**JVM 常用参数：**
```bash
java -Xms512m -Xmx2g -Xss256k Main          # 初始堆、最大堆、栈大小
java -XX:+UseG1GC Main                       # 使用 G1 垃圾回收器
java -XX:+PrintGCDetails Main                # 打印 GC 详情
java -XX:+HeapDumpOnOutOfMemoryError Main    # OOM 时导出堆转储
java -Dspring.profiles.active=prod Main      # 设置系统属性
```

**调试工具：**
```bash
# JPS - 查看 Java 进程
jps -l

# JStack - 线程转储
jstack <pid>

# JMap - 堆信息
jmap -heap <pid>
jmap -dump:format=b,file=heap.hprof <pid>

# JStat - GC 统计
jstat -gcutil <pid> 1000

# JConsole / VisualVM - 图形化监控
jconsole
```

---

## 二十一、完整示例：学生管理系统

```java
import java.util.*;
import java.util.stream.Collectors;

// 数据类（Record）
record Student(int id, String name, double score) {
    @Override
    public String toString() {
        return String.format("%-4d %-10s %.1f", id, name, score);
    }
}

public class StudentManager {
    private final List<Student> students = new ArrayList<>();
    private int nextId = 1;

    // 添加学生
    public Student add(String name, double score) {
        Student student = new Student(nextId++, name, score);
        students.add(student);
        return student;
    }

    // 删除学生
    public boolean remove(int id) {
        return students.removeIf(s -> s.id() == id);
    }

    // 查找学生
    public Optional<Student> findById(int id) {
        return students.stream().filter(s -> s.id() == id).findFirst();
    }

    // 按姓名查找
    public List<Student> findByName(String name) {
        return students.stream()
                .filter(s -> s.name().equalsIgnoreCase(name))
                .collect(Collectors.toList());
    }

    // 按成绩排序
    public List<Student> sortByScore(boolean ascending) {
        Comparator<Student> comparator = Comparator.comparingDouble(Student::score);
        if (!ascending) comparator = comparator.reversed();
        return students.stream()
                .sorted(comparator)
                .collect(Collectors.toList());
    }

    // 获取前 N 名
    public List<Student> getTopN(int n) {
        return students.stream()
                .sorted(Comparator.comparingDouble(Student::score).reversed())
                .limit(n)
                .collect(Collectors.toList());
    }

    // 统计信息
    public Map<String, Object> getStatistics() {
        var stats = students.stream()
                .mapToDouble(Student::score)
                .summaryStatistics();
        return Map.of(
            "count", stats.getCount(),
            "average", String.format("%.1f", stats.getAverage()),
            "max", stats.getMax(),
            "min", stats.getMin()
        );
    }

    // 成绩分段
    public Map<String, Long> getScoreDistribution() {
        return students.stream()
                .collect(Collectors.groupingBy(
                    s -> {
                        double score = s.score();
                        if (score >= 90) return "A (90-100)";
                        if (score >= 80) return "B (80-89)";
                        if (score >= 70) return "C (70-79)";
                        if (score >= 60) return "D (60-69)";
                        return "F (<60)";
                    },
                    TreeMap::new,
                    Collectors.counting()
                ));
    }

    // 显示所有学生
    public void displayAll() {
        System.out.printf("%-4s %-10s %s%n", "ID", "Name", "Score");
        System.out.println("-".repeat(25));
        students.forEach(System.out::println);
    }

    // 主方法测试
    public static void main(String[] args) {
        StudentManager mgr = new StudentManager();

        mgr.add("Alice", 92.5);
        mgr.add("Bob", 85.0);
        mgr.add("Charlie", 97.8);
        mgr.add("David", 72.3);
        mgr.add("Eve", 88.6);

        System.out.println("=== 所有学生 ===");
        mgr.displayAll();

        System.out.println("\n=== 成绩排名 ===");
        mgr.sortByScore(false).forEach(System.out::println);

        System.out.println("\n=== 前 3 名 ===");
        mgr.getTopN(3).forEach(System.out::println);

        System.out.println("\n=== 统计信息 ===");
        mgr.getStatistics().forEach((k, v) -> System.out.println(k + ": " + v));

        System.out.println("\n=== 成绩分布 ===");
        mgr.getScoreDistribution().forEach((k, v) -> System.out.println(k + ": " + v));
    }
}
```