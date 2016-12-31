# Copy Semantics

This topic is completely foreign to Java.

The basic idea is that sometimes we want to make a physical copy of an object. We don't just want to alias it; we want to actually create a new object.

A simple solution is to actually copy the bits of the old object to the new. This is a very reasonable default implementation, but it isn't always correct. Imagine implementing a data structure that contains a pointer to some memory on the heap. A bitwise copy of this data structure would literally copy the bits of the pointer. So you would end up with two objects that contain the same pointer. This may be fine, or it may be really, really bad. For example, imagine if one of the object was deleted and its constructor deallocated the space while the other copy was using it!