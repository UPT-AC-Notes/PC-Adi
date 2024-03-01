```C
//wget http://staff.cs.upt.ro/~valy/pt/nr.txt

//uint16_t -> 20000 elem

//void read_array_random(uint16_t* arr, size_t size);

//populate the array with random elements

//max number should be 500

//function that print_array

//void sort_array

//uint16_t max_array

//uint16_t min_array

//array should not be declared globally

//int read_array (from stdin) -- returns how much it read

#include <stdlib.h>

#include <time.h>

#include <stdio.h>

#include <stdint.h>
  

#define MAX_SIZE 20

#define MAX_NUMBER 500


void read_array_random(uint16_t* arr, size_t size){

	for(int i=0;i<size;i++){
	
		arr[i] = rand() % MAX_NUMBER;
	
	}

}
  

void print_array(uint16_t* arr, size_t size){
 

	for(int i=0;i<size;i++){
	
		printf("%d ", arr[i]);
	
	}
	
	printf("\n");

}


void sort_array(uint16_t* arr, size_t size){

	for(int i=0;i<size-1;i++){
	
		for(int j=i+1;j<size;j++){
		
			if(arr[i]>arr[j]){
			
				uint16_t tmp = arr[i];
				
				arr[i] = arr[j];
				
				arr[j] = tmp;
			
			}
		
		}
	
	}

}


uint16_t max_array(uint16_t* arr, size_t size){

	uint16_t maxi = *arr;
	
	for(int i=1;i<size;i++){
	
		if(*(arr+i)>maxi){
		
		maxi = *(arr+i);
		
		}

	}

	return maxi;

}


uint16_t min_array(uint16_t* arr, size_t size){

	uint16_t mini = *arr;
	
	for(int i=1;i<size;i++){
	
		if(*(arr+i)<mini){
		
			mini = *(arr+i);
		
		}
	
	}
		
	return mini;

}


int read_array_stdin(uint16_t* arr, size_t size){

	int i = 0;
	
	while(i<size && scanf("%hd",(arr+i))==1){
		i++;
	}
	
	return i;

}
  

int main(void){

	uint16_t arr[MAX_SIZE];

	srand(time(NULL));
	
	  
	read_array_random(arr, MAX_SIZE);
	
	print_array(arr, MAX_SIZE);
	
	  
	sort_array(arr, MAX_SIZE);
	
	print_array(arr, MAX_SIZE);
	
	  
	printf("Max elm from array is: %d\n", max_array(arr, MAX_SIZE));
	
	printf("Min elm from array is: %d\n", min_array(arr, MAX_SIZE));
	
	print_array(arr, read_array_stdin(arr, MAX_SIZE));

  
	return 0;

}
```

- For this algorithm, you can reference [[Random Numbers]]