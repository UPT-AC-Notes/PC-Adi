## Standard Error Codes
- In C standard libraries there are multiple mechanisms to handle errors by their type (``<errno.h>``)
- Standard function write their last error code (``0 means ok by default``) in the variable ``errno``.
```C
#include <stdio.h> 
#include <errno.h>
#include <stdlib.h> 
int main(void) { 
	char text[] = "1234"; 
	strtol(text, NULL, -1); // invalid base
	printf ("%d\n", errno); 
	// if we would print errno here again, it would be 0, sinc the 'printf'' function also write its error/success code to errno
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
22 
valy@staff:~/teaching$
```

- We can check the ``man page`` for ``errno`` to find all error codes.
- From ``man strtol``:
```Bash
RETURN VALUE
       The strtol() function returns the result of the conversion, unless the value would underflow or overflow.  If an underflow oc-
       curs,  strtol()  returns  LONG_MIN.  If an overflow occurs, strtol() returns LONG_MAX.  In both cases, errno is set to ERANGE.
       Precisely the same holds for strtoll() (with LLONG_MIN and LLONG_MAX instead of LONG_MIN and LONG_MAX).

ERRORS
       EINVAL (not in C99) The given base contains an unsupported value.

       ERANGE The resulting value was out of range.

       The implementation may also set errno to EINVAL in case no conversion was performed (no digits seen, and 0 returned).
```

```C
#include <stdio.h>  
#include <stdlib.h>  
#include <errno.h>  
int main(void){  
    int n;  
    n = strtol("1230", NULL, 100);  
    if(errno == EINVAL){  
        printf("Incorrect base\n");  
    }else{  
        printf("%d\n",n);  
    }  
    
  return 0;
}
```

- There are easier methods to work with ``errno``:
- ``void perror(const char *s);``
```C
#include <stdio.h> 
#include <stdlib.h> 
int main(void) { 
	char text[] = "1234"; 
	strtol(text, NULL, -1); 
	
	perror("my error"); 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
my error: Invalid argument 
valy@staff:~/teaching$
```

- ``char *strerror(int errnum);``
```C
#include <stdio.h> 
#include <errno.h> 
#include <stdlib.h> 
#include <string.h> 
int main(void) { 
	char text[] = "1234"; 
	strtol(text, NULL, -1); 
	char *error_string; error_string = strerror(errno); 
	
	printf ("%s\n", error_string); 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
Invalid argument 
valy@staff:~/teaching$
```

- ``void exit(int status);``
```C
#include <stdio.h> 
#include <errno.h> 
#include <stdlib.h> 
#include <string.h> 
int main(void) { 
	char text[] = "1234"; 
	strtol(text, NULL, -1); 
	if (errno != 0) { 
		perror("my error"); 
		exit(-1); 
	} 
	printf ("message\n"); 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
Invalid argument 
valy@staff:~/teaching$
```
## Files
- [[Exercises Files]]
- A file is a stream of bytes usually stored in a non-volatile storage media.
- Information is stored in an unstructured way. The program chooses the way to interpret those bytes (ex. Image files, Music files, Text files, etc.)
- File extensions are just a convention to help programs interpret specified files. Under the hood, all files are the same, even if the file extension is different.
- A program cannot directly access a file, it has to ask the OS to do so. That's because the OS created it in a specific structure (FAT-32, NTFS, etc.)

- ``FILE *fopen(const char *pathname, const char *mode);``
	- The first arguments contains the relative or absolute path to the file
	- The second argument contains the mode in which the file should be opened:
		- ``r`` (read) - file will be opened in READ-ONLY mode. If the file doesn't exist, it will throw an error.
		- ``w`` (write) - file will be opened in WRITE-ONLY mode. If the file doesn't exist, it will be created. If the file already exists, the existing content will be **truncated**.
		- ``a`` (append) - file will be opened in APPEND mode. If the file doesn't exist, it will be created. If the file already exists, the new content will be written continuous to the previous content.
		- ``+`` - shortcut ( ``r+ -> rw``, ``w+ -> rw`` )
		- ``b`` - file will be opened in BINARY mode
	- Returns a pointer to a ``FILE`` struct pointer. Its fields are hidden and unaccessible.
		- This pointer represents an unique way to identify the opened file in the program's scope until the end of the program or until it is explicitly closed. 
		- This pointer will be passed to specialised functions that make file operations.
	- Returns ``NULL`` in case of error (file does not exist, permission denied, etc.)

