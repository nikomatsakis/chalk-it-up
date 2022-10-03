# Type relation

Implementation of Rust's subtyping and outlives rules.  Defines what it means to have `T1 <: T2`, and how to prove that is true.

In rustc, this is roughly the [subtyping code](https://github.com/rust-lang/rust/blob/75d3027fb5ce1af6712e4503c9574802212101bd/compiler/rustc_infer/src/infer/sub.rs) from the `rustc_infer` module.

In a-mir-formality, this is the `ty` layer.