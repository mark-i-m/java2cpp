# The Heap

I briefly mentioned before that in C++ `new` has a special meaning different from Java. In Java, \`new\` is simply a keyword that causes a new object to be constructed. In C++, \`new\` is an operator \(which will become more significant to you when you read the chapter on operator overloading\), and it specifically _allocates_ and _constructs_ an object on the heap.

At this point, it is useful to take a step back and explain memory layout. Generally, memory is laid out in four main portions:

1. _Text_: this is were the program itself lives.
2. _Static_: this is were global variables live.
3. _Stack:_ this is literally a big stack. It lives at the top of memory \(high-numbered addresses\), and grows downwards \(towards low addresses\). The elements in the stack are called _stack frames_. Each frame keeps track of the local variables and computation of a single invokation of a function or method. All local variables live on the stack. When a function is called, information is stored in the stack frame about who the calling function was \(so we can return\), along with some other stuff. And space is allocated for all local variables. While the function is running the stack keeps track of the values of local variables. When the function returns, the stack frame is popped from the stack.
4. _Heap_: this is a large region of memory where heap-allocated objects live. It's organization depends heavily on which version of \`libc\` you have. It doesn't really matter for the purposes of this book, but generally, the heap starts at low-ish memory and grows upwards, toward the stack. This chapter is all about the heap!

Most serious programs requires some heap allocation, even if it is hidden within some library data structure. The lifetime of an object on the heap in C++ is very similar to that of a struct in the heap in C. First, the object needs to be allocated. In C, this is done via a call to \`malloc\`. In C++, \`new\` is a wrapper around \`malloc\`, which automatically uses the correct size. \`new\` returns a pointer to the object on the heap. Unlike \`malloc\`, the pointer is already of the correct type. \(Btw, if you are not familiar with C, don't worry about the comparisons to \`malloc\`\). This pointer can be passed around just like any other pointer in C/C++. Finally, when you are done with the object, you must \`free\` its memory. In C, this is done by calling \`free\`. In C++, this is done with the \`delete\` operator.

So here we go:

```cpp
Foo *foo = new Foo();
do_stuff_with_foo(foo);
delete foo;
```

There are a few tricky parts to be careful of:

1. You _must never_ dereference a pointer to unallocated memory. This means that you should make sure that if you pass a pointer to a function, it must be valid. It also means that you should make sure that nobody is using a pointer when you deallocate it. And it also means that if somebody passes a pointer to you, you should make sure they will not deallocate it while you are holding it. You should never dereference a pointer after freeing it. This is _undefined behavior_ :(
2. You _must never_ deallocate a pointer twice. This _undefined behavior_ :(
3. You _should_ deallocate a pointer exactly once. Otherwise, you have a _memory leak_. That memory will not be allocated to anyone else as long as it is allocated to you. If you lose the pointer, it is impossible to deallocate the memory. For short-running programs, this might be ok since the OS will reclaim all of the program's memory when it terminates. But for long-running programs, this can lead to unexpected and hard-to-reproduce crashes due to running out of memory.

No pressure.

If you feel intimidated, it is because this can be a hard thing to do. Memory errors are a common source of bugs and security vulnerabilities. That's why some more modern languages opt to put restrictions on pointers. For example, Java does not have pointers at all, and memory management is done entirely through the JVM. Rust restricts aliasing pointers and forces the programmer to prove that pointers are valid when they are used. Go does not have a null pointer.

So why go to all the trouble?

First, performance: when passing values to and returning from functions and methods, arguments and return values may be passed on the stack if they are large (as opposed to passing them through a processor register which is super fast). Copying large values to the stack is very slow for large data structures. Instead, passing a pointer to the structure is fast, since a pointer is a single word (4 or 8 bytes).

Second, functionality: consider the following function:
```cpp
Foo get_foo() {
    return Foo();
}
```
This function will create a `Foo` on the stack frame of `get_foo`. Then it will copy the `Foo` to the caller's stack upon return. This is slow, so we want to use pointers:
```cpp
Foo *get_foo() {
    return &Foo();
}
```
This, however, is _undefined behavior_! We are creating a `Foo` on the stack frame of `get_foo` and returning a pointer to that new object. But when `get_foo` returns, the stack frame is no longer valid! So we now have a pointer to the unallocated memory! Bad!

Instead, we need to allocate the new `Foo` on the _heap_ and pass around its pointer.
```cpp
Foo *get_foo() {
    return new Foo();
}
```
Now, the following is safe to do:
```cpp
Foo *my_foo = get_foo();
do_stuff_with_foo(my_foo);
delete my_foo;
```

Great! That's pretty much it, except for one thing: arrays...

Imagine doing the following:
```cpp
int *my_nums = new int[10];

// do some completely safe and correct stuff

delete my_nums;
```

This seems innocent enough, but actually, we have a memory leak!

Here's the thing. When we allocated `my_nums`, we claimed enough space for 10 `int`s. Remember, in C/C++, arrays are pointers, so even though we have an `int *` there is actually 10 `int`s-worth of space reserved, and all of the space needs to be freed, but the pointer does not know that! In fact, if you think about it, how did the `new` operator know to create 10 `int`s of space anyway?

The key is that there are actually two `new` operators: `new` and `new[]`. We actually used `new[]` here to create an array. Likewise, there are two `delete` operators: `delete` and `delete[]`. So the correct thing to do is this:

```cpp
delete[] my_nums;
```

