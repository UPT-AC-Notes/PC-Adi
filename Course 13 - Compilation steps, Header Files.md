## Compilation steps

1. C-Preprocessor (that also handles header files)
2. Compiler (which transforms C code in assembly)
3. Assembler (which transforms .asm code in object code(`.o`))
4. Link-editor / linker (which takes all object files and produces only one executable)

- https://medium.com/@larmalade/gcc-the-hard-way-how-to-include-functions-from-the-math-library-1cfe60f24a7a
	- This article describes why the ``-lm`` flag is needed when using the ``math.h`` library. 
- ChatGPT explanation:
```
In C, the mathematical functions like those declared in `math.h` are not part of the standard C library(libc) but are instead part of a separate library called the math library, often named `libm`. This is why you need to explicitly link against it using the `-lm` option when compiling your code.

Here are a few reasons for this design choice:

1. **Modularity**: Separating the math functions into their own library keeps the standard C library smaller and more manageable. Not all programs require advanced mathematical functions, so it makes sense not to include them by default.
    
2. **Efficiency**: For programs that don't need the functionality provided by `math.h`, not linking against `libm` reduces the final executable size and potentially improves load times.
    
3. **Historical Reasons**: This separation stems from the early days of C and Unix, where minimizing disk and memory usage was crucial. This tradition has continued for reasons of compatibility and convention.
    
4. **Portability and Standards Compliance**: The C standard (ANSI C and ISO C) defines the language and the standard library. Implementations might choose to organize these libraries differently based on the operating system and the compiler. Keeping the math library separate allows for flexibility in implementation and adherence to standards.
    

When compiling a program that uses math functions, the `-lm` flag tells the compiler to link against the math library. Without this flag, the compiler will not include the necessary references, leading to errors during linking.
```

## Header Files
- Definitions vs. declaration:
	- ``int f(int n);`` this is a declaration of a function
	- ``int f(int n){...}`` this is a definition of a function
	- ``int a;`` this is the definition of a variable
	- ``extern int a;`` this is the declaration of a variable

- **Header files should only contain declarations**.
	- We can write definitions in header files, but it shouldn't be done. The best practice says that any definition should be in ``.c`` files. That's because definitions produce code, and declarations don't produce code.

- ``.c`` files should always be compiled(specified in gcc CLI) and never included.
	- ``.h`` files should never be compiled and always be included (if used)
- complex.h:
```C
// this is a header guard to prevent circular or multiple inclusions of the same file
#ifndef __COMPLEX_H //one of POSIX's file naming convention: for file.h __FILE_H 
#define __COMPLEX_H 
typedef struct { 
	int re; 
	int im; }COMPLEX; 
	
COMPLEX complex_add(COMPLEX a, COMPLEX b); 
COMPLEX complex_sub(COMPLEX a, COMPLEX c); 

#endif
```
- complex.c:
```C
#include "complex.h" 
COMPLEX complex_add(COMPLEX x, COMPLEX y) { 
	COMPLEX r; 
	r.re = x.re + y.re; 
	r.im = x.im + y.im; 
	return r; 
} 
COMPLEX complex_sub(COMPLEX x, COMPLEX y) { 
	COMPLEX r; 
	r.re = x.re - y.re; 
	r.im = x.im - y.im; 
	return r; 
}
```

-  main.c:
```C
#include <stdio.h> 
#include "complex.h" 
int main(void) { 
	COMPLEX a = {1,-2}; 
	COMPLEX b = {10,20}; 
	COMPLEX t; 
	t = complex_add(a,b); 
	t = complex_sub(a,b); 
	
	return 0; 
}
```
```Bash
gcc –Wall –o p complex.c complex_test.c
```
- If we placed a variable ``int count = 0;`` in the header file, we would receive a link-editor error because the variable will be present twice (because the header file is included twice). The solution is to follow the rule of declarations vs. definitions (declare ``extern int count;`` in the header file and declare it normally in the designated ``.c`` file)
- The C++ alternative to ``#ifndef, #ifdef`` is ``#pragma once``
	- When placed at the beginning of a header file, the compiler will be alerted to only include it once.
	- In industry (both in C and C++), this concept is called  ``once only headers``.
- The syntax ``#include "test.h"`` will search for ``test.h`` in the relative path of the program. That means ``#include "test.h"`` is equivalent to ``#include "./test.h"``
	- However, ``#include <test.h>`` will search for ``test.h`` in the compiler specified paths (by using the ``-i`` CLI argument for gcc). When the gcc process spawns, it always has some paths specified by default, where it looks for standard libraries. 

- We can use the ``-c`` flag to generate object files from C files:
	- ``gcc -c main.c`` will generate ``main.o`` that can be further used with gcc to skip the compilation step and directly call the linker: ``gcc main.o``
### Dynamically linked libraries
 - ``.so`` in linux, ``.dll`` in windows. While running, the program knows how to link the needed libraries. We don't need to link all the libraries in the compilation step.