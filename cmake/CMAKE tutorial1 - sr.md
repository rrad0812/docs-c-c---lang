
# CMAKE tutorijal

## Uvod u osnove

### Minimalna verzija

Evo prvog reda svakog CMakeLists.tkt, što je potrebno ime datoteke koju CMake traži:

```sh
cmake_minimum_required(VERSION 3.15)
```

Pomenimo malo CMake sintaksu.

Komanda `cmake_minimum_required` ne razlikuje velika i mala slova, pa je uobičajena praksa da se koriste mala slova[^1]. `VERSION` je posebna ključna reč za ovu funkciju. A vrednost verzije prati ključnu reč. Kao i svuda u ovoj knjizi, samo kliknite na ime komande da biste videli zvaničnu dokumentaciju i koristite padajući meni za prebacivanje dokumentacije između verzija CMake-a.

Ova linija je posebna![^2] Verzija CMake-a će takođe diktirati politike koje definišu promene ponašanja. Dakle, ako postavite `minimum_required` na VERZIJU 2.8, dobićete pogrešno ponašanje povezivanja na macOS-u, na primer, čak iu najnovijim verzijama CMake-a. Ako ga postavite na 3.3 ili manje, dobićete pogrešno ponašanje skrivenih simbola itd. Lista smernica i verzija je dostupna u smernicama.

Počevši od CMake 3.12, ovo podržava opseg, kao što je `VERZIJA 3.15...4.1` to znači da podržavate samo 3.15, ali ste je takođe testirali sa novim postavkama smernica do 4.1. Ovo je mnogo lepše za korisnike kojima su potrebna bolja podešavanja, a zbog trika u sintaksi, kompatibilan je unazad sa starijim verzijama CMake-a (iako će zapravo pokretanje CMake-a 3.1-3.11 samo postaviti staru verziju smernica, pošto te verzije to nisu posebno tretirale). Nove verzije smernica obično su najvažnije za korisnike macOS-a i Windows-a, koji takođe obično imaju najnoviju verziju CMake-a.

Evo šta novi projekti treba da urade:

```sh
cmake_minimum_required(VERSION 3.15...4.1)
```

**Savet**
Ako zaista treba da podesite nisku vrednost ovde, možete da koristite `cmake_policy` da biste uslovno povećali nivo smernica ili postavili određenu smernicu.

### Postavljanje projekta

Sada će svaka CMake datoteka najvišeg nivoa imati sledeći red:

```sh
project(MyProject VERSION 1.0 DESCRIPTION "Very nice project" LANGUAGES CXX)
```

Sada vidimo još više sintakse. Stringovi su pod znacima navoda, razmak nije bitan, a ime projekta je prvi argument (pozicioni). Svi argumenti ključnih reči ovde su opcioni. Verzija postavlja gomilu promenljivih, kao što su `MyProject_VERSION` i `PROJECT_VERSION`. Jezici su C, CXX, Fortran, ASM, CUDA (CMake 3.8+), CSharp (3.8+) i SWIFT (CMake 3.15+ eksperimentalni). C CXX je podrazumevana. U CMake 3.9, DESCRIPTION je dodat i za postavljanje opisa projekta. Dokumentacija za projekat može biti od pomoći.

**Savet**
Možete da dodate komentare sa znakom `#`. CMake takođe ima ugrađenu sintaksu za komentare, ali se retko koristi.

Zaista nema ništa posebno u nazivu projekta. U ovom trenutku se ne dodaju mete.

### Pravljenje izvršne datoteke

Iako su biblioteke mnogo interesantnije i sa njima ćemo provoditi većinu vremena, počnimo sa jednostavnim izvršnim programom.

```sh
add_executable(one two.cpp two.h)
```

Ovde postoji nekoliko stvari za raspakivanje. Ovde je "one" i jedno i drugo:

- naziv generisane izvršne datoteke,
- i ime kreiranog CMake cilja (uskoro ćete čuti mnogo više o ciljevima, obećavam).

