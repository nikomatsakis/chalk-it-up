# Overview

Welcome to "chalk it up". This is a repository intended to serve as a shared design document for the Rust type teams and the "chalk effort", which is a plan to improve the compiler's trait implementation.

Chalk is a kind of shorthand for a style of solver that can be both efficient, correct, and shared across multiple codebases. This will likely build on some parts of today's chalk repo, but also integrate lessons from a-mir-formality and other work that has happened in the meantime.

This section aims to explain the proposed high-level architecture. It also makes connections to rustc's current trait solver (as of this writing, anyway, that's a moving target) as well as a-mir-formality, which aims to be a model and specification for the stuff in this document.

## The components in the system

The major components in the system are:

### The [database](./components/database.md)

The database represents Rust programs. It can tell you things like "what generics are declared on this trait" or "what associated types does this trait have".

In rustc, this is the HIR layer, and the various queries that let you query it.
    
There is no perfect analog to this in a-mir-formality. The closest is the grammar of the decl layer (which is effectively the HIR).

### [Rust-to-clause](./components/rust-to-clause.md)

The Rust-to-clause logic converts a piece of Rust into program clauses. Converts Rust source code (like `impl<T: Debug> Debug for Vec<T>`) into logical predicates like

```
∀T. is_implemented(T: Debug) => is_implemented(Vec<T>: Debug)
```

In rustc today, this layer doesn't exist in a clean way, and adding this layer is one of the major goals of this effort. The closest analog is the code "candidate assembly" code in trait selection, which has the job of finding the various candidates one could use to prove something like `Vec<u32>: Debug`.

In a-mir-formality, this logic is embodied in the [decl-to-clause module](https://github.com/nikomatsakis/a-mir-formality/blob/main/racket-src/decl/decl-to-clause.rkt) of the [decl](https://github.com/nikomatsakis/a-mir-formality/blob/main/racket-src/decl/) layer.

### Type relations

Implementation of Rust's subtyping and outlives rules.  Defines what it means to have `T1 <: T2`, and how to prove that is true.

In rustc, this is roughly the [subtyping code](https://github.com/rust-lang/rust/blob/75d3027fb5ce1af6712e4503c9574802212101bd/compiler/rustc_infer/src/infer/sub.rs) from the `rustc_infer` module.

In a-mir-formality, this is the `ty` layer.

* The "solver"
    * Given a predicate goal, manages the search process to find solutions
* The "chalk-ir" crate
    * This crate is a common intefcace that allows the above actors to represent types in a unified way across rustc, rust-analyzer, etc. Extracting this crate is its own effort and not discussed further in this particular section.
* The "region checker"
    * As today, the solver and type relation code ultimately produce outlives obligations that must be passed to a region solver. Unlike today, those never involve higher-ranked regions, and are limited to outlives obligations.

## 