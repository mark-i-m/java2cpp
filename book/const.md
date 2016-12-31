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
my_
```

const variables
pointers to const values vs const pointers to values

const parameters
const methods