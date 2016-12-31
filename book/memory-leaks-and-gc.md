# Memory management

Memory management in C++ is very different from Java. In Java, you really don't need to think about memory safety at all, but as you have probably notice, this is not true of C++.

This chapter deals heavily with how memory is allocated and deallocated, and how object's memory is manipulated in C++.

Simply put, every computer has some amount of memory made available to it by the operating system. In particular, the OS provides the abstraction that the whole address space is at the disposal of the program. Some of these addresses, the OS may reserve for its own purposes, but the bulk of the space is available to the program.

The question now is how to use that space. The next chapter discusses this further.


