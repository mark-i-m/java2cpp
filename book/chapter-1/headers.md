# Header Files

C++ actually has two types of files: Source files (ending with .cc) and Header
files (ending with .h). The intention is to separate interfaces and
implementations from each other with two primary objectives:

1. Improve code readability
2. Improve compile time

Let's take these in order.

First, by separating interface from implemenation, C++ allows consumers of a
library to be able to see what classes, methods, and functions are defined in a
library without worrying about the implementation. This is a classic principle
of OOP, which Java follows too, though in different ways.

Second, separating interface from implementation means that we don't have to
recompile the whole program if we change a portion of it, as long as the
interface remains the same. To understand this, we need to look at how the
compiler operates. There are a few steps:

1. **Preprocessing**
2. **Compilation** This is the actual step that involves compiling each file. The
   output is some assembly code representing the contents of only that file.
   This is where you see all of the compile errors.
3. **Assembly** This stage takes the assembly instructions generated in the
   previous stage and actually produces machine code representing the contents
   of the source file. This is called an _object file_ (usually they have a ".o"
   extension).  Up until now, we have only been compiling a single file, so the
   compiler does not need to output any intermediate files; it can just spit out
   an executalbe. But when you compile a large project, the compiler will
   compile each source (.cc) file individually, taking into account any code it
   has included from header files with a `#include`. These object files are not
   runnable programs; they represent compiled code from only a single source
   file. Placeholders are left in the object file for code that is expected to
   exist in other source files or libraries.
4. **Linking** This process takes all object files and libraries and produces an
   executable program. The placeholders in the object files are replaced based
   on the contents of the other object files. If the linker cannot find a
   symbol, which the object file uses, it will complain and abort. This is
   called a "linker error".

Notice now that changing one source file means we only need to change a single
object file and re-link the program. If you have hundreds of source files, this
can save a lot of time. Unfortunately, if you change a header file, you will
have to recompile all sources that include that header file. This makes sense
programmatically too. Changing an implementation for one portion of the program
should not require recompiling the whole thing, whereas chaging an interface
means multiple components might change.

So what does a header file look like? Really, it contains ordinary C++. In fact,
there are no restrictions on what can go in a header file. Generally, for the
reasons mentioned above, you want to keep it minimal, though. Headers will
continually be compiled with the files that include them.

```cpp
// test.h
#ifndef _TEST_H_
#define _TEST_H_

int f(int, char);

#define MAX(x,y) ((x) > (y) ? (x) : (y))

#endif
```

```cpp
// test.cc
#include "test.h"

int f(int x, char y) {
    return MAX(x + y, 3);
}
```

```cpp
// main.cc
#include "test.h"
#include <iostream>

int main() {
    std::cout << MAX(f(10, 'a'), 2) << std::endl;
}
```

There are a few things to say here:

1. Any file that includes a header can use the definitions of that header.

2. `int f(int, char);` is called a forward declaration. It is basically a hint
   to the compiler that a function called `f` exists that returns an `int` and
   takes an `int` and a `char`. If this is not true, the linker will complain
   that it cannot find the symbol `f`.

3. System headers are referenced using the angle brackets, as in `<iostream>`,
   while custom headers are referenced using their file path an double quotes,
   as in `"test.h"`, or hypothetically `"foo/bar/other_test.h"`.

4. While not illustrated in this example, header files can actually `#include`
   other headers. This is extremely useful.

5. Remember `#include` is preprocessor directive. Yes, the preprocessor is
   actually textually substituting _the whole header file_ into your source
   before compilation. For this reason, we need the `#ifndef` guards in our
   header files. If x.cc includes x.h and y.h, and y.h also includes x.h, then
   the definitions in x.h will be duplicated. Surrounding a header file with
   `#ifndef`, `#define`, and `#endif`, as shown above is common practice to
   avoid this duplication problem. If the file has previously been imported,
   then `_TEST_H_` will already be defined (it is an empty macro), so the
   contents of the header will not be included again.

---

So that's it! You have now seen the basics of C++, which also happens to be the
majority of the C language, too. The rest of this book deals with OOP and how
Java and C++ differ.
