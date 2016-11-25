# Polymorphism

Polymorphism is a huge principle in OOP. This is going to be a pretty massive
chapter, so hold on to your hat (if you wear a hat while reading C++ books).

Let's say we have the following Java code:
```java
class A {
    public void foo() { System.out.println("A"); } 
}

class B extends A {
    public void foo() { System.out.println("B"); } 
}

class C extends B{
    public void foo() { System.out.println("C"); } 
}

// In a main method somewhere
C c = new C();
B b = new C();
A a = new C();

c.foo();
b.foo();
a.foo();
```

Notice that each subclass redefines the method `foo`. They *override* `foo`.

So what prints?

```
C
C
C
```

Even though `a`, `b`, and `c` are all declared as different types, the JVM will
find and run the most specific `foo` method possible. Since all of these are
actually `C` objects, it is `C`'s `foo` that is called. This is called
*polymorphism* because each object can change what type in the heirarchy it
behaves as.

What about C++?

```cpp
#include <iostream>

struct A {
    void foo() { std::cout << "A" << std::endl; }
};

struct B : public A {
    void foo() { std::cout << "B" << std::endl; }
};

struct C : public B {
    void foo() { std::cout << "C" << std::endl; }
};

int main() {
    A a = C();
    B b = C();
    C c = C();

    c.foo();
    b.foo();
    a.foo();
}
```

```
C
B
A
```

Wait, so C++ doesn't have polymorphism?

Wrong.

The key is understanding what the declaration `A a;` does. It allocates space
on the stack, remember? So how much space does it leave? Enough space for any
subclass of `A`? Unfortunately, this is not possible because we might not know
how large this is; imagine providing a library with some types that users will
need to subtype.

So instead, C++ only leaves enough space on the stack for an `A`. So
effectively, this declaration will reduce a `C` object to an `A`; you _lose_
the `B` and `C` portions.

Great :( So how do we fix it?

Well, first, we need to find a way to avoid this space problem, so we will use
pointers.

```cpp
int main() {
    C c;
    B *b = &c;
    A *a = &c;

    c.foo();
    b->foo();
    a->foo();
}
```

Here we are creating a single `C` on the stack, and using pointers to it.
Notice, that really, Java has the same problem, but it is hidden because Java
already passes objects around by reference.

```
C
B
A
```

_sigh_ What now?

#### Static and Dynamic Dispatch

I know. I know. This is an awful time for a theory interlude, but bare with me.

_Dynamic dispatch_ is what Java does. The most specific `foo` for an object is
the one that is called. It's called "dynamic" because it depends on the runtime
type of the object. That is, even though `a` is declared as an `A`, the runtime
type is `C`, so `C`'s methods are used.

In contrast, _static dispatch_ uses the static type of the object to determine
what methods are called. The static type of `a` is `A`, so `A`'s methods would
be called. This is what C++ does by default.

The reason is that dynamic dispatch is slower. Each object needs to keep track
of all of the correct methods to call. This information is stored in a
structure called the _vtable_. Now, every method call requires an extra lookup
in the vtable to figure out which method to actually call.  But with static
dispatch, the compiler can just hardcode which method to call. The observation
is that very often, you simply don't need to use dynamic dispatch, so the extra
overhead is wasted.

As I said, by default C++ does static dispath, but we can specify dynamic dispatch on a per method basis by marking that method as virtual:

```cpp
#include <iostream>

struct A {
    virtual void foo() { std::cout << "A" << std::endl; }
};

struct B : public A {
    virtual void foo() { std::cout << "B" << std::endl; }
};

struct C : public B {
    virtual void foo() { std::cout << "C" << std::endl; }
};

int main() {
    C c;
    B *b = &c;
    A *a = &c;

    c.foo();
    b->foo();
    a->foo();
}
```

```
C
C
C
```

Now there is a subtlety here. Consider the following examples:

```cpp
#include <iostream>

struct A {
    void foo() { std::cout << "A" << std::endl; }
};

struct B : public A {
    virtual void foo() { std::cout << "B" << std::endl; }
};

struct C : public B {
    virtual void foo() { std::cout << "C" << std::endl; }
};

int main() {
    C c;
    B *b = &c;
    A *a = &c;

    c.foo();
    b->foo();
    a->foo();
}
```

```
C
C
A
```

```cpp
#include <iostream>

struct A {
    void foo() { std::cout << "A" << std::endl; }
};

struct B : public A {
    virtual void foo() { std::cout << "B" << std::endl; }
};

struct C : public B {
    void foo() { std::cout << "C" << std::endl; }
};

int main() {
    C c;
    B *b = &c;
    A *a = &c;

    c.foo();
    b->foo();
    a->foo();
}
```

```
C
C
A
```

```cpp
#include <iostream>

struct A {
    void foo() { std::cout << "A" << std::endl; }
};

struct B : public A {
    void foo() { std::cout << "B" << std::endl; }
};

struct C : public B {
    virtual void foo() { std::cout << "C" << std::endl; }
};

int main() {
    C c;
    B *b = &c;
    A *a = &c;

    c.foo();
    b->foo();
    a->foo();
}
```

```
C
B
A
```

So basically, if you consider the class heirarchy,
```
A
 \
  B
   \
    C
```
a method is only virtual from the top-most declaration of that method as
virtual. Notice in these examples that declaring `A::foo` as non-virtual
(omitting the "virtual" keyword) and `B::foo` as virtual means that any `A`
objects will use static dispatch while `B` and `C` objects use dynamic
dispatch.
