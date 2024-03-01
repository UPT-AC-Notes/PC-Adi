- Slide 218 [[CursPC.pdf]]
- In C, strings are character arrays that always end in the **NULL character** ( ``'\0'``, ``0x00``).
- If a string doesn't end in the NULL character, it's simply a character array, not a string.

```C
char mystring1[] = {'s','a','l','u','t',0};
char mystring2[] = "salut";
# This is different
char *mystring3 = "salut";
```

- When creating a character array with fixed size, we need to be aware that the last element needs to be the NULL character, so we need to have space for it.

- Declaration of variable ``mystring3`` is not the same thing as ``mystring2`` and ``mystring1``, because there we declare a pointer to a constant that's inside the ``.code`` or ``.text`` memory zone and can only be read (``read-only``). In this case, if we try to modify the character array, a **runtime** error will be thrown. The compiler may or may not throw a warning depending on the version.

- A null (empty) string will occupy 1 byte in memory, since the ``'\n'`` character still needs to be stored: ``char c[] = ""``

- ``char t[3]="tx"`` is a correct syntax, but ``char t[3]="txt"`` will produce buffer overflow. 
- ``char t[]="txt"`` is equivalent to ``char t[4]="txt"``

- To read string with ``scanf``, we can use the ``%s`` modifier. However we need to specify the **MAX SIZE**.

- The following code presents a **BIG RISK** of buffer overflow, since a string of any length can be entered to STDIN.
```C
#include <stdio.h> 
int main(void) { 
	char text[30]; 
	printf ("Scrieti un sir:"); 
	scanf ("%s", text); 
	printf ("Sirul introdus este: %s\n", text); 
	
	return 0; 
}
```

- We can solve this by specifying the maximum size in the ``scanf`` function:
```C
#include <stdio.h> 
int main(void) { 
	char text[30]; 
	printf ("Scrieti un sir:"); 
	scanf ("%29s", text); 
	printf ("Sirul introdus este: %s\n", text); 
	
	return 0; 
}
```
- In this case, the ``scanf`` function consumes a maximum of 29 characters, before finishing. The rest of the characters in STDIN are left unconsumed and can be read after.

- This code is safe, but it reads 80 characters and doesn't place the NULL character at the end of the array. That's the difference between "%s" and "%c" directives. In this case, the text array should not be printed nor used as an argument to any functions.
```C
#include <stdio.h> 
int main(void) { 
	char text[80]; 
	scanf("%80c", text); 

	return 0;
}
```

- The function ``char *gets(char *s)`` is legacy content and was only kept for backwards compatibility. Modern versions of ``gcc`` won't even let you compile using this function without a lot of flag arguments. The reason this function is not used anymore is its vulnerability to **buffer overflows**, since no MAX_SIZE can be specified.

- The function ``char *fgets(char *s, int size, FILE *stream)`` reads a string until EOF or a new line (the new line character is also added to the string), adding a NULL character at the end. 
	- It reads maximum ``size-1`` characters.
	- It returns ``s`` if a minimum of 1 character was read, otherwise ``NULL`` if an error appeared and no character was read.
- It's very important to check if the result of ``fgets`` is not null since otherwise we would print the ``text`` variable which is uninitialised. 
```C
#include <stdio.h> 
int main(void) { 
	char text[20]; 
	printf ("Scrieti o linie: "); 
	if (fgets(text, 20, stdin) != NULL) { 
		printf ("Linia: %s\n", text); 
	} else { 
		printf ("error\n"); 
	} 
	
	return 0; 
}
```


- For functions from the `string.h` library:
	- They expect a NULL character at the end of each string, otherwise they will corrupt the memory (buffer overflow) by trying to search for it.
	- They expect arrays to be initialised, otherwise they will again corrupt the memory
	- It's the programmer's responsibility to use these functions correctly, not passing NULL or unallocated pointers to them. They do not have any error handling implemented.
	- Each function has a ``man`` page and should be referenced for correct use.

- ``size_t`` is defined in the ``stddef.h`` library as an unsigned integer (size being dependent on the architecture)

## Strlen
- ``size_t strlen(const char *s);``
	- Returns the length of a character array by counting until a NULL character is found. The NULL character at the end is **NOT** counted.
	- It doesn't verify if the provided string is NULL, in this case a segmentation fault will be thrown since it will start from address zero trying to find the NULL character.
```C
#include <stdio.h> 
#include <string.h> 
int main(void) { 
	char t[64] = "text"; 
	int n = 0; n = strlen(t); 
	printf ("strlen - %d\n", n); 
	n = sizeof(t); 
	printf ("sizeof - %d\n", n); 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
strlen - 4 
sizeof - 64 
valy@staff:~/teaching$
```
- Sizeof here returns the size of the array(**IN MEMORY**, not the same as ``strlen``) and doesn't have to be divided by ``sizeof(char)`` since the size of a char is only 1 byte, so we would divide by 1.


