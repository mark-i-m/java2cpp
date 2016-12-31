# References

We now come to another important and fundamental feature of C++.

Simply put, a reference is an alias of another variable. In Java, all variable of object types are references by default (you don't have a choice). In C++, you do have a choice.

First, it is necessary to explain another important concept in C++: _l-values_ and _r-values_.

An _l-value_ is a value that has a memory location. That is, it can appear on the left-hand-side of an assignment (hence the 'L'). An _r-value_ is a value that does not have a memory location. That is, it can _only_ appear on the right-hand-side of an assignment.

For example, variables are l-values, where as literals are r-values. You can take the address of an l-value with the `&` operator. You cannot do that for r-values. For example, you cannot do `&3`.

In C++, you can create a reference to any l-value:
```cpp
int x = 0;
int& y = x;     // y is a reference to x

y = 0;          // x now also is 0
y = &x;         // compile error: y is of type int, but &x is an int*

if(&y == &x) {
    std::cout << "Same" << std::endl; // This will print
}

int& z = 3;    // compile error: cannot initialize a reference from an r-value
```

`y` can never be disentangled from `x`. And it is interchangeable with `x` (assuming that `x` remains in scope). Anything done to `y` will happen also to `x`, and vice versa. Take the address of `y` results in the same value as taking the address of `x`. They are identical in every way, except possibly scope and `const`-ness, as we will see.

You may be wondering (quite naturally) why you would do this, as opposed to passing a pointer.

The answer is simply that there are a lot of places where references are more convenient for various reasons. This will become more apparent in upcoming chapters.

In particular, not that it is often useful to take parameters by value:
```cpp
void foo(int& x) {
    x++;
}

// somewhere else
int x = 0;
foo(x);
std::cout << x << std::endl; // prints '1'
```

Voila! That's about it. But before moving on, let me address `const` references.

```cpp
void foo(const int& x) {
    x++;                 // compile error: x is const!
}

int x = 0;
foo(x);
```

Just as in the previous chapter, declaring `x` as `const` represents a promise that `foo` will not change its value, even though `x` is a reference. This is even more useful when dealing with objects.
```cpp
void foo(const Foo& my_foo) {
    my_foo.print();
}
```
Here we can use the dot-operator, rather than the arrow-operator (`->`), because `my_foo` is not a pointer. However, we cannot call any methods of `Foo` that would mutate `my_foo`; we can only call `const` methods. Thus, references in this context represent a convenient way to pass objects without dealing with pointers and with all the benefits of having immutability. We can give the `foo` method our `Foo` object and let it do whatever it wants, and we know that it will never change because `my_foo` is const. At the same time, `foo` can avoid dealing with dereferences and arrows. And since references are essentially arrows under the hood, they are fast to pass around.