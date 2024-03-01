```C
#include <stdio.h>

#include <stdint.h>

  
int testBit(int p, uint8_t n){

	uint8_t mask = 1 << (p-1);
	
	if ( (mask & n) == 0){
	
		return 0;

	}

	return 1;

}

  

uint8_t setBit(int p, uint8_t n){

	uint8_t mask = 1 << (p-1);

	return (mask|n);

}

  

uint8_t resetBit(int p, uint8_t n){

	uint8_t mask = 1 << (p-1);

	mask = (~mask);

	return (mask&n);

}

  

uint8_t invertBit(int p, uint8_t n){

	if(testBit(p,n)){

		return resetBit(p,n);

	}else{

		return setBit(p,n);

	}

}

  

void displayNumber(uint8_t n){

	uint8_t mask = 1 << 7;

	while(mask){

		printf("%d", (mask&n) != 0);

		mask >>= 1;

	}

	printf("\n");

}

  

uint8_t rotateRight(uint8_t v, int n){

	return (v >> n) | (v<<(8-n));

}

  

uint8_t rotateLeft(uint8_t v, int n){

	return (v << n) | (v>>(8-n));

}

  

uint8_t rotateBits(uint8_t v, int n){

	if(n>0){

		return rotateRight(v,n);

	}else{

		return rotateLeft(v,-n);

	}

	return 0;

}

  

int main(void){

	for(int i=1;i<=8;i++){

		printf("Bit on position %d is %d\n", i, testBit(i, 0b10100010));

	}

	printf("\n\n");

	for(int i=1;i<=8;i++){

		displayNumber(invertBit(i,0b11100000));

	}

	printf("\n\n");

  
	displayNumber(rotateBits(0b10100110, -2));

  

	return 0;

}
```

```C
#include <stdio.h>

#include <stdint.h>

  

//count the number of 1 bits in a 32 bit number

uint8_t nr_bit_1(uint32_t n){

	uint32_t mask = 1<<31;

	int count = 0;

	while(mask!=0){

		if( (mask & n) != 0){

			count++;

		}

		mask >>= 1;

	}

	return count;

}

int main(void){

	printf("%d\n", nr_bit_1(0b10001111));

	return 0;

}
```

```C
#include <stdio.h>

#include <stdint.h>

  

// given is a number on 32 bits, create a 16 bit number

// that has on the first byte the number of zeros

// and on the second byte the number of ones

uint16_t merge_bit_counts(uint32_t n){

	uint32_t mask = 1<<31;
	
	uint8_t count_0 = 0;
	
	uint8_t count_1 = 0;
	
	while(mask){
	
		if( (mask&n) ==0 ){
	
			count_0++;
	
		}else{
	
			count_1++;
	
		}
	
		mask >>= 1;

	}

	uint16_t result=0;
	
	result = (result | count_0);
	
	result <<= 8;
	
	result = (result | count_1);
	
	return result;

}

void display_number(uint16_t n){

	uint16_t mask = 1<<15;
	
	while(mask){
	
		if((mask&n)==0){
		
			printf("0");
		
		}else{
		
			printf("1");
	
		}
		
		mask >>= 1;
	
	}
	
	printf("\n");

}

int main(void){

	printf("%X\n", merge_bit_counts(0b11110000111100001111000011110000));
	
	display_number(merge_bit_counts(0b11110000111100001111000011110000));
	
	return 0;

}
```

```C
#include <stdio.h>

#include <stdint.h>

  

// return the number of pairs of ones in a 16 bit number

int bit_1_pair(uint16_t n){

	uint16_t mask = 1<<15;
	
	int8_t last_bit = -1;
	
	int pair_count = 0;
	
	while(mask){
	
		if((mask&n)!=0){
		
			if(last_bit==1){
		
				pair_count++;
	
			}
	
		last_bit = 1;
	
		}else{
	
			last_bit = 0;
	
		}
	
		mask >>= 1;
	
	}
	
	return pair_count;

}

int main(void){

	printf("%d\n", bit_1_pair(0b0110111000001111));  
	
	return 0;

}
```

```C
#include <stdio.h>

#include <stdint.h>

  

// for each byte of the 32 bit number

// get the parity and place it in another 32 bit number

// 0 - even, 1 - odd; nr of 1 bits == nr 0 bits => even

// ex: (for 16 bit) n = 1110 0101 0001 1100

					//   1     0    1    0

				  // => 0001 0000 0001 0000

uint32_t parity_byte(uint32_t n){

	uint32_t result = 0;
	
	for(int i=0;i<8;i++){
	
		char mask = 1<<3;
		
		uint8_t count_1 = 0;
		
		uint8_t count_0 = 0;
	
		while(mask!=0){
		
			if((mask&n)!=0){
		
				count_1++;
		
			}else{
		
				count_0++;
		
			}
	
			mask >>= 1;
	
		}
	
		n >>= 4;
	
		if(count_1!=count_0){
	
			result = (result|(1<<(4*i)));
	
		}
	
	}
	
	return result;

}

  

void display_number(uint32_t n){

	uint32_t mask = 1<<31;

	while(mask){
	
		if((mask&n)==0){
	
			printf("0");
	
		}else{
	
			printf("1");
	
		}
	
		mask >>= 1;
	
	}
	
	printf("\n");
	
}

int main(void){

	display_number(parity_byte(0b11100101000111001110010100011100));  

return 0;

}
```