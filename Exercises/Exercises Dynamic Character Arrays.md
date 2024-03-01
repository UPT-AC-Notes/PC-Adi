```C
#include <stdio.h>  
#include <stdlib.h>  
#define CHUNK 64  
#define LINE_CHUNK 16  
// read any number of lines of any size from STDIN  
  
char *get_line(){  
    char *line = NULL;  
    char ch;  
    int i = 0;  
    int current_size = 0;  
    while( (ch = getchar()) != EOF ){  
        if(ch == '\n'){  
            break;  
        }  
        if(i == current_size){  
            current_size += CHUNK;  
            if( (line = realloc(line, current_size*sizeof(char))) == NULL ){  
                return NULL;  
            }  
        }  
        line[i] = ch;  
        i++;  
    }  
    if(line == NULL){ // protection from infinite loop  
        return NULL;  
    }  
    if(i == current_size){  
        current_size += 1;  
        if( (line = realloc(line, current_size*sizeof(char))) == NULL ){  
            return NULL;  
        }  
    }  
    line[i] = '\0';  
    // TODO: realloc with current_size to shorten the array  
    return line;  
}  
  
char **get_lines(){  
    char *line = NULL;  
    char **lines = NULL;  
    int current_size = 0;  
    int i = 0;  
    while( (line = get_line()) != NULL){  
        if(i==current_size){  
            current_size += LINE_CHUNK;  
            if( (lines = realloc(lines, current_size*sizeof(char*))) == NULL){  
                return NULL;  
            }  
        }  
        lines[i++] = line;  
    }  
    if(lines == NULL){ // protection from infinite loop  
        return NULL;  
    }  
    if(i == current_size){  
        current_size++;  
        if( (lines = realloc(lines, current_size*sizeof(char*))) == NULL){  
            return NULL;  
        }  
    }  
    lines[i] = NULL;  
    return lines;  
}  
  
void print_lines(char **lines){  
    int i = 0;  
    while(lines[i]!=NULL){  
        printf("%s\n", lines[i]);  
        i++;  
    }  
}  
  
void free_lines(char **lines){  
    int i = 0;  
    while(lines[i]!=NULL){  
        free(lines[i]);  
        i++;  
    }  
}  
  
int main(void){  
    char **lines = NULL;  
  
    if ( (lines = get_lines()) != NULL){  
        print_lines(lines);  
    }  
    free_lines(lines);  
    free(lines);  
  
  
    return 0;  
}
```