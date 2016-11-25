# Hello, World!

Java
```java
// Test.java
public class Test {
    public static void main(String []args) {
        System.out.println("Hello, World!");
    }
}
```

C++
```cpp
// whatever.cc
#include <iostream>

int main(int argc, char **argv) {
    std::cout << "Hello, World!" << std::endl;
}
```

Ok, there is a lot to go through here.

1. Unlike Java, C++ files don't have to have the same name as the class.

2. C++ files can have a `.cc` or `.cpp` extension. It doesn't matter which.

3. In fact, a C++ file may not contain a class at all. C++ has C-style functions
   AND classes with methods.

4. The `main` _function_ is the entry point of the program in C++. Notice, it is a
   function, not a method, as opposed to Java's `main` _method_.

5. To print to the console in C++, you need to import the `iostream` header,
   which contains some definitions for working with I/O streams (such as stdin
   and stdout). These are not imported automatically. In C++, the way to import
   these definitions is with `#include`.

6. In Java, command line arguments are passed to `main` through `String []args`.
   But C++ arrays are not sized, so there would be no way to know how many
   arguments where passed in. Instead C++ (in the tradition of C) uses `argc`
   and `argv`. `argc` is the argument count, and `argv` points to the arguments.
   We will discuss pointers more later.

7. In C++, `std` is the standard library _namespace_. Namespaces are discussed
   much later, but generally, they are a way to prevent name clashes between
   different libraries and/or program definitions. The `::` is the resolution
   operator in C++. While in Java `.` (the dot operator) is used for both name
   resolution and field/method access, in C++, `::` is used to resolve a name,
   and `.` is used for field/method access. In this case, we are choosing `cout`
   from `std` and `endl` from `std`.

8. `cout` is a handle on `stdout`, and is roughly analogous to `System.out` in
   Java.

9. `<<` is an operator that has been overloaded in C++ to pipe things into a
   stream. More on operator overloading later.

10. `endl` is the "End of Line" character, and it makes the "Hello, World!"
    print a newline afterwards.

### Functions

You may have noticed that the syntax for a method/function in
C++ is similar to Java.

To define a function in C++, you need to follow something like this:
```
return_type name(type arg0, type arg1, ...) { // possibly no params
    // function body

    return value; // Same return statement as in Java
}
```

Calling a function is also similar to a Java method call:
```cpp
int x = foo();
bar(1, x, 3);
```

One other nice feature that C++ has: default parameter values.
```cpp
int foo(int a = 0) {
    return a + 1;
}

int main() {
    int x = foo();
    int y = foo(1);
}
```

In this example, the default value of `a` is 0. The user can then opt to either
pass a value explicitly or rely on the default. So `x` is 1, and `y` is 2.

Notice that parameters with default values must come after those without
defaults, and that if you have multiple parameters with default values, you must
specify all preceeding arguments. The following are both illegal:

```cpp
int foo(int a = 0, int b) { // ERROR: `b` does not have a default
    return a + b;
}
```

```cpp
int foo(int a = 0, bool b = false) {
    return a + 1;
}

int main() {
    int x = foo(true);  // ERROR: need to pass a value for `a`
}
```
