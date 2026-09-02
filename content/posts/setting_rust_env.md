---
title: "Setting Up a Rust Development Environment"
subtitle: "What you need to start writing Rust, and nothing more"
date: 2026-09-02
tags: ["rust", "cargo", "vscode", "beginners"]
featured: true
mood: "focused"
---

Here is the minimum setup that gives you a comfortable environment, plus the few conventions that keep a project readable once it grows past one file.

## 1. Install the toolchain

Use `rustup`, the official installer.

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

On Windows, download `rustup-init.exe` from [rustup.rs](https://rustup.rs).

You now have three commands:

- **`rustup`** manages your Rust installation. `rustup update` is the only one you will run regularly.
- **`rustc`** is the compiler. You will almost never call it directly.
- **`cargo`** is the build tool and package manager. This is the one you use all day.

Add the linter and the formatter:

```bash
rustup component add clippy rustfmt
```

## 2. Set up VS Code

Three extensions is all you need:

| Extension | ID | What it does |
|---|---|---|
| **rust-analyzer** | `rust-lang.rust-analyzer` | code-completion, go-to-definition, refactoring, errors as you type |
| **Even Better TOML** | `tamasfe.even-better-toml` | Makes editing `Cargo.toml` pleasant |
| **CodeLLDB** | `vadimcn.vscode-lldb` | Debugger |

## 3. The cargo commands you actually need

```bash
cargo new my-tool        # create a new project
cd my-tool
```

| Command | What it does |
|---|---|
| `cargo check` | Verifies your code compiles, without producing a binary — **fast** |
| `cargo run` | Compiles and runs your program |
| `cargo build --release` | Builds an optimized binary in `target/release/` |
| `cargo test` | Runs your tests |
| `cargo clippy` | Runs the linter |
| `cargo fmt` | Formats every file in the project |
| `cargo add serde` | Adds a dependency to `Cargo.toml` |

**`cargo check` is much faster than `cargo build`**, because it does all the type checking but skips generating the actual machine code. When you are writing code and just want to know whether the compiler will work, use `check`. Build only when you actually want to run something.

You can also use `cargo clean`. It deletes the whole `target/` folder, meaning your next build recompiles every dependency from scratch.

## 4. Creating a new project

A new project gives you a single `src/main.rs`.

``` bash
my-tool/
├── Cargo.toml
└── src/
    ├── main.rs
```

## 5. Handling errors properly

When you start coding in Rust you will use `.unwrap()`, and the first thing you should learn after that is to stop using it. `unwrap()` crashes your program on failure with an unhelpful message. It is fine while experimenting, not in something you intend to keep.

Instead, define your errors in one place and call it `error.rs`.

``` bash
src/
├── main.rs
└── error.rs
```

In Rust, you can handle application errors by using an enum to represent the different kinds of errors that can occur.

For example, we can define an AppError:

``` rust
use std::io;

#[derive(Debug)]
pub enum AppError {
    Io(io::Error),
    NotFound,
    InvalidInput(String),
}
```

Now the application can represent three types of errors:

- Io for input/output errors;
- NotFound when a resource cannot be found;
- InvalidInput when some provided data is invalid.

You can implement Display, to make the errors readable:

``` rust
impl std::fmt::Display for AppError {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        match self {
            AppError::Io(err) => write!(f, "IO error: {err}"),
            AppError::NotFound => write!(f, "Resource not found"),
            AppError::InvalidInput(msg) => write!(f, "Invalid input: {msg}"),
        }
    }
}
```
Then return these errors from your functions using Result<T, AppError> and handle them. You can do it with a simple println! for example:

``` rust
println!("Error: {error}");
```