- ``int fclose(FILE *stream);`` 
	- Allows us to close the opened file.
	- It's a bad practice to leave the files unclosed. However, the OS will take care of this and close any opened files when the program ends. 
	- The function is not only used to close the file, but effectively write the data to the file. The OS doesn't physically write the data instantly, since it tries to optimise the writes. The ``fclose`` function also calls the ``flush`` function, which forces the OS to write the data to the file.
```C
#include <stdio.h>  
#include <stdlib.h>  
#include <errno.h>  
int main(void){  
    FILE *f = NULL;  
    f = fopen("scrisoare.txt", "r");  
    if(f == NULL){  
        perror("Eroare la deschidere fisier");  
        exit(-1);  
    }  
    printf("Am deschis fisierul\n");  
    if(fclose(f) != 0){  
        perror("Eroare la inchidere fisier:");  
        exit(-2);  
    }  
    printf("Am inchis fisierul\n");  
  
    return 0;  
}
```
### Read-Write Operation
- The ``FILE`` struct contains a cursor that points to the next byte that was't read. At first, it will point to the first character. This cursor is automatically updated when something is written/read from the FILE. There are also some functions to manually modify the cursor. 
- ``int fgetc(FILE *stream);``
	- Reads a character from the file.
	- Returns the character that was read or ``EOF`` in case of error or the file has reached an end.
	- After each operation, the cursor is incremented
```C
uint32_t count_chars(const char *file) { 
	FILE *f = NULL; 
	uint32_t result = 0; 
	int ch; 
	
	f = fopen(file, "r"); 
	if (f == NULL) { 
		perror(NULL); 
		return 0;
	} 
	
	while ((ch = fgetc(f)) != EOF) { 
		result++; 
	} 
	
	fclose(f); 
	
	return result; 
}
```

- ``char *fgets(char *s, int size, FILE *stream);``
	- Reads a character array until ``EOF`` or ``'\n'`` is met and adds the ``'\0'`` character at the end.
	- Reads a maximum of ``size-1`` (because it also needs to have space to store the null character) characters and stores them in ``s``.
	- If it stops because ``'\n'`` was met, it also adds it to the array.
	- Returns ``s`` if a minimum of one character was successfully read, otherwise ``NULL`` if an error was encountered or no character was read.
```C
#include <stdio.h> 
#include <stdint.h> 
#include <stdlib.h> 
int main(void) { 
	FILE *f = NULL; 
	char text[20]; 
	if ((f = fopen("file.txt", "r")) == NULL) { 
		perror(NULL); return 0; 
	} 
	if (fgets(text, 20, f) != NULL) { 
		printf ("Linia: %s\n", text); 
	} else { 
		perror(NULL); 
	} 
	fclose(f); 
	
	return 0; 
}
```

- ``int fputc(int c, FILE *stream);``
	- Writes the character ``c`` in the file
	- Increments the cursor by one
	- Returns the written character or ``NULL`` if an error was encountered

- ``int ungetc(int c, FILE *stream);``
	- Pushes the ``c`` character back into the file stream where it is available for subsequent read operations.  Pushed-back characters will be returned in reverse order
	- Only one pushback is guaranteed. 
	
- ``int fputs(const char *s, FILE *stream);``
	- Writes the string ``s`` to the file stream. 
	- It doesn't write the ``NULL`` character present at the end of the string
	- Increments the cursor by adding the length of the written string
	- Returns a non-negative number on success and ``EOF`` in case of an error.

- ``int feof(FILE *stream);``
	- Tests if the file's cursor reached ``EOF``. If this happened it returns a non-zero value
		- If the cursor didn't reach ``EOF``, the function returns 0.

- Additional file-write functions:
	- ``fprintf`` - works exactly as ``printf``, but writes the output tot a FILE.
	- ``fscanf`` - works exactly as ``scanf``, but it reads from a FILE.
	
	- ``sprintf`` - works exactly as ``printf``, but writes the output tot a string.
	- ``sscanf`` - works exactly as ``scanf``, but it reads from a string.