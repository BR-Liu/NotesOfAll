## 从hello world入门Rust

---
&ensp;&ensp;最近对Rust这门语言产生了浓厚的兴趣，在一堆手忙脚乱搭了环境搞好了ide之后就开始从官方文档开始入手学习这门语言了。今天是开始学习Rust的第一天，所以我就准备从hello world这个经典的入门程序着手开始对Rust进行系统的学习，以下的代码和主石是我今天的学习所得，也包括了一些从我个人角度出发的一点理解，有错误和不严谨的地方希望能够得到Rust大佬的指摘。

---
1. 先上toml（个人理解可以把这个文件当做maven的pom文件）

```
[package]
name = "HelloWorld"
version = "0.1.0"
authors = ["BR-liu <liuxin999999@sohu.com>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
rand = "0.5.5"
```
2. 一个以官方文档为基础的猜数字小程序

```
use std::io;
use rand::Rng;
use std::cmp::Ordering;

///一个hello_world猜数字程序
fn main() {
    //方法名后面携带"!"代表这是一种被称为宏(macro)的特殊方法。
    //虽然还不了解宏方法的特点，但是结合以往学习其他语言的经验本人猜测这个方法可能类似于C和java中的内联方法
    println!("guess the number");
    //let为变量声明，所有的变量都可以使用let进行声明
    //rand::thread_rng() 代表引用rand下面的thread_rng()方法，你可能会好奇为什么有"::"和"."两种方式引用方法？
    //根据官方文档，通过"::"引用的方法属于类方法而不是实例方法，这很容易让人联想到java中的static关键字。是的，官方文档也用了"静态方法"一词来形容这种引用方式
    //那么使用"."来调用方法不言而喻就应该是正常实例方法的调用了
    let secret_number = rand::thread_rng().gen_range(1, 101);
    //loop关键字可以等同于java中的for，while关键字，用来进行循环，可以使用continue、break关键字进行中断
    loop {
        println!("please input your guess");
        //mut用来修饰可修改变量，在rust语言中，所有的变量默认的声明是不可修改的，一旦定义了初始值，就不允许进行修改。而使用mut修饰则可以打破这个规矩
        let mut guess = String::new();
        //可以看到read_line接收了一个参数，"&"代表变量内存的指针，这个指针指向前面定义的guess的内存空间，即将接收到的参数赋值给前面定义的可变变量guess
        //read_line方法会返回一个枚举类Result，except会对返回的Result进行判断，如果是枚举值Ok那么就无视，如果是Err那么就会输出方法体中的信息（个人觉得类似于java的异常机制）
        io::stdin().read_line(&mut guess)
            .expect("failed to read line");
        //rust的print方法允许使用"{}"当做占位符
        println!("your guess: {}", guess);
        //match匹配 模式（pattern）为guess.trim().parse()，分支（arms）则为parse方法可能返回的结果，当其值为Result的枚举值Ok时，则返回这个数字，Err时表示不是数字，则结束本次匹配，重新开始循环
        //parse方法会尝试将参数格式化为数字，但是需要告知parse方法格式化成哪种类型的数字，rust提供了u32(无符号32位数字),i32(带符号32位数字),u64,i64同理
        //这里使用重复的变量名进行定义会将之前定义过的同名变量值进行"隐藏"（官方说法），这样我们能够对这个变量名进行复用，而不是重新定义一个新的变量
        //parse方法同样返回一个Result，通过match可以看到下面的代码中列举除了Ok以及Err的两种枚举类型
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            //Err(_)中的"_"代表一个通配符，能够匹配err中的全部信息，这里表示不管Err包含了哪些信息，只要它是Err就代表类型不匹配，那么就结束当前循环重新进入循环
            Err(_) => continue
        };
        //匹配规则为guess.cmp(&secret_number)，cmp函数返回值为Ordering中定义的三种枚举值之一，匹配到一下成员变量时则根据不同的策略进行返回
        //值得注意的是，match不同于java中的switch语句，在match中如果匹配到符合预期的结果就不会继续向后匹配
        //match中出现的continue和break都是针对loop循环的关键字
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("too small"),
            Ordering::Greater => println!("too big"),
            Ordering::Equal => {
                println!("you win");
                break;
            }
        };
    }
}
```

代码中的注释是根据我的理解结合官方文档一个字一个字敲出来的，也代表了我个人的理解，希望能够帮助到那些同样初学Rust语言的人。当然如果有不对的地方也请大家不吝赐教，希望大家能够在探讨中不断进步。以后我也会抽时间将我学习Rust的历程笔记分享出来。