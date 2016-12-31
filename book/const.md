# `const`

Constants are really nice. They are especially easy to reason about. In C++, a variable can be declared `const` to let the compiler know that its value should never change after it is first declared; it is _immutable_.

```cpp
const int x = 0;
x = 1; // compile error: cannot reassign to const value
```

We can pass a constant variable around just like normal because it is copied on the stack. So the following compiles (though not very useful):
```cpp
const int x = 0;
foo(x);
...
void foo(int x) {
    x = 0; // Assigning to our local copy
}
```

The nice thing is that we can never accidentally change the value. This is very useful if we are trying to keep track of a pointer. We don't want to accidentally change or lose the pointer before we deallocate our memory:
```cpp
const int *my_arr = new int[10];

// do stuff with my_arr

delete[] my_arr; // we know that we are actually freeing the correct pointer here
```

Note that the `const`-ness of the pointer is independent of the `const`-ness of the pointee:

```cpp
int x0 = 0;                // normal int
const int x1 = x0;         // constant int
const int *x2 = &x0;       // constant pointer to non-constant int
const int * const x3 = &x1;// constant pointer to constant int
int * const x4 = &x1;      // non-constant pointer to constant int

x0 = 1;                    // ok :)

x1 = x0;                   // compile error: x1 is const

*x2 = 2;                   // ok :)
x2 = &x0;                  // compile error: x2 is const

*x3 = 2;                   // compile error: *x3 is const
x3 = &x0;                  // compile error: x3 is const

*x4 = 2;                   // compile error: *x4 is const
x4 = &x0;                  // ok :)
```

const parameters
const methods