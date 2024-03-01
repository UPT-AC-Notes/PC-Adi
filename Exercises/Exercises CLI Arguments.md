```C
#include <stdio.h>  
  
int main(int argc, char **argv){  
  
    printf("argc: %d\n", argc);  
  
    for(int i=0; i<argc; i++){  
        printf("argv[%d]: %s\n", i, argv[i]);  
    }  
  
    return 0;  
}
```


- Sum of two numbers received as CLI arguments:
```C
#include <stdio.h>  
#include <stdlib.h>  
#include <errno.h>  
int main(int argc, char **argv){  
    if(argc != 3){  
        fprintf(stderr,"Argument error\n"); // if an error is encountered, ./a.out > output.txt will not redirect "Argument error"  
        // to the text file, because it displays it to standard error        exit(-1);  
    }  
    int a;  
    int b;  
    a = (int)strtol(argv[1], NULL, 10);  
    // TODO: Check why this isn't working (errno)    
    if (errno != 0){  
        perror(NULL);  
        fprintf(stderr,"Incorrect argument 1\n");  
        exit(-1);  
    }  
    b = (int)strtol(argv[2], NULL, 10);  
    if (errno != 0){  
        perror(NULL);  
        fprintf(stderr,"Incorrect argument 2\n");  
        exit(-1);  
    }  
  
    printf("%d + %d = %d\n", a, b, a+b);  
  
    return 0;  
}
```

```C
// get random numbers in a binary file from  www.random.cs.upt.ro  
  
#include <stdio.h>  
#include <stdint.h>  
#include <stdlib.h>  
  
#define READ_ARRAY_SIZE 4000  
#define ALLOC_CHUNK 4000  
  
typedef struct{  
    uint16_t data;  
    uint32_t count;  
}NUMBER;  
  
int findNumber(const NUMBER *array, int size, uint16_t number){  
    for(int i=0;i<size;i++){  
        if(array[i].data==number){  
            return i;  
        }  
    }  
    return -1;  
}  
  
NUMBER *addToArray(NUMBER *array, int *size, const uint16_t *data_array, int data_array_size){  
    static int index = 0;  
    static int current_size = 0;  
    int j = -1;  
    for(int i=0;i<data_array_size;i++){  
        if(index == current_size){  
            current_size += ALLOC_CHUNK;  
            if ( (array = realloc(array, current_size*sizeof(NUMBER))) == NULL){  
                perror(NULL);  
                exit(-1);  
            }  
        }  
        if( (j = findNumber(array, index, data_array[i])) == -1){  
            array[index].data = data_array[i];  
            array[index].count = 1;  
            index++;  
        }else{  
            array[j].count++;  
        }  
    }  
    *size = index;  
    return array;  
}  
  
void printArray(const NUMBER *array, int size){  
    for(int i=0;i<size;i++){  
        printf("%04X - %2d\n", array[i].data, array[i].count);  
    }  
}  
  
int main(int argc, char **argv){  
    FILE *input = NULL;  
    uint16_t aux[READ_ARRAY_SIZE];  
    NUMBER *array = NULL;  
    int size = 0;  
    int r = 0;  
    if (argc != 2){  
  
    }  
  
    if( (input = fopen(argv[1],"rb")) == NULL){  
        perror(argv[1]);  
        exit(-1);  
    }  
  
    while( (r=fread(aux,sizeof(uint16_t),READ_ARRAY_SIZE,input)) >0){  
        array = addToArray(array, &size, aux, r);  
    }  
  
    if(fclose(input)!=0){  
        perror(NULL);  
        exit(-2);  
    }  
  
    printArray(array, size);  
  
    return 0;  
}
```