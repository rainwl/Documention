# C++ wrapper

## C++

```C++
// Example.h
class Example {
public:
    void DoSomething();
};
```

```C++
// Example.cpp
#include "Example.h"
#include <iostream>

void Example::DoSomething() {
    std::cout << "Doing something in C++" << std::endl;
}

```

```C++
// example_wrapper.cpp
#include "example.h"
#include <iostream>

extern "C" {
    Example* Example_new() { return new Example(); }
    void Example_DoSomething(Example* example) { example->DoSomething(); }
    void Example_delete(Example* example) { delete example; }
}

```

## CSharp

```C#
using System;
using System.Runtime.InteropServices;

class Program {
    [DllImport("Example.dll")]
    private static extern IntPtr Example_new();

    [DllImport("Example.dll")]
    private static extern void Example_DoSomething(IntPtr example);

    [DllImport("Example.dll")]
    private static extern void Example_delete(IntPtr example);

    static void Main() {
        IntPtr example = Example_new();
        Example_DoSomething(example);
        Example_delete(example);
    }
}

```

