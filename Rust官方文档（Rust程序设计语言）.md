## Rust官方文档（Rust程序设计语言）

### 读书时间20220318-20220318

### 目录

1、入门指南

2、猜数字游戏

3、通用编程概念

​	3.1 变量和可变性

​	3.2 数据类型

​	3.3 函数

​	3.4 注释

​	3.5控制流

4、认识所有权

5、适用结构体关联数据

6、枚举和模式匹配

7、使用包、Crate和模块管理不断增长的项目

8、常见集合

9、错误处理

10、泛型、trait和生命周期

11、编写自动化测试

12、I/O项目：构建命令行程序

13、Rust中的函数式功能语言：闭包和迭代器

14、更多关于Cargo和Crates.io的内容

15、智能指针

16、无畏并发

17、Rust的面向对象编程特点

18、模式和匹配

19、高级特性

20、最后的项目：构建多线程Web服务器

### 涉及书目

### 涉及专业名词

**Crate**，代码包，原意木箱，分割箱，音似create

**Prelude**，指引入作用域当中标准库中定义的程序项，原意开端、序幕，音/'preliju:d/

**Trait**，愿意特征特质特点，音[treɪt]

> 一个Trait描述了一个抽象的接口，这个抽象接口可以被类型继承，Trait由三部分组成：
>
> Functions，Types，Constants，并包含一个隐含类型指向Trait的self类型

**Shadow(遮蔽)**，Rust允许重新声明新值来覆盖之前的值，这个功能常用来类型转换

**Mut**，可变的，为mutable的缩写

### 零散笔记

#### 1、简介

Rust拒绝编译存在难以捕获bug的代码，其中包括并发bug，与编译器合作，团队可以花费更少时间追踪bug。

Rust的特性：重视速度和稳定性，速度指程序**运行的速度**，消除安全和生产力之间的矛盾，提升速度和人机交互之间的顺畅度

#### 2、入门指南

具体内容：安装、cargo（包管理器和构建系统）、HelloWorld

##### 2.1安装

> rustup：管理Rust版本和相管工具的命令行工具

rust版本控制，现在版本1.59.0

```shell
$ rustc --version
```

##### 2.2Hello,World

> 文件存放目录C:\Users\Some die young\projects

编译语句Rustc将rs文件编译为可执行的exe文件

```shell
$ rustc xxx.rs
```

##### 2.3Hello,Cargo

Cargo：Rust构建系统和包管理器，完成功能：构建代码、下载依赖库、编译这些库（类似于npm？）

Cargo新建项目，类似于npm init

```shell
$ cargo new hello_cargo
```

cargo.toml文件用于管理Rust项目依赖，类似于package.json

###### 项目结构

<img src="C:\Users\Some die young\AppData\Roaming\Typora\typora-user-images\image-20220318220217652.png" alt="image-20220318220217652"  />

[package]标题表明下面的文件将用于配置一个包

> name（package包名）
>
> version（版本）
>
> edition（用的rust大版本号）

最后一行[dependencies]罗列项目以来的表块，Rust中代码包被称为Crate(木箱、分割箱 n)

###### Cargo的编译

```shell
$ cargo build
# 编译完成的文件在./target/debug下
$ cargo run
# 若文件尚未编译则这一步会完成编译
$ cargo check
# 构建项目而无需生成二进制文件来检查错误
```

###### 发布构建

```shell
$ cargo --release
# 用于优化编译项目，会在targe/release下生成可执行文件，通常用于上线前优化
```

#### 3、猜数字游戏

###### 程序目的

程序随机输入一个数字，1到100之间，玩家猜一个数字并输入并提示猜测大了还是小了

###### 处理一次猜测

```rust
use std::io;

fn main() {
    println!("Guess the number begin!");
    println!("Please input your guess num");

    let mut guess = String::new();// 新建变量过程，guess应该是个字符串

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");
    
    println!("You guessed: {}", guess);
}
```

其中包括了

1、引入标准库的程序项

```rust
use std::io
// 引入标准库项，use代表将标准库中定义的item引入作用域，这些项叫做prelude，指标准库中定义的程序项
```

2、处理打印字符串的宏

```rust
println!("Guess the number!");
```

3、使用变量储存值

**Rust中变量是默认不可变的**，为Const格式，需要加mut使变量可变

```rust
let app = 5;// app不可变
let mut bananas = 5;// bananas可变
```

4、变量声明

```rust
let mut guess = String::new();
```

String是标准库提供的字符串类型，是UTF-8的可增长文本块，而“::new”是String类型的关联函数。

