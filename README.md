# Graphics Programmer Questions, Research and Answers

## C++ Questions

1. When should you use virtual destructors?
(Examples are taken from Scott Meyers' Effective C++ book with my take on the answer.)
We have a base class with a few derived classes.

```
class TimeKeeper {
public:
TimeKeeper();
~TimeKeeper();
...
};
class AtomicClock: public TimeKeeper { ... };
class WaterClock: public TimeKeeper { ... };
class WristWatch: public TimeKeeper { ... };
```

We want to use the class in a polymorphic way like:

```
TimeKeeper *ptk = getTimeKeeper();
...
delete ptk;
```

The problem with the above code is that we are deleting a pointer to a base class without virtual destructor. This is undefined behaviour.
As a result, only the base class' data will be cleaned and the derived class members' destructors won't be called.
To fix this, mark the destructor of the base class as `virtual`. That way C++ will do magic to call the derived class' destructor too.
Luckily, most of the time when we have base classes, we have virtual functions or pure virtual functions. Most compilers I used trigger an error when you have a virtual method in a class, but non-virtual destructor.

2. What is the difference between allocating memory on the heap versus the stack?

In C++ we have two memory regions at which we can store data - the heap and the stack. All objects instantiated with `new` keyword are put on the heap. And the other, local, data is pushed on a stack.
A stack is associated with a scope, usually curly braces. The lifecycle of a local variable starts at it's initialisation and ends with the end of the block. The cleanup happens automatically with the unwinding(?! popping ?) of the stack.
For heap data, however, the client (c++ programmer) has to manage the lifecycle of the data and has to free the memory region when not needed in order to avoid leaks.
It is a good practice to avoid heap allocations, but sometimes they are necessary. For example, if we have a vector with Enemy objects and we do not know how many to spawn until runtime, then we have to use the heap.
Stack allocation requires us to specify the amount of memory we are going to need at compile time, so the stack memory region can be reserved.

3. What C++11 and C++14 features are you using?

From C++ 11:
- `auto` keyword
- lambda expressions
- deleted and defaulted functions.
- nullptr keyword
- delegating constructors

From C++ 17:
- Nested namespaces

4. What are templates used for?

Templates give us a way to reuse algorithms for different data types. We can parametrize a class, method or a function with a template type and the compiler will generate copies of that template for all the different types that it has been used with in the code. Templates aid code reuse. The downside is that if the template is used in a excessive manner, then it could cause code bloat.
Templates improve on C macros and void* parameters and return types with their type-safety.
Another downside is that a template class has to be fully specified in the header file, because template expansion happens at compile time.

5. Explain the inline keyword

