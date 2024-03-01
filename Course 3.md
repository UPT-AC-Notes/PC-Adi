- The switch statement cannot compare to float/double values
- Variables declared inside blocks
	- Memory for them is only allocated **ONCE**
	- If initialisation is specified, then they will be initialised each loop cycle 
```C
	#include <stdio.h> 
	int main(void) { 
		int i = 0; 
		for (i = 0; i < 10; i++) 
		{ 
			int n = 0; n++; 
			printf ("%d\n", n); 
		} 
	}
```
- In this case, memory for the variable n is allocated only once, but it's initialised every loop cycle. If some step in the cycle the variable wasn't initialised, it would hold the last value it had.
- The 'best' C standard is ANSII C
- A function's name is an address to where that block of code starts in memory.
	- Splitting the program into functions is necessary to fit **DRY** principles and code duplication
- In **C**:
	- `void foo()` means "a function `foo` taking an unspecified number of arguments of unspecified type"
	- `void foo(void)` means "a function `foo` taking no arguments"
- In **C++**:
	- `void foo()` means "a function `foo` taking no arguments"
	- `void foo(void)` means "a function `foo` taking no arguments"

- By writing `foo(void)`, therefore, we achieve the same interpretation across both languages and make our headers multilingual
- In **C** this code is possible and does now throw any error:
```C
#include <stdio.h>

// function with unknown number of arguments of unknown type
int testFunction(){
	return 1;
}

int main(void){

	printf("%d",testFunction(1,2,3,4,5));

	return 0;

}
```

- A lot of exam questions will be in this form: 'Where is variable X allocated/declared' (in heap, stack, .bass, .data, etc. )
- Check [[CursPC.pdf]] at slide 145 for a description of memory zones
- In **C**, parameters of function are only sent by value, so the following code is impossible and will throw an error, but possible and will work as expected in **C++**:
```C
void testFunction(int& a){
	a = a + 1;
}
```
- The **static keyword** moves a variable from the stack memory zone to .bss (if it's not initialised) or .data (if it's initialised) - **slide 153**
- **ulimit -a** display maximum stack size [[ulimit.png]] (in this case 8176 kb)
	- If the stack limit is exceeded, the program is killed.
	
- This program **won't run** because it exceeds the stack size
```C
#include <stdio.h>

int main(void){

	int tab[4000000];

	return 0;

}
```

- This program **will run** because the variable is declared in the .bss memory zone
```C
#include <stdio.h>

int main(void){

	static int tab[4000000];

	return 0;

}
```

- You can use the **size** CLI tool to check the memory allocated (for each zone) of an executable
	- example: [[size.png]]