- Slide 245 [[CursPC.pdf]]
- The order of compute for a C program is: Preprocessor, Compiler, Link-editor

# C Preprocessor
#### Include directive
- `#include <file.h>` vs ``#include "file.h"``
	- if quotation marks are used, the file is searched in the local directory, where the ``main.c`` is present as well. It can be used with a relative path as well.
	- the ``<>`` notation is used to search the included file in the **OS Paths** used and set by the compiler, OS, or the user.

#### Define directive
- It blindly replaces the value declared with the value assigned everywhere in the source code.
- The following code is replaced (**on the text level**)
```C
#include <stdio.h>

#define COUNT 50
int main(void){
	int n;
	n = COUNT;
	for(int i=0; i<COUNT; i++){
	
	}

	return 0;
}
```
- With the following code:
```C
#include <stdio.h>

int main(void){
	int n;
	n = 50;
	for(int i=0; i<50; i++){
	
	}

	return 0;
}
```

- It doesn't care about the C-syntax, if an error is to be thrown, it will be thrown on the compile part.
- We usually don't specify ``;`` after the define directive, because it will also be parsed and placed everywhere in the code. (``define TXT "txt";`` will replace any appearance of TXT with ``"txt";`` ) 

- It works differently with inline-if statements since it supports and replaces their arguments:
```C
#define max(A, B) ( (A>B) ? A : B )

int main(void){
	int n;
	n = max(n, 2);
}
```
- will be replaced with:
```C
int main(void){
	int n;
	n = n > 2 ? n : 2;
}
```

- ``#define to_string(str) # str`` will replace ``to_string(un_string)`` with ``un_string``
- ``#define to_string_2(a, b) a ## b`` will replace ``to_string_2(poli, UPT)`` with ``poliUPT``

#### if, ifdef, ifndef, else, endif Directives

- We can use these directives to compile only some parts of the code. This is again done in the preprocessor level.
- This is very useful since we can declare preprocessor variable/constants directly in the gcc CLI:
	- ``gcc –DDEBUG prog.c``
```C
#define DEBUG 
int main(void) { 
	#ifndef DEBUG 
	printf ("cod fara debug\n"); 
	#endif 
	
	#ifdef DEBUG 
	printf ("cod cu debug\n"); 
	#endif 
	
	#ifdef DEBUG 
	printf ("cod cu debug\n"); 
	#else 
	printf ("cod fara debug\n"); 
	#endif 
}
```
- As shown as the example, this can be used to switch ``development`` and ``production`` environments.


#### Static calculations

- Every static calculation is done by the C preprocessor, the compiler will never see it.
	- ``int a = 1+2`` will always be replaced with ``int a = 3``

# Dynamic allocation

- Slide 251 [[CursPC.pdf]]
- If, for example, we have a web server and we statically allocate memory for 100 clients. Then, if 101 clients use the application, we would need to recompile the application and increase the statically allocated size. This is a bad approach. A bad approach would also be to directly compile the application for more clients, since that would increase the memory requirements of the applications hence making it unable to run on a server with less memory.

- Here comes in the true usage of **pointers**.
- Memory blocks that are dynamically allocated are stored in the ``heap``.
- Losing the reference to a memory block means losing any connection with that memory.
- Memory blocks are kept allocated until the programmers chooses to free them.
- The map of memory blocks is stored in the heap.

#### Malloc
- ``void *malloc(size_t size);``
- Allocates a continuous memory block of size ``size`` bytes. The memory block is uninitialised (a random value by default) 
- Returns a void pointer to the allocated memory block or NULL if an error has appeared and the memory block wasn't allocated (this can happen if we ran out of memory or the memory was fragmented too much to support any more allocations).
```C
#include <stdio.h> 
#include <stdlib.h> 
#define LEN 16 
int main(void) { 
	int *int_ptr = NULL; 
	int *int_dyn_array = NULL; 
	int_ptr = malloc(sizeof(int)); 
	int_dyn_array = malloc(LEN * sizeof(int)); 
	*int_ptr = 5; 
	printf ("value of int_ptr: %d\n", *int_ptr); 
	for (int i = 0; i < LEN; i++) { 
		int_dyn_array[i] = i * i; 
	} 
	printf ("array values: "); 
	for (int i = 0; i < LEN; i++) { 
		printf ("%d ", int_dyn_array[i]); 
	} 
	printf ("\n"); 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
array values: 0 1 4 9 16 25 36 49 64 81 100 121 144 169 196 225 
valy@staff:~/teaching$
```
#### Calloc
- ``void *calloc(size_t nmemb, size_t size);``
- Allocates a continuous memory block formed of ``nmemb`` elements, each of ``size`` size.
- The memory block will also be initialised with ``0`` (each byte of the block will have the value zero)
- Returns a void pointer to the allocated memory block or NULL if an error has appeared and the memory block wasn't allocated.
```C
q = calloc(10, sizeof(int));
// is equivalent to:
q = malloc(10 * sizeof(int));
memset(q, 0, 10 * sizeof(int));
```


