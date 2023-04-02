# Rust

## 入门

main.rs (hello_world.rs)

```rs
fn main() {
    // println! 调用的是宏，如果是函数，则没有感叹号
    println!("Hello, world!");
}
```

```sh
rustc main.rs
./main
# Hello, world!
```

Rust 是一种 预编译静态类型（ahead-of-time compiled）语言，这意味着你可以编译程序，并将可执行文件送给其他人，他们甚至不需要安装 Rust 就可以运行。

Cargo 是 Rust 的构建系统和包管理器。

```sh
# 检查cargo是否安装
cargo --version

# 使用 Cargo 创建项目
cargo new hello_cargo
cd hello_cargo
# Cargo 默认初始化了git仓库，如果在已有git仓库的目录下运行，则不会生成；可以用 cargo new --vcs=git 覆盖
# 构建
cargo build
# 编译并运行
cargo run
# 快速检查代码确保其可以编译
cargo check
# 优化编译项目
cargo build --release
```

文件名: Cargo.toml

```
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

这个文件使用 TOML (Tom's Obvious, Minimal Language) 格式，这是 Cargo 配置文件的格式。

第一行，[package]，是一个片段（section）标题，表明下面的语句用来配置一个包。随着我们在这个文件增加更多的信息，还将增加其他片段（section）。

示例程序：随机生成1到100的数字，玩家输入数字，看能否匹配上

```rust
// 将 io输入/输出库引入当前作用域. Rust有很多预先导入的标准库内容
use std::io;
use rand::Rng;
use std::cmp::Ordering;