## Strcpy
- ``char *strcpy(char *dest, const char *src);`` 
	- Copies the ``src`` string until it hits the NULL character at the end. It also places ``'\0'`` at the end of ``dest``.
	- It doesn't (and it **can't**) verify that ``dest`` has enough space to fit the copied content.
	- It doesn't verify if one or both of the provided arguments is the NULL pointer. It will try reading/writing from ``address 0`` which will result in a segmentation fault.
	- Memory zones of the two arrays should **NOT** collide.
```C
#include <stdio.h> 
#include <string.h> 
int main(void) { 
	char text[] = "ana are mere"; 
	char text2[128] = "text"; 
	printf ("%s\n", text2); 
	strcpy(text2, text); 
	printf ("%s\n", text2);
	 
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
text 
ana are mere 
valy@staff:~/teaching$
```


## Strcat
- ``char *strcat(char *dest, const char *src);``
	- It concatenates ``src`` to ``dest``. (adds ``src`` at the end of ``dest``) and adds ``'\0'`` at the end.
	- It doesn't (and it **can't**) verify that ``dest`` has enough space to fit the concatenated content.
	- It doesn't verify if one or both of the provided arguments is the NULL pointer. It will try reading/writing from ``address 0`` which will result in a segmentation fault.
	- Memory zones of the two arrays should **NOT** collide.
```C
#include <stdio.h> 
#include <string.h> 
int main(void) { 
	char text[128] = "ana are mere"; 
	char text2[128] = " si pere"; 
	printf ("%s\n", text); 
	strcat(text, text2); 
	printf ("%s\n", text); 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
ana are mere 
ana are mere si pere 
valy@staff:~/teaching$
```

## Strchr

- ``char *strchr(const char *s, int c);``
	- Searches for the first occurrence of the character ``c`` in the string ``s``
	- Returns a pointer to the first occurrence or NULL if it didn't find it.
	- The pointer returned is from the same memory block allocated for the provided string.
	- It doesn't verify if the provided address is NULL, in this case a segmentation fault will be thrown since it will start from address zero trying to find the NULL character.
```C
#include <stdio.h> 
#include <string.h> 
int main(void) { 
	char text[128] = "ana are mere"; 
	char *result = NULL; 
	result = strchr(text, 'm'); 
	if (result == NULL) { 
		printf ("not found\n"); 
	} else { 
		printf ("found: %s\n", result); 
	} 
	
	return 0; 
}
```
Output:
```
valy@staff:~/teaching$ ./p 
found: mere 
valy@staff:~/teaching$
```


## Strstr
- ``char *strstr(const char *haystack, const char *needle);``
	- Searches for the first occurrence of the string ``needle`` in the string ``haystack``
	- Returns a pointer to the first occurrence or NULL if it didn't find it.
	- The pointer returned is from the same memory block allocated for the provided string.
	- It doesn't verify if the provided address is NULL, in this case a segmentation fault will be thrown since it will start from address zero trying to find the NULL character.
```C
#include <stdio.h> 
#include <string.h> 
int main(void) { 
	char text[128] = "ana are mere"; 
	char *result = NULL; 
	result = strstr(text, "are"); 
	if (result == NULL) { 
		printf ("not found\n"); 
	} else { 
		printf ("found: %s\n", result); 
	} 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
found: are mere 
valy@staff:~/teaching$
```

