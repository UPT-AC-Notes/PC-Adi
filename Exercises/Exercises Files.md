```C
#include <stdio.h>  
#include <stdlib.h>  
#include <errno.h>  
#include <string.h>  
  
// A very efficient way would be to read up to the maximum read buffer size (in linux is 4096)  
// Reading character by character is very inneficient  
// A more efficient approach would be to read line by line  
#define SIZE 2000  
  
int histoLine(char *line, int *tab){  
    int count = 0;  
  
    for(int i=0;i<strlen(line);i++){  
        tab[(int)line[i]]++;  
        count++;  
    }  
  
    return count;  
}  
  
void printHistogram(int *tab, int count){  
    int i = 0;  
    // fun fact: In ANSI C, you can't do for(int i=0)  
    for(i=32;i<127;i++){  
        printf("%c - %4d - %.3f\n", i, tab[i], ((float)tab[i]*100)/count);  
    }  
}  
  
void printHistogramInFile(int *tab, int count){  
    int i = 0;  
    FILE *out = NULL;  
    if( (out = fopen("histogram.txt","w")) == NULL){  
        perror(NULL);  
        exit(-5);  
    }  
  
    for(i=32;i<127;i++){  
        fprintf(out, "%c - %4d - %.3f\n", i, tab[i], ((float)tab[i]*100)/count);  
    }  
    if(fclose(out) != 0){  
        perror(NULL);  
        exit(-7);  
    }  
}  
  
  
int main(void){  
    FILE *f = NULL;  
    int tab[255]; // for letter histogram  
    memset(tab, 0, 255*sizeof(int));  
    int count = 0;  
  
    f = fopen("scrisoare.txt", "r");  
    if(f == NULL){  
        perror("Eroare la deschidere fisier");  
        exit(-1);  
    }  
    printf("Am deschis fisierul\n");  
  
    char line[SIZE];  
    while( (fgets(line, SIZE, f)) != NULL){  
        printf("%s", line);  
        count += histoLine(line, tab);  
    }  
  
    if(fclose(f) != 0){  
        perror("Eroare la inchidere fisier:");  
        exit(-2);  
    }  
    printf("Am inchis fisierul\n");  
  
    printHistogram(tab, count);  
  
    printHistogramInFile(tab, count);  
  
    return 0;  
}
```

- Read fields from a CSV file:
```C
#include <stdio.h>  
#include <stdlib.h>  
#include <stdint.h>  
#include <string.h>  
  
// http://staff.cs.upt.ro/~valy/pt/movies.csv  
// from this file we need field 0,2,6  
  
#define LINE_SIZE 4096  
#define TITLE_SIZE 256  
  
typedef struct{  
    uint16_t year;  
    char title[TITLE_SIZE+1];  
    uint32_t budget;  
}MOVIE;  
  
int process_movie(char *line, MOVIE *m){  
    char *p;  
    if((p=strtok(line,","))==NULL){  
        return 1;  
    }  
    for(int i=0;i<7;i++){  
        switch (i) {  
            case 0:  
                m->year = strtol(p, NULL, 10);  
                break;  
            case 2:  
                strcpy(m->title, p);  
                break;  
            case 6:  
                m->budget = strtol(p, NULL, 10);  
                break;  
        }  
        if((p=strtok(NULL,","))==NULL){  
            return 1;  
        }  
    }  
    return 0;  
}  
void printMovie(const MOVIE *m){  
    printf("year: %4d ",m->year);  
    printf("title: %s ",m->title);  
    printf("budget: %d ",m->budget);  
    printf("\n");  
}  
  
int main(void){  
  
    FILE *f = NULL;  
    char line[LINE_SIZE];  
    MOVIE aux;  
  
    if( (f=fopen("movies.csv", "r")) == NULL){  
        perror(NULL);  
        exit(-1);  
    }  
  
    while(fgets(line,LINE_SIZE,f) != NULL){  
        if(process_movie(line, &aux)==0){  
            printMovie(&aux);  
        }  
    }  
  
    if(fclose(f) !=0){  
        perror(NULL);  
        exit(-2);  
    }  
}
```

```C
// Judet;Populatie;Beneficiari plãtiti;Beneficiari suspendati la sfârsit de lunã;Suma totalã plãtitã drepturi curente;Alte plati  
// wget http://staff.cs.upt.ro/~valy/pt/indemnizatie.csv  
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
#include <stdint.h>  
#define LINE_LENGTH 256  
#define JUDET_LENGTH 128  
#define ALLOC_CHUNK 64  
typedef struct{  
    char judet[JUDET_LENGTH+1];  
    uint32_t b_p;  
    uint32_t s;  
}DATA;  
  
char *get_string(char *s){  
    int i = 0, current_size = 0;  
    char *result = NULL;  
    while(*s!='\n' && *s!='\0' && *s!=';'){  
        if(i==current_size){  
            current_size += ALLOC_CHUNK;  
            result = realloc(result, current_size*sizeof(char));  
            if(result==NULL){  
                fprintf(stderr, "Error allocating memory\n");  
                exit(-2);  
            }  
        }  
        result[i++] = *s;  
        s = s + 1;  
    }  
    result[i]='\0';  
    return result;  
}  
  
DATA *get_data_from_line(char *line){  
    DATA *output = malloc(1*sizeof(DATA));  
    char *judet = get_string(line);  
  
    strcpy(output->judet,judet);  
    free(judet);  
  
    char *p=strtok(line,";");  
    p = strtok(NULL, ";");  
  
    char *b_p = get_string(p);  
    output->b_p = strtol(b_p, NULL, 10);  
    free(b_p);  
  
    for(int i=1;i<=3;i++){  
        p = strtok(NULL,";");  
    }  
  
    char *s = get_string(p);  
    output->s = strtol(s, NULL, 10);  
    free(s);  
  
    return output;  
}  
  
DATA* add_to_array(DATA *arr, int *size, DATA *elm){  
    static int i = 0;  
    static int current_size = 0;  
    if(i==current_size){  
        current_size += ALLOC_CHUNK;  
        arr = realloc(arr, current_size*sizeof(DATA));  
        if(arr==NULL){  
            fprintf(stderr, "Error allocating memory\n");  
            exit(-1);  
        }  
    }  
    arr[i++] = *elm;  
    free(elm);  
    *size = i;  
    return arr;  
}  
  
void print_array(DATA *arr, int size){  
    for(int i=0;i<size;i++){  
        printf("judet: %s - p_n: %d - s: %d\n", arr[i].judet, arr[i].b_p, arr[i].s);  
    }  
}  
  
int main(int argc, char **argv){  
    if(argc!=2){  
        fprintf(stderr,"Incorrect number of arguments provided!\n");  
        exit(-2);  
    }  
  
    FILE *f = fopen(argv[1],"r");  
    if(f==NULL){  
        perror(NULL);  
        exit(-1);  
    }  
  
    char line[LINE_LENGTH];  
    DATA *arr = NULL;  
    int size = 0;  
  
    fgets(line,LINE_LENGTH,f);  
    while(fgets(line, LINE_LENGTH, f)){  
        DATA* d = get_data_from_line(line);  
        arr = add_to_array(arr, &size, d);  
    }  
  
  
    if(fclose(f)!=0){  
        perror(NULL);  
        exit(-1);  
    }  
  
  
    print_array(arr,size);  
    free(arr);  
  
    return 0;  
}
```