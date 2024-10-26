# 1 The Basics

## 1.1 Introduction

This chapter informally presents the notation of C++, C++'s model of memory and computation, and the basic mechanisms for organizing code into a program. These are the language facilities supporting the styles most often een in C and sometimes called _procedural programming_.

## 1.2 Programs

C++ is a compiled language. For a program to run, its source text has to be processed by a compiler, producing object files, which are combined by a linker yielding an executable program. A C++ program typically consists of many source code files (usually simply called _source files_).

__COMPILATION PIPELINE FIGURE__

An executable program is created for a specific hardware/system combination; it is not portable, say, from a Mac to a Windows PC. When we talk about the portability of C++ programs, we usually mean portability of source code; that is, the source code can be successfully compiled and run on a variety of systems.

The ISO C++ standard defines two kind of entities:
- _Core language features_, such as built-in types (e.g., `char` and `int`) and loops (e.g., `for`-statements and `while`-statements)
- _Standard-library components_, such as containers (e.g., `vector` and `map`) and I/O operations (e.g., `<<` and `getline()`)

The standard-library components are perfectly ordinary C++ code provided by every C++ implementation. That is, the C++ standard library can be implemented in C++ itself and is (with very minor uses of machine code for things such as thread context switching). This implies that C++ is sufficiently expressive and efficient for the most demanding systems programming tasks.

C++ is a statically typed language. That is, the type of every entity (e.g., object, value, name, and expression) must be known to the compiler at its point of use. The type of an object determines the set of operations applicable to it.

### 1.2.1 Hello, World!

The minimal C++ program is
```cpp
int main() {} // the minimal C++ program
```
This defines a function called `main`, which takes no arguments and does nothing.

Curly braces, `{ }`, express grouping in C++, Here, they indicate the start and end of the function body. The double slash, `//`, begins a comment that extends to the end of the line. A comment is for the human reader; the compiler ignores comments.

Every C++ must have exactly one global function named `main()`. The program starts by executing that function. The `int` integer value returned by `main()`, if any, is the program's return value to "the system". If no value is returned, the system will receive a value indicating successful completion. A nonzero value from `main()` indicates failure. Not every operating system and execution environment make use of that return value: Linux/Unix-based environments do, but Windows-based environments rarely do.

Typically, a program produces some output. Here is a program that writes `Hello, World!`:
```cpp
#include <iostream>

int main() {
    std::cout << "hello, World!\n";
}
```

The line `#include <iostream>` instructs the compiler to _include_ the declarations of the standard stream I/O facilities as found in `iostream`. Without these declarations, the expression `std::cout << "Hello, World!\n"` would make no sense. The operator `<<` ("put to") writes its second argument onto its first. In this case, the string literal `"Hello, World!\n"` is written onto the standard output stream `std:;cout`. A string literal is a sequence of characters surrounded by double quotes. In a string literal, the backslash character `\` followed by another character denotes a single "special character". In this case, `\n` is the newline character, so tht the characters written are `Hello, World!` followed by a newline.

The `std::` specifies that the name `cout` is to be found in the standard-library namespace (§3.4). I usually leave out the `std::` when discussing standard features; §3.4 shows how to make names from a namespace visible without explicit qualification.

Essentially all executable code is placed in functions and called directly or indirectly from `main()`. For example:

```cpp
// include ("import") the declarations for the I/O stream library
#include <iostream>

using namespace std;        // make names from `std` visible without std:: (§3.4)

double square(double x) {   // square a double precision floating-point number
    return x ∗ x;
}

