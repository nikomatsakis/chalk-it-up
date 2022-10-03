# Rust-to-clause


The Rust-to-clause logic converts a piece of Rust into program clauses. Converts Rust source code (like `impl<T: Debug> Debug for Vec<T>`) into logical predicates like

```
∀T. is_implemented(T: Debug) => is_implemented(Vec<T>: Debug)
```

In rustc today, this layer doesn't exist in a clean way, and adding this layer is one of the major goals of this effort. The closest analog is the code "candidate assembly" code in trait selection, which has the job of finding the various candidates one could use to prove something like `Vec<u32>: Debug`.

In a-mir-formality, this logic is embodied in the [decl-to-clause module](https://github.com/nikomatsakis/a-mir-formality/blob/main/racket-src/decl/decl-to-clause.rkt) of the [decl](https://github.com/nikomatsakis/a-mir-formality/blob/main/racket-src/decl/) layer.