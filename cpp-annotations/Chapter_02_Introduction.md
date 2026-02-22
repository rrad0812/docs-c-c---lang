
# Chapter 2: Introduction

[Contents](Chapter_01_Overview.md)

This document offers an introduction to the C++ programming language. It is a guide for C/C++ programming courses, yearly presented by Frank at the University of Groningen. This document is not a complete C/C++ handbook, as much of the C-background of C++ is not covered. Other sources should be referred to for that (e.g., the on-line book suggested to me by George Danchev (danchev at spnet dot net)).

The reader should be forewarned that extensive knowledge of the C programming language is actually assumed. The C++ Annotations continue where topics of the C programming language end, such as pointers, basic flow control and the construction of functions.

Some elements of the language, like specific lexical tokens (like digraphs (e.g., <: for [, and >: for ])) are not covered by the C++ Annotations, as these tokens occur extremely seldom in C++ source code. In addition, trigraphs (using ??< for {, and ??> for }) have been removed from C++.

The working draft of the C++ standard is freely available, and can be cloned from the git-repository at <https://gitlab.com/cplusplus/draft.git>

The version number of the C++ Annotations (currently 13.04.00) is updated when the content of the document change. The first number is the major number, and is probably not going to change for some time: it indicates a major rewriting. The middle number is increased when new information is added to the document. The last number only indicates small changes; it is increased when, e.g., series of typos are corrected.

This document is published by the Center of Information Technology, University of Groningen, the Netherlands under the GNU General Public License.

The C++ Annotations were typeset using the yodl formatting system.

All correspondence concerning suggestions, additions, improvements or changes to this document should be directed to the author:

Frank B. Brokken  
University of Groningen,  
The Netherlands  
<mailto: `f.b.brokken@rug.nl`>

In this chapter an overview of C++'s defining features is presented. A few extensions to C are reviewed and the concepts of object based and object oriented programming (OOP) are briefly introduced.

## 2.1: What's new in the C++ Annotations

This section is modified when the first or second part of the version number changes (and occasionally also for the third field of the version number). At a major version upgrade the entries of the previous major version are kept, and entries referring to older releases are removed.

- Version 13.04.00: added new sections: the std::quoted manipulator (6.3.2.3), and Intermezzo: hashing and unordered containers (12.3.12). The unordered_set (12.3.13) is now covered before covering the unordered_map (12.3.14).

- Version 13.03.00: 13.03.00 updated description of lambda expression syntax; new section 22.1.9: Template parameter initialization.

- Version 13.02.00's chapter Modules was rewritten, and adds a section to chapter POLYMORPHISM about deriving a std::streambuf class that can be used by std::iostream objects (cf. section 14.8.2).

- Version 13.01.00 reorganized section 4.3 (note: changing __file_clock to file_clock); removed section 12.3 (allocators, removed in the C++20 standard); included missing examples; and repaired typos.

- Version 13.0.0 adds a new chapter (25) about C++ Modules, covers the _ name-independent declaration, upgraded the C++ standard to C++26, clarified and repaired typos in several sections.

- Version 12.5.0 adds a subsection about constructors of polymorphic classes (cf. section 14.1.1) and adds a section about reading and writing devices using the std::iostream class (replaced by section 14.8.2 in version 13.02.00).

- Version 12.4.0 adds a section about the std::byte type (cf. chapter 3), offers an alternative implementation (cf. section 11.4), and covers the definition of multi-argument index operators (chapter 11). The description of how to implement class constructors that may throw exceptions was updated (cf. section 10.12).

- Version 12.3.0 updates and reorganizes the coverage of the generic algorithms, fixes many typos and unclarities in the Annotations' text, removed superfluous sections since C++20, and adds an overview of facilities to handle objects constructed in raw memory.

- Version 12.2.0 takes into account that std::iterator is deprecated. Section 22.14 was rewritten; section 11.1 was updated (operator[] const should return Type const & instead of Type values); added section 5.3 covering std::string_view; added section 20.15 covering synchronization of output to streams in multi-threaded programs; added sections 22.10.2.1 and 23.13.7.2 about bound-friends; 'typedef' definitions were replaced by 'using' declarations.

- Version 12.1.0 adds a description of the __file_clock::to_sys static member, repaired the descriptions of pop*() members of various abstract containers, and reorganized the description of the facilities of the filesystem::path class.

- Version 12.0.0 adds a new chapter about coroutines (chapter 24) and a new section (20.1.3) to chapter 20.

## 2.2: C++'s history

