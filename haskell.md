# Haskell

## Lect 1 & 2 Functions, Binders and Layout Rule
### Types
###### Bounded Type
A bounded type has a max and min value whereas unbounded type has infinite precision. E.g Int is a bounded type and Integer is unbounded type.


### Type Conversion
```haskell
fact3 :: Int -> Integer
fact3 0 = 1
fact3 n = n * fact3(n-1)
```
This code snippet is mixing `Int` * `Integer`, there will be an error.

`Int` and `Integer` are types of class `Integral`.

`fromIntegral` convert any `Integral` type into `Num` type which contains `Int, Integer, Float, Double`

- `fromInteger` -> `Num a => Integer -> a` convert `Integer` to `Num`
- `toInteger` -> `Integral a => a -> Integer` convert `Integral` to `Integer`

### Infinite Lists
`data List a = Nil | Cons a (List a)` where a is a type variable, e.g `Int, Float, or Double`
```haskell
infinite :: Int -> List Int
infinite n = Cons n (infinite (n+1))
```

### Local Binding
- Binders are a special class of constructs which declare new identifiers and their possible values.
- Each binder has some lexical scope where the identifier is visible.

Examples of name binders (Declaration)
- Local declarations
- Method declaration (parameters)
- Pattern-Matching

###### Local Declaration
```haskell
-- e1 and e2 are two expressions
let v = e1 in e2
```
Scope of `v` is in both `e1` and `e2`.

###### Method Declaration
```haskell
let quad x =
    let add x y = x + y
        double x = add x x
    in (double x)+(double x)
```
Here, `add` and `double` are local functions, while `quad` is a global declaration.

Rule : Next character after keywords where/let/of/do determines the starting columns for declarations. Starting after this point continues a declaration, while starting before this point terminates a declaration.

## Lect 3 Data Types and Control Constructs
### Data Types
#### Primitive Types
- Built-In Types of Languages
- Directly implemented in hardware (usually).
- Simpler types e.g. boolean, integer, float, char, string, arrays
- Important for performance
- May include Standard Libraries (extensible)
- Language without primitive type
    - Lambda Calculus
    - Use just functions!
###### Unboxed Types
An unboxed type is represented by the value itself, no pointers or heap allocation are involved.

###### Boxed Types
Values of boxed type are represented by a pointer to a heap object

