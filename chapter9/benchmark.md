# 9.4 Benchmark

### Ruby

#### 标准库的Benchmark

* `Benchmark.measure {code}`
* `Benchmark.bm(label_width = 0) {|job| job.report(label) {code} }` 可以有多个job.report， label可选
* `Benchmark.bmbm(label_width)` 2次测试，第一次是演练，第二次是真实

示例:

```ruby
require 'benchmark'
n = 100_000
size = 10_000
array = (0...size).to_a.shuffle

benchmark.bm do |x|
  x.report("#at") {
    n.times { array.at rand(size)}
  }

  x.report("#index") {
    n.times { array.index rand(size) }
  }
end

       user     system      total        real
#at  0.020000   0.000000   0.020000 (  0.025401)
#index  3.060000   0.010000   3.070000 (  3.062166)
```

输出四个时间分别是(单位是秒)：

```
user: the amount of time spent executing userspace code (i.e.: your code),
system: the amount of time spent executing kernel code
user + system
real: the "real" amount of time it took to execute the code (i.e. system + user + time spent waiting for I/O, network, disk, user input, etc.). Also known as "wallclock time".
```

#### benchmark/ips

<https://github.com/evanphx/benchmark-ips>

* 会计算一个合适的运算次数, 用以提供合理的比较结果, 结果通过每秒多少次进行展示
* 输出标准方差 (各数据偏离平均数的距离（离均差）的平均数)
* 比较功能

#### benchmark/bigo

<https://github.com/davy/benchmark-bigo>

* 不同大小样本下, 进行比较
* 图表比较功能(前端图表库ChartKick) 非常直观
* 终端输出ASCII 图表(需要安装gnuplot) 将就可用

参考: <https://zhongfox.github.io/2015/11/30/ruby-benchmark/>

---

### Javascript

#### `process.hrtime([time])`

返回`current high-resolution real time`, 格式为[seconds, nanoseconds]

可选参数也是process.hrtime的上一个返回值, 如果传递将diff2个时间:

```javascript
var time = process.hrtime();
// [ 1800216, 25 ]

setTimeout(() => {
  var diff = process.hrtime(time);
  // [ 1, 552 ]

  console.log(`Benchmark took ${diff[0] * 1e9 + diff[1]} nanoseconds`);
  // benchmark took 1000000527 nanoseconds
}, 1000);
```

---

### Go

---

### Lua

---

### Java
