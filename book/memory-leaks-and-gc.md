# Memory management

Memory management in C++ is very different from Java. In Java, you really don't need to think about memory safety at all, but as you have probably notice, this is not true of C++.

This chapter deals heavily with how memory is allocated and deallocated, and how object's memory is manipulated in C++.

Simply put, every computer has some amount of memory made available to it by the operating system. In particular, the OS provides the abstraction that the whole address space is at the disposal of the program. Some of these addresses, the OS may reserve for its own purposes, but the bulk of the space is available to the program.

The question now is how to use that space. The next chapter discusses this further with regards to C++ (and most low-level languages).

So how does Java do this?

When you use the `new` keyword in Java, space is allocated for the object being created and a new reference to it is returned.

One question is how we know when to release the memory when we are done with the object. In Java, this is done via the _garbage collector_ (GC).

The JVM actually keeps track of which objects the user currently has a reference to. When an object is no longer reachable, the JVM will reclaim its memory. An object is not reachable when both of the following are true:
- the user does not have any reference to the object (e.g. no locals in any method that is executing).
- there is no reachable object which contains a reference to the object.

Usually, the JVM will only run the GC if memory starts to run low. Since GC can be rather slow and may require pausing the user's program, numerous schemes and heuristics have been proposed for improving GC performance. In Java, you may find that if your program creates and drops lots of object, the GC might make up a large portion of run time.

In C++, none of this stuff exists...