// 函数
fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);
    println!("The secret number is: {secret_number}");

    loop {
        println!("Please input your guess.");
        // let 来创建变量
        // 变量默认不可变，增加 mut 修饰，可以创建可变变量
        // :: 语法表明 new 是 String 类型的一个 关联函数（associated function）。一些语言中把它称为 静态方法（static method）。
        let mut guess = String::new();
        // 调用 io 库中的函数 stdin，它返回了一个标准输入句柄
        // read_line 将用户如输入追加到内容里
        // & 表示参数是一个引用，它也是不可变的，所以加上 mut 使其可变
        // read_line 返回 Result 类型的值。它是一个枚举类型 enum。成员有 Ok 和 Err。
        // Ok.expect 会获取Ok中的值并返回；Err.expect 会导致程序崩溃并显示expect的参数。 
        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        // Rust 有一个静态强类型系统，同时也有类型推断。
        // 新建一个guess变量来隐藏(shadow)之前的guess的值
        // trim() 去掉字符串开头和结尾的空白字符，如 \n \r\n
        // _是一个通配符值
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        // {} 是占位符，也可以这样使用 println!("x = {} and y = {}", x, y);
        println!("You guessed: {guess}");

        // 一个 match 表达式由分支(arms)构成。一个分支包含一个模式(pattern)和表达式开头的值与分支模式相匹配时应该执行的代码。
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

文件名: Cargo.toml

```
.......

[dependencies]
rand = "0.8.3"
```

`rand = "0.8.3"` 是一种定义版本号的标准。0.8.3 事实上是 ^0.8.3 的简写，它表示任何至少是 0.8.3 但小于 0.9.0 的版本。

Cargo.lock 文件确保构建是可重现的。在第一次构建后，使用的依赖包版本会记录在这里。可以使用 `cargo update` 来忽略 Cargo.lock 并使用符合 Cargo.toml 声明的最新版本。

`cargo doc --open` 在浏览器打开本地依赖的帮助文档

## 常见编程概念

关键字(keywords): 只能由语言本身使用。不能使用这些关键字作为变量或函数的名称。

### 变量和可变性

变量默认是不可改变的（immutable）

```rust
// 不可变变量
let x = 5;
// 可变变量。变量的类型不能改变
let mut y = 6;
y = 7;
// 常量。在声明它的作用域之中，常量在整个程序生命周期中都有效
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
// 隐藏
fn main() {
    let x = 5;
    let x = x + 1;
    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}"); // 12
    }
    println!("The value of x is: {x}"); // 6
}
```

### 数据类型

Rust 是 静态类型（statically typed）语言，也就是说在编译时就必须知道所有变量的类型。根据值及其使用方式，编译器通常可以推断出我们想要用的类型。当多种类型均有可能时，如使用 parse 将 String 转换为数字时，必须增加类型注解。

#### 标量类型

标量（scalar）类型代表一个单独的值。Rust 有四种基本的标量类型：整型、浮点型、布尔类型和字符类型。

整型

| 长度 | 有符号 | 无符号 |
| --- | --- | --- |
| 8-bit | i8 | u8 |
| 16-bit | i16 | u16 |
| 32-bit | i32 | u32 |
| 64-bit | i64 | u64 |
| 128-bit | i128 | u128 |
| arch | isize | usize |

有符号数以补码形式（two’s complement representation） 存储。数字类型默认是 i32

isize 和 usize 类型依赖运行程序的计算机架构：64 位架构上它们是 64 位的， 32 位架构上它们是 32 位的。

```rust
// 57 是一个8位无符号整数
let x = 57u8
// _是分隔符，方便读数，不影响变量的值
let x = 1_000
// 十六进制数
let x = 0xff
// 二进制数
let x = 0b1111_0000
// 八进制数
let x = 0o77
// Byte 单字节字符
let x = b'A'
```

整型溢出

在 release 构建中，Rust 不检测溢出，相反会进行一种被称为二进制补码回绕（two’s complement wrapping）的操作。

为了显式地处理溢出的可能性，你可以使用标准库 Wrapping 在原生数值类型上提供的以下方法:

-     所有模式下都可以使用 wrapping_* 方法进行回绕，如 wrapping_add
-     如果 checked_* 方法出现溢出，则返回 None值
-     用 overflowing_* 方法返回值和一个布尔值，表示是否出现溢出
-     用 saturating_* 方法在值的最小值或最大值处进行饱和处理

浮点型

Rust 的浮点数类型是 f32 和 f64，分别占 32 位和 64 位。默认类型是 f64。所有的浮点型都是有符号的。

浮点数采用 IEEE-754 标准表示。f32 是单精度浮点数，f64 是双精度浮点数。

```rust
let x = 2.0; // f64
let y: f32 = 3.0; // f32
```

数值运算

```rust
// addition
let sum = 5 + 10;

// subtraction
let difference = 95.5 - 4.3;

// multiplication
let product = 4 * 30;

// division 整数除法会向下舍入到最接近的整数
let quotient = 56.7 / 32.2;
let floored = 2 / 3; // Results in 0

// remainder
let remainder = 43 % 5;
```

布尔型

```rust
let t = true;
let f: bool = false; // with explicit type annotation
```

字符类型

Rust 的 char 类型的大小为四个字节(four bytes)，并代表了一个 Unicode 标量值（Unicode Scalar Value）

```rust
// 用单引号声明 char 字面量
let c = 'z';
let z: char = 'ℤ'; // with explicit type annotation
let heart_eyed_cat = '😻';
```

#### 复合类型

复合类型（Compound types）可以将多个值组合成一个类型。

元组（tuple）

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);

let tup = (500, 6.4, 1);
// 解构（destructure），将tup分成了3个不同的变量
let (x, y, z) = tup;
println!("The value of y is: {y}");

let x: (i32, f64, u8) = (500, 6.4, 1);
let five_hundred = x.0;
let six_point_four = x.1;
let one = x.2;
```

不带任何值的元组有个特殊的名称，叫做 单元（unit） 元组。这种值以及对应的类型都写作 ()。

数组（array）

## 参考资料

Rust官网，Rust程序设计语言：https://kaisery.github.io/trpl-zh-cn/ch00-00-introduction.html