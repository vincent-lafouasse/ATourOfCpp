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

A declaration introduces its name into a scope:
- _Local scope_: A name declared in a function (§1.3) or lambda (§6.3.2) is called a _local name_. Its scope extends from its point of declaration to the end of the block in which its declaration occurs. A _block_ is delimited by a `{` `}` pair. Function argument names are considered local names.
- _Class scope_: A name is called a _member name_ (or a _class member name_) if it is defined in a class (§2.2, §2.3, Chapter 4), outside any function (§1.3), lambda (§6.3.2), or `enum class` (§2.5). Its scope extends from the opening `{` of its enclosing declaration to the end of that declaration.
- _Namespace scope_: A name is called a _namespace member name_ if it is defined in a namsepace (§3.4), outside any function, lambda (§6.3.2), class (§2.2, §2.3, Chapter 4), or `enum class` (§2.5). Its scope extends from the point of declaration to the end of its namespace.

A name not declared inside any other construct is called a _global name_ and is said to be in the _global namespace_.

In addition, we can have objects without names, such as temporaries and objects created using `new` (§4.2.2). For example:

```cpp
vector<int> vec;  // vec is global (a global vector of integers)

struct Record {
    string name;  // name is a member of `Record` (a string member)
    // ...
};

void fct(int arg) { // fct is global, arg is local
    string motto {"Who dares win"}; // motto is local
    auto p = new Record{"Hume"};    // p points to an unnamed `Record`
}
```

An object must be constructed (initialized) before it is used and will be destroyed at the end of its scope. For a namespace object the point of destruction is the end of the program. For a member, the point of destruction is determined by the point of destruction of the object of which it is a member. An object created by `new` "lives" until destroyed by `delete` (§4.2.2).

## 1.6 Constants

C++ supports two notions of immutability:
- `const`: meaning roughly "I promise not to change this value.". This is used primarily to specify interfaces so that data can be passed to functions using pointers and references without fear of it being modified. The compiler enforces the promise made by `const`. The value of a `const` can be calculated at runtime.
- `constexpr`: meaning roughly "to be evaluated at compile time". Tis is used primarily to specify constants, to allow placement of data in read-only memory (where it is unlikely to be corrupted), and for performance. The value of a `constexpr` must be calculated by the compiler.

For example:

```cpp
constexpr int dmv = 17;        // dmv is named constant
int var = 17;                  // var is not a constant
const double sqv = sqrt(var);  // sqv is a named constant, possibly computed at runtime

double sum(const vector<double>&); // sum will not modify its argument (§1.7)

vector<double> v {1.2, 3.4, 4.5};  // v is not a constant
const double s1 = sum(v);          // OK: sum(v) is evaluated at runtime
constexpr double v2 = sum(v);      // error: sum(v) is not s constant expression
```

For a function to be usable in a _constant expression_, that is, in an expression that will be evaluated by the compiler, it must be defined `constexpr`, For example:

```cpp
constexpr double square(double x) { return x * x; }

constexpr double max1 = 1.4 * square(17);  // OK: 1.4 * square(17) is a constant expression
constexpr double max2 = 1.4 * square(var); // error: var is not a constant expression
const double max3 = 1.4 * square(var);     // OK: may be evaluated at runtime
```

A `constexpr` function can be used for non-constant arguments, but when that is done the result is not a constant expression. We allow a `constexpr` functin to be called with non-constant-expression arguments in contexts that do not require constant expressions. That way, we don't have to define essentially the same function twice: once for constant expressions and once for variables.

To be `constexpr`, a function must be rather simple and cannot have side effects and can only use informatin passed to it as arguments. In particular, it cannot modify non-local variables, but it can have loops and use its own local variables. For example:

```cpp
constexpr double nth(double x, int n) {  // assume n >= 0
    double res = 1;
    int i = 0;
    while (i < n) {
        res *= x;
        ++i;
    }
    return res;
}
```

