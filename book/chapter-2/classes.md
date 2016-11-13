# Classes, Structs, Access Modifiers

So now we get to it!

An object has two parts. The first is state and the second is operations. Let us
start with state. Like C, C++ has structs. Like Java, C++ has classes.

```cpp
struct Foo {
    int x;
    bool y;
};              // notice the semi-colons at this line

class Bar {
    int x;
    bool y;
};              // notice the semi-colons at this line too
```
As you might imagine, here we have a class and a struct, both with fields `int
x` and `bool y`.

So what is the difference?

Access.

structs have all public fields by default, while classes have all private fields
by default. Other than that, they are exactly the same. So the following will
cause a compile error:
```cpp
int main() {
    Foo foo;
    Bar bar;

    foo.x = 0;
    bar.x = 0; // compile error here; bar.x is private
}
```
Notice that declaring `foo` or `bar` will initialize the variable automatically
on the _stack_.

We can fix this by using access modifiers:
```cpp
struct Foo {
    int x;      // public by default
  private:
    bool y;     // private
};

class Bar {
  public:
    int x;      // public
  private:
    bool y;     // private
};
```

Notice that the `private` modifiers are not really necessary to fix the compile
error, but they demonstrate the fact that we can specify any access modifers we
want. The modifiers apply to all fields until the next modifier. You can also
repeat modifiers as needed.

```cpp
class Foo {
    int x0;     // private by default
    int x1;     // private by default
    int x2;     // private by default
  private:
    int x3;     // private
    int x4;     // private
    int x5;     // private
  public:
    int x6;     // public
    int x7;     // public
  private:
    int x8;     // private
}
```
