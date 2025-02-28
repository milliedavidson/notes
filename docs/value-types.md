# Value Types

C# organises types in two categories and they are stored differently:

- **Value** types.
- **Reference** types.

A variable that has a *value type* will **contain an instance of that type** (different to a *reference type*, which contains a reference to an instance of that type). This means that operations on a value type variable will **only affect that instance of the value type**.

## Memory management

First, we must understand memory management. Data is stored in the memory and there are two types of memory: **the stack** and **the heap**.

### The stack

The stack works based on **LIFO (last in, first out)** - think of this as a stack of pancakes; the last pancake you put on the stack is the first one you take off.

- Placing data at the top of the stack is called **pushing**.
- Removing data from the top of the stack is called **popping**.

![Image of pushing and popping data](/images/value-types-push-pop.png)

The stack stores data such as:

- The value of variables which are marked **value types**.
- **Parameters** which have been passed to methods.
- The current **execution environment** of the program.

### The heap

The heap is an area of memory where the data stored is a bit more 'random'. Data on the heap can be added or removed in any order.

The heap stores data such as:

- Data that is **not known** at compile time.
- Data that is **too large** to store on the stack.

## Value types location

Objects of value types are stored in **one location of memory** - on the stack. Whereas *reference types need two locations in memory*. The actual data is stored on the stack; the reference pointing to the real data is stored on the heap.

Value types include:

- byte
- int
- short
- bool
- long
- enum
- struct

## How do I know if it's a value type?

You can check if a type is a value by using the ```typeof``` operator to get the ```System.Type``` instance for the type. Then, check the ```IsValueType``` property. For example:

```csharp
using System;

WriteLine(typeof(int).IsValueType); // will output True
WriteLine(typeof(string).IsValueType); // will output False
```
