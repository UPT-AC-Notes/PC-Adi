- Example of program for the second test: ``Afisati paritatea pe biti``
- Slide 184 [[CursPC.pdf]]
- Bit = binary digit
- Nibble = a group of 4 bits
- Byte = a group of 8 bits
- Bytes are numbered from **RIGHT TO LEFT**. The first one is the least significant byte (LSB), the last one is the most significant byte (MSB)
- We can't physically work with memory at the bit level. We are only able to work at the byte level. We can't bring 7 or 9 bytes from memory, only powers of two (1, 2, 4, ...)
- Be AWARE to use parentheses since not all compilers respect the same standards for bitwise operators.
```python
unint8_t a = 10011101
unint8_t b = 11010111
a | b = 10011101 | 11010111 = 11011111 (!RIGHT TO LEFT!) = 0xDF
a & b = 10011101 & 11010111 = 10010101 = 0x95
~ a = ~ 10011101 = 01100010

--------------------------------------------------------------------------------

unint8_t a = 11010101
a << 1 = 10101010 (WE LOSE THE FIRST 1 because the datatype can only store 1 byte and a new zero appears at the end)
a << 8 = 00000000

a >> 1 = 01101010 (WE LOSE THE LAST 1 and a new zero appears at the beginning)

--------------------------------------------------------------------------------

! It's a different for SIGNED integers

int8_t a = S1010101 
a >> 1 = SS101010

! The new bit that appeared is the sign bit, not zero as before. 

int8_t a = 01100101
a >> 1 = 00110010

int8_t a = 11100101 - negative number
a >> 1 = 111100101

--------------------------------------------------------------------------------

uint8_t a = 11100101
uint8_t b = 11010011
a ^ b = 00110110

```

-  Setting, Resetting, Inverting, Testing a BIT
```python
       7654 3210
a =    XXXX XXXX
	APPLYING OR OPERATOR ( | )
mask = 0010 0000
=>     XX1X XXXX
1 = 0000 0001
1 << 5 = 0010 0000
=> mask = 1 << 5

=> By doing a = a | (1<<5), we're SETTING the 5th bit to 1
	a = a | (1<<k) - SETTING the k-th bit
	
--------------------------------------------------------------------------------
		7654 3210
	a = XXXX XXXX
		APPLYING AND OPERAtoR ( & )
mask =	1011 1111
=>      X0XX XXXX
1 << 6 = 0100 0000
=> ~(1<<6) = 1011 1111
=> mask = ~(1<<6)

=> By doing a = a & ~(1<<6), we're RESETTING the 6th bit to 0
	a = a & ~(1<<k) - RESETTING the k-th bit

```

- First exercise:
	- a = XXXX XXXX
	- Determine if the 4th bit is 0 or 1.
		- We create a mask: 0001 0000
		- r = a & mask = 000X 0000
			- If 4th bit was 0, r will be zero, otherwise the bit was 1
```C
	if(a & (1<<k) != 0){
		printf("The kth bit is 1");	
	}else{
		printf("The kth bit is 0");
	}
```

- Function that displays each bit of a number with 16 bits 

```C
#include <stdio.h> 
#include <stdint.h> 
void print_bit_16(uint16_t nr){ 
	uint16_t mask = 0x8000; // 0b1000000000000000 
	uint8_t i = 0; 
	for (i = 0; i < 16; i++){ 
		if ((nr & mask) == 0){ 
			printf ("0");
		} 
		else{ 
			printf ("1"); 
		} 
		mask = mask >> 1; // mask >>= 1; 
	} 
	printf ("\n"); 
} 
int main(void){ 
	print_bit_16(0x8001); 
	return 0; 
}
```

```C
#include <stdio.h>
#include <stdint.h>

// 0 -> paritate para pe biti - nr par de biti de 1

// 1 -> paritate impara pe biti - nr impar de biti de 1

void parity(uint8_t n){
	int c = 0;
	int m = 1<<7;
	for(int i=0;i<8;i++,m=m>>1){
		if( (n & m) != 0 ){
			c++;
		}
	}
	if(c%2==0){
		printf("paritate para\n");
	}else{
		printf("paritate impara\n");
	}
}

int main(void){

	parity(0b11001100);
	
	return 0;

}
```

- Ex2 - Glueing 2 8 bit numbers into a 16 bit one:
	- ``unint16_t r = n1 | ( (unint16_t)n2<<8)``
- HW: slide 188 [[CursPC.pdf]]  
- Page 46-47 [[The.C.Programming.Language.2Nd.Ed Prentice.Hall.Brian.W.Kernighan.and.Dennis.M.Ritchie..pdf]]

- Solved Exercises:
	- [[Exercises Bitwise Operators]]