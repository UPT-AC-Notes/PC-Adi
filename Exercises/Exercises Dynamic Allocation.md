#### Reading an array of an unknown size:
- Using ``realloc`` would be a bad approach, so we allocate memory in chunks.
- Use ``-g`` in gcc for debug, so tools like ``valgrind --leak-check=full`` to also get the line where the memory leak happened. 
	- ``Valgrind`` can be used to both detect memory leaks and know where segmentation faults are happening. 
```C
#include <stdio.h>  
#include <stdint.h>  
#include <stdlib.h>  
  
#define CHUNK 2000  
  
uint16_t *read_array_dynamic(int *size){  
    uint16_t temp;  
    uint16_t *array = NULL;  
    int current_size = 0, i = 0;  
  
    while(scanf("%hu", &temp) == 1){  
        if(i == *size){  
            // Nu mai am loc  
            current_size += CHUNK;  
            if ( (array = realloc(array, current_size * sizeof(uint16_t))) == NULL){  
                return NULL;  
            }  
        }  
        array[i] = temp;  
        i++;  
    }  
    // we can do a realloc with i size to remove the last trailing memory  
    if ( (array = realloc(array, i*sizeof (uint16_t))) == NULL ){  
        *size = 0;  
        return NULL;  
    }  
    *size = i;  
    return array;  
}  
  
void print_array(uint16_t* arr, int size){  
    for(int i=0;i<size;i++){  
        printf("%d ", arr[i]);  
    }  
    printf("\n");  
}  
  
int main(void){  
    uint16_t *array = NULL;  
    int size = 0;  
    if( (array = read_array_dynamic(&size)) == NULL ){  
        printf("Eroare\n");  
    }  
    print_array(array, size);  
  
    free(array)  
  
    return 0;  
}

```


```C
// Reading a string of an unknown size  
// Fgetsc doesn't solve the problem since it wants the size  
#include <stdio.h>  
#include <stdlib.h>  
#define CHUNK 32  
char *read_line(void){  
    char *line = NULL;  
    int ch, current_size = 0, i = 0;  
    while( (ch= getchar()) != EOF){  
        if(ch=='\n'){  
            break;  
        }  
        if(i==current_size) {  
            current_size += CHUNK;  
            line = realloc(line, current_size * sizeof(char));  
        }  
        line[i++] = ch;  
    }  
    if(line == NULL){  
        return NULL;  
    }  
    if(i==current_size) {  
        current_size ++;  
        line = realloc(line, current_size * sizeof(char));  
    }  
    line[i] = '\0';  
    return line;  
}  
  
int main(void){  
    char *string = NULL;  
    string = read_line();  
    printf("%s\n", string);  
    return 0;  
}
```

- Implement the following function ``char *text2hex(char *string)``
	- ``ex: "abc" -> "616263"``
```C
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define SIZE 1024
char *hex2text(char *string){
	// TODO: convert hex to txt
}
char *text2hex(char *string){
	char *result = NULL;
	if ( (result = malloc((strlen(string)*2+1)*sizeof(char))) == NULL){
		return NULL;
	}
	result[0] = 0; // sau strcpy(result, "")
	// sau memset(result, 0, (strlen(string)*2+1)*sizeof(char))
	char aux[3];
	for(int i=0;i<strlen(string); i++){
		sprintf(aux, "%02X", string[i]);
		strcat(result, aux);
	}

	return result;
}

int main(void){

	char line[SIZE];
	char *str = NULL;
	if(fgets(line, SIZE, stdin) != NULL){
		line[strlen(line)-1]=0;
		if( (str = text2hex(line)) != NULL){
			printf("result is: %s\n", str);
		}
	}
	free(str);
	return 0;
}
```