#### Realloc
- ``void *realloc(void *ptr, size_t size);``
- Reallocates a block of continuous memory that was already allocated to a new ``size``.
- Returns a void pointer to the allocated memory block or NULL if an error has appeared and the memory block wasn't allocated.
- ``size`` can be bigger or smaller than the initial allocated size of the block.
- We use the syntax ``p = realloc(p, 10)`` because chances are **high** that the memory address will change if it doesn't have space in that block (even with the overhead) hence needing to move the entire memory block elsewhere.
- It guarantees that the data will be kept the same, since the memory block is moved.
- Common mistake: ``int *p; p = realloc(p, 10)``. This will access uninitialised memory resulting in a segmentation fault.
```C
#include <stdio.h> 
#include <stdlib.h> 
#define LEN 16 
int main(void) { 
	int *int_dyn_array = NULL;
	int_dyn_array = calloc(LEN, sizeof(int)); 
	for (int i = 0; i < LEN; i++) { 
		int_dyn_array[i] = i * i; 
	} 
	printf ("array values before realloc: "); 
	for (int i = 0; i < LEN; i++) { 
		printf ("%d ", int_dyn_array[i]); 
	} 
	printf ("\n"); 
	int_dyn_array = realloc(int_dyn_array, (LEN + 1) * sizeof(int));
	int_dyn_array[LEN] = 99; 
	printf ("array values after realloc: "); 
	for (int i = 0; i < (LEN + 1); i++) { 
		printf ("%d ", int_dyn_array[i]); 
	} 
	printf ("\n");
	 
	return 0; 
}
```
Output
```Bash
valy@staff:~/teaching$ ./p 
array values before realloc: 0 1 4 9 16 25 36 49 64 81 100 121 144 169 196 225
array values after realloc: 0 1 4 9 16 25 36 49 64 81 100 121 144 169 196 225 99
valy@staff:~/teaching$
```

#### Free
- ``void free(void *ptr);``
- Frees ('deletes') the memory zone referenced by the address ``ptr``.
	- Behind the scene, the memory block is removed from the allocated memory that was given to the program by the OS.
	- The value ``ptr`` stays the same even after it was freed, but it will point to unallocated memory and will result in a segmentation fault when trying to access it.
	- Usually, the freed pointer is set to a value, but not necessarily. 
```C
#include <stdio.h> 
#include <stdlib.h> 
#define LEN 16 
int main(void) { 
	int *int_dyn_array = NULL; 
	int_dyn_array = calloc(LEN, sizeof(int)); 
	for (int i = 0; i < LEN; i++) { 
		int_dyn_array[i] = i * i; 
	} 
	printf ("array values: "); 
	for (int i = 0; i < LEN; i++) { 
		printf ("%d ", int_dyn_array[i]); 
	} 
	printf ("\n"); 
	free(int_dyn_array); 
	
	return 0; 
}
```

### Consequences and Observations

- This won't work, since the above presented functions can only work with memory allocated in the heap, not statically allocated memory (in the stack, in the .data, in the .bss)
```C
#include <stdlib.h> 
int main(void) { 
	int tab[10]; 
	tab = malloc(20 * sizeof(int));  
	
	return 0; 
}
```
Output:
```Bash
dyn1.c: In function ‘main’: 
dyn1.c:6:7: error: assignment to expression with array type 
	tab = malloc(10 * sizeof(int)); 
		^ 
valy@staff:~/teaching$
```

- Also, statically allocated memory cannot be freed.
```C
#include <stdlib.h> 
int main(void) { 
	int tab[10]; 
	free(tab); 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ gcc -Wall -o p dyn2.c 
dyn2.c: In function ‘main’: 
dyn2.c:6:3: warning: attempt to free a non-heap object ‘tab’ [-Wfree-nonheap-object] 
	free(tab); 
	^~~~~~~~~ 
valy@staff:~/teaching$
```

- It is very dangerous to free a unallocated pointer.
	-  **In every of these situations the behaviour is not defined and will result in memory corruption.**
	 - For most of these situations, a warning is not even thrown at compile time.
```C
#include <stdlib.h> 
int main(void) { 
	int *p; 
	free(p); 
	
	p = NULL; 
	free(p); 
	
	p = malloc(sizeof(int)); 
	free(p); 
	free(p); 
	
	return 0; 
}
```

- Memory leaks usually result in removing/losing the linkage to an allocated pointer:
```C
#include <stdlib.h> 
int main(void) { 
	int *p = NULL; 
	int x = 0; 
	p = malloc(sizeof(int)); 
	p = &x; // blocul initial referit de p este pierdut 
	
	return 0; 
}
```

- Another example to memory leaks is un-freed memory at the end of the program:
```C
#include <stdlib.h> 
int main(void) { 
	int *p = NULL; 
	p = malloc(sizeof(int)); 
	
	return 0; 
}
```

#### Valgrind
- A CLI tool used to detect memory leaks.
```Bash
valy@staff:~/teaching$ valgrind ./p 
==12696== Memcheck, a memory error detector 
==12696== Copyright (C) 2002-2017, and GNU GPLd, by Julian Seward et al. 
==12696== Using Valgrind-3.14.0 and LibVEX; rerun with -h for copyright info 
==12696== Command: ./p 
==12696== 
==12696== 
==12696== HEAP SUMMARY: 
==12696== in use at exit: 4 bytes in 1 blocks 
==12696== total heap usage: 1 allocs, 0 frees, 4 bytes allocated 
==12696== 
==12696== LEAK SUMMARY: 
==12696== definitely lost: 4 bytes in 1 blocks 
==12696== indirectly lost: 0 bytes in 0 blocks 
==12696== possibly lost: 0 bytes in 0 blocks 
==12696== still reachable: 0 bytes in 0 blocks 
==12696== suppressed: 0 bytes in 0 blocks
==12696== Rerun with --leak-check=full to see details of leaked memory 
==12696== 
==12696== For counts of detected and suppressed errors, rerun with: -v 
==12696== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

- Useful for returning a value from a function.
```C
#include <string.h> 
char *paritate(int n) {
	char *rezultat = NULL; 
	rezultat = malloc(8 * sizeof(char)); 
	if ((n % 2) == 0) { 
		strcpy(rezultat, "par"); 
	} else { 
		strcpy(rezultat, "impar"); 
	} 
	
	return rezultat; 
}
```