# Coherence

## Goal

* Port coherence code to use the new trait solver

## How to do it

* Coherence will issue a canonical query created by:
    * Open Impl 1 existentially
    * Open Impl 2 existentially
    * Create goal `(P1 == P2 && coherence-mode { where-clauses-from-impl-1 where-clauses-from-impl-2 }`

## What coherence needs

* Recursive solver structure 
* On roughly the predicates we have today?
    * need the "coherence mode" concept
* Using the existing type system code 
* Callback interjects "ambiguous" as we talked about

## Error reporting

* If we can get back the tree, we can identify the points where we introduced ambiguity and issue diagnostics around those

