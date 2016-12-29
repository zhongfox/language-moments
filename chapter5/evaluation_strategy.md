# 5.2 求值策略

> 求值策略 [Evaluation_strategy](https://en.wikipedia.org/wiki/Evaluation_strategy), 如值传递/引用传递, 属于函数调用时参数的求值策略, 这是对调用函数时, 求值和传值的方式的描述

 <table><tbody>
   <tr>
     <th>Evaluation</th>
     <th >对原值的影响</th>
     <th >Value Content</th>
     <th >实例</th>
     <th >改变方式</th>
     <th >对原值指向的原始对象的影响</th>
   </tr>
   <tr>
     <td  rowspan="4">值传递</td>
     <td  rowspan="4">对原值复制<br>无法改变原值</td>
     <td  rowspan="2">值类型</td>
     <td >Go<br>JavaScript_primitive<br>Ruby_Symbol_Fixnum</td>
     <td >change</td>
     <td >无法改变<br>见实例一</td>
   </tr>
   <tr>
     <td >Go</td>
     <td >mutate</td>
     <td >无法改变<br>见实例二</td>
   </tr>
   <tr>
     <td  rowspan="2">引用类型<br>Call by sharing</td>
     <td  rowspan="2">Ruby<br>Javascript_Object<br>Go_map_channel_slice</td>
     <td >change</td>
     <td >无法改变<br>见情况三</td>
   </tr>
   <tr>
     <td >mutate</td>
     <td >可以改变<br>见情况四</td>
   </tr>
   <tr>
     <td colspan="1">引用传递</td>
     <td colspan="1">没有复制<br>可以改变原值</td>
     <td colspan="1">值类型/引用</td>
     <td colspan="1">C#</td>
     <td colspan="1">change/mutate</td>
     <td colspan="1">可以改变</td>
   </tr>
 </tbody></table>


---

### 区分求值策略(Evaluation Strategy)和内容类型(Value Content):

**求值策略**(值传递和引用传递)的关注的点在于:

* 求值的时机:  传入的参数(表达式)在调用函数的过程中，求值的时机、值的形式的选取等问题

  求值时机可能是在调用前, 调用后(用到才求值, 类似Lazy Loading)

* 传递形式: 主要关注有无副本

| 求值策略                    | 求值时机 | 传递方式         | 根本区别     | 影响                     |
|-----------------------------|----------|------------------|--------------|--------------------------|
| 值传递(Pass by value)       | 调用前   | 原值的副本       | 会创建副本   | 函数无法改变(change)原值 |
| 引用传递(Pass by reference) | 调用前   | 原值(无副本)     | 不会创建副本 | 函数可以改变(change)原值 |
| 名传递(Pass by name)        | 调用后   | 与值无关的一个名 | -            | -                        |



**内容类型**(值类型/引用类型), 是用于区分两种内存分配方式, 值类型在调用栈上分配, 引用类型在堆上分配。

总结: 一个描述参数求值策略, 一个描述内存分配方式, 两者之间无任何依赖或约束关系。

---

### 理解改变

「改变」一词在函数中通常有2种不同的含义:

* mutate: 表示内容属性的修改, 但是内容引用不变, 如在ruby中:
  > `person[:name] = 'zhong'`
* change: 表示直接改变量指向, 表现通常是进行重新赋值:
  >  `person = { name: 'zhong' }`

值传递无法改变原值, 说的「改变」是指的「change」, 而不是「mutate」, 函数中的mutate能否改变原值指向的原始对象还和传递的内容类型(Content Value)有关:

| 求值策略 | Content Value    | 函数内mutate(如果对象可变) | 函数内change   |
|----------|------------------|----------------------------|----------------|
| 值传递   | 值类型(被复制)   | 不影响原始对象             | 不影响原始对象 |
| 值传递   | 引用类型(被复制) | 影响原始对象               | 不影响原始对象 |

---

### Show Me the Code

#### 实例一: 值传递+值类型+change

Javascript:

```javascript
function change(name) {
  name = 'zhong';
}

var myName = 'fox';
change(myName);
console.log(myName); //fox; 没有改变
```

#### 实例二: 值传递+值类型+mutate

Go:

```go
package main
import "fmt"

type Person struct {
  name string
}

func change(person Person) {
  person.name = "zhong"
}

func main() {
  me := Person{"fox"}
  change(me)
  fmt.Println(me.name) //fox; 没被改变
}
```

#### 实例三: 值传递+引用类型+change

Ruby:

```ruby
def change(person)
  person[:name] = { name: 'zhong' }
end

me = { name: 'fox' }

change(me)
puts(me[:name]) # fox; 没被改变
```

#### 实例四: 值传递+引用类型+mutate

Ruby:

```ruby
def change(person)
 person[:name] = 'zhong'
end

me = { name: 'fox' }

change(me)
puts(me[:name]) # zhong; 被改变了
```

Go:

```go
package main

import "fmt"

type Person map[string]string

func change(person Person) {
  person["name"] = "zhong"
}

func main() {
  me := Person{"name": "fox"}

  change(me)
  fmt.Println(me["name"]) //zhong, 被改变了
}
```

---

### 对比引用传递

以上的语言中都没有引用传递方式, C# 里既支持值传递, 又支持引用传递, 比对起来看看:

通过值传递调用:

```cs
using System;
public class Person
{
  public Person(string name)
  {
    Name = name;
  }
  public string Name { get; set; }
}

public class testEvaluation
{
   public static void Change(Person person) //值传递方式, 存在复制副本
   {
      person = new Person("zhong"); //仅副本变化, 原始实参没有变化
   }

   public static void Main(string[] args)
   {
      Person me = new Person("fox");
      testEvaluation.Change(me);
      Console.WriteLine(me.Name); //fox, 值传递, 原始实参没有变化
   }
}
```

通过引用传递调用:

```cs
using System;
public class Person
{
  public Person(string name)
  {
    Name = name;
  }
  public string Name { get; set; }
}

public class testEvaluation
{
   public static void Change(ref Person person) //引用传递方式, 没有复制副本
   {
      person = new Person("zhong"); //改变了原始值
   }

   public static void Main(string[] args)
   {
      Person me = new Person("fox");
      testEvaluation.Change(ref me);
      Console.WriteLine(me.Name); //zhong, 原始值被改变
   }
}
```
