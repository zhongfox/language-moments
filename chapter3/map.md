# 3.11 Hash

哈希是一种数据结构, 它维护了一个键值对集合, 而且将每个键都与一个值关联起来. 哈希也被称为映射(map), 有时也叫作关联数组(associative array)

|         | Ruby     | Javascript | Javascript ES6 | Go  | Lua  |
|---------|----------|------------|----------------|-----|------|
| 实现    | Hash     | Object     | Map            | map |      |
| key类型 | 任意     | 仅字符串   | 任意           |     |      |
| 顺序    | 插入顺序 | 无序       | 插入顺序       |     | TODO |

哈希在底层通常是利用叫做**哈希表**的数据结构来实现的, 那些作为哈希的键, 通常有方法算得一个哈希码, 如果两个键相同, 算出来的哈希码相等, 但是不同的键算出来的哈希码可能相同, 这种现象叫做哈希冲突.

---

### 1. Ruby

Ruby Hash 键可以是任一类型, 在Ruby1.9之后, Hash是有序的, `keys()`, `values()` 都会按照属性插入的顺序返回.

```ruby
# 字面量
person = {age: 8, name: "fox"}         // key 为Symbol
person = {"age" => 8, "name" => "fox"} // key 为String

# 设置
person["age"] = 8

# 读取
person["age"]

# 获取键
person.keys() #["age", "name"]

# 获取值
person.values() # [8, "fox"]
```

---

### 2. Javascript

#### Object

在ES6 之前, Object是大量用于需要哈希的场景, Javascript 的Object键只能使用字符串.

属性读取时, 有两种形式:
* 方括号+属性字符串: 优点是可以通过变量来访问
* 点号+属性: 在属性确定的情况下, 这种访问更加简洁

```Javascript
// 字面量
var person = {"age": 8, name: "fox"};
var key = "age";

// 设置
person.age = 8;
person[key] = 8; //通常只用于key是变量的情况

// 读取
person.age  //8
person[key] //8

// 获取键
Object.keys(person) //[ 'age', 'name' ]

// 获取值没有内置方法
```

#### Map

Object结构提供了“字符串—值”的对应，Map结构提供了“值—值”的对应，是一种更完善的Hash结构实现。如果你需要“键值对”的数据结构，Map比Object更合适.

```javascript
var m = new Map();
var o = {p: 'Hello World'};

// Map 的key可以是任意值:
m.set(o, 'content') // 设置
m.get(o) // 获取, 返回 "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false

// 可以用数组进行初始化
var map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

// 提供了长度读取
map.size // 2
```

primitive 类型数据如果值相同, 后设置的值会覆盖前设置的值; 对象类型的key, 只要内容地址不同, 就视为2个key, 这就解决了同名属性碰撞（clash）的问题:

```javascript
var map = new Map();

map.set('x', 1) // Map { 'x' => 1 }
map.set('x', 2) // Map { 'x' => 2 } // 覆盖

map.set({}, 3) // Map { 'x' => 2, {} => 3 }
map.set({}, 4) // Map { 'x' => 2, {} => 3, {} => 4 } 不会覆盖
```

---

### 3. Go

---


### 4. Lua

---


<!--
| 尾逗号    | 允许 | 不允许                            |        |    |     |
-->