Sledi lista datoteka sa kodom i možete ih navesti koliko god želite. CMake je pametan i kompajliraće samo ekstenzije datoteka sa kodom. Zaglavlja će biti, za većinu namera i svrha, ignorisana; jedini razlog da ih navedemo je da ih nateramo da se pojave u IDE-ovima. Ciljevi se pojavljuju kao direktorijumi u mnogim IDE-ovima. Više o opštem sistemu izgradnje i ciljevima dostupno je na [buildsistem](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html).

### Pravljenje biblioteke

Pravljenje biblioteke se obavlja pomoću `add_library` i otprilike je jednostavno:

```sh
add_library(one STATIC three.cpp three.h)
```

Možete da izaberete tip biblioteke, `STATIC`, `SHARED` ili `MODULE`. Ako ostavite ovaj izbor isključen, vrednost `BUILD_SHARED_LIBS` će koristiti za izbor između `STATIC` i `SHARED`.

Kao što ćete videti u sledećim odeljcima, često ćete morati da napravite izmišljeni cilj, to jest onu gde ništa ne treba da se kompajlira, na primer, za biblioteku samo za zaglavlje. To se zove INTERFACE biblioteka i drugi je izbor; jedina razlika je u tome što ne mogu biti praćeni nazivima datoteka.

Takođe možete napraviti ALIAS biblioteku sa postojećom bibliotekom, koja vam jednostavno daje novo ime za cilj. Jedina prednost ovoga je što možete da pravite biblioteke sa :: u imenu (koje ćete videti kasnije)[^3].

### Mete su tvoji prijatelji

Sada smo odredili cilj, kako da dodamo informacije o njemu? Na primer, možda treba uključiti direktorijum:

```sh
target_include_directories(one PUBLIC include)
```

`target_include_directories` dodaje direktorijum za priključivanje cilju. PUBLIC ne znači mnogo za izvršnu datoteku; za biblioteku daje CMake-u do znanja da svim ciljevima koji se povezuju sa ovim ciljem takođe mora biti potreban direktorijum uključivanja. Ostale opcije su PRIVATNO (utiče samo na trenutni cilj, ne i na zavisnosti) i INTERFEJS (potrebno samo za zavisnosti).

Zatim možemo lančati ciljeve:

```sh
add_library(another STATIC another.cpp another.h)
target_link_libraries(another PUBLIC one)
```

`target_link_libraries` je verovatno najkorisnija i najzbunjujuća komanda u CMake-u. Uzima cilj (drugi) i dodaje zavisnost ako je cilj dat. Ako cilj sa tim imenom (jedan) ne postoji, onda dodaje vezu ka biblioteci koja se zove jedan na vašoj putanji (otuda i naziv komande). Ili mu možete dati punu putanju do biblioteke. Ili zastavicu linkera. Samo da dodamo konačnu zabunu, klasični CMake vam je omogućio da preskočite izbor ključnih reči PUBLIC, itd. Ako je ovo urađeno na meti, dobićete grešku ako pokušate da mešate stilove dalje u lancu.

Usredsredite se na korišćenje ciljeva svuda i ključnih reči svuda i bićete dobro.

Ciljevi mogu da sadrže direktorijume, povezane biblioteke (ili povezane ciljeve), opcije kompajliranja, definicije kompajliranja, karakteristike kompajliranja (pogledajte poglavlje C++11) i još mnogo toga. Kao što ćete videti u dva poglavlja, uključujući projekte, često možete da dobijete ciljeve (i uvek pravite ciljeve) da biste predstavili sve biblioteke koje koristite. Čak i stvari koje nisu prave biblioteke, kao što je OpenMP, mogu biti predstavljene ciljevima. Zbog toga je Modern CMake odličan!

Pogledajte da li možete da pratite sledeću datoteku. Pravi jednostavnu C++11 biblioteku i program koji je koristi. Nema zavisnosti. Kasnije ću razgovarati o više standardnih opcija C++, koristeći sistem CMake 3.8 za sada.

