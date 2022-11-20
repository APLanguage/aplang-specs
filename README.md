# APLang(uage), a programming language by me and for me.

## Introduction

Hello stranger! I'm Amejonah, also known in some communities as AP. For some time, a thought came into my mind: to create my own language. "Why?" do you ask? Simple, because I can, I thought so. But after my time spending with the parser in [Rust](https://github.com/APLanguage/aplang-rs), I realized that maybe I should start lower, with less features and with a programming language which is easier to write for me: I started in [Kotlin](https://github.com/APLanguage/aplang-lite-kt), a light version of the language, with "light"/"lite" I mean, really simple. It has classes, methods, fields, inheritance and... nothing more, well, and static strong typing, I hate dynamic typing and weak typing.  
After some time, I finally started with writing down a [list of features (Projects)](https://github.com/orgs/APLanguage/projects/1) for APLang, let's hope it doesn't get to much for me to handle.

## Core Concepts

What is the problem APLang tries to solve? If we look at the memmory management and see how the compile time is doing, we can clearly see that most of GC languages, even the main ones, does not have macros and such incorporated in the language, but rather use concepts like reflections or compiler plugins.
I see APLang in the position where
- it has many compile time features in the language built-in (like Rust for example),
- strong and staticly typed,
- having many neat helping functions (ike Kotlin) and
- working on a GC VM, where you dont have spend time with lifetimes or malloc/free.
