## Enum
- Declaration:
```C
enum my_enum{
	constanta_1,
	constanta_2,
	constanta_3
};
```
- When you declare an anonymous enum like this, the compiler essentially treats the enumerator names as if they were simple integer constants.
- Usage:
```C
int main(){
	enum my_enum var1; // la baza var1 este un int, unde indexarea incepe de la 0
	// constanta_1 = 0, constanta_2 = 1, constanta_3 = 2
	var1 = constanta_2;
	if(var1 == constanta_3){
		// 
	}
	var1 = var1 + 1; // => var1 = constanta_3
	var1 = 17; // this works but has no constant correspondence
}
```

```C
#include <stdio.h>
enum saptamana{
	luni = 4,
	marti = 78,
	miercuri = 15,
	joi = 0,
	vineri = 34,
	sambata,
	duminica
};
// by default, if the value is not specified, it will just be incremented by one
// that means here sambata will be 35
int main(void){
	enum saptamana zi;
	zi = marti;
	printf("%d\n", zi);
	if(zi == marti){
		printf("marti\n");
	}
	// it is impossible to convert an enum to string(its correspondence) since unde the hood they're just ints
	switch(zi){
		case luni:
		case marti:
			printf("luni sau marti\n");
			break;
		default:
			printf("restul\n");
			break;
	}

	// we can also use enums as pointers
	enum saptamana *zi2;
	zi2 = &zi;
	zi2 = malloc(sizeof(enum saptamana));
	
	free(zi);
	
	return 0;
}
```
- ``sizeof(enum)`` will be 4, since it's a signed integer.

## Struct
- Memory for each field is allocated independently.
- Declaration:
```C
struct my_struct{
	int a;
	char text[100];
	float f;
	char *p;
}; // this occupies a total of 116 bytes
```
- Usage:
```C
int main(void){
	struct my_struct s;
	
	s.a=15;
	strcpy(s.text, "hello");
	if(s.f>3.15){
		//...
	}
	s.p = malloc(sizeof(char));
}
```

- However,
```C
#include <stdio.h>
#include <stdint.h>
struct my_struct{
	uint32_t a;
	char ch;
}
int main(void){
	struct my_struct str;
	printf("%ld\n", sizeof(str));
}
```
- !!!! This program will display 8 even though ``sizeof(uint_32) + sizeof(char) = 5``. This happens because of the padding left in memory. After the ``char`` memory is allocated, a padding of ``3 bytes is left`` to acomodate further allocation of variables. 

- In fact, the size will be the same for this struct as well:
```C
#include <stdio.h>
#include <stdint.h>
struct my_struct{
	uint32_t a;
	char ch1;
	char ch2;
	char ch3;
	char ch4;
}
int main(void){
	struct my_struct str;
	printf("%ld\n", sizeof(str));
}
```

- Another alignment problem (even though these two structs contain essentially the same fields, they will occupy different sizes):
```C
#include <stdio.h>
#include <stdint.h>

struct my_struc2{
	uint16_t b;
	uint32_t a;
	char ch;
}

struct my_struc{
	uint32_t a;
	uint16_t b;
	char ch;
}

int main(void){
	struct my_struct str;
	struct my_struct2 str2;
	
	printf("%ld\n", sizeof(str));
	printf("%ld\n", sizeof(str2));
	return 0;
}
```
Output:
```Bash
8
12
```
- Struct2 will be allocated like this:
	- (uint16_t)2bytes
		- -- because uint32_t needs to be stored at a address that a multiple of 4, a padding of two bytes will be needed
		- (padding)2 bytes
			- (uint32_t)4 bytes
				- (char)1 byte
					- (padding)3 bytes
	- This results in 12 bytes
	
- Struct1 will be allocated like this:
	- (uint32_t)4bytes
		- (uint16_t)2 bytes
			- (char)1 byte
				- (padding)1 byte

- This can be very inefficient when declaring an array of structs. The solution is to assure that the fields are in order of their multiples, so the least amount of padding is allocated.

- By default, compilers leave struct allocation and alignment alone, but by specifying a flag it can realign them.
```C
#include <stdio.h>

// This pragma is added to show the effect of -fpack-struct
#pragma pack(1)

// Define a structure with three members
struct ExampleStruct {
    char a;
    int b;
    char c;
};

// Restore the default packing
#pragma pack()

int main() {
    // Print the size of the structure
    printf("Size of struct: %zu\n", sizeof(struct ExampleStruct));

    return 0;
}
```
- In this example, the `#pragma pack(1)` directive is used to specify a packing of 1 byte for the structure. This means that the compiler should try to pack the structure members as tightly as possible, with no padding between members. The `#pragma pack()` directive is then used to restore the default packing after the structure definition.
- When you compile this code with GCC, you can use the `-fpack-struct` flag to achieve the same effect (alternative to ``pragma`` directive):
```Bash
gcc -fpack-struct example.c -o example
```
- Keep in mind that using tight packing can have performance implications on some architectures, as accessing unaligned data may be slower than accessing aligned data. Additionally, relying on specific struct packing might make your code less portable across different compilers and architectures. Therefore, it's generally a good practice to be cautious when using such options and to be aware of the potential implications for portability and performance.
#### Finding the distance between struct fields
- By using pointer difference
```C
#include <stdio.h> 
struct MyStruct { 
	int n; 
	char text[10]; 
	float f; 
}; 
int main(void) { 
	struct MyStruct str1; 
	printf ("deplasament n: %ld\n", (char*)&str1.n - (char*)&str1); 
	printf ("deplasament text: %ld\n", (char*)&str1.text - (char*)&str1); 
	printf ("deplasament f: %ld\n", (char*)&str1.f - (char*)&str1); 
	printf ("dimensiune structura: %ld\n", sizeof(str1)); 
	
	return 0; 
}

```
Output:
```Bash
valy@staff:~/teaching$ ./p 
deplasament n: 0 
deplasament text: 4 
deplasament f: 16 
dimensiune structura: 20 
valy@staff:~/teaching$
```
- By using the ``offsetof`` macro (``offsetof(tip_struct, nume_camp)``) from the ``<stddef.h>`` library:
```C
#include <stdio.h> 
#include <stddef.h> 
struct MyStruct { 
	int n; 
	char text[10]; 
	float f; 
}; 
int main(void) { 
	struct MyStruct str1; 
	printf ("deplasament n: %ld\n", offsetof(struct MyStruct, n)); 
	printf ("deplasament text: %ld\n", offsetof(struct MyStruct, text)); 
	printf ("deplasament f: %ld\n", offsetof(struct MyStruct, f)); 
	printf ("dimensiune structura: %ld\n", sizeof(str1)); 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ ./p 
deplasament n: 0 
deplasament text: 4 
deplasament f: 16 
dimensiune structura: 20 
valy@staff:~/teaching$
```

