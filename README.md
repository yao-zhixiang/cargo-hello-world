# rust-playground

使用 Rust cargo 创建

## 编译且运行

```bash
npm run run
```

## 编译

```bash
npm run build
```

## 检查错误

```bash
npm run clippy
```

## 格式化

```bash
npm run fmt
```

## [多文件模块的层级关系](https://wiki.jikexueyuan.com/project/rust-primer/module/module.html)

Rust 的模块支持层级结构, 但这种层级结构本身与文件系统目录的层级结构是解耦的.

`mod xxx;` 这个 `xxx` 不能包含 `::` 号. 也即在这个表达形式中, 是没法引用多层结构下的模块的. 也即, 你不可能直接使用 `mod a::b::c::d;` 的形式来引用 `a/b/c/d.rs` 这个模块.

那么, Rust 的多层模块遵循如下两条规则:

- 优先查找 `xxx.rs` 文件
  - `main.rs`、`lib.rs`、`mod.rs` 中的 `mod xxx;` 默认优先查找同级目录下的 `xxx.rs` 文件;
  - 其他文件 `yyy.rs` 中的 `mod xxx;` 默认优先查找同级目录的 yyy 目录下的 `xxx.rs` 文件;
- 如果 `xxx.rs` 不存在, 则查找 `xxx/mod.rs` 文件, 即 `xxx` 目录下的 `mod.rs` 文件.

上述两种情况, 加载成模块后, 效果是相同的. Rust 就凭这两条规则, 通过迭代使用, 结合 pub 关键字, 实现了对深层目录下模块的加载;

下面举个例子, 现在我们建了一个测试工程, 目录结构如下:

```
src
├── a
│   ├── b
│   │   ├── c
│   │   │   ├── d
│   │   │   │   ├── test.rs
│   │   │   │   └── mod.rs
│   │   │   └── mod.rs
│   │   └── mod.rs
│   └── mod.rs
└── main.rs
```

`a/b/c/d/test.rs` 文件内容:

```rust
pub fn print_ddd() {
    println!("i am ddd.");
}
```

`a/b/c/d/mod.rs` 文件内容:

```rust
pub mod test;
```

`a/b/c/mod.rs` 文件内容:

```rust
pub mod d;
```

`a/b/mod.rs` 文件内容:

```rust
pub mod c;
```

`a/mod.rs` 文件内容:

```rust
pub mod b;
```

`main.rs` 文件内容:

```rust
mod a;

use a::b::c::d::test::print_ddd;

fn main() {
  print_ddd()
}
```

输出结果为: `i am ddd.`