In a few places, constant expressions are required by language rules (e.g., array bounds (§1.7), case labels (§1.8), template value arguments (§6.2), and constants declared using `constexpr`). In other cases, compile-time evaluation is important for performance. Independently of performance issues, the notion of immutability (an object with an unchangeable state) is an important design concern.

## 1.7 Pointers, Arrays and References

The most fundamental collection of data is contiguously allocated sequence of elements of the same type, called an _array_. This is basically what the hardware offers. An array of elements of type `char` can be declaired like this:

```cpp
char v[6];   // array of 6 characters
```

Similarly, a pointer can be declared like this:

```cpp
char* p;     // pointer to character
```

In declarations, `[ ]` means "array of" and `*` means "pointer to". All arrays have `0` as their lower bound, so `v` has six elements, `v[0]` to `v[5]`. The size of an array must be a constant expression (§1.6). A pointer variable can hold the address of an object of the appropriate type:

```cpp
char* p = &v[3];     // `p` points to `v`'s fourth element
char x = *p;         //  `*p` is the object that `p` points to
```

In an expression, prefix unary `*` means "contents of" and prefix unary `&` means "address of". We can represent the result of the initialized definition graphically:

__POINTER TO v[3] GRAPHICS__

Consider copyinh ten elements from one array to another:

```cpp
void copy_fct() {
    int v1[10] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
    int v2[10];       // to become a copy of `v1`

    for (auto i = 0; i != 10; ++i) // copy elements
        v2[i] = v1[i];
    // ...
}
```

This `for`-statement can be read as "set `i` to zero; while `i` is not `10`, copy the `i`th element and increment `i`". When applied to an integer or floating-point variable, the increment operator `++`, simply adds `1`. C++ also offrs a simpler `for`-statement, called a range-`for`-statement, for loops that traverse a sequence in the simplest way:

```cpp
void print() {
    int v[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};

    for (auto x: v) // for each `x` in `v`
        cout << x << '\n';

    for (auto x: {10, 21, 32, 43, 54, 65})
        cout << x << '\n';
}
```

The first range-`for`-statement can be read as "for every element of `v`, from the first to the last, place a copy in `x` and print it". Note that we don't have to specify an array bound when we initialie it with a list. The range-`for`-statement can be used for any sequence of elements (§12.1).

If we didn't want to copy the values from `v` into the variable `x`, but rather just have `x` refer to an element, we could write:

```cpp
void increment() {
    int v[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};

    for (auto& x: v) // add 1 to each `x` in `v`
        ++x;
    // ...
}
```

In a declaration, the unary suffix `&` means "reference to". A reference is similar to a pointer, except that you don't need to use a prefix `*` to access the value reffered to by the reference. Also a reference cannot be made to refer to a different objet after its initialization.

References are particularly useful for specifying function arguments. For example:

```cpp
void sort(vector<double>&);
```

By using a reference, we ensure that for a call `sort(my_vec)`, we do not copy `my_vec` and that it really is `my_vec` that is sorted and not a copy of it.

When we don't want to modify an argument but still don't want the cost of copying, we can use a `const` reference (§1.6). For example:

```cpp
double sum(const vector<double>&);
```

Functions taking `const` references are very common.

When used in declarations, operators (such as `&`, `*`, and `[]`) are called _declarator operators_:

```cpp
T a[n];   // `T[n]`: `a` is an array of `n` `T`s
T* p;     // `T*`: `p` is a pointer to `T`
T& r;     // `T&`: `r` is a ref to `T`
T f(A);   // `T(A)`: `f` is a function taking an argument of type `A1 and returning a result of type `T`
```

### 1.7.1 The Null Pointer

We try to ensure that a pointer always points to an object so that dereferencing it is valid. When we don't have an object to point to or if we need to represent the notion of "no object available" (e.g., for an end of a list), we give the pointer the value `nullptr` ("the null pointer"). There is only one `nullptr` share by all pointer types:

```cpp
double* pdf = nullptr;
Link<Record>* lst = nullptr;  // pointer to a `Link` to a `Record`
int x = nullptr;              // error: `nullptr` is a pointer not an integer
```

It is often wise to check that a pointer argument actually points to something:

```cpp
// count the number of occurences of `x` in `p[]`
// p is assumed to point to a zero-terminated array of char (or to nothing)
int count_x(const char* p, char x) {
    if (p == nullptr)
        return 0;

    int count = 0;
    for (; *p != 0; ++p) {
        if (*p == x)
            ++count;
    }
    return count;
}
```

Note how we can advance a pointer to point to the next element of an array using `++` and that we can leave out the initializer in a `for`-statement if we don't need it.

The definition of `count_x()` assumes that the `char*` is a _C-style string_, that is, that the pointer points to a zero-terminated array of `char`. The characters in a string literal are immutable, so to handle `count_x("Hello!")`, I declared `count_x()` a `const char*` argument.

In older code, `0` or `NULL` is typically used instead of `nullptr`. However, using `nullptr` eliminates confusion between integers (such as `0` or `NULL`) and pointers (such as `nullptr).

In the `count_x()` example, we are not using the initializer part of the `for`-statement, so we can use the simpler `while`-statement:

```cpp
// count the number of occurences of `x` in `p[]`
// p is assumed to point to a zero-terminated array of char (or to nothing)
int count_x(const char* p, char x) {
    if (p == nullptr)
        return 0;

    int count = 0;
    while (*p) {
        if (*p == x)
            ++count;
        ++p;
    }
    return count;
}
```

The `while`-statement executes until its condition becomes `false`.