**关联函数**(Associated function)是实现一种特定类型的函数，如题所示new创建了一个新的空字符串，将他绑定到了String空实例上。

5、变量读取和赋值

```rust
io::stdin()
	.read_line(&mut guess)
	.expect("Failed to Read")
```

标准输入传入guess变量，并且&表示这个参数是一个引用，允许多处代码访问同一个数据，而无需在内存中进行多次拷贝，引用也是默认不可变的，所以要用&mut guess

io::stdin().read_line(&mut guess)反悔了一个Result类型，这个例子中是io::Result，数据类型是***枚举enumerations***，枚举类型持有固定集合的值，枚举通常跟match一起用，可以方便根据枚举值是哪个来执行不同的代码。（有点类似于字典和发布订阅模式？）

Result的成员目前是{Ok和Err}，这些Result类型的作用是编码错误处理信息，io::Result的实例拥有expect()方法，如果实例值为Err，会导致程序底层崩溃，并显示当做参数传递给expect的信息，如果read_line方法返回Err，则可能是底层操作系统的错误，如果io::Result的实例值为Ok，expect会获取Ok中的值并原样返回，本例中这个值是用户输入的字节数。

###### 添加外部源

在Dependencies里加入“rand”源

> 踩坑点：在.cargo/config.rs里换源，github的crate源开了vpn都下载不下来，记得一定换成国内源，尤其是crate那一项

**Cargo.lock**文件来确保构建是可以重现的，项目会持续使用已构建成功版本并直到升级

###### 更新Crate到新版本

```shell
$ cargo update
```

cargo更新crate

###### 生成随机数

```rust
use rand::Rng

let secret_number = rand::thread_rng().gen_range(1..101);
```

第一行use rand::Rng。Rng是一个trait，定义了随机数生成器应用的方法。

> ***Trait***
>
> 一个Trait描述了一个抽象的接口，这个抽象接口可以被类型继承，Trait由三部分组成：
>
> Functions，Types，Constants，并包含一个隐含类型指向Trait的self类型

调用rand::thread_rng函数来为我们提供将要使用的特定随机数生成器，位于当前执行线程的本地环境当中

gen_range()方法由刚才使用use rand::Rng语句引用的Rng Trait所定义

> cargo doc --open可以对打开构建本地依赖所提供的文档

###### 分支结构语句

```rust
let guess: u32 = guess.trim().parse().expect("Plz type a number");

match guess.cmp(&secret_number){
	Ordering::Less=>println!("smaller"),
	Ordering::Equal=>println!("congradualation"),
	Ordering::Greater=>println!("bigger")
}
```

**Shadow(遮蔽)**:Rust允许重新声明新值来覆盖之前的值，这个功能常用来类型转换

u32是一个无符号的32位整型数据，parse方法和javascript一样只能转换含有数字的字符串，不同之处在于字符串只能包含字符而不能包含其他变量，如果parse不能从字符串生成一个数字则会返回一个Result的Err成员。

###### 错误处理

```rust
let guess: u32 = match guess.trim().parse() {
	Ok(num)=> num,
	Err(_)=> continue
};
```

如果可以就返回num，如果不可以转换为u32类型则会进入Err，“_”为通配符，程序可以有效忽略所有由parse产生的错误

#### 4、通用编程概念

##### 4.1变量和可变性

变量和可变性：默认情况下变量是不可变的，也可以选择让变量是可变的（mutable）

###### 常量Constant：

绑定到变量名且不允许修改的值，不允许加mut，且**必须注明值的类型**

```rust
const THREE_HOURS : u32 = 60*60*3
```

**const常量声明一般全部大写**

###### 遮蔽Shadow：

后续的赋值方式会改变前有的赋值，这在我们需要改变变量类型时很有用

```rust
let space = "    ";
let space = space.len();
// 遮蔽改变变量类型
let mut space = "   ";
space = spaces.len();
// 不可以改变数据类型的赋值
```

##### 4.2数据类型

Rust的每个值都有确切的数据类型，分为两种：标量数据和复合类型；

Rust是静态类型语言，必须在编译期间知道所有变量的类型，编译器通常可以通过值来才出我们想要的类型，但是有多种情况时必须加一个类型标注

```rust
let guess: u32 = "42".parse().expect("Not a num")
```

###### 标量类型

表示单个值，四个基本的标量类型，整型，浮点型，布尔型，字符型

**整数类型**

<img src="C:\Users\Some die young\AppData\Roaming\Typora\typora-user-images\image-20220319211052709.png" alt="image-20220319211052709"  />

如果不确定使用哪种类型的话整型选择i32是个不错的选择

**浮点类型**











