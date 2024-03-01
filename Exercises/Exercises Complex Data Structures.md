- https://data.gov.ro/ - for CSV input file examples
- Vehicle (struct)
	- Number of Wheels (uint8_t)
	- Vehicle Type (enum)
		- Scooter
		- Bicycle
		- Car
		- Bus
	- Characteristic (UNION is the best for this use case since we'll only use one of the characteristics at once)
		- Scooter Type -- only if the vehicle type is Scooter
			- Electric
			- Classic
		- Number Of Gears -- only if vehicle type is Bicycle
		- Fabrication Year -- only if vehicle type is Car
		- Number of Seats -- only if vehicle type is Bus
```C
#include <stdio.h>  
#include <stdint.h>  
  
typedef enum{ // en_tip_vehicul{  
    trotineta, // convention is to do tip_vehicul_trotineta, tip_vehicul_bicicleta, etc. to show where the field comes from  
    bicicleta,  
    automobil,  
    autobuz  
}TIP_VEHICUL;  
  
typedef enum{  
    clasica,  
    electrica  
}TIP_TROTINETA;  
  
typedef union{  
    TIP_TROTINETA tip_Trotineta;  
    uint8_t nr_viteze;  
    uint16_t an_fabricatie;  
    uint8_t nr_locuri;  
  
}TIP_CARACTERISTICA;  
  
typedef struct{  
    uint8_t nr_roti;  
    TIP_VEHICUL tip_vehicul;  
    TIP_CARACTERISTICA car;  
}VEHICUL;  
  
void printVehicul(const VEHICUL *v){  
    printf("nr roti: %d - ", v->nr_roti);  
    switch (v->tip_vehicul) {  
        case trotineta:  
            printf("trotineta - ");  
            switch (v->car.tip_Trotineta) {  
                case clasica:  
                    printf("clasica - ");  
                    break;  
                case electrica:  
                    printf("electrica - ");  
                    break;  
            }  
            break;  
        case bicicleta:  
            printf("bicicleta - ");  
            printf("nr viteze %d -", v->car.nr_viteze);  
            break;  
        case automobil:  
            printf("automobil - ");  
            printf("an fabricatie %4d -", v->car.an_fabricatie);  
            break;  
        case autobuz:  
            printf("autobuz   - ");  
            printf("nr locuri %2d -", v->car.nr_locuri);  
            break;  
    }  
    printf("\n");  
}  
  
void printVehiculeArray(const VEHICUL *array, int size){  
    for(int i=0;i<size;i++){  
        printVehicul(&array[i]);  
    }  
}  
  
int main(void){  
  
    //TIP_VEHICUL tip;  
    // enum en_tip_vehicul tip1;    // en_tip_vehicul este tipul enum-ului    // TIP_VEHICUL este tipul typedef-ului  
  
    //printf("%ld\n", sizeof(VEHICUL)); // 12 for TIP_CARACTERISTICA being a union, 20 for TIP_CARACTERISTICA being a struct  
    VEHICUL v[10];  
    v[0].nr_roti = 2;  
    v[0].tip_vehicul = trotineta;  
    v[0].car.tip_Trotineta = clasica;  
  
    v[1].nr_roti = 2;  
    v[1].tip_vehicul = trotineta;  
    v[1].car.tip_Trotineta = electrica;  
  
    v[2].nr_roti = 2;  
    v[2].tip_vehicul = bicicleta;  
    v[2].car.nr_viteze = 18;  
  
    v[3].nr_roti = 4;  
    v[3].tip_vehicul = automobil;  
    v[3].car.an_fabricatie = 2023;  
  
    v[4].nr_roti = 6;  
    v[4].tip_vehicul = autobuz;  
    v[4].car.nr_locuri = 50;  
  
    printVehiculeArray(v, 5);  
  
    return 0;  
}
```


- We have some of the following CSV data:
```CSV
"JUDET","CATEGORIE_NATIONALA","CATEGORIA_COMUNITARA","MARCA","DESCRIERE_COMERCIALA","TOTAL"  
"ALBA","AUTOBUZ","M2","IVECO","DAILY","18"  
"ALBA","AUTOBUZ","M2","IVECO","","2"  
"ALBA","AUTOBUZ","M3","A8","","1"
```
```C
#define SIZE 100
typedef struct{
	char judet[SIZE+1]; // we use SIZE+1 to make space for the NULL character. by defining the size we want to know how much free space we have in that array (in this case 100)
	char cat[SIZE+1];
	char marca[SIZE+1];
	char denumire[SIZE+1];
	uint32_t total;
}
```

```C
#include <stdio.h>  
#include <stdint.h>  
#include <stdlib.h>  
#include <string.h>  
  
#define LINE_SIZE 2048  
#define SIZE 128  
#define ALLOC_CHUNK 1024  
  
typedef struct {  
    char judet[SIZE + 1];  
    char cat[SIZE + 1];  
    char marca[SIZE + 1];  
    char descriere[SIZE + 1];  
    uint32_t total;  
} AUTO;  
  
AUTO *addAutoToArray(AUTO *array, const AUTO *element, int *size){  
    static int index = 0;  
    static int current_size = 0;  
    if(index == current_size){  
        current_size = current_size + ALLOC_CHUNK;  
        if ( (array = realloc(array, current_size*sizeof(AUTO))) == NULL){  
            perror(NULL);  
            exit(-1);  
        }  
    }  
    array[index++] = *element;  
    *size = index;  
    return array;  
}  
  
int8_t process_line(char *line, AUTO *a) {  
  
    char *p;  
    if ((p = strtok(line, ",")) == NULL) {  
        return -1; // format error  
    }  
    strcpy(a->judet, p + 1);  
    a->judet[strlen(a->judet) - 1] = 0;  
  
    if ( (p= strtok(NULL, ",")) == NULL){  
        return -1;  
    }  
    strcpy(a->cat, p + 1);  
    a->cat[strlen(a->cat) - 1] = 0;  
  
    // a strtok call to skip CATEGORIE_COMUNITARA  
    if ( (p= strtok(NULL, ",")) == NULL){  
        return -1;  
    }  
  
    if ( (p= strtok(NULL, ",")) == NULL){  
        return -1;  
    }  
    strcpy(a->marca, p + 1);  
    a->marca[strlen(a->marca) - 1] = 0;  
  
    if ( (p= strtok(NULL, ",")) == NULL){  
        return -1;  
    }  
    strcpy(a->descriere, p + 1);  
    a->descriere[strlen(a->descriere) - 1] = 0;  
  
    if ( (p= strtok(NULL, ",")) == NULL){  
        return -1;  
    }  
    a->total = strtol(p+1, NULL, 10);  
  
    return 0;  
}  
  
void printAUTO(const AUTO *a){  
    printf("-- %s - %s - %s - %s - %d\n", a->judet, a->cat, a->marca, a->descriere, a->total);  
}  
  
void printAutoArray(const AUTO *array, int size){  
    for(int i=0;i<size;i++){  
        printAUTO(&array[i]);  
    }  
}  
  
void WriteCountToFile(const char *filename, const AUTO *array, int size, const char *judet){  
    FILE *f;  
    int count = 0;  
    for(int i=0;i<size;i++){  
        if(strcmp(array[i].judet, judet) == 0){  
            count = count + array[i].total;  
        }  
    }  
    if ( (f = fopen(filename, "w")) == NULL){  
        perror(NULL);  
        exit(-1);  
    }  
    fprintf(f, "The count is: %d\n", count);  
    if(fclose(f)!=0){  
        perror(NULL);  
        exit(-2);  
    }  
}  
  
int main(void) {  
  
    FILE *f = NULL;  
    char line[LINE_SIZE];  
    AUTO aux;  
    AUTO *arr = NULL;  
    int size = 0;  
    if ((f = fopen("./parc-auto-2013.csv", "r")) == NULL) {  
        perror(NULL);  
        exit(-1);  
    }  
  
    while (fgets(line, LINE_SIZE, f) != NULL) {  
        if (process_line(line, &aux) == 0){  
            arr = addAutoToArray(arr, &aux, &size);  
        }  
  
    }  
  
    if (fclose(f) != 0) {  
        perror(NULL);  
        exit(-2);  
    }  
  
    printAutoArray(arr, size);  
    WriteCountToFile("out.txt", arr, size, "ALBA");  
  
    free(arr);  
  
    return 0;  
}
```