- Word Count Clone (``wc`` utility in Linux)
```C
#include <stdio.h>  
#include <ctype.h>  
  
// wget http://staff.cs.upt.ro/~valy/pt/random_text.txt  
  
/*  
    student@b418b-9:~$ cat random_text.txt | wc       
	    1225   64600  464135
    student@b418b-9:~$ ./a.out < random_text.txt  
	    1225   64600  464135  
*/  
  
void word_count(int *lines, int *words, int *characters){  
  
    int w = 0, c = 0, l = 0;  
    char letter, lastLetter=0;  
  
    while((letter = getchar()) != EOF){  
       c++;  
       if(isalpha(lastLetter) && isspace(letter)){  
          w++;  
       }       
       if(letter == '\n'){  
          l++;  
       }  
       lastLetter = letter;  
    }  
    *lines = l;  
    *words = w;  
    *characters = c;  
}  
int main(void){  
  
    int lines, words, characters;  
    word_count(&lines, &words, &characters);  
  
    printf("%7d %7d %7d\n",lines, words, characters);  
  
    return 0;  
}
```


- Histogram of the letter usage:
```C
#include <stdio.h>  
#include <ctype.h>  
#include <stdint.h>  
#include <stdlib.h>  
#include <string.h>  
// wget http://staff.cs.upt.ro/~valy/pt/random_text.txt  
  
  
void word_count(uint32_t *freq, int *lines, int *words, int *characters, int *alpha_characters){  
  
    int w = 0, c = 0, l = 0, a_c = 0;  
    char letter, lastLetter=0;  
  
    while((letter = getchar()) != EOF){  
       c++;  
       if(isalpha(letter)){  
          a_c++;  
          freq[(int)letter]++;  
       }  
       if(isalpha(lastLetter) && isspace(letter)){  
          w++;  
       }       
       if(letter == '\n'){  
          l++;  
       }  
       lastLetter = letter;  
    }  
    *lines = l;  
    *words = w;  
    *characters = c;  
    *alpha_characters = a_c;   
}  
int main(void){  
  
    int lines, words, characters, alpha_characters;  
    int first_ch = (int)'A', last_ch = (int)'z';  
    // malloc also need to set first last_ch + 1 characters  
    // this error was caught by valgrind    
    uint32_t *freq = (uint32_t*)malloc( (last_ch+1) * sizeof(uint32_t) );  
    memset(freq, 0, (last_ch+1) * sizeof(uint32_t));  
    word_count(freq, &lines, &words, &characters, &alpha_characters);  
    for (int i=first_ch; i<=last_ch; i++){  
       if(isalpha(i)){  
          printf("%5c %.3f%%\n", (char)i, ((double)freq[i]/alpha_characters)*100);  
       }  
    }  
    free(freq);  
  
    //printf("%7d %7d %7d\n",lines, words, characters);  
  
    return 0;  
}
```

- Read a line from STDIN. Capitalise the first letter of each word
```C
#include <stdio.h>  
#include <string.h>  
#include <ctype.h>  
#define SIZE 1024  
  
size_t read_line(char *line){  
    if ( (line = fgets(line, SIZE, stdin)) != NULL ){  
       return strlen(line);  
    }  
    return 0;  
}  
  
void capitalizeFirstLetter(char *line, size_t size){  
    char lastLetter = 0;  
    for(int i=0;i<size;i++){  
       if(isalpha(line[i]) && !isalpha(lastLetter)){  
       line[i] = toupper(line[i]);      
    }  
       lastLetter = line[i];  
    }    
}  
  
void display_string(char *line, size_t size){  
    for(int i=0;i<size;i++){  
       printf("%c",line[i]);  
    }  
}  
  
  
int main(void){  
    char line[SIZE];  
    size_t size = read_line(line);  
  
    capitalizeFirstLetter(line, size);  
  
    display_string(line, size);  
}
```

- Write a function with the following header ``uint32_t upper_sub_string(char *str, const char *substr);``. This function should modify the string ``str`` so that each occurrence of the ``substr`` string will be capitalised.
```C
#include <stdio.h>  
#include <ctype.h>  
#include <string.h>  
#include <stdint.h>  
#define SIZE 1024  
  
size_t read_line(char *line){  
    if ( (line = fgets(line, SIZE, stdin)) != NULL ){  
       return strlen(line);     
    }  
    return 0;  
}  
  
uint32_t upper_sub_string(char *str, const char *substr){  
    char *found;  
    uint32_t r = 0;  
    while( (found = strstr(str, substr)) ){  
       while(isalpha(*found)){  
          *found = toupper(*found);  
          found++;  
       }  
       r++;  
    }  
    return r;  
  
}  
  
void display_string(char *line, size_t size){  
    for(int i=0;i<size;i++){  
       printf("%c",line[i]);  
    }  
}  
  
int main(void){  
    char line[SIZE];  
    size_t size = read_line(line);  
    uint32_t r = upper_sub_string(line, "ana");  
      
    display_string(line, size);  
    printf("Number of replace operations: %d", r);  
      
      
}
```

- Write a function with the following header ``uint32_t string_replace(char *where, const char *what, const char *replace)``. This function should replace every occurrence of the ``what`` string in the ``where`` string with the ``replace`` string.
```C
#include <stdio.h>  
#include <ctype.h>  
#include <string.h>  
#include <stdint.h>  
#define SIZE 1024  
  
void display_string(char *line, size_t size){  
    for(int i=0;i<size;i++){  
       printf("%c",line[i]);  
    }  
}  
  
uint32_t string_replace(char *where, const char *what, const char *replace){  
      
    char *found;  
    uint32_t r = 0;  
    while( (found = strstr(where, what)) ){  
       char cpy[SIZE];  
       strcpy(cpy, found+strlen(what));  
       strcpy(found, replace);  
       *(found+strlen(replace))='\0';  
       strcat(where, cpy);  
  
       r++;  
    }  
    return r;  
}  
  
int main(void){  
      
    char s1[1000];  
    char s2[] = "string";  
    char s3[] = "sir de caractere";  
    strcpy(s1, "Acesta este un string si un string este terminat cu 0x00");  
  
    int r = string_replace(s1, s2, s3);      
  
    display_string(s1, strlen(s1));  
    printf("\nNumber of replace operations: %d", r);  
      
      
}
```

- ! Be very careful when using the ``fgets`` function to read a string, because it will also add the ``'\n'`` character at the end.
	- This will result in buggy code when trying to search for a substring with ``strstr`` if the substring was read with ``fgets``.