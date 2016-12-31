# The Heap

I briefly mentioned before that in C++ \`new\` has a special meaning different from Java. In Java, \`new\` is simply a keyword that causes a new object to be constructed. In C++, \`new\` is an operator \(which will become more significant to you when you read the chapter on operator overloading\), and it specifically _allocates_ and _constructs_ an object on the heap.

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

After the \`delete\`, it is no longer valid to use the \`foo\` pointer, which now points to un-allocated memory.