void print_square(double x) {
    cout << "the square of " << x << " is " << square(x) << "\n";
}
int main() {
    print_square(1.234);    // print: `the square of 1.234 is 1.52276`
}
```

A "return type" `void` indicates that a function does not return a value.

## 1.3 Functions

The main way of getting something done in a C++ program is to call a function to do it. Defining a function is the way you specify how an operation is to be done. A function cannot be called unless it has been previously declared.

A function declaration gives the name of the function, the type of the value returned (if any), and the number and types of the arguments that must be supplied in a call. For example:

```cpp
Elem* next_elem();   // no argument; return a pointer to `Elem` (an `Elem*`)
void exit(int);      // int argument, return nothing
double sqrt(double); // double argument; return a double
```

In a function declaration, the return type comes before the name of the function and the argument types come after the name eclosed in parentheses.

The semantics of argument passing are identical to the semantics of initialization (§3.6.1). That is, argument types are checked and implicit argument type conversion takes places when necessary (§1.4). For example:

```cpp
double s2 = sqrt(2);       // call `sqrt()` with the argument `double{2}`
double s3 = sqrt("three"); // error: `sqrt()` requires an argument of type `double`
```

The value of such compile-time checking and type conversion should not be underestimated.

A function declaration may contain argument names. This can be a help to a reader of a program, but unless the declaration is also a function definition, the compiler simply ignores such names. For example:

```cpp
double sqrt(double d); // return the square root of d
double square(double); // return the square of the argument
```

The type of a function consists of its return type and the sequence of its argument types. For example:

```cpp
// type: `double(const vector<double>&, int)`
double get(const vector<double>& vec, int index);
```

A function can be a member of a class (§2.3, §4.2.1). For such a _member function_, the name of its class is also part of the function type. For example:

```cpp
char& String::operator[](int index); // type: `char& String::(int)`
```

We want our code to be comprehensible, because that is the first step on the way to maintainability. The first step to comprehensibility is to break computational tasks into meaningful chunks (represented as functions and classes) and name those. Such functions then provide the basic vocabulary of computation, just as the types (built-in and user-defined) provide the basic vocabulary of data. The C++ standard algorithms (e.g., `find`, `sort`, and `iota`) provide a good start (Chapter 12). Next we can compose functions representing common or specialized tasks into larger computations.

The number of errors in code correlates strongly with the amount of code and the complexity of the code. Both problems can be addressed by using more and shorter functions. Using a function to do a specific task often saves us from writing a specific piece of code in the middle of other code; making it a function foces us to name the activity and document its dependdencies.

If two functions are defined with the same name, but with different argument types, the compiler will choose the most appropriate function to invoke for each call. For example:

```cpp
void print(int);    // takes an integer argument
void print(double); // takes a floating-point argument
void print(string); // takes a string argument

void user() {
    print(42);          // calls `print(int)`
    print(9.65);        // calls `print(double)`
    print("Barcelona"); // calls `print(string)`
}
```

If two alternative functions could be called, but neither is better than the other, the call is deemed ambiguous and the compiler gives an error. For example:

```cpp
void print(int, double);
void print(double, int);

void user2() {
    print(0, 0); // error: ambiguous
}
```

Defining multiples functions with the same name is known as _function overloading_ and is one of the essential parts of generic programmint (§7.2). When a function is overloaded, each function of the same name should implement the same semantics. The `print()` functions are an example of this; each `print()` prints its argument.

## 1.4 Types, Variables and Arithmetic

Every name and every expression has a type that determines the operations that may be performed on it. For example, the declaration

```cpp
int inch;
```

specifies that `inch` is of type `int`; that is, `inch` is an integer variable.

A _declaration_ is a statement that introduces an entity into the program. It specifies a type for the entity:
- A _type_ defines a set of possible values and a set of operations (for an object).
- An _object_ is some memory that holds a value of some type.
- A _value_ is a set of bits interpreted according to a type
- A _variable_ is a named object.

C++ offers a small zoo of fundamental types, but since I'm not a zoologist, I will not list them all. You can find them all in reference sources, such as [Stroustrup, 2013], or the [Cppreference] on the web. Examples are:

```cpp
bool x;       // Boolean, possible values are true and false
char x;       // character, for example, 'a', 'z', and '9'
int x;        // integer, for example, -273, 42, and 1066
double x;     // double-precision floating-point number, for example, -273.15, 3.14, and 6.626e-34
unsigned x;   // non-negative integer, for example, 0, 1, and 999 (use for bitwise logical operations)
```

Each fundamental type corresponds directly to hardware facilities and has a fixed size that determines the range of values that can be stored in it:

__SIZEOF VARIABLES GRAPHICS__

A `char` variable is of the natural size to hold a character on a given machine (typically an 8-bit byte), and the sizes of other types are multiples of the size of a `char`. The size of a type is implementation-defined (i.e., it can vary among different machines) and can be obtained by the `sizeof` operator; for example `sizeof(char)` equals `1` and `sizeof(int)` is often `4`.

Number can floating-point or integers.
- Floating-point numbers are recognized by a decimal point (e.g., `3.14`) or by an exponent (e.g., `3e-2`).
- Integer literals are by default decimal (e.g., `42` means forty-two). An `0b` prefix indicates a binary (base 2) integer literal (e.g., `0b10101010`). A `0x` prefix indicates a hexadecimal (base 16) integer literal (e.g., `0xBAD1234`). A `0` prefix indicates an octal (base 8) integer literal (e.g., `0334`).

To make long literals more readable for humans, we can use a single quote (`'`) as a digit separator. For example, `π` is about `3.14159'26535'89793'23846'26433'83279'50288` or if you prefer hexadecimal `0x3.243F'6A88'85A3'08D3`

### 1.4.1 Arithmetic

The arithmetic operators can be used for appropriate combinations of the fundamental types:

```c
x + y    // plus
+x       // unary plus
x − y    // minus
−x       // unary minus
x ∗ y    // multiply
x / y    // divide
x % y    // remainder (modulus) for integers
```

So can the comparison operators:

```c
x == y   //equal
x != y   // not equal
x < y    // less than
x > y    // greater than
x <= y   // less than or equal x>=y // greater than or equal
```

Furthermore, logical operators are provided:

```cpp
x & y    // bitwise and
x | y    // bitwise or
x ˆ y    // bitwise exclusive or
~x       // bitwise complement
x && y   // logical and
x || y   // logical or
!x       // logical not (negation)
```

A bitwise logical operator yields a result of the operand type for which the operation has been performed on each bit. The logical operators `&&` and `||` simply return `true` or `false` depending on the values of their operands.

In assignments and in arithmetic operations, C++ performs all meaningful conversions between the basic types so that they can be mixed freely:

```cpp
void some_function() { // function that doesn't return a value
    double d = 2.2;    // initialize floating-point number
    int i = 7;         // initialize integer
    d = d + 9;         // assign sum to d
    i = d * i;         // assign product to i; beware: truncating the double d*i to an int
}
```

The conversions used in expressions are called _the usual arithmetic conversion_ and aim to ensure that expressions are computed at the highest precision of its operands. For example, an addition of a `double` and an `int` is calculated using double-precision floating-point arithmetic.

Note that `=` is the assigment operator and `==` tests equality.

In addition to the conventional arithmetic and logical operators, C++ offers more specific operations for modifying a variable:

```cpp
x += y   //x=x+y
++x      // increment: x = x+1
x −= y   //x=x-y
−−x      // decrement: x = x-1
x ∗= y   // scaling: x = x*y
x /= y   // scaling: x = x/y
x %= y   //x=x%y
```

These operators are concise, convenient, and very frequently used.

The order of evaluation of expressions is left to right, except for assignments, which are right-to-left. The order of evaluation of function arguments is unfortunately unspecified.

### 1.4.2 Initialization

Before an object can be used, it must be given a value. C++ offers a variety of notations for expressing initialization, such as the `=` used above, and a universal form based on curly-brace-delimited initializer lists:

```cpp
double d1 = 2.3;   // initialize d1 to 2.3
double d2 {2.3};   // initialize d2 to 2.3
double d2 = {2.3}; // initialize d3 to 2.3 (the `=` is optional with `{...}`)

