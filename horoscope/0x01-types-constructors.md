# Horoscope 0x01 - Types & constuctors

One main idea in APLang is being simple if it needs to and complex when you want it to. As APLang is a statically and mostly (some considerations on conversions and compromises should be made, so it is possible to be frictionless).

To start, the definition of a simple struct is:

```kotlin
struct Test(var a: i32, val b: str) {
    // it is TBD if the first parameter needs to be "self" or "&self",
    // it doesn't matter for this horoscope tho
    fn test() {
        println("test")
    }    
}
```

This looks nearly identically to Kotlin and will likely be so to be fammiliar to this target audience. Anyway, the next question would be, how to initialize this?

```kotlin
// the type in comments after a statement
val t1 = Test // Type<Test>[const, owned]

// as this is const and basically a value, the name can be accessed easily
t1.name // str[value<"Test">, attr{type: "Test"}, const, owned, ref?]


// now, to be able to instanciate, the compiler generates
// (or more like appends more data to "Type" type)
// Test being invokable if and only if all type constraints are made with (a: Int, b: str)
// And yes, invokable can have names on each parameters, they have no impact on invariance or whatsoever
val t2 = Test(1, "test") // Test[const, owned, values{a: 1, str: "test"}]
// t2 contains a implicit const variable and is owned by the current context

// it is currently TBD if references are a thing, but this may look like this
val t3 = &t2 // Ref<var[attr{var: t2}>[const, owned]
// t2 is now Test{a: 1, str: "test"}[const, referenced]
// the reference itself is now owned too
```

The `[]` part of types are "type attributes" or "type tags". These can contain data on types themselves and maybe matched onto it. What does it mean? It means that you can write an `impl` or a function accepting types which are needed to have to be accepted.

The main concept of ownership and borrowing should be discussed at a later horoscope, but can we do it? I dunno.

```rust
fn acceptOwned(test: Test[owned]): Test[owned] {
    test.a = 3
    // test is now Test[owned, values{a: 3}]
    return test
}

// for the caller:
val t2 = Test(1, "test") // Test[const, owned, values{a: 1, str: "test"}]
acceptOwned(test)
// is t2 now Test[moved]? I dunno how I should do it, sadly.
```

One main issue currently is, how to passt type parameters into constructors?

Rust has the `::<>` turbofish syntax, but this is basically a different, inconsistent, way to add parameters in constrast to `()`. We could argue that `::` could be used for type access, but... `Test.name` can already work... let's see how this will go.

As TL;DR, APLang should be able to have Data appended and read to/from Types themselves. Ths allows mixing compile time features (read from type name or something like that) and runtime (being able to pass the name into functions accepting a string). Additionally, some ideas on ownership & borrowing have been dropped and tried to be put into type attributes themselves.