The first implementation of C++ was developed in the 1980s at the AT&T Bell Labs, where the Unix operating system was created.

C++ was originally a "pre-compiler", similar to the preprocessor of C, converting special constructions in its source code to plain C. Back then this code was compiled by a standard C compiler. The "pre-code", which was read by the C++ pre-compiler, was usually located in a file with the extension .cc, .C or .cpp. This file would then be converted to a C source file with the extension .c, which was thereupon compiled and linked.

The nomenclature of C++ source files remains: the extensions .cc and .cpp are still used. However, the preliminary work of a C++ pre-compiler is nowadays usually performed during the actual compilation process. Often compilers determine the language used in a source file from its extension. This holds true for Borland's and Microsoft's C++ compilers, which assume a C++ source for an extension .cpp. The GNU compiler g++, which is available on many Unix platforms, assumes for C++ the extension .cc.

The fact that C++ used to be compiled into C code is also visible from the fact that C++ is a superset of C: C++ offers the full C grammar and supports all C-library functions, and adds to this features of its own. This makes the transition from C to C++ quite easy. Programmers familiar with C may start "programming in C++" by using source files having extensions .cc or .cpp instead of .c, and may then comfortably slip into all the possibilities offered by C++. No abrupt change of habits is required.

### 2.2.1: History of the C++ Annotations

The original version of the C++ Annotations was written by Frank Brokken and Karel Kubat in Dutch using LaTeX. After some time, Karel rewrote the text and converted the guide to a more suitable format and (of course) to English in September 1994.

The first version of the guide appeared on the net in October 1994. By then it was converted to SGML.

Gradually new chapters were added, and the content was modified and further improved (thanks to countless readers who sent us their comments).

In major version four Frank added new chapters and converted the document from SGML to yodl.

- The C++ Annotations are freely distributable. Be sure to read the legal notes.

- Reading the annotations beyond this point implies that you are aware of these notes and that you agree with them.

If you like this document, tell your friends about it. Even better, let us know by sending email to Frank.

### 2.2.2: Compiling a C program using a C++ compiler

Prospective C++ programmers should realize that C++ is not a perfect superset of C. There are some differences you might encounter when you simply rename a file to a file having the extension .cc and run it through a C++ compiler:

- In C, `sizeof('c')` equals `sizeof(int)`, 'c' being any ASCII character. The underlying philosophy is probably that chars, when passed as arguments to functions, are passed as integers anyway. Furthermore, the C compiler handles a character constant like 'c' as an integer constant. Hence, in C, the function calls

    ```c
    putchar(10);
    ```

    and

    ```c
    putchar('\n');
    ```

  are synonymous.
  
  </br>

  By contrast, in C++, `sizeof('c')` is always 1 (but see also section 3.4.2). An int is still an int, though. As we shall see later (section 2.5.4), the two function calls

    ```cpp
    somefunc(10);
    ```

  and

    ```cpp
    somefunc('\n');
    ```

  may be handled by different functions: C++ distinguishes functions not only by their names, but also by their argument types, which are different in these two calls. The former using an int argument, the latter a char.

- C++ requires very strict prototyping of external functions. E.g., in C a prototype like

    ```c
    void func();
    ```

  means that a function func() exists, returning no value. The declaration doesn't specify which arguments (if any) are accepted by the function.

  However, in C++ the above declaration means that the function func() does not accept any arguments at all. Any arguments passed to it result in a compile-time error.

  Note that the keyword `extern` is not required when declaring functions. A function definition becomes a function declaration simply by replacing a function's body by a semicolon. The keyword `extern` is required, though, when declaring variables.

### 2.2.3: Compiling a C++ program

To compile a C++ program, a C++ compiler is required. Considering the free nature of this document, it won't come as a surprise that a free compiler is suggested here. The Free Software Foundation (FSF) provides at <http://www.gnu.org> a free C++ compiler which is, among other places, also part of the Debian ( <http://www.debian.org> ) distribution of Linux ( <http://www.linux.org> ).

Always use the latest C++ standard supported by your compiler. When the latest standard isn't used by default, but is already partially implemented it can usually be selected by specifying the appropriate flag. E.g., to use the C++26 standard specify the flag --std=c++26.

**Note**: in the C++ Annotations it is assumed that the lastest available standard is specified using the --std flag, even if no --std flag has been specified.

#### 2.2.3.1: C++ under MS-Windows

