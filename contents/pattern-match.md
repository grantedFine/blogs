# 模式匹配

对于某些复杂的条件判断逻辑，如果使用 if...else swich...case, :? 来实现，需要的代码量会很多，而如果可以使用 “模式匹配” 特性，代码将会非常整洁明晰。

以 rust 为例：

```rust
fn print_integer(x: i32) {
    let value = match x {
        1 => 1,
        2 => 20,
        3 | 4 => 3,
        5...7 => 5,
        v @ 8...10 => v * 2,
        v @ 11...20 if v % 2 == 0 => v * 3,
        v @ 11...20 if v % 2 == 1 => v * 4,
        _ => 0,
    };
    println!("{}", value);
}
```

这个例子用 if...else 的话，就像：

```rust
fn print_integer(x: i32) {
    let value: i32;
    if x == 1 {
        value = 1;
    } else if x == 2 {
        value = 20;
    } else if x == 3 || x == 4 {
        value = 3;
    } else if x >= 5 && x <= 7 {
        value = 5;
    } else if x >= 8 && x <= 10 {
        value = x * 2;
    } else if x >= 11 && x <= 20 {
        if x % 2 == 0 {
            value = x * 3;
        } else {
            value = x * 4;
        }
    } else {
        value = 0;
    }
    println!("{}", value);
}
```

除了匹配值之外，还可以匹配类型，这时，被匹配的值的类型是类似于 A or B or C 的情景。

以 C# 7 里的语法为例：

```c#
var result = x match(
    case Func<int> f: f(),
    case int i: i,
    case *: default(int)
);
```

在 rust 中，`or` 的关系更倾向于用 enum 来实现，例如 `Result` 的定义，`Result::Ok(T)` or `Result::Err(E)`：

```rust
pub enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

匹配类型的语法例如：

```rust
let a: Result<i32, String> = Result::Ok(1);
match a {
    Result::Ok(value) => println!("got a value: {}", value),
    Result::Err(err) => println!("an error occurred: {}", err),
}
```

rust 中的模式匹配还负责 destructuring 复杂数据结构，例如 struct：

```rust
struct Point {
    x: i32,
    y: i32,
}
let origin = Point { x: 0, y: 0 };
match origin {
    Point { x: x1, y: y1 } => println!("({},{})", x1, y1),
}
```

tuple 虽然也可以用 match 来 destructuring，但是可以用更简单的语法：

```rust
let (x, y, z) = (1, 2, 3);
println!("x is {}", x);
```

对比着看，js 中对 array, object 和 string 的 destructuring，就很简单了：

```js
const [a, b] = [1, 2];
const {c, c} = { a: 1, b: 2 };
const [e, f] = "12";
```
