# Basics

Both C++ and Java fall into the category of C-like languages. That is, their
declaration syntax looks similar, they use semicolons to delimit statements, and
they use curly braces to delimit blocks. Naturally, since Java is a decendant of
C++, the syntax for classes in C++ and Java have similarities, too.

However, I encourage you not to take anything for granted. Their are some subtle
difference which can and have led to tricky bugs. So read the whole page!

#### Comments

C++ and Java have the same commenting syntax:
```cpp
// This is a line comment

/*
This is a 
big 
multi-line comment
*/
```

#### Variables

Variable declarations in C++ look similar to Java, and the same set of
identifiers is allowed.

```
Type name = value;
```

For example,
```cpp
int x = 0;
bool c = true;
char _l = ' ';
```

#### Primitives

Being a decendant of C, C++ has a number of integer types of different sizes and
behaviors. The most common being `char`, `int`, `long`, `float`, `double`.
Moreover, the integer types can be `unsigned` or `signed`. `signed` means that
the variable can take both positive and negative values, and this corresponds to
Java's integer types. However, C++ can also have `unsigned` integers, which can
only take non-negative values, but have twice the range. The following table
sums things up:

Type            | Size (bytes)          | Range
----------------|-----------------------|------
`bool`          |                       | `true` or `false`
`unsigned char` | 1                     | 0 to 255
`signed char`   | 1                     | -128 to 127
`unsigned int`  | 2, 4, or 8            | 0 to 2^(#bits)
`signed int`    | same as unsigned int  | -2^(#bits - 1) to 2^(#bits - 1) - 1
`unsigned long` | 4 or 8                | 0 to 2^(#bits)
`signed long`   | same as unsigned long | -2^(#bits - 1) to 2^(#bits - 1) - 1
`float`         | 4                     | Implements IEEE 754 floating point
`double`        | 8                     | Implements IEEE 754 floating point


But wait! In my example above, I just used `int` and `char`, not `unsigned int`
or `unsigned char`. Why?

Well, you can actually use a bunch of shorthands in C++. For example,
```cpp
char x2 = 0;     // The standard does not specify if this is signed or not
signed x0 = 0;   // same as signed int
unsigned x0 = 0; // same as unsigned int
int x1 = 0;      // same as signed int
```

You may also have noticed some empty spots in the table, which represent places
where the C++ standard does not specify the answer. Compilers can choose what
they want to do in these cases. Moreover, you may have noticed that the size of
`int` and `long` have multiple options. The exact size of an `int` or `long` on
a system depends on the compiler and architecture of the machine. On most 32-bit
machines now-a-days, `int` is 4B and `long` is 8B. On 64-bit machines, `int` and
`long` may be the same size (8B)... or may be not.

As you can see, this is a bit of a mess. That's why Java specifies that `int` is
4B and `long` is 8B, and both are signed.

C++ has a different solution. The standard library includes a number of type
definitions that are guaranteed to be of a certain size. For example, `sint32_t`
is a signed 32-bit integer, and `uint8_t` is an unsigned 8-bit integer. For this
book, though, I will mostly stick to the normal primitives.

Finally, you may have noticed that `String` is conspiculously missing from the
whole discussion. We will discuss strings later.

#### Arrays

Like Java, C++ has arrays. However, they behave _very_ differently from Java
arrays. We will discuss them further later. For now, the main point is that C++
arrays are *not bounds checked*. This means that you will not get an exception
if you read or write past the end of the array. The following will compile and
possibly run in C++:

```cpp
int x[3]; // Create an array on the stack... more later
x[0] = 0;
x[1234003] = 13;
x[-34] = x[242];
```
In fact, even though `x` is declared to have size 3, in general, it is
impossible to know how large an array is. Unlike Java, there is no `.length`. It
is up to the programmer to keep track of the length of an array and make sure
accesses are in bounds.

One other important point to note is that **all variable are created on the stack
unless otherwise specified**.
