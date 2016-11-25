# Subtyping

As in Java, C++ allows the programmer to subclass in accordance with the Liskov
substitution principle. That is, if `A` _is a_ `B`, an `A` can be used where
`B` is required.

In Java:
```java
class A extends B { ... }
```

In C++:
```cpp
class A : public B { ... };
```

So far, so good :)

In Java, you will remember that a constructor can use `super` and that you must
call the super constructor at the beginning of a type's constructor (unless
there is a default [no args] constructor of the supertype, which can be
implicitly called). The same is true in C++, but you will need to do this in the
initializer list:

```java
public A(int x) {
    super(x);
    // more stuff
}
```
```cpp
A(int x) : B(x) {
    // more stuff
}
```

An important concept in C++ is that the subclass is a superclass plus more
stuff, literally. In the memory layout of an `A`, there is a `B` and some extra
stuff which belongs only to the `A` portion of the object.  Constructing the
`B` in the initializer list constructs the `B` portion of the object. The
superclass of an object is *always* constructed before this class. Likewise,
the destructors run in the reverse order, with the highest superclass being
destroyed last.

Now, you may be wondering what the `public` notation means in the line
```cpp
class A : public B { ... };
```

Can you do this?
```cpp
class A : private B { ... };
```
or
```cpp
class A : protected B { ... };
```

Indeed, you can!

This access modifier relates to who knows `A` is a subclass of `B`. If you use
`public`, everyone knows that `A` subtypes `B`. This is just like Java. If you
use `private`, nobody knows that `A` subtypes `B`. In other words, `A` contains
all of the things that `B` contains, but you cannot use an `A` where a `B` is
required.  The following would be illegal for example:

```cpp
void foo(B b) {
    // stuff
}

int main() {
    A a(3);
    foo(a);     // Type mismatch: expected B, got A
}
```

This is something very similar to composition rather than inheritance. It's
kind of like you wrote something like
```cpp
struct B { ... };

struct A {
  private: 
    B b;

  public:
    A(int x) : b(B(x)) { 
        // stuff
    }
};
```

I haven't come across this too often in the wild, but it can happen.

#### Multiple Inheritance

Java forbids you from doing something of the following:
```java
class A extends B, C, D {
    // stuff
}
```

This behavior is called *multiple inheritance*. Put simply, a class cannot have
more than direct parent class in Java. If you like graphs: Java requires all
inheritance heirarchies to be *trees*.

In C++, inheritance heirarchies can be *DAGs*. Yes, C++ allows multiple
inheritance:
```cpp
class A : public B, public C, public D {
    // stuff
};
```

You can even mix and match:
```cpp
class A : public B, private C, protected D {
  public:
    A(int x, char y) : B(x), C(y), D(34) { ... }
};
```

Naturally, as you would expect, you need to initialize all three parent classes
in the initializer list of the constructor.

Here is a better example:
```cpp
struct Person {
    Person(char *name) { ... }

    void speak() {
        std::cout << "Hi, there!" << std::endl;
    }
    void eat(Food f) {
        f.consume();
    }
};

struct Employee : public Person {
    Person employer;

    Employee(char *name, Person employer) :
      Person(name),
      employer(employer) { }

    // We can override the `speak` method
    void speak() {
        std::cout << "Yes, master!?" << std::endl;
    }
};

struct GradStudent : public Person, public Employee {
    GradStudent(char *name, Person advisor) :
      Person(name),
      Employee(name, advisor) { }

    void eat(Food f) {
        // no implementation; grad students don't eat
    }
};
```

Hmm... ok, maybe this wasn't a simple example.

1. `GradStudent` inherits from both `Person` and `Employee`.
2. `Employee` also inherits from `Person`.
3. So `GradStudent` inherits from `Person` *twice*. This means there are two
   `Person`s in a single `GradStudent` AND each `Person` is initialized!

So what's the problem? Why doesn't Java allow this? It seems benign enough.

Now, imagine this piece of code:
```cpp
int main() {
    Person prof("bob");
    GradStudent g("mark", prof);

    g.speak();
}
```

Ok, compiling:
```
$ g++ -o grad test.cc

test.cc:45:7: error: request for member ‘speak’ is ambiguous
     g.speak();
       ^
test.cc:10:10: note: candidates are: void Person::speak()
     void speak() {
          ^
test.cc:26:10: note:                 void Employee::speak()
     void speak() {
          ^
test.cc:10:10: note:                 void Person::speak()
     void speak() {
          ^
```

Uh, wat?

Think about it. `GradStudent` inherits from both `Person` and `Employee`! It
has two `speak` methods! The compiler is complaining that it doesn't know which
to call, so we have to disambiguate...

```cpp
int main() {
    Person prof("bob");
    GradStudent g("mark", prof);

    g.Person::speak();
}
```

Ok, compiling:
```
$ g++ -o grad test.cc

test.cc:45:15: error: ‘Person’ is an ambiguous base of ‘GradStudent’
     g.Person::speak();
               ^
```

Arrgg :[ Now what?!

I lied... `GradStudent` has **3** `speak` methods, two of which are
`Person::speak`, since it gets two `Person` portions. Unfortunately, there is
no way to disambiguate this as is...

Let's step back for a second.

The problem is primarily that our class heirarchy looks like this:

```
                  Person
                   / \   
                  /   \  
                 /     \
                /    Employee
                \      /
                 \    /
                  \  /   
                   \/
                GradStudent
```

Conceptually, should `GradStudent` have two `Person`s or one? After all,
`GradStudent` and `Employee` might be keeping some of their state in `Person`,
which might need to be kept distinct.  Thus, we would want to have each
`GradStudent` have two `Person`s. But this leads to the problem we have above.

This is a notorious problem called *the diamond problem*. It is the reason Java
does not allow multiple inheritance.

C++ takes a different approach. It defaults to having two `Person`s, but you
can tell C++ to create only one:

```cpp
struct Employee : public virtual Person {
```
```cpp
struct GradStudent : public virtual Person, public Employee {
```

By telling `Employee` and `GradStudent` to both inherit *virtually* from `Person`, we
force them to share a single `Person` portion, which is what we want.

Now, our program will compile and output `Hi, there!`.
