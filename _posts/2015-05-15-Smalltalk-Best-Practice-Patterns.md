---
layout: single
title: Smalltalk Best Practice Patterns
categories: [reading]
tags: [smalltalk, oop]
---

I was recently asked by a friend about which programming books I've found most
useful over the years.
I've read a lot of software development books but one of the ones which I found
most useful, and which I always flick back over, was [Smalltalk Best Practice Patterns](https://www.bookdepository.com/Smalltalk-Best-Practice-Patterns-Kent-Beck/9780134769042) by Kent Beck.


Although I am fond of the language, I am not a Smalltalk developer. You don't
need to be one to enjoy this book.
The examples are written in Smalltalk, but the code snippets are not as
important as the principles and best practices for object oriented development.
I write better code having read this book.

Broken down into chapters such as Behaviour, State, Collections, and Classes,
Kent provides detailed patterns for producing effective code by first phrasing
the problem as a question

> How do you provide safe, general access to collection elements?

and then providing a concise answer

> Implement a method that executes a block fo each element of the collection.

```smalltalk
Department>>employeesDo: aBlock
    employees do: aBlock
```

Topic such as
* Choosing meaningful, intention-revealing names for objects and methods
* Decomposing larger methods into smaller more atomic units
* State management

are all covered in the *92* patterns.

I would highly recommend that you read this book if you are writing object-oriented code. You will immediately be writing better code.
