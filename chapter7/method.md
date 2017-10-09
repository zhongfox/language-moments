# 7.0 方法

|          | Ruby                                 | Javascript   | Lua          | Java         | Go                           |
|----------|--------------------------------------|--------------|--------------|--------------|------------------------------|
| 动态消息 | Y                                    | Y            |              |              |                              |
| 调用方式 | duck.quack()<br>duck::quack() 不常用 | duck.quack() | duck.quack() | duck.quack() | duck.quack()<br>duck:quack() |


方法和函数的区别

方法调用有时候也叫作发送消息

---

### Ruby

命名约定:

* 以`?`结尾的方法称为断言(predicate), 通常要求返回一个Boolean值, 但并非强制.

* 以`!`结尾的方法叫做 bang! 方法, 代表可变(mutator)方法, 它会改变对象内部状态(也并非强制), 需要小心使用.

  多数 bang! 方法如果什么都没做就会返回 nil, **最佳实践**: 永远不要依赖于内建的 bang! 方法的返回值.

---

### Javascript

---

### Go

只能为当前包, 以及除接口和(指针???)以外的任何类型定义方法.

| 分类        | 分类                        | 特点                   |
|-------------|-----------------------------|------------------------|
| named type  | pre-declared, 如 int        | 可以定义自己的method   |
| named type  | type declared new type      | 可以定义自己的method   |
| unamed type | type literal, 如*int ,[]int | 不可以定义自己的method |

但是, 一个type定义方法必须在该类型的所在的package , int 的scope （作用域是）universe (全局的)，int 是语言层面预声明的，其属于任何package,也就没有办法为其增加method.

#### 在声明方法时，如果一个类型名称本身就是一个指针的话，不允许出现在方法的接收器中

```go
import (
	"fmt"
)
type littleGirl struct{
	Name string
	Age int
}
type girl *littleGirl
func(this girl) changeName(name string){
	this.Name = name
}
func main(){
	littleGirl := girl{Name:"Rose", Age:1}

	girl.changeName("yoyo")
	fmt.Println(littleGirl)
}
```

以上代码会编译报错: `invalid receiver type girl(girl is a pointer type)`

Go语言中规定，只有类型（Type）和指向他们的指针（*Type）才是可能会出现在接收器声明里的两种接收器，为了避免歧义，明确规定，如果一个类型名本身就是一个指针的话，是不允许出现在接收器中的

---

### Lua
