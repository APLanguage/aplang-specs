# APLang(uage), a programming language by me and for me.

## Introduction

Hello stranger! I'm Amejonah, also known in some communities as AP. For some time, a thought came into my mind: to create my own language. "Why?" do you ask? Simple, because I can, I thought so. But after my time spending with the parser in [Rust](https://github.com/APLanguage/aplang-rs), I realized that maybe I should start lower, with less features and with a programming language which is easier to write for me: I started in [Kotlin](https://github.com/APLanguage/aplang-lite-kt), a light version of the language, with "light"/"lite" I mean, really simple. It has classes, methods, fields, inheritance and... nothing more, well, and static strong typing, I hate dynamic typing and weak typing.

> What abut this version? I saw that it's resembles Kotlin and, Rust?

Yes, that's right. APLang is in the Idea a mixture of them both, but I would like to add some more features to it.

> So, why do I need to use APLang if I can take Kotlin right away?

You don't need it, as the title says, it's a language for me, so I can add features on the fly without taking notes if they are good or not, because **I** want them. I miss some features in Kotlin or Rust, so I can just add them in APLang when I want.

> If you want to describe APLang with one sentence, what would it be?

"You can do whatever you want, but don't blame me if your code is trash then."
As APLang is not suited, I clearly say it, for beginners, I will not impose a certain code or naming style, because I want this language to be used freely and use the paradigms and styles which you are familiar with. Of course, I will hopefully implement and add enough features to be used so. Thing is tho, as APLang will be have targets like JVM, I would be good if the naming styles of Java are used to be consistent with these environments.

> "Add enough features to be used so", if there are so many feature, can it end not well for larger teams, because they code everyone with different styles?

The solution to this is simple: configuration. I plan to make the compiler and parser most configurable possible, so some features can be disabled easily to ensure consistency and styles within the code in a team.

> Let's get started with the features then...

Table of Contents:

[TOC]

## Feature Set

### Code, Files & packages

#### Namespaces

Inside a src folder (src/main/aplang in gradle) there is a notion of packages and files. We must take first the notion of "namespaces", because this is the fondation of the code. A namespace, is a collection of functions/methods, variables/fields (& constants) and classes.
Is a package a namespace or not? So for example you can write in a file functions outside a class, and it will be accessible from the package not this file, because this question cannot be resolved easly, it will be configurable.

#### Entrypoint

#### Namespace Extentions

There are many ways to extend the abilities of a Namespace: Extention Methods, Namespace declarations, Traits, inline classes, `in path` in the class declaration and Inheritance.

##### Namespace declaration

There are instances where a developer wants to add additional classes to another namespace, or extracting a namespace from another one for clarity. Example:

```
Shape
├ Circle
├ Rectangle
└ Triangle
```
We want to access each of these Shapes like `Shape.Circle`, but if the Circle class is huge, it will result in a huge file. So it would be a good idea to extract to an individual file, but then you cannot access this class with Shape, because it's not in this Namespace anymore. So there is a construct `namespace ("." path | "." | path) ("{" namespace "}")` which modifies the parent namespace.
Example:

```kotlin
namespace .Shape

class Circle
class Rectangle
class Triangle
```
This will make these classes available through the Shape class. The `.` means "same package", if it's not supplied, it will count as a fully qualified namespace path starting from root. With this method it's actually possible to add functions to packages. The `{ namespace }` adds the ability to only add only `{` what's inside `}` in the supplied namespace.

##### Traits

Traits are basically externally implemented interfaces. This is extremely useful when a class has many interfaces which must be implemented and you don't want to implement them all in one file. Although traits can be used to implement interfaces for external classes (details see below), however the usage of visibility is restricted where the interface is implemented and if it's inlined or not.

##### Extension methods

Extension Methods are methods which are implemented on top of existing classes matching giving Generics or other at-compile known properties. Like traits, the visibility of members can be restricted, and extention methods cannot be overriden using inheritance.

### Expressions

What is an expression? An expression is in this context a term, a identifier, a construct, which returns a value. For example the addition. In APLang, well, to be "do what you want", almost everything will be an expression: if, while, for, return, break, loop, code blocks, ops, casts, `is`, method calls, array calls...

### Control Statements

All of this control statements works as expressions, the return type of break and return is the type of the receiver: as it breaks the execution, it will not return any value,
therefor `val test = if(someBool) "return" else return` will work, and the type of test will be evaluated as String and the return will be ignored.

#### if
  Syntax: `"if"  "(" expression  ")" expression ( "else" expression )?`

  As everything is an expression, the if works as statement and expression. If the if is used as an expression, the if will return `Optional<T>` or `T?` depending on the context, in some contexts it will raise an error signaling to specify an else branch.

#### break & label

  Label Syntax: `identifier ":" ( while | for | loop )`
  Break Syntax: `"break"(+"@"+IDENTIFIER)? expression?`

  The break stops the execution of the actual for/while/loop, if the loop is an expression and depending on the data type, this break can either return a single value out of the loop, stop the loop and append 1 additional last entry or just break it. Example:
