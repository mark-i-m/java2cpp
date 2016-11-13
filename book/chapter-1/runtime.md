# Runtimes

Every language has a _runtime environment_ of some sort (usually just called the
"runtime"). In Java, this is the JVM, which executes Java bytecode. Java uses
the JVM to achieve platform independence. That is, you can run the same Java
bytecode on any platform without modification, as long as you have the
appropriate JVM for that platform.

C++, however, does not use a JVM. Instead, the compiler will output machine
code directly in a special format which is platform and architecture dependant.
So if you take a compiled binary from one machine to another, it may or may not
run.

The C++ runtime is tightly coupled with the standard libraries and C runtime.
Dynamically loaded libraries are extremely common, and definitions in
`libstdc++` are often used. This helps reduce the size of compiled programs by
loading dependencies dynamically at runtime, with some OS support.
