# Database

The database represents Rust programs. It can tell you things like "what generics are declared on this trait" or "what associated types does this trait have".

In rustc, this is the HIR layer, and the various queries that let you query it.
    
There is no perfect analog to this in a-mir-formality. The closest is the grammar of the decl layer (which is effectively the HIR).