- After each element in the array, insert its cube.
```C
#include <stdio.h>  
#include <stdint.h>  
#include <stdlib.h>  
  
#define CHUNK 64  
  
uint16_t *read_array(int *size){  
    int current_size = 0, i=0;  
    int nr = 0;  
    uint16_t *arr = NULL;  
    while(scanf("%d",&nr)==1){  
        if(i==current_size){  
            current_size += CHUNK;  
            arr = realloc(arr, current_size * sizeof(uint16_t));  
        }  
        arr[i++] = nr;  
    }  
    arr = realloc(arr, i * sizeof(uint16_t));  
    *size = i;  
    return arr;  
}  
  
void cube_elements_array(uint16_t **arr, int *size){  
    *size = 2 * (*size);  
    *arr = realloc(*arr, (*size)*sizeof(uint16_t));  
  
    int current_size = (*size)/2;  
    for(int i=0;i<current_size;i+=2){  
        current_size++;  
        for(int j = current_size-1; j>i; j--){  
            (*arr)[j] = (*arr)[j-1];  
        }  
        (*arr)[i+1] = (*arr)[i]*(*arr)[i]*(*arr)[i];  
    }  
}  
  
void display_array(uint16_t *arr, int size){  
    for(int i=0;i<size;i++){  
        printf("%d ",arr[i]);  
    }  
    printf("\n");  
}  
  
  
int main(void){  
    int size = 0;  
    uint16_t *arr = read_array(&size);  
  
    display_array(arr,size);  
  
    cube_elements_array(&arr, &size);  
    display_array(arr,size);  
  
    free(arr);  
  
    return 0;  
}
```

```C
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
  
int compute_size(const char* where, const char* what, const char* replace){  
    int what_count = 0;  
    char *s = strstr(where, what);  
    while(s){  
        what_count++;  
        s = strstr(s+1,what);  
    }  
    return strlen(where)-what_count*strlen(what)+what_count*strlen(replace);  
}  
  
int is_what_at_current(const char* where, const char* what){  
    for(int i=0;i<strlen(what);i++){  
        if(where[i]!=what[i]){  
            return 0;  
        }  
    }  
    return 1;  
}  
  
char *string_replace(const char* where, const char* what, const char* replace){  
    char *result = (char*)malloc((compute_size(where, what, replace)+1)*sizeof(char));  
    int r_i = 0;  
  
    for(int i=0;i<strlen(where);i++){  
        if(is_what_at_current(where+i,what)){  
            for(int j=0;j<strlen(replace);j++){  
                result[r_i++] = replace[j];  
            }  
            i+=strlen(what)-1;  
        }else{  
            result[r_i++] = where[i];  
        }  
  
    }  
    result[r_i] = 0;  
    return result;  
}  
  
int main(void){  
  
    char *s = string_replace("ana are multe mere", "multe", "123456789");  
    printf("%s\n",s);  
  
    free(s);  
  
    return 0;  
}
```

```C
#include <stdio.h>  
#include <ctype.h>  
#include <stdlib.h>  
#include <string.h>  
// a -> apa  
// e -> epe  
// i -> ipi  
// o -> opo // u -> upu  
int isVowel(char c){  
    char l = tolower(c);  
    return l == 'a' || l == 'e' || l == 'i' || l == 'o' || l == 'u';  
}  
char *dic(char c){  
    if(isVowel(c)){  
        char *r = (char*)malloc(3*sizeof(char));  
        r[0]=c;  
        r[2]=c;  
        if(isupper(c)){  
            r[1]='P';  
        }else{  
            r[1]='p';  
        }  
        return r;  
    }  
    return NULL;  
}  
char *pasareasca(const char* text){  
    int nVowels = 0;  
    for(int i=0;i<strlen(text);i++){  
        if(isVowel(text[i])){  
            nVowels++;  
        }  
    }  
    int size = strlen(text) + 2*nVowels;  
    char *result = (char*)malloc(size*sizeof(char)+1);  
    int r_i = 0;  
    for(int i=0;i<strlen(text);i++){  
        if(!isVowel(text[i])){  
            result[r_i++] = text[i];  
        }else{  
            char *d_r = dic(text[i]);  
            for(int j=0;j<3;j++){  
                result[r_i++] = d_r[j];  
            }  
            free(d_r);  
        }  
    }  
    result[r_i]=0;  
    return result;  
}  
int main(void){  
  
    char *r = pasareasca("apa");  
    printf("%s\n", r);  
    free(r);  
  
    return 0;  
}
```