A test of a numeric value (e.g., `while (*p)` in `count_x()` is equivalent to comparing the value to `0` (e.g., `while (*p != 0)`). A test of a pointer value (e.g., `if (p)`) is equivalent to comparing the value to `nullptr` (e.g., `if (p != nullptr)`).

There is no "null reference". A reference must refer to a valid object (and implementations assume that it does). There are obscure and clever ways to violate that rule; don't do that.

## 1.8 Tests

C++ provides a conventional set of statements for expressing selection and looping, such as `if`-statements, `switch`-statements, `while`-loops, and `for`-loops. For example, here is a simple function that prompts the user and returns a Boolean indicating the response:

```cpp
bool accept() {
    cout << "Do you want to proceed (y/n)?\n";
    char answer = 0;
    cin >> answer;


    if (answer == 'y')
        return true;
    return false;
}
```

To match the `<<` output operator ("put to"), the `>>` operator ("get from") is used for input; `cin` is the standard input stream (Chapter 10). The type of the right-hand operand of `>>` determinies what input is accepted, and its right-hand operand is the target of the input operation. The `\n` character at the end of the output string represents a nweline (§1.2.1).

Note that the definition of `answer` appears where it is needed (and not before that). A declaration can appear anywhere a statement can.

The example could be improved by taking a `n` (for "no") anwser into account:

```cpp
bool accept2() {
    cout << "Do you want to proceed (y/n)?\n";
    char answer = 0;
    cin >> answer;

    switch (answer) {
        case 'y':
            return true;
        case 'n':
            return false;
        default:
            cout << "I'll take that for a no.\n";
            return false;
    }
}
```

A `switch` statement tests a value against a set of constants. Those constants, called `case`-labels, must be distinct, and if the value tested does not match any of them, the `default` is chosen. If the value doesn't match any `case`-label and no `default` is provided, no action is taken.

We don't have to exit a `case` by returning from the function that contains its `switch`-statement. Often we just want to continue execution with the statement following the `switch`-statement. We ca do that using a `break` statement. As an example, consider an overly clever, yet primitive, parser for a trivial command video game:

```cpp
void action() {
    while (true) {
        cout << "enter action:\n";
        string act;
        cin >> act;
        Point delta {0, 0};

        for (char ch: act) {
            switch (ch) {
                case 'u': // up
                case 'n': // north
                    ++delta.y;
                    break;
                case 'r': // right
                case 'e': // east
                    ++delta.x;
                    break;
                // more actions
                default:
                    cout << "I freeze!\n";
            }
            move(current + delta * scale);
            update_display();
        }
    }
}
```

Like a `for`-statement, an `if`-statement can introduce a variable and test it. For example:

```cpp
void do_something(vector<int>& v) {
    if (auto n = v.size(); n != 0) {
        // ... we get here if `n` != `0` ...
    }
    // ...
}
```

Here, the integer `n` is defined for use within the `if`-statement, initialized with `v.size()`, and immediately tested by the `n != 0` condition after the semicolon. A name declared in a condition is in scope on both branches of the `if`-statement.

As with the `for`-statement, the purpose of declaring a name in the condition of an `if`-statement is to keep the scope of the variable limited to improve readability and mimnimize errors.

The most common case is testing a variable against `0` (or the `nullptr`). To do that, simply leave out the explicit mention of the condition. For example:

```cpp
void do_something(vector<int>& v) {
    if (auto n = v.size()) {
        // ... we get here if `n` != `0` ...
    }
    // ...
}
```

Prefer to use this terser and simpler form when you can.

## 1.9 Mapping to Hardware

C++ offers a direct mapping to hardware. When you use one of the fundamental operations, the implementation is what the hardware offers, typically a single machine operation. For example, adding two `int`s, `x + y` executes an integer add machine instruction.

A C++ implementation sees a machine's memory as a sequence of memory locations into which it can place (typed) objects and address them using pointers:

__POINTER GRAPHICS__

A pointer is represented in memory as a machine address, so the numeric value of `p` in this figure would be `3`. If this looks much like an array (§1.7), that's because an array is C++'s basic abstraction of "a contiguous sequence of objects in memory".

The simple mapping of fundamental language constructs to hardware is crucial for the raw low-level performance for which C and C++ have been famous for decades. The basic machine model of C and C++ is based on computer hardware, rather than some form of mathematics.

### 1.9.1 Assignment

An assignment of a built-in type is a simple machine copy operation. Consider:

```cpp
int x = 2;
int y = 3;
x = y;    // `x` becomes 3
// Note: `x == y`
```

This is obvious. We can graphically represent that like this:

__GRAPHICS__

Note that the two objects are independent. We can change the value of `y` without affecting the value of `x`. For example `x = 99` will not change the value of `y`. Unlike Java, C#, and other languages, but like C, that is true for all types, not just `int`s.

If we want different objects to refer to the same (shared) value, we must say so. We could use pointers:

```cpp
int x = 2;
int y = 3;
int* p = &x;
int* q = &y;  // now `p != q` and `*p != *q`
p = q;        // `p` becomes `&y`; now `p == q` and `*p == *q`
```

We can represent that graphically like this:

__GRAPHICS__

I arbitrarily chose `88` and `92` as the addresses of the `int`s. Again, we can see that the assigned-to object gets the value from the assigned object, yielding two independent objects (here, pointers), with the same value. That is, `p = q` gives `p == q`. After `p = q`, both pointers point to `y`.

A reference and a pointer both refer/point to an object and both are represented in memory as a machine address. However, the language rules for using them differ. Assignment to a reference does not change what the references refers to but assignes to the referenced object:

```cpp
int x = 2;
int y = 3;
int& r = x;    // `r` refers to `x`
int& r2 = y;   // now `r2` refers to `y`
r = r2;        // read through `r2`, write through `r`: `x` becomes `3`
```

We can represent that graphically like this:

__GRAPHICS__

To access the value pointed to by a pointer, you use `*`; that is automatically (implicitely) done for a reference.

After `x = y`, we have `x == y` for every built-in type and well-designed user-defined type (Chapter 2) that offers `=` (assignment) and `==` (equality comparison).

### 1.9.2 Initialization