```kotlin
val statements: List<Statement> = while(scanner.peek() != '}') yield statement()
```
Because the type of `statements` is a collectible type (list, collection, sequence,flow, stream, array...), for each iteration, the return value of `statement()` will be collected into a List. If the type is `Statement?`, the while above will return ALWAYS null, if you want to have a value, it must be broken, example:
```kotlin
val user = /* ... */
val name = /* ... */
val friend: Friend? = for(friend in user.friends) 
                        if(friend.name == name)
                          break friend
```
If a friend was found, it will be broken and it returns the found friend. We can also notice, that the if has no else branch, this is due that this the for should return 1 value, which is provided only by breaks, therefore, there s no need for the if to explicitly return a value.

Moreover there are a notion of "label", with `break@label [expr]` you can specify which loop to break. The specified expression is returened or added to collection, as if it where in the with the label specified loop. Example:

```kotlin
fn findFriendAndDoSomething(users: List<User>, name: String) {
  var foundFriend: User? = outer: for(user in users) {
    inner: for(friend in user.friends) {
      if(friend.name == name) {
        break@outer friend
      }
    }
  }
  // do something with foundFriend
}
```
As the break is an expression, it will be accepted in places in which expression are expected, the expression returns in it self nothing, because the execution of the particular place will not occur (Ex.: `methodCall(break test)`), so the type checker will expect the inferred break type to match the loop and not the call place.

#### yield

  Syntax: `"yield"(+"@"+IDENTIFIER)? expression?`

yield is a keyword used in loops to append/add, if the loop is used as an expression, a value to the stream of values.

#### for-loop
  Syntax: `"for" "(" IDENTIFIER ( (":" | "as" | "as?") type )? "in" expression ")" expression`

```
for    → "for" "(" ( "(" for_id ("," for_id)* ")" | for_id ) ( "in" expression )+ ")" expression
for_id → IDENTIFIER ( (":" | "as" | "as?") type )?
```

  The for provides a way to iterate over an iterable.
  `( (":" | "as" | "as?") type )?` allows to specify a type if it cannot be inferred, `as` and `as?` will cast each element, while the `as?` is a safe cast, `as` will raise an Exception if it cannot be cast.

#### while-loop
  Syntax: `"while" "(" expression ")" expression`

It works exactly like the for, but instead of iterating, it tests the condition.

#### loop
  Syntax: `"loop" expression`

The equivalent of a `while(true) expression`.

#### return

  Syntax: `"return"(+"@"+IDENTIFIER)? expression?`

Returns from a method or a lambda. The @ specifies the target from which it should return, per default it's in the scope it's in.

### Declarations

#### Val & Var

Syntax: `("val" | "var") IDENTIFIER (":" type)? ("=" expression)?`

It's only allowed to omit the assignment if and only if there is a moment when it's directly assigned like in a constructor or in an initializer. If the type is omitted, the type will be inferred if there is no ambiguity. `val` marks the variable as not reassignable, has unfortunately nothing to do with mutability (we are not in Rust . _.).

#### Constants

( Preview: const evaluations )

### Classes

#### Normal Classes

```
class_constructor? (":" type invoc_cnstrctr? ("," type invoc_cnstrctr?)*)? ("{" declaration* "}")?
cnstrctr_invoc → "(" expression ("," expression)* ")"
visibility     → "pub" | "prv" | "pak" | "pro"
generic        → "<" generic_dclr ("," generic_dclr)* ">"
generic_dclr   → IDENTIFIER (":" | ">=" | "<=") type
cnstrctr_param → ("var" | "val")? IDENTIFIER ":" type ("=" expression)?
cnstrctr_class → "(" cnstrctr_param ("," cnstrctr_param) ")"
```

##### Declaration

Syntax: `visibility? "open"? "inner"? "class" IDENTIFIER generic? ("in" path)?`

#### Data Classes

Syntax: `visibility? "open"? "inner"? "data" "class" IDENTIFIER generic? ("in" path)?`

#### Wrapper Classes

Syntax: `visibility? "wrapper" "for" type "as" IDENTIFIER ("in" path)? class_constructor? (":" type invoc_cnstrctr? ("," type invoc_cnstrctr?)*)? ("{" declaration* "}")?`

#### Sealed Classes

Syntax: `visibility? "open"? "inner"? "sealed" "class" IDENTIFIER generic? ("in" path)?`

#### Inline Classes

#### Enum Classes



### Methods

#### Syntax

#### Method Expression

#### Default Parameters

#### Overloading

##### Parameter Overloading

##### Return Type Overloading

### Fields

#### Properties

### OOP

#### Inheritance

#### Generics

#### Interfaces & Traits

### Functional

#### Lambdas

#### Currying

### Meta programming

### Miscellaneous

#### Pattern Matching

#### Deconstruction

