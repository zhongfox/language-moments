# 7.2 继承链




### 1. Ruby

```ruby
class A
  def hi
    puts 'hello'
  end
end

class B < A
end

b = B.new
b.hi #hello
```

简单的继承链如下, 包括class关系和superclass关系, 可以通过「向右一步, 向上查找」, 进行方法查找:

![简单Ruby 继承链](/images/ruby_inherit_1.png)


如果加上单键类, 就会比较复杂

![复杂Ruby 继承链](/images/ruby_inherit_2.png)


---

### 2. Javascript

最简单的javascript继承类似这样:

```javascript
function A() {
  this.name = 'fox'
};
A.prototype.puts = function() {console.log('hello world');};
function B() {};
B.prototype = new A();

var a = new A();
var b = new B();
b.puts() //hello world
```

以上实现有如下问题:

* B的对象的constructor失真
* 在构造器A中的对象属性, 在B的对象中没有实现, 仅仅成为了继承属性(原型链上的, 只能读), 而不是对象私有属性(可读可写)


```javascript
b.constructor //[Function: A]
b.name // 'fox' 这个属性属于原型链, 而不是b本身
```

node.js中的util核心包是node.js自带的核心代码, 其中提供了inherits方法:

```javascript
var util=require('util');

function A() {
  this.name = 'fox'
};
A.prototype.a = function() {console.log('a')};
//目的是让this去执行A方法, 这样this可以有A方法里定义的属性 (类似其他语言中调用super)
function B() {A.call(this)};
util.inherits(B, A);

var a = new A();
var b = new B();

b.constructor //{ [Function: B] super_: [Function: A] }
b.name // b 自身的属性
```

注意`A.call(this)`, 作用是让this去执行A方法, 这样this可以有A方法里定义的属性.

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

* `x instanceof y`~可以理解为:

  `x[.__proto__]+ === y.prototype` //[和]和+是正则表达式中的含义

  因为`b.__proto__ === B.prototype` 所以 `b instanceof B 为 true`

  因为`b.__proto__.__proto__ === A.prototype` 所以 `b instanceof A 为 true`

  因为`b.__proto__.__proto__.__proto__ === Object.prototype` 所以 `b instanceof Object 为 true`

  而`b instanceof Function 为 false`

* `__proto__`

  * 函数: 所有函数的`__proto__` 指向Function.prototype

  * 对象:

    手动实现的继承`B.prototype = new A()` `子类.__proto__` 指向`父类.prototype`

    除此之外的`__proto__`指向`Object.prototype`

* 原型链继承的本质是: 在对象上迭代`__proto__` 直到找到需要的方法.


Ruby继承关系中方法查找有个口诀叫做: 向右一步, 向上查找, 对Javascript继承链稍作调整, 可以看到相似的关系:

![Javascript 继承链变形](/images/javascript_inherit_2.png)

* 对象和类的关系

  黑色 `__proto__` 类似 ruby中`.class`

  如果`obj.__proto__ === klass.prototype`  那么obj是klass的实例

* 类的继承关系

  蓝色`__proto__` 类似 ruby中`.superclass`

  如果`subklass.prototype.__proto__ === superklass.prototype` 那么subklass是superklass的子类

* prototype的作用有点类似ruby中的singletonclass, 都是存放实例方法/属性的地方

* Javascript Function 类比 Ruby Class

* Javascript Object 类比 Ruby Object


---



[comment]: <> (复制)
