# Ughh... Macros

Macros are part of the ugly side of C++. Java opted not have macros at all. An
modern languages like Go and Rust opt for alternative implementations with
better semantics.

The truth is that macros can be extremely useful, but they are also completely
aweful.

A macro is a small definition for a common piece of code that you define. For
example,

```cpp
#define C 3

#define ABS(x) ((x) >= 0 ? (x) : -(x))
```

And this could be used as such:
```cpp
int x = ABS(foo());
```

You can also do condition compilation with them:
```cpp
#ifdef DEBUG
    std::cout << "Debugging... foo has value " << foo << std::endl;
#endif
```

You may also recall `uint8_t` and friends from the "Basics" chapter. Well, guess
what! They are implemented using `#ifdef`s.

And in fact, the `#include <iostream>` is a macro too, which includes a bunch of
declarations.

So what's so bad about macros? They seem to be pretty nice, right?

Wrong.

Before compiling the code, the compiler will invoke the _C preprocessor_ on your
code. The preprocessor will do _textual substitution_ on you code. Note this
_textual substitution_, not a function call. If you are not careful, you can get
extremely hard-to-find bugs and strange behavior.

For example, you may have noticed the strange, seeming-excessive
parenthesization of my `ABS` macro above. What happens if I remove them?

```cpp
#include <iostream>
#define ABS(x) x >= 0 ? x : -x

int main() { // we can omit argc and argv if we are not using them
    int y = ABS(3);
    std::cout << y << std::endl;
}
```

```
$ g++ -o test test.cc
$ ./test
3
```

Hmmm... seems to still work... How about something more complicated?

```cpp
#include <iostream>
#define ABS(x) x >= 0 ? x : -x

int main() {
    int y = ABS(0 > 1 ? 3 : -3);
    std::cout << y << std::endl;
}
```

```
$ g++ -o test test.cc
$ ./test
-3
```

Wat?

Remember this is textual substitution, so the code that actually got compiled
after the preprocessor went through is this:

```cpp
#include <iostream>

int main() {
    int y = 0 > 1 ? 3 : -3 >= 0 ? 0 > 1 ? 3 : -3 : -0 > 1 ? 3 : -3;
    std::cout << y << std::endl;
}
```

So
```
0 > 1 ? 3 : -3 >= 0 ? 0 > 1 ? 3 : -3 : -0 > 1 ? 3 : -3
(0 > 1 ? 3 : -3) >= 0) ? (0 > 1 ? 3 : -3) : (-0 > 1 ? 3 : -3)
(false ? 3 : -3) >= 0) ? (false ? 3 : -3) : (false ? 3 : -3)
-3 >= 0 ? -3 : -3
false ? -3 : -3
-3
```

These sorts of looney errors are a nightmare to debug because they are so
unintuitive. Avoid macros as much as you can.

Here is another brain teaser for you:

```cpp
#define NTH_POWER_OF_2(n) (1 << n)

int main() {
    int x = NTH_POWER_OF_2(64 >> 5); // Why is this not 4?
}
```