For MS-Windows Cygwin ( <http://cygwin.com> ) or MinGW ( <http://mingw-w64.org/doku.php> ) provide the foundation for installing the Windows port of the GNU g++ compiler ( see also <https://docs.microsoft.com/en-us/windows/wsl/about> ).

The GNU g++ compiler's official home page is <http://gcc.gnu.org>, also containing information about how to install the compiler in an MS-Windows system.

#### 2.2.3.2: Compiling a C++ source text

Generally the following command can be used to compile a C++ source file `source.cc':

```sh
    g++ source.cc
```

This produces a binary program (a.out or a.exe). If the default name is inappropriate, the name of the executable can be specified using the -o flag (here producing the program source):

```sh
    g++ -o source source.cc
```

If a mere compilation is required, the compiled module can be produced using the -c flag:

```sh
    g++ -c source.cc
```

This generates the file source.o, which can later on be linked to other modules.

C++ programs quickly become too complex to maintain `by hand'. With all serious programming projects program maintenance tools are used. Usually the standard make program is used to maintain C++ programs, but good alternatives exist, like the icmake or ccbuild program maintenance utilities.

It is strongly advised to start using maintenance utilities early in the study of C++.

## 2.3: C++: advantages and claims

Often it is said that programming in C++ leads to `better' programs. Some of the claimed advantages of C++ are:

- New programs would be developed in less time because old code can be reused.
- Creating and using new data types would be easier than in C.
- The memory management under C++ would be easier and more transparent.
- Programs would be less bug-prone, as C++ uses a stricter syntax and type checking.
- "Data hiding", the usage of data by one program part while other program parts cannot access the data, would be easier to implement with C++.

Which of these allegations are true? Originally, our impression was that the C++ language was somewhat overrated; the same holding true for the entire object-oriented programming (OOP) approach. The enthusiasm for the C++ language resembles the once uttered allegations about Artificial-Intelligence (AI) languages like Lisp and Prolog: these languages were supposed to solve the most difficult AI-problems "almost without effort". New languages are often oversold: in the end, each problem can be coded in any programming language (say BASIC or assembly language). The advantages and disadvantages of a given programming language aren't in "what you can do with them", but rather in "which tools the language offers to implement an efficient and understandable solution to a programming problem". Often these tools take the form of syntactic restrictions, enforcing or promoting certain constructions or simply suggesting intentions by applying or "embracing" such syntactic forms. Rather than a long list of plain assembly instructions we now use flow control statements, functions, objects or even (with C++) so-called templates to structure and organize code and to express oneself "eloquently" in the language of one's choice.

Concerning the above allegations of C++, we support the following, however.

- The development of new programs while existing code is reused can also be implemented in C by, e.g., using function libraries. Functions can be collected in a library and need not be re-invented with each new program. C++, however, offers specific syntax possibilities for code reuse, apart from function libraries (see chapters 13 and 21).
- Creating and using new data types is certainly possible in C; e.g., by using structs, typedefs etc.. From these types other types can be derived, thus leading to structs containing structs and so on. In C++ these facilities are augmented by defining data types which are completely `self supporting', taking care of, e.g., their memory management automatically (without having to resort to an independently operating memory management system as used in, e.g., Java).
- In C++ memory management can in principle be either as easy or as difficult as it is in C. Especially when dedicated C functions such as xmalloc and xrealloc are used (allocating the memory or aborting the program when the memory pool is exhausted). However, with functions like malloc it is easy to err. Frequently errors in C programs can be traced back to miscalculations when using malloc. Instead, C++ offers facilities to allocate memory in a somewhat safer way, using its operator new.
- Concerning "bug proneness" we can say that C++ indeed uses stricter type checking than C. However, most modern C compilers implement "warning levels"; it is then the programmer's choice to disregard or get rid of the warnings. In C++ many of such warnings become fatal errors (the compilation stops).
- As far as "data hiding" is concerned, C does offer some tools. E.g., where possible, local or static variables can be used and special data types such as structs can be manipulated by dedicated functions. Using such techniques, data hiding can be implemented even in C; though it must be admitted that C++ offers special syntactic constructions, making it far easier to implement "data hiding" (and more in general: "encapsulation") in C++ than in C.

C++ in particular (and OOP in general) is of course not the solution to all programming problems. However, the language does offer various new and elegant facilities which are worth investigating. At the downside, the level of grammatical complexity of C++ has increased significantly as compared to C. This may be considered a serious drawback of the language. Although we got used to this increased level of complexity over time, the transition was neither fast nor painless.

With the C++ Annotations we hope to help the reader when transiting from C to C++ by focusing on the additions of C++ as compared to C and by leaving out plain C. It is our hope that you like this document and may benefit from it.

Enjoy and good luck on your journey into C++!

## 2.4: What is Object-Oriented Programming?

Object-oriented (and object-based) programming propagates a slightly different approach to programming problems than the strategy usually used in C programs. In C programming problems are usually solved using a "procedural approach": a problem is decomposed into subproblems and this process is repeated until the subtasks can be coded. Thus a conglomerate of functions is created, communicating through arguments and variables, global or local (or static).

In contrast (or maybe better: in addition) to this, an object-based approach identifies the keywords used in a problem statement. These keywords are then depicted in a diagram where arrows are drawn between those keywords to depict an internal hierarchy. The keywords become the objects in the implementation and the hierarchy defines the relationship between these objects. The term object is used here to describe a limited, well-defined structure, containing all information about an entity: data types and functions to manipulate the data. As an example of an object oriented approach, an illustration follows:

- The employees and owner of a car dealer and auto garage company are paid as follows. First, mechanics who work in the garage are paid a certain sum each month. Second, the owner of the company receives a fixed amount each month. Third, there are car salesmen who work in the showroom and receive their salary each month plus a bonus per sold car. Finally, the company employs second-hand car purchasers who travel around; these employees receive their monthly salary, a bonus per bought car, and a restitution of their travel expenses.

When representing the above salary administration, the keywords could be mechanics, owner, salesmen and purchasers. The properties of such units are: a monthly salary, sometimes a bonus per purchase or sale, and sometimes restitution of travel expenses. When analyzing the problem in this manner we arrive at the following representation:

- The owner and the mechanics can be represented by identical types, receiving a given salary per month. The relevant information for such a type would be the monthly amount. In addition this object could contain data as the name, address and social security number.
- Car salesmen who work in the showroom can be represented as the same type as above but with some extra functionality: the number of transactions (sales) and the bonus per transaction.
- In the hierarchy of objects we would define the dependency between the first two objects by letting the car salesmen be `derived' from the owner and mechanics.
- Finally, there are the second-hand car purchasers. These share the functionality of the salesmen except for travel expenses. The additional functionality would therefore consist of the expenses made and this type would be derived from the salesmen.

The overall process in the definition of a hierarchy such as the above starts with the description of the most simple type. Traditionally (and still encountered with some popular object oriented languages) more complex types are thereupon derived from the basic type, with each derived type adding some new functionality. From these derived types, more complex types can again be derived ad infinitum, until a representation of the entire problem can be made.

Over the years this approach has become less popular in C++ as it typically results in very tight coupling among those types, which in turns reduces rather than enhances the understanding, maintainability and testability of complex programs. The term coupling refers to the degree of independence between software components: tight coupling means a strong dependency, which is frowned upon in C++. In C++ object oriented programs more and more favor small, easy to understand hierarchies, limited coupling and a developmental process where design patterns (cf. Gamma et al. (1995)) play a central role.

In C++ classes are frequently used to define the characteristics of objects. Classes contain the necessary functionality to do useful things. Classes generally do not offer all their functionality (and typically none of their data) to objects of other classes. As we will see, classes tend to hide their properties in such a way that they are not directly modifiable by the outside world. Instead, dedicated functions are used to reach or modify the properties of objects. Thus class-type objects are able to uphold their own integrity. The core concept here is encapsulation of which data hiding is just an example. These concepts are further elaborated in chapter 7.

## 2.5: Differences between C and C++

In this section some examples of C++ code are shown. Some differences between C and C++ are highlighted.

### 2.5.1: The function `main'

In C++ there are only two variants of the function main: `int main()` and `int main(int argc, char **argv)`.

Notes:

- The return type of main is int, and not void;
- The function main cannot be overloaded (for other than the abovementioned signatures);
- It is not required to use an explicit return statement at the end of main. If omitted main returns 0;
- The value of argv[argc] equals 0;
- The third `char **envp parameter` is not defined by the C++ standard and should be avoided.Instead, the global variable `extern char **environ` should be declared providing access to the program's environment variables. Its final element has the value 0;
- A C++ program ends normally when the main function returns. Using a function try block (cf. section 10.11) for main is also considered a normal end of a C++ program. When a C++ ends normally, destructors (cf. section 9.2) of globally defined objects are activated. A function like exit(3) does not normally end a C++ program and using such functions is therefore deprecated.

### 2.5.2: End-of-line comment

According to the ANSI/ISO definition, "end of line comment" is implemented in the syntax of C++. This comment starts with `//` and ends at the end-of-line marker. The standard C comment, delimited by `/*` and `*/` can still be used in C++:

```cpp
    int main()
    {
        // this is end-of-line comment
        // one comment per line

        /*
            this is standard-C comment, covering
            multiple lines
        */
    }
```

Despite the example, it is advised not to use C type comment inside the body of C++ functions. Sometimes existing code must temporarily be suppressed, e.g., for testing purposes. In those cases it's very practical to be able to use standard C comment. If such suppressed code itself contains such comment, it would result in nested comment-lines, resulting in compiler errors. Therefore, the rule of thumb is not to use C type comment inside the body of C++ functions (alternatively, #if 0 until #endif pair of preprocessor directives could of course also be used).

### 2.5.3: Strict type checking

C++ uses very strict type checking. A prototype must be known for each function before it is called, and the call must match the prototype. The program

```cpp
    int main()
    {
        printf("Hello World\n");
    }
```

often compiles under C, albeit with a warning that `printf()` is an unknown function. But C++ compilers (should) fail to produce code in such cases. The error is of course caused by the missing `#include <stdio.h>` (which in C++ is more commonly included as `#include <cstdio>` directive).

And while we're at it: as we've seen in C++ main always uses the `int` return value. Although it is possible to define `int main()` without explicitly defining a return statement, within main it is not possible to use a return statement without an explicit int-expression. For example:

```cpp
    int main()
    {
        return;     // won't compile: expects int expression, e.g.
                    // return 1;
    }
```cpp

Implicit conversions from void * to non-void pointers are not allowed. E.g., the following is not accepted in C++:

```cpp
    void *none()
    {
        return 0;
    }
    
    int main()
    {
        int *empty = none();
    }
```

### 2.5.4: Function Overloading

In C++ it is possible to define functions having identical names but performing different actions. The functions must differ in their parameter lists (and/or in their const attribute). An example is given below:

```cpp
    #include <stdio.h>

    void show(int val)
    {
        printf("Integer: %d\n", val);
    }

    void show(double val)
    {
        printf("Double: %lf\n", val);
    }

    void show(char const *val)
    {
        printf("String: %s\n", val);
    }

    int main()
    {
        show(12);
        show(3.1415);
        show("Hello World!\n");
    }
```

In the above program three functions show are defined, only differing in their parameter lists, expecting an int, double and char *, respectively. The functions have identical names. Functions having identical names but different parameter lists are called `overloaded`. The act of defining such functions is called "function overloading".

The C++ compiler implements function overloading in a rather simple way. Although the functions share their names (in this example show), the compiler (and hence the linker) use quite different names. The conversion of a name in the source file to an internally used name is called "name mangling". E.g., the C++ compiler might convert the prototype void show (int) to the internal name VshowI, while an analogous function having a char * argument might be called VshowCP. The actual names that are used internally depend on the compiler and are not relevant for the programmer, except where these names show up in e.g., a listing of the content of a library.

Some additional remarks with respect to function overloading:

- Do not use function overloading for functions doing conceptually different tasks. In the example above, the functions show are still somewhat related (they print information to the screen).

- However, it is also quite possible to define two functions lookup, one of which would find a name in a list while the other would determine the video mode. In this case the behavior of those two functions have nothing in common. It would therefore be more practical to use names which suggest their actions; say, findname and videoMode.

- C++ does not allow identically named functions to differ only in their return values, as it is always the programmer's choice to either use or ignore a function's return value. E.g., the fragment

    ```cpp
    printf("Hello World!\n");
    ```

  provides no information about the return value of the function `printf`. Two functions printf which only differ in their return types would therefore not be distinguishable to the compiler.

- In chapter 7 the notion of const member functions is introduced (cf. section 7.7). Here it is merely mentioned that classes normally have so-called member functions associated with them (see, e.g., chapter 5 for an informal introduction to the concept). Apart from overloading member functions using different parameter lists, it is then also possible to overload member functions by their const attributes. In those cases, classes may have pairs of identically named member functions, having identical parameter lists. Then, these functions are overloaded by their const attribute. In such cases only one of these functions must have the const attribute.

### 2.5.5: Default function arguments

In C++ it is possible to provide `default arguments` when defining a function. These arguments are supplied by the compiler when they are not specified by the programmer. For example:

```cpp
    #include <stdio.h>

    void showstring(char *str = "Hello World!\n");

    int main()
    {
        showstring("Here's an explicit argument.\n");

        showstring();           // in fact this says:
                                // showstring("Hello World!\n");
    }
```

The possibility to omit arguments in situations where default arguments are defined is just a nice touch: it is the compiler who supplies the lacking argument unless it is explicitly specified at the call. The code of the program will neither be shorter nor more efficient when default arguments are used.

Functions may be defined with more than one default argument:

```cpp
    void two_ints(int a = 1, int b = 4);

    int main()
    {
        two_ints();            // arguments:  1, 4
        two_ints(20);          // arguments: 20, 4
        two_ints(20, 5);       // arguments: 20, 5
    }
```

When the function two_ints is called, the compiler supplies one or two arguments whenever necessary. A statement like two_ints(,6) is, however, not allowed: when arguments are omitted they must be on the right-hand side.

Default arguments must be known at compile-time since at that moment arguments are supplied to functions. Therefore, the default arguments must be mentioned at the function's declaration, rather than at its implementation:

```cpp
    // sample header file
    void two_ints(int a = 1, int b = 4);

    // code of function in, say, two.cc
    void two_ints(int a, int b)
    {
        ...
    }
```

It is an error to supply default arguments in both function definitions and function declarations. When applicable default arguments should be provided in function declarations: when the function is used by other sources the compiler commonly reads the header file rather than the function definition itself. Consequently the compiler has no way to determine the values of default arguments if they are provided in the function definition.

### 2.5.6: NULL-pointers vs. 0-pointers and nullptr

In C++ all zero values are coded as 0. In C NULL is often used in the context of pointers. This difference is purely stylistic, though one that is widely adopted. In C++ NULL should be avoided (as it is a macro, and macros can --and therefore should-- easily be avoided in C++, see also section 8.1.4). Instead 0 can almost always be used.

Almost always, but not always. As C++ allows function overloading (cf. section 2.5.4) the programmer might be confronted with an unexpected function selection in the situation shown in section 2.5.4:

```cpp
    #include <stdio.h>

    void show(int val)
    {
        printf("Integer: %d\n", val);
    }

    void show(double val)
    {
        printf("Double: %lf\n", val);
    }

    void show(char const *val)
    {
        printf("String: %s\n", val);
    }

    int main()
    {
        show(12);
        show(3.1415);
        show("Hello World!\n");
    }
```

In this situation a programmer intending to call "show(char const *)" might call "show(0)". But this doesn't work, as "0" is interpreted as int and so show(int) is called. But calling "show(NULL)" doesn't work either, as C++ usually defines "NULL" as "0", rather than `((void *)0)`. So, "show(int)" is called once again. To solve these kinds of problems the new C++ standard introduces the keyword `nullptr` representing the 0 pointer. In the current example the programmer should call show(nullptr) to avoid the selection of the wrong function. The nullptr value can also be used to initialize pointer variables. E.g.,

```cpp
    int *ip = nullptr;      // OK
    int value = nullptr;    // error: value is no pointer
```

### 2.5.7: The `void' parameter list

In C, a function prototype with an empty parameter list, such as

```c
    void func();
```

means that the argument list of the declared function is not prototyped: for functions using this prototype the compiler does not warn against calling func with any set of arguments. In C the keyword void is used when it is the explicit intent to declare a function with no arguments at all, as in:

```c
    void func(void);
```

As C++ enforces strict type checking, in C++ an empty parameter list indicates the total absence of parameters. The keyword void is thus omitted.

### 2.5.8: The `#define __cplusplus'

Each C++ compiler which conforms to the ANSI/ISO standard defines the symbol `__cplusplus`: it is as if each source file were prefixed with the preprocessor directive #define `__cplusplus`.

We shall see examples of the usage of this symbol in the following sections.

### 2.5.9: Using standard C functions

Normal C functions, e.g., which are compiled and collected in a run-time library, can also be used in C++ programs. Such functions, however, must be declared as C functions.

As an example, the following code fragment declares a function xmalloc as a C function:

```cpp
    extern "C" void *xmalloc(int size);
```

This declaration is analogous to a declaration in C, except that the prototype is prefixed with extern "C".

A slightly different way to declare C functions is the following:

```cpp
    extern "C"
    {
        // C-declarations go in here
    }
```

It is also possible to place preprocessor directives at the location of the declarations. E.g., a C header file myheader.h which declares C functions can be included in a C++ source file as follows:

```cpp
    extern "C"
    {
        #include <myheader.h>
    }
```

Although these two approaches may be used, they are actually seldom encountered in C++ sources. A more frequently used method to declare external C functions is encountered in the next section.

### 2.5.10: Header files for both C and C++

The combination of the predefined symbol `__cplusplus` and the possibility to define `extern "C"` functions offers the ability to create header files for both C and C++. Such a header file might, e.g., declare a group of functions which are to be used in both C and C++ programs.

The setup of such a header file is as follows:

```cpp
    #ifdef __cplusplus
    extern "C"
    {
    #endif

        /* declaration of C-data and functions are inserted here. E.g., */
        void *xmalloc(int size);

    #ifdef __cplusplus
    }
    #endif
```

Using this setup, a normal C header file is enclosed by `extern "C" {` which occurs near the top of the file and by `}`, which occurs near the bottom of the file. The #ifdef directives test for the type of the compilation: C or C++. The "standard" C header files, such as `stdio.h`, are built in this manner and are therefore usable for both C and C++.

In addition C++ headers should support include guards. In C++ it is usually undesirable to include the same header file twice in the same source file. Such multiple inclusions can easily be avoided by including an #ifndef directive in the header file. For example:

```cpp
    #ifndef MYHEADER_H_
    #define MYHEADER_H_
        // declarations of the header file is inserted here,
        // using #ifdef __cplusplus etc. directives
    #endif
```

When this file is initially scanned by the preprocessor, the symbol MYHEADER_H_ is not yet defined. The #ifndef condition succeeds and all declarations are scanned. In addition, the symbol MYHEADER_H_ is defined.

When this file is scanned next while compiling the same source file, the symbol MYHEADER_H_ has been defined and consequently all information between the #ifndef and #endif directives is skipped by the compiler.

In this context the symbol name MYHEADER_H_ serves only for recognition purposes. E.g., the name of the header file can be used for this purpose, in capitals, with an underscore character instead of a dot.

Apart from all this, the custom has evolved to give C header files the extension .h, and to give C++ header files no extension. For example, the standard `iostreams cin`, `cout` and `cerr` are available after including the header file `iostream`, rather than `iostream.h`. In the Annotations this convention is used with the standard C++ header files, but not necessarily everywhere else.

There is more to be said about header files. Section 7.11 provides an in-depth discussion of the preferred organization of C++ header files. In addition, starting with the C++26 standard modules are available resulting in a somewhat more efficient way of handling declarations than offered by the traditional header files.

Currently, the C++ Annotations very briefly covers modules (cf. section 25).

### 2.5.11: Defining local variables

Although already available in the C programming language, local variables should only be defined once they're needed. Although doing so requires a little getting used to, eventually it tends to produce more readable, maintainable and often more efficient code than defining variables at the beginning of compound statements. We suggest to apply the following rules of thumb when defining local variables:

- Local variables should be created at `intuitively right' places, such as in the example below. This does not only entail the for-statement, but also all situations where a variable is only needed, say, half-way through the function.

- More in general, variables should be defined in such a way that their scope is as limited and localized as possible. When avoidable local variables are not defined at the beginning of functions but rather where they're first used.

- It is considered good practice to avoid global variables. It is fairly easy to lose track of which global variable is used for what purpose. In C++ global variables are seldom required, and by localizing variables the risk of using the same variable for multiple purposes (thereby invalidating the separate purposes of the variable), can easily be avoided.

If considered appropriate, nested blocks can be used to localize auxiliary variables. However, situations exist where local variables are considered appropriate inside nested statements. The just mentioned for statement is of course a case in point, but local variables can also be defined within the condition clauses of if-else statements, within selection clauses of switch statements and condition clauses of while statements. Variables thus defined are available to the full statement, including its nested statements. For example, consider the following `switch` statement:

```cpp
    #include <stdio.h>

    int main()
    {
        switch (int c = getchar())
        {
            case 'a':
            case 'e':
            case 'i':
            case 'o':
            case 'u':
                printf("Saw vowel %c\n", c);
            break;

            case EOF:
                printf("Saw EOF\n");
            break;

            case '0' ... '9':
                printf("Saw number character %c\n", c);
            break;

            default:
                printf("Saw other character, hex value 0x%2x\n", c);
        }
    }
```

Note the location of the definition of the character 'c': it is defined in the expression part of the `switch` statement. This implies that 'c' is available only to the `switch` statement itself, including its nested (sub)statements, but not outside the scope of the switch.

The same approach can be used with `if` and `while` statements: a variable that is defined in the condition clause of an `if` and `while` statement is available in their nested statements. There are some caveats, though:

- The variable that is defined in the condition clause must be a variable which is initialized to a numeric or logical value;
- The variable definition cannot be nested (e.g., using parentheses) within a more complex expression.

The former point of attention should come as no big surprise: in order to be able to evaluate the logical condition of an `if` or `while` statement, the value of the variable must be interpretable as either zero (false) or non-zero (true). Usually this is no problem, but in C++ objects (like objects of the type `std::string` (cf. chapter 5)) are often returned by functions. Such objects may or may not be interpretable as numeric values. If not (as is the case with `std::string` objects), then such variables can not be defined at the condition or expression clauses of condition- or repetition statements. The following example will therefore not compile:

```cpp
    if (std::string myString = getString())     // assume getString returns
    {                                           // a std::string value
        // process myString
    }
```

The above example requires additional clarification. Often a variable can profitably be given local scope, but an extra check is required immediately following its initialization. The initialization and the test cannot both be combined in one expression. Instead two nested statements are required. Consequently, the following example won't compile either:

```cpp
    if ((int c = getchar()) && strchr("aeiou", c))
        printf("Saw a vowel\n");
```

If such a situation occurs, either use two nested if statements, or localize the definition of int c using a nested compound statement:

```cpp
    if (int c = getchar())             // nested if-statements
        if (strchr("aeiou", c))
            printf("Saw a vowel\n");

    {                                  // nested compound statement
        int c = getchar();
        if (c && strchr("aeiou", c))
           printf("Saw a vowel\n");
    }
```

The C++26 standard introduced name-independent declarations. They're covered in section 20.3.1.

### 2.5.12: The keyword `typedef'

The keyword typedef is still used in C++, but is not required anymore when defining union, struct or enum definitions. This is illustrated in the following example:

```cpp
    struct SomeStruct
    {
        int     a;
        double  d;
        char    string[80];
    };
```

When a struct, union or other compound type is defined, the tag of this type can be used as type name (this is SomeStruct in the above example):

```cpp
    SomeStruct what;

    what.d = 3.1415;
```

### 2.5.13: Functions as part of a struct

In C++ we may define functions as members of structs. Here we encounter the first concrete example of an object: as previously described (see section 2.4), an object is a structure containing data while specialized functions exist to manipulate those data.

A definition of a struct Point is provided by the code fragment below. In this structure, two int data fields and one function draw are declared.

```cpp
    struct Point            // definition of a screen-dot
    {
        int x;              // coordinates
        int y;              // x/y
        void draw();        // drawing function
    };
```

A similar structure could be part of a painting program and could, e.g., represent a pixel. With respect to this struct it should be noted that:

- The function draw mentioned in the struct definition is a mere declaration. The actual code of the function defining the actions performed by the function is found elsewhere (the concept of functions inside structs is further discussed in section 3.2).
. The size of the struct Point is equal to the size of its two ints. A function declared inside the structure does not affect its size. The compiler implements this behavior by allowing the function draw to be available only in the context of a Point.

The Point structure could be used as follows:

```cpp
    Point a;                // two points on
    Point b;                // the screen

    a.x = 0;                // define first dot
    a.y = 10;               // and draw it
    a.draw();

    b = a;                  // copy a to b
    b.y = 20;               // redefine y-coord
    b.draw();               // and draw it
```

As shown in the above example a function that is part of the structure may be selected using the dot (.) (the arrow (->) operator is used when pointers to objects are available). This is therefore identical to the way data fields of structures are selected.

The idea behind this syntactic construction is that several types may contain functions having identical names. E.g., a structure representing a circle might contain three int values: two values for the coordinates of the center of the circle and one value for the radius. Analogously to the Point structure, a Circle may now have a function draw to draw the circle.

### 2.5.14: Evaluation order of operands

Traditionally, the evaluation order of expressions of operands of binary operators is, except for the boolean operators and and or, not defined. C++ changed this for postfix expressions, assignment expressions (including compound assignments), and shift operators:

- Expressions using postfix operators (like index operators and member selectors) are evaluated from left to right (do not confuse this with postfix increment or decrement operators, which cannot be concatenated (e.g., variable++++ does not compile)).
- Assignment expressions are evaluated from right to left;
- Operands of shift operators are evaluated from left to right.

In the following examples first is evaluated before second, before third, before fourth, whether they are single variables, parenthesized expressions, or function calls:

```cpp
    first.second
    fourth += third = second += first
    first << second << third << fourth
    first >> second >> third >> fourth
```

In addition, when overloading an operator, the function implementing the overloaded operator is evaluated like the built-in operator it overloads, and not in the way function calls are generally ordered.

[Contents](Chapter_01_Overview.md)
