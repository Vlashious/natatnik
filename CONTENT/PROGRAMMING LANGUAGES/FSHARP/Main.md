# F#
[Go back](../../../README.md)

## Functions and modules
The most fundamental pieces of any F# program are functions organized into modules. Functions perform work on inputs to produce outputs, and they are organized under Modules, which are the primary way you group things in F#. They are defined using the let binding, which give the function a name and define its arguments.\
`let a = 3` declares a function `a` returning `int 3`.
```f#
module Functions = 
    let printInt (a:int)=
        printfn "Prints int %d" a

Functions.printInt 5 // Outputs "Prints int 5"
```