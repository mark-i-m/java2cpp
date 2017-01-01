# Copy Semantics

This topic is completely foreign to Java.

The basic idea is that sometimes we want to make a physical copy of an object. We don't just want to alias it; we want to actually create a new object.

A simple solution is to actually copy the bits of the old object to the new. This is a very reasonable default implementation, but it isn't always correct. Imagine implementing a data structure that contains a pointer to some memory on the heap. A bitwise copy of this data structure would literally copy the bits of the pointer. So you would end up with two objects that contain the same pointer. This may be fine, or it may be really, really bad. For example, imagine if one of the object was deleted and its destructor deallocated the space while the other copy was using it! (Notice that this is not a problem in Java).

In C++, the solution is to define the _ copy semantics_ of a class. This consists of defining two behaviors:

1. How to construct a copy of an existing object.
2. How to assign a copy of an existing object to another existing object.

We will deal with #2 in an upcoming chapter. In this chapter, we will talk about #1.

We define how to construct an object as copy of another object using a _copy constructor_. Basically, we overload the constructor to take another object of the same type. A default implementation is available if we don't provide one. It simply copies bits, as described above.

```cpp
class List {
    int *my_storage;
    
    // ...
    
    // a copy constructor
    List(const List& other) : x(x)     // can use initializer lists, of course
    {
        // make a deep copy of the List contents...
    }
}
```
By convention, `other` is always pass by `const` reference. Usually, there is no need to take `other` by value or by non-`const` reference. Passing by value would involve needlessly copying `other` to the stack, and passing `other` as non-`const` would restrict the places where a new object can be copy constructed.

And now we can construct copies in the expected way:
```cpp
List other_list;
List list(other_list);
```