## Strcmp
- ``int strcmp(const char *s1, const char *s2);``
	- Compares the two strings and returns the following:
		- 0 - if the strings are identical
		- > 0 - if s1 is lexicographically bigger than s2 (if the first character that doesn't match has a bigger ASCII value in s1 than in s2)
		- < 0 - if s2 is lexicographically bigger than s1 (if the first character that doesn't match has a smaller ASCII value in s1 than in s2)
	-  It doesn't verify if one or both of the provided arguments is the NULL pointer. It will try reading/writing from ``address 0`` which will result in a segmentation fault.
```C
#include <stdio.h> 
#include <string.h> 
int main(void) { 
	char text[128] = "ana are mere"; 
	int result = 0; 
	result = strcmp(text, "ana are dere"); 
	printf ("%d\n", result); 
	result = strcmp(text, "ana are mere"); 
	printf ("%d\n", result); 
	result = strcmp(text, "ana are pere"); 
	printf ("%d\n", result); 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
9 
0 
-3 
valy@staff:~/teaching$
```


## Strncpy
- ``char *strncpy(char *dest, const char *src, size_t n);``
	- It works similarly as ``strcpy``, but only copies a maximum of ``n`` elements from ``src`` to ``dest``.
	- If none of the ``n`` characters copied were a ``'\0'``, it will be placed at the end.


## Strncat
- ``char *strncat(char *dest, const char *src, size_t n);``
	- It works similarly as ``strcat``, but only concatenates a maximum of ``n`` elements from ``src`` and places ``'\0'``.
	- ``dest`` needs to have space for another ``n`` characters and ``'\0'``

## Strncmp
- ``int strncmp(const char *s1, const char *s2, size_t n);``
	- It works similarly as ``strcmp``, but only compares the first ``n`` characters from ``s1`` and ``s2``.

------------------------------------------------------------------------
# Functions to manipulate memory zones
## Memset
- ``void *memset(void *s, int c, size_t n);``
	- Sets ``n`` bytes from the memory zone ``s`` to the value ``n``.
	- Returns a pointer to the memory zone ``s``.
	- Since ``s`` is a void pointer type, it can accept any type of pointer.
	- It doesn't verify if ``s`` is a NULL pointer or it's uninitialised.
```C
#include <string.h> 
int main(void) { 
	char tab[10]; 
	int tab_int[10]; 
	memset(tab, 0, 10); 
	memset(tab_int, 0, 10 * sizeof(int)); 
	
	return 0; 
}
```


## Memcpy
- ``void *memcpy(void *dest, const void *src, size_t n);``
	- Copies ``n`` bytes from ``src`` to ``dest``
	- ``src``, being a const pointer, cannot be modified by the function and can also be a static string ("test").
	- It doesn't verify if ``src`` or ``desr`` are a NULL pointer or it's uninitialised.
	- The two memory zones should NOT overlap.
```C
#include <string.h> 
int main(void) { 
	char array1[5] = {0, 1, 2, 3, 4}; 
	char array2[10]; 
	memcpy(array2, array, 5); 
	
	return 0; 
}
```

## Memmove
- ``void *memmove(void *dest, const void *src, size_t n);``
	- It works the same as ``memcpy`` but allows the two memory zones to overlap.

## Memcmp
- ``int memcmp(const void *s1, const void *s2, size_t n);``
	- It compares the two memory blocks and returns:
		- `0` if the values in the two memory zones are equal
		- the difference between the first value that isn't equal in the memory blocks (`s1-s2`)
	- Each byte is compared
	- It doesn't verify if ``s1`` or ``s2`` are a NULL pointer or it's uninitialised.

## Atoi
- ``int atoi(const char *nptr);``
	- Converts an 'ASCII' to 'Integer'
	- The string ``nptr`` should contain a number in base 10
	- Returns an integer with the converted value from the string
	- The conversion ends when it reaches a character that's not a digit (or ``'\n', ' ', '\0``)
```C
#include <string.h> 
#include <stdio.h> 
#include <stdlib.h> 
int main(void) { 
	char text[10]; 
	int nr = 0; 
	printf ("introduceti un numar:"); 
	scanf("%9s", text); 
	nr = atoi(text); 
	nr = nr + 1; 
	printf ("numarul incrementat este: %d\n", nr); 
	
	return 0; 
}
```
Output
```C
valy@staff:~/teaching$ ./p 
introduceti un numar:17 
numarul incrementat este: 18 
valy@staff:~/teaching$
```

## Strtol
- ``long int strtol(const char *nptr, char **endptr, int base);``
	- Converts a string that contains a number in a specified base (<=36) 
	- The function writes in ``endptr`` a pointer to where the conversion has stopped in ``nptr``
	- The conversion ends when it reaches a character that's not a digit (or ``'\n', ' ', '\0``)
```C
#include <stdio.h> 
#include <stdlib.h> 
int main(void) { 
	char text_dec[]="156320"; 
	char text_hex[]="A09BD25"; 
	char text_bin[]="01001001"; 
	int n = 0; 
	n = strtol(text_dec, NULL, 10); 
	printf ("%d\n", n); 
	n = strtol(text_hex, NULL, 16); 
	printf ("%d - %X\n", n, n); 
	n = strtol(text_bin, NULL, 2); 
	printf ("%d\n", n); 
	
	return 0; 
}
```
Output
```Bash
valy@staff:~/teaching$ ./p 
156320 
168410405 - A09BD25 
73 
valy@staff:~/teaching$
```

- Other string functions:
```C
long long int strtoll(const char *nptr, char **endptr, int base); 
long atol(const char *nptr); 
long long atoll(const char *nptr); 
double atof(const char *nptr); 
double strtod(const char *nptr, char **endptr); 
float strtof(const char *nptr, char **endptr); 
long double strtold(const char *nptr, char **endptr);
```

- Solved C string exercises:
	- [[Exercises C Strings]]