#### Initialisation

```C
struct MyStruct { 
	int n; 
	char text[10]; 
}; 
int main(void) { 
	struct MyStruct str3 = {78, "test"}; 
	
	return 0; 
}
```
- We don't have to set all the fields in the initialisation, but we can't skip one.

- The syntax above cannot however be used for assignment:
```C
struct MyStruct { 
	int n; 
	char text[10]; 
}; 
int main(void) { 
	struct MyStruct str3; 
	str3 = {65, "abc"}; 
	
	return 0; 
}
```
Output:
```Bash
valy@staff:~/teaching$ gcc -Wall -o p struct1.c 
struct1.c: In function ‘main’: 
struct1.c:10:10: error: expected expression before ‘{’ token 
	str3 = {65, "abc"}; 
		   ^ 
struct1.c:9:19: warning: variable ‘str3’ set but not used [-Wunused-but-set-variable] 
	struct MyStruct str3; 
					^~~~
valy@staff:~/teaching$
```

#### Assignment
- When assigning one struct's value to another, each value is copied in its corresponding type.
- Under the hood, a ``memcpy`` is performed to copy every field from one struct to another.
```C
struct MyStruct{
	int n;
	char text[10];
};

int main(void){
	struct MyStruct str1;
	str1.n = 123;
	strcpy(str1.text, "salut");
	struct MyStruct str2;
	str2 = str1; // equivalent to memcpy(&str2, &str1, sizeof(str1));
	
	return 0;
}
```
#### Operations
- We cannot use ``+, -, /, *, >, <, etc.`` operators between structs. We have to implement functions that can describe those operators (ex: ``addStructs(struct MyStruct a, struct MyStruct b)``). 

- The **->** UNARY operator was invented to replace ``*().`` **when using pointers to structures**.
```C
#include <stdlib.h> 
#include <string.h> 
struct MyStruct { 
	int n; 
	char text[10]; 
}; 
int main(void) { 
	struct MyStruct *str1; 
	str1 = malloc(sizeof(struct MyStruct)); 
	
	str1->n = 123; // same as (*str1).n = 123
	strcpy(str1->text, "salut"); 
	
	struct MyStruct str2; 
	str2 = *str1; 
	
	return 0; 
}
```

- The **->** operator has two roles:
	- To access a member of a struct
	- To dereference the member pointer
- In any other context, the operator generates a compile error.
- ``p->x++`` is equivalent to ``(p->x)++``
- ``++p->x`` is equivalent to ``++(p->x)++``
- ``*p->x`` is equivalent to ``*(p->x)``
- ``*p->s++`` is equivalent to ``*((p->s)++)``

#### Usages and considerations
- Here are some usages of structs:
	- We can use them to define arrays, which results in an efficient data organisation.
	- We can use them as function arguments or return parameters (to return multiple values).
- Considerations:
	- Arrays of structs can be quite big which can result in a stack overflow.
	- A recursive function that has a struct as its argument can result in a stack overflow if the struct is quite big. That's why we tend to pass structs around by their corresponding pointer in memory.
## Union
- The difference between union and structs is that the memory is shared between all fields. The size will be the size of the biggest element inside the union.

- Declaration:
```C
union my_union{
	uint32_t n;
	char text[4];
}
```
- This means, when we modify ``n``, ``text`` will also be modified since they share the same memory zone.
- Usage when reading big CSV files and creating complex data structures from that data.

- Usage:
```C
union my_union{
	uint32_t n;
	char text[4];
}
int main(void){
	strcpy(u.text,"abc");
	// that means u.n  = 0x00636261, because 00 is the NULL character at the end
}
```

## User-defined types
- Allows us to 'rename' datatypes/arrays/existing user-defined types.

- ``typedef definitie_tip identificator_nume_tip ;``

```C
#include <stdio.h> 
#include <stdint.h> 
#include <string.h> 
typedef unsigned int MyUnsignedIntType; 
typedef uint32_t MySpecialType; 
typedef union MyUnion { 
	uint32_t n; 
	char text[4]; 
}MyUnionType; 
typedef struct { 
	uint16_t x; 
	MyUnionType t; 
	double d; 
}MyStructType; 

int main(void) { 
	MyUnsignedIntType n; 
	MySpecialType special; 
	MyUnionType myunion; 
	MyStructType str; 
	MyStructType *pstr; 
	pstr = &str; 
	pstr->t.n = 3; 
	pstr->x = 16; 
	
	return 0; 
}
```