```sh
cmake_minimum_required(VERSION 3.15...4.1)

project(Calculator LANGUAGES CXX)

add_library(calclib STATIC src/calclib.cpp include/calc/lib.hpp)
target_include_directories(calclib PUBLIC include)
target_compile_features(calclib PUBLIC cxx_std_11)

add_executable(calc apps/calc.cpp)
target_link_libraries(calc PUBLIC calclib)
```

## Variables and the Cache

### Local Variables

We will cover variables first. A local variable is set like this:

```sh
set(MY_VARIABLE "value")
```

The names of variables are usually all caps, and the value follows. You access a variable by using ${}, such as ${MY_VARIABLE}[^4]. CMake has the concept of scope; you can access the value of the variable after you set it as long as you are in the same scope. If you leave a function or a file in a sub directory, the variable will no longer be defined. You can set a variable in the scope immediately above your current one with PARENT_SCOPE at the end.

Lists are simply a series of values when you set them:

```sh
set(MY_LIST "one" "two")
```

which internally become ; separated values. So this is an identical statement:

```sh
set(MY_LIST "one;two")
```

The list( command has utilities for working with lists, and separate_arguments will turn a space separated string into a list (inplace). Note that an unquoted value in CMake is the same as a quoted one if there are no spaces in it; this allows you to skip the quotes most of the time when working with value that you know could not contain spaces.

When a variable is expanded using ${} syntax, all the same rules about spaces apply. Be especially careful with paths; paths may contain a space at any time and should always be quoted when they are a variable (never write ${MY_PATH}, always should be "${MY_PATH}").
Cache Variables

If you want to set a variable from the command line, CMake offers a variable cache. Some variables are already here, like CMAKE_BUILD_TYPE. The syntax for declaring a variable and setting it if it is not already set is:

```sh
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "Description")
```

This will not replace an existing value. This is so that you can set these on the command line and not have them overridden when the CMake file executes. If you want to use these variables as a make-shift global variable, then you can do:

```sh
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "" FORCE)
mark_as_advanced(MY_CACHE_VARIABLE)
```

The first line will cause the value to be set no matter what, and the second line will keep the variable from showing up in the list of variables if you run cmake -L .. or use a GUI. This is so common, you can also use the INTERNAL type to do the same thing (though technically it forces the STRING type, this won’t affect any CMake code that depends on the variable):

```sh
set(MY_CACHE_VARIABLE "VALUE" CACHE INTERNAL "")
```

Since BOOL is such a common variable type, you can set it more succinctly with the shortcut:

```sh
option(MY_OPTION "This is settable from the command line" OFF)
```

For the BOOL datatype, there are several different wordings for ON and OFF.

See cmake-variables for a listing of known variables in CMake.

### Environment variables

You can also set(ENV{variable_name} value) and get $ENV{variable_name} environment variables, though it is generally a very good idea to avoid them.

### The Cache

The cache is actually just a text file, CMakeCache.txt, that gets created in the build directory when you run CMake. This is how CMake remembers anything you set, so you don’t have to re-list your options every time you rerun CMake.
Properties

The other way CMake stores information is in properties. This is like a variable, but it is attached to some other item, like a directory or a target. A global property can be a useful uncached global variable. Many target properties are initialized from a matching variable with `CMAKE_` at the front. So setting CMAKE_CXX_STANDARD, for example, will mean that all new targets created will have CXX_STANDARD set to that when they are created. There are two ways to set properties:

```sh
set_property(TARGET TargetName
             PROPERTY CXX_STANDARD 11)

set_target_properties(TargetName PROPERTIES
                      CXX_STANDARD 11)
```

The first form is more general, and can set multiple targets/files/tests at once, and has useful options. The second is a shortcut for setting several properties on one target. And you can get properties similarly:

```sh
get_property(ResultVariable TARGET TargetName PROPERTY CXX_STANDARD)
```

See cmake-properties for a listing of all known properties. You can also make your own in some cases.[^5]

## Programming in CMake

### Control flow

CMake has an if statement, though over the years it has become rather complex. There are a series of all caps keywords you can use inside an if statement, and you can often refer to variables by either directly by name or using the ${} syntax (the if statement historically predates variable expansion). An example if statement:

```sh
if(variable)
    # If variable is `ON`, `YES`, `TRUE`, `Y`, or non zero number
else()
    # If variable is `0`, `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`, `""`, or ends in `-NOTFOUND`
endif()
# If variable does not expand to one of the above, CMake will expand it then try again
```

Since this can be a little confusing if you explicitly put a variable expansion, like ${variable}, due to the potential expansion of an expansion, a policy (CMP0054) was added in CMake 3.1+ that keeps a quoted expansion from being expanded yet again. So, as long as the minimum version of CMake is 3.1+, you can do:

```sh
if("${variable}")
    # True if variable is not false-like
else()
    # Note that undefined variables would be `""` thus false
endif()
```

There are a variety of keywords as well, such as:

- Unary: NOT, TARGET, EXISTS (file), DEFINED, etc.

- Binary: STREQUAL, AND, OR, MATCHES (regular expression), VERSION_LESS, VERSION_LESS_EQUAL (CMake 3.7+), etc.

- Parentheses can be used to group

### generator-expressions

generator-expressions are really powerful, but a bit odd and specialized. Most CMake commands happen at configure time, include the if statements seen above. But what if you need logic to occur at build time or even install time? Generator expressions were added for this purpose.[^6] They are evaluated in target properties.

The simplest generator expressions are informational expressions, and are of the form $<KEYWORD>; they evaluate to a piece of information relevant for the current configuration. The other form is $<KEYWORD:value>, where KEYWORD is a keyword that controls the evaluation, and value is the item to evaluate (an informational expression keyword is allowed here, too). If KEYWORD is a generator expression or variable that evaluates to 0 or 1, value is substituted if 1 and not if 0. You can nest generator expressions, and you can use variables to make reading nested variables bearable. Some expressions allow multiple values, separated by commas.[^7]

If you want to put a compile flag only for the DEBUG configuration, for example, you could do:

```sh
target_compile_options(MyTarget PRIVATE "$<$<CONFIG:Debug>:--my-flag>")
```

This is a newer, better way to add things than using specialized *_DEBUG variables, and generalized to all the things generator expressions support. Note that you should never, never use the configure time value for the current configuration, because multi-configuration generators like IDEs do not have a “current” configuration at configure time, only at build time through generator expressions and custom `*_<CONFIG>` variables.

Other common uses for generator expressions:

- Limiting an item to a certain language only, such as CXX, to avoid it mixing with something like CUDA, or wrapping it so that it is different depending on target language.

- Accessing configuration dependent properties, like target file location.

- Giving a different location for build and install directories.

That last one is very common. You’ll see something like this in almost every package that supports installing:

```sh
target_include_directories(
    MyTarget
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
```

### Macros and Functions

You can define your own CMake function or macro easily. The only difference between a function and a macro is scope; macros don’t have one. So, if you set a variable in a function and want it to be visible outside, you’ll need PARENT_SCOPE. Nesting functions therefore is a bit tricky, since you’ll have to explicitly set the variables you want visible to the outside world to PARENT_SCOPE in each function. But, functions don’t “leak” all their variables like macros do. For the following examples, I’ll use functions.

An example of a simple function is as follows:

```sh
function(SIMPLE REQUIRED_ARG)
    message(STATUS "Simple arguments: ${REQUIRED_ARG}, followed by ${ARGN}")
    set(${REQUIRED_ARG} "From SIMPLE" PARENT_SCOPE)
endfunction()

simple(This Foo Bar)
message("Output: ${This}")
```

The output would be:

```sh
-- Simple arguments: This, followed by Foo;Bar
Output: From SIMPLE
```

If you want positional arguments, they are listed explicitly, and all other arguments are collected in ARGN (ARGV holds all arguments, even the ones you list). You have to work around the fact that CMake does not have return values by setting variables. In the example above, you can explicitly give a variable name to set.

### Arguments

CMake has a named variable system that you’ve already seen in most of the build in CMake functions. You can use it with the cmake_parse_arguments function. If you want to support a version of CMake less than 3.5, you’ll want to also include the CMakeParseArguments module, which is where it used to live before becoming a built in command. Here is an example of how to use it:

```sh
function(COMPLEX)
    cmake_parse_arguments(
        COMPLEX_PREFIX
        "SINGLE;ANOTHER"
        "ONE_VALUE;ALSO_ONE_VALUE"
        "MULTI_VALUES"
        ${ARGN}
    )
endfunction()

complex(SINGLE ONE_VALUE value MULTI_VALUES some other values)
```

Inside the function after this call, you’ll find:

```sh
COMPLEX_PREFIX_SINGLE = TRUE
COMPLEX_PREFIX_ANOTHER = FALSE
COMPLEX_PREFIX_ONE_VALUE = "value"
COMPLEX_PREFIX_ALSO_ONE_VALUE = <UNDEFINED>
COMPLEX_PREFIX_MULTI_VALUES = "some;other;values"
```

If you look at the official page, you’ll see a slightly different method using set to avoid explicitly writing the semicolons in the list; feel free to use the structure you like best. You can mix it with the positional arguments listed above; any remaining arguments (therefore optional positional arguments) are in COMPLEX_PREFIX_UNPARSED_ARGUMENTS.

## Communication with your code

### Configure File

CMake allows you to access CMake variables from your code using configure_file. This command copies a file (traditionally ending in .in) from one place to another, substituting all CMake variables it finds. If you want to avoid replacing existing ${} syntax in your input file, use the @ONLY keyword. There’s also a COPY_ONLY keyword if you are just using this as a replacement for file(COPY.

This functionality is used quite frequently; for example, on Version.h.in:

"Version.h.in"

```sh
#pragma once

#define MY_VERSION_MAJOR @PROJECT_VERSION_MAJOR@
#define MY_VERSION_MINOR @PROJECT_VERSION_MINOR@
#define MY_VERSION_PATCH @PROJECT_VERSION_PATCH@
#define MY_VERSION_TWEAK @PROJECT_VERSION_TWEAK@
#define MY_VERSION "@PROJECT_VERSION@"
```

CMake lines:

```sh
configure_file (
    "${PROJECT_SOURCE_DIR}/include/My/Version.h.in"
    "${PROJECT_BINARY_DIR}/include/My/Version.h"
)
```

You should include the binary include directory as well when building your project. If you want to put any true/false variables in a header, CMake has C specific #cmakedefine and #cmakedefine01 replacements to make appropriate define lines.

You can also (and often do) use this to produce .cmake files, such as the configure files (see installing).

### Reading files

The other direction can be done too; you can read in something (like a version) from your source files. If you have a header only library that you’d like to make available with or without CMake, for example, then this would be the best way to handle a version. This would look something like this:

```sh
# Assuming the canonical version is listed in a single line
# This would be in several parts if picking up from MAJOR, MINOR, etc.
set(VERSION_REGEX "#define MY_VERSION[ \t]+\"(.+)\"")

# Read in the line containing the version
file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/include/My/Version.hpp"
    VERSION_STRING REGEX ${VERSION_REGEX})

# Pick out just the version
string(REGEX REPLACE ${VERSION_REGEX} "\\1" VERSION_STRING "${VERSION_STRING}")

# Automatically getting PROJECT_VERSION_MAJOR, My_VERSION_MAJOR, etc.
project(My LANGUAGES CXX VERSION ${VERSION_STRING})
```

Above, file(STRINGS file_name variable_name REGEX regex) picks lines that match a regex; and the same regex is used to then pick out the parentheses capture group with the version part. Replace is used with back substitution to output only that one group.

## How to structure your project

The following information is biased. But in a good way, I think. I’m going to tell you how to structure the directories in your project. This is based on convention, but will help you:

- Easily read other projects following the same patterns,

- Avoid a pattern that causes conflicts,

- Keep from muddling and complicating your build.

First, this is what your files should look like when you start if your project is creatively called project, with a library called lib, and a executable called app:

```sh
- project
  - .gitignore
  - README.md
  - LICENSE.md
  - CMakeLists.txt
  - cmake
    - FindSomeLib.cmake
    - something_else.cmake
  - include
    - project
      - lib.hpp
  - src
    - CMakeLists.txt
    - lib.cpp
  - apps
    - CMakeLists.txt
    - app.cpp
  - tests
    - CMakeLists.txt
    - testlib.cpp
  - docs
    - CMakeLists.txt
  - extern
    - googletest
  - scripts
    - helper.py
```

The names are not absolute; you’ll see contention about test/ vs. tests/, and the application folder may be called something else (or not exist for a library-only project). You’ll also sometime see a python folder for python bindings, or a cmake folder for helper CMake files, like `Find<library>`.cmake files. But the basics are there.

Notice a few things already apparent; the CMakeLists.txt files are split up over all source directories, and are not in the include directories. This is because you should be able to copy the contents of the include directory to /usr/include or similar directly (except for configuration headers, which I go over in another chapter), and not have any extra files or cause any conflicts. That’s also why there is a directory for your project inside the include directory. Use add_subdirectory to add a subdirectory containing a CMakeLists.txt.

You often want a cmake folder, with all of your helper modules. This is where your `Find*.cmake` files go. An set of some common helpers is at github.com/CLIUtils/cmake. To add this folder to your CMake path:

```sh
set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})
```

Your extern folder should contain git submodules almost exclusively. That way, you can control the version of the dependencies explicitly, but still upgrade easily. See the Testing chapter for an example of adding a submodule.

You should have something like /build* in your .gitignore, so that users can make build directories in the source directory and use those to build. A few packages prohibit this, but it’s much better than doing a true out-of-source build and having to type something different for each package you build.

If you want to avoid the build directory being in a valid source directory, add this near the top of your CMakeLists:

```sh
# Require out-of-source builds
file(TO_CMAKE_PATH "${PROJECT_BINARY_DIR}/CMakeLists.txt" LOC_PATH)
if(EXISTS "${LOC_PATH}")
    message(FATAL_ERROR "You cannot build in a source directory (or any directory with a CMakeLists.txt file). Please make a build subdirectory. Feel free to remove CMakeCache.txt and CMakeFiles.")
endif()
```

## Running other programs

### Running a command at configure time

Running a command at configure time is relatively easy. Use execute_process to run a process and access the results. It is generally a good idea to avoid hard coding a program path into your CMake; you can use ${CMAKE_COMMAND}, find_package(Git), or find_program to get access to a command to run. Use RESULT_VARIABLE to check the return code and OUTPUT_VARIABLE to get the output.

Here is an example that updates all git submodules:

```sh
find_package(Git QUIET)

if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
    execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                    RESULT_VARIABLE GIT_SUBMOD_RESULT)
    if(NOT GIT_SUBMOD_RESULT EQUAL "0")
        message(FATAL_ERROR "git submodule update --init --recursive failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
    endif()
endif()
```

### Running a command at build time

Build time commands are a bit trickier. The main complication comes from the target system; when do you want your command to run? Does it produce an output that another target needs? With this in mind, here is an example that calls a Python script to generate a header file:

```sh
find_package(PythonInterp REQUIRED)
add_custom_command(OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/include/Generated.hpp"
    COMMAND "${PYTHON_EXECUTABLE}" "${CMAKE_CURRENT_SOURCE_DIR}/scripts/GenerateHeader.py" --argument
    DEPENDS some_target)

add_custom_target(generate_header ALL
    DEPENDS "${CMAKE_CURRENT_BINARY_DIR}/include/Generated.hpp")

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/include/Generated.hpp DESTINATION include)
```

Here, the generation happens after some_target is complete, and happens when you run make without a target (ALL). If you make this a dependency of another target with add_dependencies, you could avoid the ALL keyword. Or, you could require that a user explicitly builds the generate_header target when making.

### Included common utilities

A useful tool in writing CMake builds that work cross-platform is `cmake -E <mode>` (seen in CMake files as `${CMAKE_COMMAND} -E)`. This mode allows CMake to do a variety of things without calling system tools explicitly, like copy, make_directory, and remove. It is mostly used for the build time commands. Note that the very useful create_symlink mode used to be Unix only, but was added for Windows in CMake 3.13. See the docs.

## A simple example

This is a simple yet complete example of a proper CMakeLists. For this program, we have one library (MyLibExample) with a header file and a source file, and one application, MyExample, with one source file.

```sh
# Almost all CMake files should start with this
# You should always specify a range with the newest
# and oldest tested versions of CMake. This will ensure
# you pick up the best policies.
cmake_minimum_required(VERSION 3.15...4.1)

# This is your project statement. You should always list languages;
# Listing the version is nice here since it sets lots of useful variables
project(
  ModernCMakeExample
  VERSION 1.0
  LANGUAGES CXX)

# If you set any CMAKE_ variables, that can go here.
# (But usually don't do this, except maybe for C++ standard)

# Find packages go here.

# You should usually split this into folders, but this is a simple example

# This is a "default" library, and will match the *** variable setting.
# Other common choices are STATIC, SHARED, and MODULE
# Including header files here helps IDEs but is not required.
# Output libname matches target name, with the usual extensions on your system
add_library(MyLibExample simple_lib.cpp simple_lib.hpp)

# Link each target with other targets or add options, etc.

# Adding something we can run - Output name matches target name
add_executable(MyExample simple_example.cpp)

# Make sure you link your targets with this command. It can also link libraries and
# even flags, so linking a target that does not exist will not give a configure-time error.
target_link_libraries(MyExample PRIVATE MyLibExample)
```

Kompletan primer je dostupan u folderu primeri.

Dostupan je i veći primer sa više datoteka.

[^1]:U ovoj knjizi ću uglavnom izbegavati da vam pokažem pogrešan način da radite stvari; možete pronaći mnogo primera za to na internetu. Povremeno ću pomenuti alternative, ali one se ne preporučuju osim ako nisu apsolutno neophodne; često su tu samo da vam pomognu da pročitate stariji CMake kod.

[^2]:Ponekad ćete ovde videti FATAL_ERROR, koja je bila potrebna za podršku lepih grešaka pri pokretanju ovoga u CMake < 2.6, što više ne bi trebalo da predstavlja problem.

[^3]: :: sintaksa je prvobitno bila namenjena za INTERFACE IMPORTED biblioteke, koje su eksplicitno trebalo da budu biblioteke definisane van trenutnog projekta. Ali, zbog ovoga, većina komandi target_* ne radi na IMPORTED bibliotekama, zbog čega ih je teško sami podesiti. Zato za sada nemojte da koristite ključnu reč IMPORTED, već umesto toga koristite ALIAS cilj; biće u redu dok ne počnete da izvozite ciljeve. Ovo ograničenje je popravljeno u CMake 3.11.

[^4]:If izjave su malo čudne u tome što mogu uzeti promenljivu sa ili bez okolne sintakse; ovo je tu iz istorijskih razloga: if prethodi sintaksi ${}.

[^5]:Ciljevi interfejsa, na primer, mogu imati ograničenja na prilagođena svojstva koja su dozvoljena.

[^6]:Ponašaju se kao da se procenjuju u vreme izgradnje/instalacije, iako se zapravo procenjuju za svaku konfiguraciju izgradnje.

[^7]:Dokumentacija CMake deli izraze na informativne, logičke i izlazne.
