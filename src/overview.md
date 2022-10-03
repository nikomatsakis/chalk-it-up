# Overview

Welcome to "chalk it up". This is a repository intended to serve as a shared design document for the Rust type teams and the "chalk effort", which is a plan to improve the compiler's trait implementation.

Chalk is a kind of shorthand for a style of solver that can be both efficient, correct, and shared across multiple codebases. This will likely build on some parts of today's chalk repo, but also integrate lessons from a-mir-formality and other work that has happened in the meantime.

This section aims to explain the proposed high-level architecture. It also makes connections to rustc's current trait solver (as of this writing, anyway, that's a moving target) as well as a-mir-formality, which aims to be a model and specification for the stuff in this document.

## The components in the system

The major components of the system are:

* [The database](./components/database.md), which understands the Rust program being compiled and can answer questions like "what associated types are declared in the trait `Iterator`?" (answer: `Item`)
* [Rust-to-clause](./components/rust-to-clause.md), which converts from Rust syntax into the logical formulae the solver can use. For example, given the impl `impl<T: Debug> Debug for Vec<T>`, the rust-to-clause code would produce the rule `∀T. is_implemented(T: Debug) => is_implemented(Vec<T>: Debug)`.
* [The logical solver](./components/solver.md), which takes a goal like `is_implemented(T: Debug)` and figures out if it is provable (i.e., should the compiler consider it true?).
* [Type relations](./components/type_relation.md), which is custom code to handle subtyping and outlives goals. It is invoked by the logical solver when such goals are encountered.
* The [borrow checker](./components/borrow_checker.md)

### [Type relations](./components/type_relation.md)


### [Logic solver](./components/solver.md)

Given a predicate goal, manages the search process to find solutions.

* The "chalk-ir" crate
    * This crate is a common intefcace that allows the above actors to represent types in a unified way across rustc, rust-analyzer, etc. Extracting this crate is its own effort and not discussed further in this particular section.
* The "region checker"
    * As today, the solver and type relation code ultimately produce outlives obligations that must be passed to a region solver. Unlike today, those never involve higher-ranked regions, and are limited to outlives obligations.

## 