# APLanguage - Docs


### Literals

#### Integer

Each literal should be the type of what is expected, for example a function needs an u64, the literal will be inferred as one.
If it's ambiguous, a suffix must be specified: `u8`, `u16`, `u32`, `u64`, `i8`, `i16`, `i32`, `i64`.
Prefixes: `0x` for hexadecimal, `0b` for binary and `0o` for octal.

Higher bit count are to be discussed, as it poses problems with certain platforms as of now.

#### Floats

Each literal should be the type of what is expected, for example a function needs a f64, the literal will be inferred as one.
If it's ambiguous, a suffix must be specified: `f32`, `f64`.

Floats are as per IEEE 754 floating point standard.
Higher precision will be discussed in another entry, as implementing it would be difficult for some targets.

#### Strings

All strings will be utf-8 encoded.

There are different syntax of strings:
```kt
r"raw string, where \n and \t are taken literally, also no interpolation"

// all strings can be written as multiline, the newline is inserted and carriage return removed.
```
```rust
// to allow quotes inside strings, you can prefix and suffix with an equal amount of #
// note: the f and r prefix still applies, but they must be put before the #,
// and """multiline strings""" will also work
#"Hello, this is "a quoted string" "#
```

### Control flow

#### If

Simple conditional:
Can be used as a statement: `"if" "(" expression ")" statement ("else" statement)?` (could be overshadowed by "Everything as expression")\
Or as an expression: `"if" "(" expression ")" expression "else" expression`\
Ternary operator `cond ? expr : expr` is not planned, as it may give more confusion.

#### While

Looping until a condition is false.\
`"while" "(" expression:boolean ")" statement`\
The while loop cannot be made as an expression as of now, as there is no trait system nor good inference to allow resolving the type when breaking the loop.

### Structs

Classes are bundling data with behavior, an object-oriented view on things.\
This can cause some trouble when resolving and doesn't treat data like data.

How is data stored and defined?

APLang splits data and behavior. So the declaration of a type goes this way:
```rust
struct Test1 {
  a: u16,
  b: str
}
```
```rust
struct Test2(a: u16, b: str)
```

The difference between these two declarations is the ability to initialize structs like tuples.
The second snippet allows `Test2` to be used for calls like `method(::Test)`
where `method` accepts a Function with 2 arguments `(u16, str)` and returning a `Test2` instance.

Initialization goes like this:
```kotlin
Test1(a: 10u16, b: "str") // only named is allowed
Test1(b: "str", a: 10u16) // unordered is also allowed, as it needs to be named anyway

Test2(10u16, "str")
Test2(a: 10u16, "str")
Test2(a: 10u16, b: "str")
Test2(b: "str", a: 10u16)
```

### Arrays

An array is an immutable List of the same type of values.

The syntax of Arrays shall be `[Type]`, as generics are not there yet.\
How length should be handled by the typing system is TBD as "const generics" aren't thought about it.

### Functions

#### Function declaration

Syntax for functions:
```kt
fn staticMethod(
  var variableArgument: type,
  val cannotBeReassigned: type,
  defaultVal: type
) -> returntype {}
```

`-> returntype` can be omitted when no return value is being returned.

Parameters can be reassigned when defined with var. val is default and optional.\
Function overloading is not supported as of step 0.\
Return type overloading is discussed in another entry.

#### Calling functions

Calling functions
```kt
staticMethod(10u16, "str", true)


fn staticMethod2(var a: u16, val b: str) {}

staticMethod2(10u16, "str")
staticMethod2(a: 10u16, "str")
staticMethod2(a: 10u16, b: "str")
staticMethod2(b: "str", a: 10u16) // when fully named, it can be unordered

fn staticMethod2(var a: u16, val b: str, /* syntax here for functions */) {}

staticMethod2(a: 10u16, "str") { /* code, there is no implicit it */ }
staticMethod2(a: 10u16, "str") { |arg1| -> /* code, functions with only 1 arg */ }
```

#### Local Variables

Local variables can be declared with:
`val identifier: Type = expression`
`var identifier: Type = expression`
Where `val` prohibits setting of the variable after initialization.

Type inference and uninitialized variables are TBD and will be in another entry.
(I)Mutability will be discussed in another entry.

### Project Structure

```
single-module:

⨽ project-dir
    ⊢ aplang.toml
    ⊢ aplang.lock
    ⊢ aplang.libraries (optional)
    ⨽ src
    ∣    ⨽ main.aplang
    ⊢ test
    ∣    ⨽ tests.aplang
    ⨽ bench
        ⨽ mybench.aplang

multi-module:

⨽ project-dir
    ⊢ aplang.toml
    ⊢ aplang.lock
    ⊢ aplang.libraries (optional)
    ⊢ sub-project1
    ∣    ⊢ aplang.toml
    ∣    ⊢ src
    ∣    ∣    ⊢ main.aplang
    ∣    ⊢ migration-scripts
    ∣    ∣   ⨽ 001.sql 
    ∣    ⨽ test
    ∣        ⨽  tests.aplang
    ⨽ sub-project2
        ⊢ aplang.toml
        ⊢ src
        ∣    ⊢ main.aplang
        ∣    ⨽ cat.png
        ⨽ bench
            ⨽ mybench.aplang

```