General Referance Guide
====


# Memory Management

Swift and Xcode now use ARC (Automatic Reference Counting). Which this is generally very helpful, it can also lead to some issues such as with cycles when using blocks and closures which capture object by reference from their enclosing scope.

Code should aim to always avoid creating reference cycles. As a way of trying to avoid this one can analyse an app's Object Graph and where you discover a reference cycle look to correct this strong cycle by using a with `weak` and `unowned` modifer on one of the strong references.

 It can be helpful to use value types (struct, enum) as a way of preventing cycles.


Recall that **References Types** in Swift like classes and closures, have got different rules in regards to memory then the rules for value types. Elaborating on this: **Value Types like int, float, and structs are stack allocated, whilst the Reference Types like classes and closures are heap allocated**.

As a refersher, recall that the stack and heap work thus:
## Stack
* Stored in computer RAM just like the heap.
* Variables created on the stack will go out of scope and are automatically deallocated.
* Much faster to allocate in comparison to variables on the heap.
* Implemented with an actual stack data structure.
* Stores local data, return addresses, used for parameter passing.
* Can have a stack overflow when too much of the stack is used (mostly from infinite or too deep recursion, very large allocations).
* Data created on the stack can be used without pointers.
* You would use the stack if you know exactly how much data you need to allocate before compile time and it is not too big.
* Usually has a maximum size already determined when your program starts.

### Heap
* Stored in computer RAM just like the stack.
* In C++, variables on the heap must be destroyed manually and never fall out of scope. The data is freed with delete, delete[], or free.
* Slower to allocate in comparison to variables on the stack.
* Used on demand to allocate a block of data for use by the program.
* Can have fragmentation when there are a lot of allocations and deallocations.
* In C++ or C, data created on the heap will be pointed to by pointers and allocated with new or malloc respectively.
* Can have allocation failures if too big of a buffer is requested to be allocated.
* You would use the heap if you don’t know exactly how much data you will need at run time or if you need to allocate a lot of data.
* Responsible for memory leaks.


## Reference Counting

The use of a retain count has been around since the early day of iOS: The way this originally worked was that when you explicitly allocated an object it got a retain count of 1 and then when you called release or autorelease on that same object, its retain count was then decremented and the object was then collected. Furthermore, if you allocated further instances of the object then the retain count would increase further.

For example:

```ObjectiveC
NSObject *someObject = [[NSObject aloc] init]; //retain count becomes 1
[someObject release]; //retain count reduces back to zero 0
```

What happened with the above code is that when an object got released its -dealloc method got called on an object and its memory will then be reclaimed.

One of the pre-ARC tools that developers could use was called an `AutoreleasePool` which was a section of your app code where you can collect objects sent an autorelease message, you would then be able to clean them up via the sending of an NSAutoreleasePool  drain message (Source: P). At this point in time, one of the only alternatives to manual memory management in Objective-C was **libauto** which was "a scanning, conservative, generational, multi-threaded garbage collector". The algorithm used involved scanning the memory in use by an app and collecting out of scope memory.

## Automatic Reference Counting (ARC)

Automatic Reference Counting (ARC) is a memory management feature of the compiler used by Xcode; the built-in Xcode compiler is called Clang Compiler was developed within Apple by Chris Lattner and others. The origins of ARC were in work carried out by Chris and his colleagues on the Clang compiler, starting with the addition of C++ support, and also tied in with the early stages of development of Swift programming language. They felt during this that the memory management solutions (such as manual memory management and the libauto garbage collector mentioned above) were not the right automatic memory management for the compiler and they were not cutting the mustard and that ARC could be the new solution for this. Whilst ARC can technically be thought of as an alternative type of "garbage collection", that term typical does not refer to referencing counting based algorithms, meaning that ARC is NOT considered "garbage collection" in the general use of the term.

Automatic Reference Counting works for both the Objective-C and Swift programming languages, and it involves the compiler inserting the "object code messages" which previously the programmer had to type in manually: that is the retain and release keywords. These keywords work to increase and decrease the reference count for a particular object at run time. What this means is that when the reference count of an object does get to zero, the object is deallocated and kicked out of memory (Ref#: C) conversely ARC won't deallocate an instance when there is still at least one active reference to it (Ref#: D).

## Limitations of ARC

ARC implements automatic memory management for objects and blocks, this means that we no longer have to explicitly insert retains and releases as was previously the case. However, ARC is not a tracing type garbage collection algorithm, and "it does not provide a cycle collector", instead we must explicitly manage the lifetime of objects. In certain scenarios ARC is not able to work out when it's safe to deallocate a particular class instance and therefore, to avoid a retention cycle being created we need to help the compiler to work out what object we need for our program and which ones are safe to be let go.

One thing that ARC didn't really handle when it was introduced was CF objects from the CoreFoundation framework, so for example when I was updating a very legacy Objective-C app I would often see leaks involving these objects. This was because, with Core Foundation, any objects which you allocate needed to be released with either CFRelease or CFMakeCollectable, and they were not picked up by ARC. The reason for this is that the Core Foundation library is written in pure low-level C code, and it's a problem to try and make the use of the reference count automatic when using these CF types.

## Strong vs Weak

A class with a strong reference is managed using "normal" Automatic Reference Counting meaning that as long as there are any references anywhere to it - then it will stay in memory.

In contrast, a weak reference means: if no other class is referencing this object, then I don't care about the object either so this reference can be made Nil. In order for the weak modifier to work it needs to be nill-able, so that means that it can only be used with optional pointers to reference types. Weak pointers will never cause an object to be retained or kept in the heap (Ref#: T). One example of this is the outlets we can use with storyboards or nibs: since these are strongly held by the view hierarchy, we're free to designate them as weak (i.e. `@IBOutlet weak var carScreenView: CatScreenView!`).

## Retain / Retention Cycles

What we call a reference cycle can happen if two class instances hold strong references to each other, leading to circular reference situations, where each instance is keeping the other one alive.  This scenario, in turn, can often lead to memory leaks, or they can be cascading leaks where the memory usage starts to increase exponentially whilst the app is running. We need to be breaking cycles in the code manually by using the `weak` or `unowned` modifiers.




