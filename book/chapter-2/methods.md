# Methods

Now, we have discussed how to create classes with fields, but we would like to
also add operations.

In Java, you can define methods on classes. You can do the same in C++. Access
modifiers work the same for methods as they do for fields.

```cpp
class Foo {
    int x;

  public:
    int getX() {
        return x;
    }

    void setX(int x) {
        this->x = x;
    }
};
```

Here, we defined a couple of methods. This is all pretty straighforward and very
similar to Java.

You may recognize `this` from Java too. In Java, `this` has type `Foo`. In C++,
`this` has type `Foo*` (pointer to `Foo`). In order to access fields given a
pointer, we need to dereference the pointer first: `(*this).x`. You can see that
writing that is a bit painful, so for convenience, we have the `->` notation.
So, `this->x` is equivalent to `(*this).x`.

Going back to the interface vs implementation theme, C++ offers a way to
separate the method definitions from the class:

```cpp
class Foo {
    int x;

  public:
    int getX();
    void setX(int);
};

int Foo::getX() {
    return x;
}

void Foo::setX(int x) {
    this->x = x;
}
```
Notice that this is the same forward declaration syntax we saw before except
inside a class. Also, the methods themselves are defined outside the body of the
class, but this has no semantic difference from the first code sample above.
Notice that you need to specify the class name in the method definition with the
`::` operator.

Common practice is to put the class definition in a header file and put the
method implementations in a source file.

```cpp
// foo.h
#ifndef _FOO_H_
#define _FOO_H_

class Foo {
    int x;

  public:
    int getX();
    void setX(int);
};

#endif
```

```cpp
// foo.cc
#include "foo.h"

int Foo::getX() {
    return x;
}

void Foo::setX(int x) {
    this->x = x;
}
```

#### Overloading

As in Java, you can overload methods. The signature of a function or method is
the same as in Java: the name and the type and number of parameters.
```cpp
void Foo::setX(int x) {
    this->x = x;
}

void Foo::setX() {
    setX(1234);
}
```

Notice that with default parameters, it is possible to create ambiguity:
```cpp
void Foo::setX(int x = 0) {
    this->x = x;
}

void Foo::setX() {
    setX(1234);
}
```
In this example, it is unclear what a call to `foo.setX()` should do.

#### Constructors

C++ also has constuctors. As a matter of fact, C++ has several types of
constructors that get invoked at different times. For now, we will just cover
the normal constructor, which is analogous to Java. We will come to the others
later.

The syntax is similar to Java. The constructor is a method with no return type
that has the same name as the class. Like normal methods, you can access `this`,
use default parameters, overload, and define inside or outside the class.

```cpp
class Foo {
    int x;

  public:
    Foo() { x = 0; }
    Foo(int x, bool y = true);
}

Foo::Foo(int x, bool y = true) {
    this->x = x;
}
```

In addition, C++ features special syntax for initializing fields in simple ways
with _initializer lists_. The initializer list code runs just before the body of
the constructor. This code is equivalent to the above:
```cpp
class Foo {
    int x;

  public:
    Foo() : x(0) { }                // Init field `x` with value 0
    Foo(int x, bool y = true);
}

Foo::Foo(int x, bool y = true) : x(x) { }   // Init field `x` with the value of
                                            // parameter `x`
```
The initializer list consists of a colon `:` followed by a comma-separate list
of `field(value)`. In this case, the list contained only one field. Notice, that there is no use of `this`, in the example, even
though `x` the field and `x` the parameter have the same name. This is because
it is unambiguous; you can only initialize fields like this, not normal
variables. This syntax is only valid for constructors.

#### Destructors

While constructor runs when an object is constructed, a _destructor_ runs when
an object is destroyed (when it goes out of scope). Its purpose is to do any
necessary clean up. It will become clear later why this is useful.

As with constructors, including destructors is optional. A destructor cannot
have parameters because it is automatically called by the compiler. It also does
not return anything, like the constructor.

Here is what it looks like:
```cpp
Foo::~Foo() {
    // do stuff
}
```

The name must be `~Foo`. Since destructors can have no parameters, they cannot
be overloaded.

#### Scope

This part is actually significantly different from Java, and it will probably
take some getting used to. However, writing efficient and correct C++ requires
you to understand it.

In Java the following is illegal because `foo` is not initialized.
```java
Foo foo;
foo.bar();
```

In C++, this is completely legal. As a matter of fact, the line `Foo foo`
actually calls the default constructor (the one with no parameters) to
initialize `foo` on the stack. Notice the conspicuous lack of `new`, which we
will get to later. Also, notice what the following does:

```cpp
Foo foo = Foo();    // Don't do this
foo.bar();
```
This calls the default constructor of `Foo` _twice_: once to initialize foo on
the stack implicitly, and once explicitly with `Foo()`. While this is not
incorrect in this case, it is inefficient, so don't do that.

The logical next question here is "how do I call the constructor with arguments?". As you might expect, the following is not desirable:
```cpp
// Call the default constructor, then the constructor with 1 arg
Foo foo = Foo(1); 
```

Instead, the correct thing to do is this:
```
Foo foo(1); // call only the 1 arg constructor
```

Finally, when an object goes out of scope, the destructor runs... there are some
nuances here, but we will get to the later.
```cpp
int main() {
    Foo foo; // default constructor

    // do some stuff
}   // ~Foo runs now...
```
