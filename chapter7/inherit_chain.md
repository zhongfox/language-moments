# 7.1 继承链

### 1. Ruby

```ruby
class Animal
  def eat
    puts 'eat food'
  end
end

class Duck < Animal
  def eat
    puts 'eat fish'
  end
end

animal = Animal.new
animal.eat # eat food

duck = Duck.new
duck.eat # eat fish
```

简单的继承链如下, 包括class关系和superclass关系, 可以通过「向右一步, 向上查找」, 进行方法查找:

![简单Ruby 继承链](/images/ruby_inherit_1.png)


如果加上单键类, 就会比较复杂

![复杂Ruby 继承链](/images/ruby_inherit_2.png)


方法查找:


```ruby
class Animal
  def eat
    puts 'eat food'
  end

  # 增加一个类方法
  class << self
    def count
    end
  end
end

class Duck < Animal
  def eat
    puts 'eat fish'
  end
end

duck = Duck.new

# duck的单键方法
def duck.fly
  puts "I'm a special, I can fly~"
end
```

* 对象的实例方法`obj.methods`存在于class中(`obj.class.instance_methods`)

  ```ruby
  duck.methods.grep /eat/ # eat
  duck.class.instance_methods(false) # eat
  ```

* 对象(包括类对象)的单键方法(包括类的类方法)`obj.singleton_methods` 是定义在对象的单键类中: `obj.singletonclass.instance_methods`

  ```ruby
  #true
  duck.singleton_methods(false) == duck.singleton_class.instance_methods(false) # fly
  #true
  Animal.singleton_methods(false) == Animal.singleton_class.instance_methods(false) # count
  ```

* 一个对象的实例方法的查找, 遵循「向右一步, 向上查找」; 这个「向右」通常找到的是对象的class, 不过, 如果要查找单键方法(包括类查找类方法), 这个「向右」需要从singletonclass开始查找.

---

### 2. Javascript

最简单的javascript继承类似这样:

```javascript
function Animal() { this.age = 0 }
Animal.prototype.eat = function() {
  console.log('eat food')
}

function Duck () {}
Duck.prototype = new Animal()
Duck.prototype.eat = function() {
  console.log('eat fish')
}

var animal = new Animal()
animal.eat() // 'eat food'

var duck = new Duck()
duck.eat() // 'eat fish'
```

以上实现有如下问题:

* Duck实例的constructor失真
* 在构造器Animal中的对象属性, 在Duck的对象中没有实现, 仅仅成为了继承属性(原型链上的, 只能读), 而不是对象私有属性(可读可写)


```javascript
duck.constructor //[Function: Animal]
duck.age // 0 这个属性属于原型链, 而不是duck本身
```

node.js中的util核心包是node.js自带的核心代码, 其中提供了inherits方法:

```javascript
var util = require('util');

function Animal() { this.age = 0 }
Animal.prototype.eat = function() {
  console.log('eat food')
}

//目的是让this去执行A方法, 这样this可以有A方法里定义的属性 (类似其他语言中调用super)
function Duck() {Animal.call(this)};
Duck.prototype.eat = function() {
  console.log('eat fish')
}
util.inherits(Duck, Animal);

var animal = new Animal()
animal.eat() // 'eat food'

var duck = new Duck()
duck.eat() // 'eat fish'

duck.constructor //{ [Function: Duck] super_: [Function: Animal] }
duck.age // duck 自身的属性
```

注意`Animal.call(this)`, 作用是让this去执行Animal方法, 这样this可以有Animal方法里定义的属性.

再看看inherits的实现:

```javascript
exports.inherits = function(ctor, superCtor) {
 ctor.super_ = superCtor;
 ctor.prototype = Object.create(superCtor.prototype, {
 constructor: {
  value: ctor,
  enumerable: false,
  writable: true,
  configurable: true
 }
 });
};
```

* 对子类设置了`super_` 指向父类
* 子类构造器的prototype采用`Object.create`, 其中一个作用就是设定constructor为子类

#### Javascript 继承链

![Javascript 继承链](/images/javascript_inherit_1.png)

* `x instanceof y` 可以理解为:

  `x[.__proto__]+ === y.prototype` //[和]和+是正则表达式中的含义

  因为`duck.__proto__ === Duck.prototype` 所以 `duck instanceof Duck 为 true`

  因为`duck.__proto__.__proto__ === Animal.prototype` 所以 `duck instanceof Animal 为 true`

  因为`duck.__proto__.__proto__.__proto__ === Object.prototype` 所以 `duck instanceof Object 为 true`

  而`duck instanceof Function 为 false`

* `__proto__`

  * 函数: 所有函数的`__proto__` 指向Function.prototype

  * 对象:

    手动实现的继承`Duck.prototype = new Animal()` `子类.__proto__` 指向`父类.prototype`

    除此之外的`__proto__`指向`Object.prototype`

* 原型链继承的本质是: 在对象上迭代`__proto__` 直到找到需要的方法.


Ruby继承关系中方法查找有个口诀叫做: 向右一步, 向上查找, 对Javascript继承链稍作调整, 可以看到相似的关系:

![Javascript 继承链变形](/images/javascript_inherit_2.png)

* 对象和类的关系(黑色箭头)

 `__proto__` 类似 ruby中`.class`

  如果`obj.__proto__ === klass.prototype`  那么obj是klass的实例

* 类的继承关系(蓝色箭头)

  `__proto__` 类似 ruby中`.superclass`

  如果`subklass.prototype.__proto__ === superklass.prototype` 那么subklass是superklass的子类

* prototype的作用有点类似ruby中的singletonclass, 都是存放实例方法/属性的地方

* Javascript Function 类比 Ruby Class

* Javascript Object 类比 Ruby Object

---

### 3. Go

Go 没有class, 只有组合, 没有继承.

---

### 4. Lua

```lua
local Animal = {}

function Animal:new ()
  local new_animal = {}

  self.__index = self
  setmetatable(new_animal, self)

  return new_animal
end

function Animal:eat ()
  print "eat food"
end

local Duck = Animal:new()

function Duck.eat ()
  print "eat fish"
end

local animal = Animal:new()
animal:eat() -- eat food

local duck = Duck:new()
duck:eat() -- eat fish
```

![Lua 继承链](/images/lua_inherit.png)

* 类和实例不是割裂的概念

  类Duck和对象animal的创建方式完全一样: `Animal:new()`, 类和对象的区分依赖于程序的语义需求.

  Duck 既是一个类, 也是对象; 或者说Animal本来是一个对象, 但是可以用作一个类.

---

[comment]: <> (复制)
