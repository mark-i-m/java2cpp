# Pointers

If you have never used C or C++ before, you will find pointers take a bit of
getting used to. They are not hard, but they do require some re-adjustment.

In Java, the programmer has no access to memory directly. The JVM will figure
out where/when to allocate memory and free it. The garbage collector will make
sure old objects get clean up. References to objects hide all of this behavior.

C++ does not have that. Being a systems language, C++ (like C) directly exposes
memory to the programmer via **pointers**.

You can think of memory as a gigantic array of bytes. Each byte has an index.
When we wish to write or read a part of memory, we can do so with its index,
which we call its _address_.

Memory addresses are just integers. The width of pointer (in bits) is a key
specification of each machines architecture. Most computers and phones today are
either 32-bit or 64-bit, meaning they have 32-bit or 64-bit pointers. This is
important because it defines the amount of memory that you can address. A 32-bit
pointer can have 2^32 different values, so we can address 2^32 bytes of memory,
which is 4GB. This means that a 32-bit machine can only have 4GB of memory.
When 4GB of memory started to be a small amount, 64-bit machines were designed.
A 64-bit architecture can address 18.4467441 EB (exabytes) of memory, which is
more than 10^10 GB.

In C++, the programmer can explicitly create a pointer to a given address and
modify pointers.

```cpp
int *x = 0x3002;
```

By convention, addresses are given in hexadecimal. The `int*` type is pronounced
"pointer to `int`". In this example, `x` is a pointer to an integer at memory
address `0x3002`.

We can also take the address of variables with the `&` operator:
```cpp
int y = 50;
int *x = &y; // x is the address of y
```

So now, `x` is the address where `y` is stored in memory (probably somewhere on
the stack). We say that "`x` points to `y`". We can access the memory at a
location by _dereferencing_ it with the `*` operator:

```cpp
int y = 50;
int *x = &y;

*x = 40; // y now has value 40
```

One way to think of this is that the declaration `int *x` tells you that `*x` is
of type `int`. More generally, `T*` for any type `T` is a "pointer to `T`".
Since `T*` is itself a type, we can have pointers to pointers. Naturally, you
can see how this can get confusing.

```cpp
int y = 50;
int *x = &y;
int **w = &x;
```

In practice, it tends to be unusual to see more than about two layers of
pointers, but there is nothing preventing an `int ********`... For this reason,
pointers are notorious for being difficult to reason about and a huge source of
nasty bugs and security vulnerabilities. That is why Java opts not to expose
pointers at all. But if used correctly, pointers can be an extremely powerful
primitive for a programmer.

To this end, there are a few more important things to know:

#### Null

First, as with Java, C++ has a "null" value. Pointers with the value `0` are
referred to as "null pointers". Accessing address 0 is an error, and the OS will
kill your program if you do it, giving you the dreadful "Segmentation Violation"
error (also known as "segfault"). Usually, null pointers are used as a marker
for "no value". For example, a linked list might terminate with a node that has
a null "next" pointer, indicating that there is no next link.

In general, it is only correct to access addresses that have been allocated. Any
other memory access is **undefined behavior**, meaning that anything could
happen. That is extremely bad because it means your program could crash (be
killed by the OS), or worse, it could continue running and propagate some error
that you only discover later. A huge portion of security vulnerabilities have to
do with illegal memory accesses.

#### Pointer Arithmetic

Second, because pointers are just integers, we can do math with them.
```cpp
char *x = &a;       // take address of some variable `a`
char *y = x + 1;
```

In this case, since `a` is probably 4B, `y` will end up pointing to the int that
starts one byte into `a`:

```
Memory: [...AA BB CC DD EE FF...] // Bytes in hex
            ^  ^
            x  y
```

So if we were to print the value of `x`, we would get `0xAABBCCDD`, but `y`
would be `0xBBCCDDEE`. (Note, that I am ignoring the issue of byte-ordering
because it would not matter in most cases. If you want more info on this see
[here](https://en.wikipedia.org/wiki/Endianness))

Doing math on a pointer is referred to as _pointer arithmetic_. One important
note to make about pointer arithmetic as that it is scaled by the type of the
pointer. For example, in the example above, `y` is the address immediately after
`x` because `char` has size 1B. If instead we had
```cpp
int *x = &a;
int *y = x + 1;
```
then `y` would point to the address 4B after `x` because `int` has size 4B.

#### Arrays

Third, arrays in C++ are nothing more than pointers to the beginning of a
portion of memory containing the elements of the array. This is why arrays have
no length associated with them.

```cpp
int arr[3];
int x* = arr;

int y1 = arr[0];
int y2 = *arr;
```

Here, `arr` has the same value as `x`, and `y1` has the same value as `y2`
(which is some arbitrary value that the uninitialized array happens to contain).
In fact, the index operator (`[]`) is nothing more than syntactic sugar for
pointer arithmetic and dereferencing: `arr[i]` is equivalent to `*(arr + i)`.


At this point, you may recall the signature of the main method:
```cpp
int main(int argc, char **argv)
```

`argc` we already discussed. `argv` is pointer to a pointer to a character. In
fact, this is one way of representing an array of strings. Each string is an
array of `char`. Since arrays are only pointers, we have a pointer to an array
of `char`, which is a pointer to pointer to `char`. You might notice that we
don't know when each string ends! This is solved by terminating each string with
the null character: `'\x00'` (a byte with value `0x00`). Strings will be
discussed more later.
