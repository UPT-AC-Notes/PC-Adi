## Pointers

- Slide 190 [[CursPC.pdf]]
- We use unary operator '&' to get the address of a variable.
	- ``&a --> int*``
- We use ``printf("%p")`` to display the address of a variable
```C
#include <stdio.h> 
int global; 
int main(void) { 
	int local; 
	static int local_static; 
	printf ("address of local \t\t%p\n", &local); 
	printf ("address of local_static \t%p\n", &local_static); 
	printf ("address of global \t\t%p\n", &global); 
	
	return 0; 
}
```

```bash
valy@staff:~/teaching$ ./p 
address of local 0x7ffebdf7835c 
address of local_static 0x5613f0500034 
address of global 0x5613f0500038 
valy@staff:~/teaching$
```

- We observe that the global and the static local variable are very close to each other in memory (since they're both in the ``.bss`` memory zone). The local variable is further apart since it's stored in the stack.

- We can't directly assign values to pointers. Chances that the value we choose is free in memory are very very low. The OS will figure out that we're trying to access memory that wasn't assigned to it and will kill the program. So, ``int *p; p = 0x....`` should never be used.

```C
int global; 
int *ptr_global; 
char ch; 
int main(void) { 

	int *ptr1; 
	int *ptr2; 
	int *ptr3; 
	char *ptr4; 
	int x; 
	
	ptr1 = &global; 
	ptr2 = &x; 
	ptr3 = ptr2; 
	ptr4 = &ch; 
	
	return 0; 
}
```
- In this example, the global variables are stored in the ``.bss`` memory zone, and the local variables that store their addresses are located in the stack. Their memory address will be inside the stack, but the value they hold will point to the ``.bss`` memory zone since they store the address of the global variables. The ``ptr2`` variable is stored in the stack and the value it holds points to the stack too (since it stores the address of the local variable ``x``).

- Using pointers, we can modify the value of other variables. We store the address of the variable we want to modify as a pointer, and then we **dereference** the pointer (using the unary ``*`` operator) to access its value
```C
	int a = 10;
	int *p = &a;
	*p = 11;
	// a will now be 11
```

- Const doesn't work the same for pointers:
```C
const int n = 3;
n = 4; // throws an error
------------------------
const int *p;
p = &a; // works
*p = 5; // throws an error
```

- If we declare a pointer as const, the constant applies to the value that the pointer references, not the pointer itself.

- We need to be **very careful to uninitialised pointers**. They may **corrupt memory** when accessing data from them or writing to them.
```C
// never do this
int *p;
*p = 8;
// or
a = *p;
```
- Another example of **corrupting data** is when we assign pointers with different types.
```C
int main(void) { 
	char ch; 
	char *p = &ch; 
	int *a; a = p; 
	(*a)++; 
	return 0; 
}
```

- In both cases, the compiler throws a warning.

- When the program throws ``Segmentation fault`` it didn't end by itself, the OS figured out that the program is accessing memory that wasn't assigned to it and kills it. This doesn't happen before damage has occurred to the memory zones corrupted.
	- On a microcontroller that has no memory management we can delete memory zones by mistake.
	- That's why we always use ``-Wall`` flag when compiling, to see all the warnings

- The **void pointer** exists and can be used, but not dereferenced. It matches every data type. The void pointer is used when we want to work with generic types and we deduce the datatype in another way:
```C
void *p;
p = &a;
*p; // throws an error
```

- The **null pointer** exists and references the memory zone zero:
```C
int *p = NULL;
*p = 3; //throws an error
```

- In functions, we use pointers to change variables passed by argument. Unlike C++, we can't pass a variable by reference ``void test(int &a)`` since that's a shortcut for pointer parameters added in C++
```C
void modifyNumber(int *p){
	*p = 200;
}

int main(void){

	int n = 100;
	modifyNumber(&n);
	// n will now be 200

	return 0;
}

```

- Another use case for passing pointers as function arguments is that the **data passed will not be copied on the stack** anymore. That will result in less execution time (less pushes, less pops). If we don't want to be able to modify the data passed, but still don't want to copy the entire data on the stack, we simply make the pointer **const**.

- Functions can return pointers. A big mistake is to return a pointer to a variable that was declared inside the function (that value will be popped form the stack when the function finishes). The correct way is to return the pointer to a global or static variable to avoid returning an uninitialised memory address.

## Arrays
- Slide 208 [[CursPC.pdf]]

```C
int a1,a2,a3,a4;
int a[4];
```
- This is usually the same thing, but the **second** declaration **guarantees** that the variables are stored **successive** in memory. That can sometimes not be the case with the first declaration.

- When declaring an array ``arr[MAX]``, the available indexes will be ``0...MAX-1``:
	- ``address(tab[i]) = address(tab[0]) + i*sizeof(tab[0])``
	- ``i=0...MAX, tab[0] = tab``

- The ``sizeof()`` function returns the size of the specified variable in memory:
	- ``int arr[N];``
	- The occupied size of ``arr`` is ``N * sizeof(int)``
	- ``sizeof(arr) = N * sizeof(int) = sizeof(int) * N``

- Equivalent operations with pointers and arrays:
	- ``tab[0] <=> *tab``
	- ``int tab[] <=> int* tab``
	- ``&tab[i] <=> tab + i``
	- ``*(tab+i) <=> tab[i]``

- Addition with pointers:
```C
int *p;
p = 0x1000;
p = p + 1 <=> p = p + 1 * sizeof(int)
=> p will be 0x1004

type *p;
p = p + n <=> p = p + n  * sizeof(type)

```

- Subtraction with pointers:
```C
int *p, *q;
q = &tab[2];
p = &tab[0];
=> q-p = 2 // 2 is how many elements of the assigned type would fit there
```

- We can also advance in an array with the number of bytes by forcing the array to be of size char (1 byte): ``(char*)a + k * sizeof(tip)``

- Iterating an array with iterators vs. with pointers
```C
int main(void){
	int tab[10]; 
	int x = 0; 
	int i = 0; 
	for (i = 0; i < 10; i++) { 
		x = tab[i]; 
	} 
	return 0;
}
//////// VS. ///////
int main(void){
	int tab[10]; 
	int x = 0; 
	int i = 0; 
	for (i = 0; i < 10; i++) { 
		x = *(tab + i); 
	} 
	return 0;
}
```

- Traversing an array using pointer operations
```C
int main(void) { 
	int tab[10]; 
	int *p; 
	int x; 
	for (p = tab; p < tab + 10; p++) {
		x = *p; 
	} 
	return 0; 
}
```

- When transmitting an array to a function, we **cannot** determine its size:
```C
void functie(int tab[]); -> sizeof(tab) = 8 (64 bit address)
void functie(int *tab); -> sizeof(tab) = 8 (64 bit address)

void functie(int tab[10]); -> sizeof(tab) = 8 (64 bit address)
// the last function's specified size cannot be used, that's why it is usually not written in code. besides that, it's confusing when calling the function
```

```C
void functie(int tab[10]){
	// we see 10 as a specified size and we think that's the size of every argument that this function is called with, when that's not the case
	for (int i=0; i<10; i++){
		tab[i]++;
	}
}
int tab[4];
functie(tab); -- will access uninitialised memory
// this bug was inside the linux kernel at some point
```

- Therefore, we need to pass the array size as an argument alongside the actual array:
```C
void functie(int tab[], unsigned int size); 
void functie(int *tab, unsigned int size);
```

- There's a big difference between ``int a=2`` and ``int a; a=2;``. For the first one, which is only an initialisation, code is not generated. The second instruction is an assignment and will generate code, even though they both do the same thing.

- Copying two arrays:
```C
void copyArray(int *src, int *dst, int size) {
	for (int i = 0; i < size; i++) { 
		dst[i] = src[i]; 
	} 
}
```
- A common **mistake** is writing **dst = src**, which sets the address of ``dst`` to be the same as the ``src``. Any modification to ``dst`` will also modify ``src``.

- !!!! Common **mistakes** with pointers: !!!!

   -  Wrong iteration of the array (starting from index 1 and reaching SIZE)
```C
int main(void) { 
	int tab[SIZE]; 
	for (int i = 1; i <= SIZE; i++) {
		tab[i] = 0; 
	}
	return 0; 
}
```

   - Wrong iteration of the array (starting correctly from index 0, but reaching SIZE)
```C
int main(void) { 
	int tab[SIZE]; 
	for (int i = 0; i <= SIZE; i++) { 
		tab[i] = 0; 
	} 
	return 0; 
}
```

   - Wrong ending point: ``sizeof(tab)`` returns the size of an int. The correct variant here would be ``SIZE * sizeof(int)``
```C
int main(void) { 
	int tab[SIZE]; 
	for (int i = 0; i <= sizeof(tab); i++) { 
		tab[i] = 0; 
	} 
	return 0; 
}
```

   -  Declaring a local array that's bigger than the stack size:
```C
void functie1(void) { 
	int tab[10000000]; 
}
```
   -  Declaring a local array means every time the function is called the stack needs to push and pop the elements
```C
void functie1(void) { 
	int tab[10000000]; 
}
int main(void){
	// very slow calls
	for(int i=1;i<=10000;i++){
		functie1();
	}
	return 0;
}
```

- A good alternative is to declare the array global, or static inside the function.
- Solved exercises:
	- [[Exercises Arrays]]