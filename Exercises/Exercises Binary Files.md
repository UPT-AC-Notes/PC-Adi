- Reading integers from a binary file:
```C
#include <stdio.h>  
#include <stdlib.h>  
#include <stdint.h>  
  
// http://staff.cs.upt.ro/~valy/pt/integers.bin  
// mc - midnight commander  
    // from mc - mcview - hex mode (F4)  
// The provided file contains numbers of type uint32_t  
int main(void){  
    FILE *f = NULL;  
    uint32_t n = 0;  
      
  
    if( (f=fopen("integers.bin","rb")) == NULL ){  
        perror(NULL);  
        exit(-1);  
    }  
  
    // bc the architecture is little endian the first number read should be 0x00 00 00 47  
    while (fread(&n, sizeof(uint32_t), 1, f) == 1){  
        printf("0x%08X\n%d\n", n,n);  
    }  
  
    if( (fclose(f)) !=0 ){  
        perror(NULL);  
        exit(-1);  
    }  
  
}
```

- A more efficient method is:
```C
#include <stdio.h>  
#include <stdlib.h>  
#include <stdint.h>  
  
// http://staff.cs.upt.ro/~valy/pt/integers.bin  
// mc - midnight commander  
    // from mc - mcview - hex mode (F4)  
// The provided file contains numbers of type uint32_t  
int main(void){  
    FILE *f = NULL;  
    uint32_t arr[100];  
    int r = 0;  
  
    if( (f=fopen("integers.bin","rb")) == NULL ){  
        perror(NULL);  
        exit(-1);  
    }  
  
    // bc the architecture is little endian the first number read should be 0x00 00 00 47  
    while ( (r = fread(arr, sizeof(uint32_t), 100, f)) >0 ){  
        for(int i=0;i<r;i++){  
            printf("0x%08X - %d\n", arr[i],arr[i]);  
        }  
  
    }  
  
    if( (fclose(f)) !=0 ){  
        perror(NULL);  
        exit(-1);  
    }  
  
}
```

- Reading the resolution from a BMP file:
	- BMP Image Viewers know how to read the color pallets, the resolution and the color for each pixel.
	- http://staff.cs.upt.ro/~valy/pt/
	- https://en.wikipedia.org/wiki/BMP_file_format
	- The BMP file format was invented by Microsoft
```C
#include <stdio.h>  
#include <stdlib.h>  
#include <stdint.h>  
  
int main(void){  
    FILE *f = NULL;  
    char header_field[2];  
    uint32_t bmp_size = 0;  
    uint32_t dib_size = 0;  
    uint32_t width = 0;  
    uint32_t height = 0;  
  
    if( (f=fopen("sampleBmpFile.bmp","rb")) == NULL ){  
        perror(NULL);  
        exit(-1);  
    }  
  
    if( (fread(header_field, sizeof(char), 2, f)) !=2){  
        printf("error\n");  
        exit(-2);  
    }  
  
    if(fread(&bmp_size, sizeof(uint32_t), 1, f) !=1){  
        printf("error\n");  
        exit(-2);  
    }  
  
    if (fseek(f, 0x0E, SEEK_SET)!=0){ // the 0xE header offset is specified in the documentation Windows BITMAPINFOHEADER  
        perror(NULL);  
        exit(-2);  
    }  
  
    if(fread(&dib_size, sizeof(uint32_t), 1, f) !=1){  
        printf("error\n");  
        exit(-2);  
    }  
  
    if(fread(&width, sizeof(uint32_t), 1, f) !=1){  
        printf("error\n");  
        exit(-2);  
    }  
  
    if(fread(&height, sizeof(uint32_t), 1, f) !=1){  
        printf("error\n");  
        exit(-2);  
    }  
  
    if( (fclose(f)) !=0 ){  
        perror(NULL);  
        exit(-1);  
    }  
  
    for(int i=0;i<2;i++){  
        printf("%c", header_field[i]);  
    }  
    printf("\n");  
    printf("bmp_size %d\n", bmp_size); // can be verified with ls -l  
    printf("dib_size %d\n", dib_size);  
    printf("resolution: %dx%d\n", width, height);  
}
```

