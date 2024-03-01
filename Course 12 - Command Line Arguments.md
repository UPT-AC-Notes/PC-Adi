
- Example syntax: ``executable_program arg1 arg2 arg3 arg4`` (e.g.``ls -la``)
- This feature is supported by the OS, so that any executable program can accept CLI arguments.
```C
int main(int argc, char **argv) { }
// or
int main(int argc, char *argv[]) { }
```
- ``argc`` represents the number of arguments received (stands for argument count)
- ``argv`` (stands for argument vector) is a string array that holds each argument received, each of them in string format (ending with ``'\0'``) 
- Always test ``argc`` before accessing ``argv``, otherwise we may access unallocated memory that results in a segmentation fault

```Bash
# When running a program like this:
./prog ana are mere
# argc will be 4, because the argument vector also contains the command that launched the priogram
# argv[0] = "./prog"
# argv[1] = "ana"
# argv[2] = "are"
# argv[3] = "mere"

# It's wrong to refer to the first argument as the 'program name':
/home/adrian/prog
# argc will be 1
# argv[0] = "/home/adrian/prog"
```


```C
#include <stdio.h> 
int main(int argc, char **argv) { 
	for (int i = 0; i < argc; i++) { 
		printf ("argumentul %d : %s\n", i, argv[i]); 
	} 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p ana are mere 
argumentul 0 : ./p 
argumentul 1 : ana 
argumentul 2 : are 
argumentul 3 : mere 
valy@staff:~/teaching$
```

- We can also have spaces in arguments if we surround them by quotation marks:
```Bash
./prog "ana are mere"
# argc will be 2
# argv[0]= "./prog"
# argv[1] = "ana are mere"
```