complex<double> z = 1;  //  a complex number with double-precision floating-point scalars
complex<double> z2 {d1, d2};
complex<double> z3 = {d1, d2}; // the `=` is optional with `{...}`

vector<int> v {1, 2, 3, 4, 5}; // a vector of `int`s
```

The `=` form is traditional and dates back to C, but if in doubt, use the general `{}`-list form. If nothing else, it saves you from conversions that lose information:

```cpp
int i1 = 7.8;  // i1 becomes 7 (surprise?)
int i2 {7.8};  // error: floating-point to integer conversion
```

Unfortunately, conversions that lose information, `narrowing conversions`, such as `double` to `int` and `int` to `char`, are allowed and implicitely applied when you use `=` (but not when you use `{}`). The problem caused by implicit narrowing conversions are a price paid for C compatibility (§16.3).

A constant (§1.6) cannot be left uninitialized and a variable should only be left uninitialized in extremely rare circumstances. Don't introduce a name until you have a suitable value for it. User-defined types (such as `string`, `vector`, `Matrix`, `Motor_controller`, and `Orc_warrior`) can be defined to be implicitely initialized (§4.2.1).

When defining a variable, you don't need to state its type explicitely when it can be deduced from the initializer:

```cpp
auto b = true;    // a bool
auto ch = 'x';    // a char
auto i = 123;     // an int
auto d = 1.2;     // a double
auto z = sqrt(y); // z has the type of whatever sqrt(y) returns
auto bb {true};   // bb is abool
```

With `auto`, we tend to use `=` because there is no potentially troublesome type conversion involved, but if yu prefer to use `{}` initialization consistently, you can do that instead.

We use `auto` where we don't have a specific reason to mention the type explicitely.
"Specific reasons" include:
- The definition is in a larger scope where we want to make the type clearly visible to readers of our code.
- We want to be explicit about a variable's range or precision (e.g., `double` rather than `float`).

Using `auto`, we avoid redundancy and writing long type names. This is especially important in generic programming where the exact type of an object can be hard for the programmer to know and the type names can be quite long (§12.2).

## 1.5 Scope and Lifetime
