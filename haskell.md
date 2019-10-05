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

This code snippet is mixing `Int` \* `Integer`, there will be an error.

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
> An algebraic data type is a data type defined out of a combination of two constructions: products and sums. A product is a way to combine multiple values of different types into one. They're present in pretty much all languages, often called “structs”, “records” or “tuples”. --- From Quora


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

Values of boxed type are represented by a pointer to a heap object, those types can not be defined by users

#### User-Defined Types

Examples:

- Ordinal type
- Record type
- Pointer type
- Tuple type
- Union types
- Algebraic Data type
- Polymorphic/Generic type
- Sum vs Product types

###### Ordinal Types in Haskell

An ordinal type is a finite enumeration of distinct values that can be associated with positive integers.

```haskell
data DaysObj = Mon | Tue | Wed | Thu | Fri | Sat | Sun
deriving (Show, Enum, Eq)

-- Enum class supports a range of useful methods:
class Enum a where
succ :: a -> a
pred :: a -> a
toEnum :: Int -> a
fromEnum :: a -> Int
```

Non-exhaustive pattern vs Exhaustive Pattern

```haskell
let weekend x = case x of {
    Mon -> False;
    Sat -> True;
    Sun -> True;
}
-- Will lead to exception non exhaustive

let weekend x = case x of {
    Sat -> True;
    Sun -> True;
    _ -> False;
}

```

###### Algebraic Data Type (in Haskell)

```haskell
data Data = I Int | F Float
| S String deriving Show

let v1 = I 3;;
let v2 = F 4.5;;
let v3 = S “CS2104”;;
```

A more systematic way to capture union types safely is to use algebraic data types which uses data constructor tags.

###### Tuple Types

A special case of algebraic data type is the tuple type. This is polymorphic and has only a single data constructor. An example is the triple:

```
data (,,) a b c = (,,) a b c
let stud1 = (“John”,”A1234567J”,2013);;
```

A special case of algebraic data type is the tuple type. This is polymorphic and has only a single data constructor. An example is the triple:

```haskell
data (,,) a b c = (,,) a b c
let stud1 = (“John”,”A1234567J”,2013);;
-- •We can write type synonym, as follow:
type Student = (String, String, Int)
type Pair a b = (a, b)
type String = [Char]

type Node = (Int, Node);;
-- Error: Cycle in type synonym declarations

-- pattern matching the component of a Tuple
let (name,_,yr) = stud1;;
case stud1 of
(name,_,yr) -> … ;;
```

NOTE: Type synonym can be recursive, it will lead to infinite expansions.`type Node = (Int, Node);; Error: Cycle in type synonym declarations`

###### Record Types

```haskell
data Student = Student {
    name :: String;
    matrix :: String;
    year :: Int
}

name :: Student -> String
matrix :: Student -> String
year :: Student -> Int

-- pattern matching with record type
data X = A | B {name :: String}
| C {x::Int, y::Int, name::String}

myfn :: X -> Int
myfn A = 50
myfn B{} = 200
myfn C{x=v} = v
```

###### Pointer Types (in Haskell?)

- By default, each algebraic data type is already implemented as a pointer to a boxed value.
- Thus, recursive type is possible but can be infinite.

We may use a general Maybe type `data Maybe a = Nothing | Just a` Possible finite data structure `data RNode2 = RNode2 Int (Maybe RNode2) deriving Show`
e.g`let list2 = RNode2 3 (Just (RNode2 4 Nothing))`

###### Product vs Sum Types
Fundamentally, there are two kinds of types.
- Product types that include tuples and records.
- Sum types that include ordinals and general algebraic data types.
>  - Product type is similar to conjunction `a and b`
    - `type Pair a b = (a, b)`
>  - Sum type is similar to disjunction `a or b`.
    - `type Either a b = Left a | Right b`


### Type Classes

### algebraic data types
> An algebraic data type is a data type defined out of a combination of two constructions: products and sums. A product is a way to combine multiple values of different types into one. They're present in pretty much all languages, often called “structs”, “records” or “tuples”.May 6, 2018
> From Quora


### Class Type
- Variables and data constructors are exist at the term level (values live and is the code that executes)
- Type variables and type constructors, typeclasses are exist at the type-level (used by program to verify and analysis types)
- Modules


Overloading (or ad-hoc polymorphism). Which allow types to be overloaded to let the compiler know what types to use when type name is the same. 
E.g `+` can be used for both integer and float and `algorithm W` could infer the type.

###### Data declaration
```haskell
data Bool = True | False
data Maybe a = Nothing | Just a
```
To define new data type, keyword `data` is used which create a new algebraic data type.
`Bool` and `Maybe` are type constructors/ type name is the name of the defined datatype which is capitalized. It is used in type signatures.

`True` and `Nothing` are data constructors which are functions create data or values(Term level)

Type class contains different types, e.g Num contains Integer, Float which contains more type. 


###### Constants and constructors
- Type and data constructors that take no arguments are constants, which only store a fixed type and amount of data
- `Arity` refer to the number of argument a function or a constructor takes.
    - Function that takes no argument is called `nullary`
    - Constructors that take one argument are called `unary`
    - Data constructors that take more than one argument are called `products`

- `Tuple` is called `anonymous product` 

###### Type alias/ synonym
A type synonym is a new name for an existing type. Values of different synonyms of the same type are entirely compatible. In Haskell you can define a type synonym using `type`.
`type MyChar = Char`

###### Data declaration, `newtype`
- `newtype` permits only one constructor and only one field.
`newtype Radius = Radius Double`
- 
