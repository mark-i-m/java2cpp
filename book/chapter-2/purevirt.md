# Interfaces and Abstracts

In Java, you can define abstract classes and interfaces, which define a
contract that objects of that type must comply with but don't define any
implementations.

```java
abstract class Foo {
    public abstract void foo();
}

class A extends Foo {
    // Must provide `foo` or be declared abstract itself
    public void foo() { }
}
```

```java
interface Foo {
    public void foo();
}

class A implements Foo {
    // Must provide `foo` or be declared abstract itself
    public void foo() { }
}
```

In Java, abstract classes can contain some implementations that may be common
to all subtypes, while interfaces cannot provide any implementations. However,
a class can only extend one abstract class, whereas it can implement multiple
interfaces. That is, multiple inheritance is allowed for interfaces.

In C++, these notions are present, but not as explicit. A method can be marked
as "abstract" by saying that there is no implementation. In C++ terms, `foo` is
called a _pure virtual_ method:
```cpp
struct Foo {
    void foo() = 0;
};
```

This implicitly marks `Foo` as abstract; it cannot be instantiated. Also,
because `Foo` only contains pure virtual methods, we say it is _pure abstract_.
