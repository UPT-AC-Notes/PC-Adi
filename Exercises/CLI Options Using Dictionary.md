```C
#include <stdio.h>  
#include <string.h>  
#define ARG_SIZE 8  
void maxi(int a, int b){  
    printf("%d\n", a > b ? a : b);  
}  
void mini(int a, int b){  
    printf("%d\n", a < b ? a : b);  
}  
void p_a(int a, int b){  
    printf("%d\n", a);  
}  
void p_b(int a, int b){  
    printf("%d\n", b);  
}  
void sum(int a, int b){  
    printf("%d\n", a+b);  
}  
void dif(int a, int b){  
    printf("%d\n", a-b);  
}  
void mult(int a, int b){  
    printf("%d\n", a*b);  
}  
void div(int a, int b){  
    printf("%f\n", (float)a/(float)b);  
}  
void (*functions[ARG_SIZE+1])(int, int);  
char messages[105][105];  
void init(){  
    functions[1] = &maxi;  
    functions[2] = &mini;  
    functions[3] = &p_a;  
    functions[4] = &p_b;  
    functions[5] = &sum;  
    functions[6] = &dif;  
    functions[7] = &mult;  
    functions[8] = &div;  
  
    strcpy(messages[0], "0. Iesire program\n");  
    strcpy(messages[1], "1. Maximul\n");  
    strcpy(messages[2], "2. Minimul\n");     
    strcpy(messages[3], "3. Afisare a\n");  
    strcpy(messages[4], "4. Afisare b\n");  
    strcpy(messages[5], "5. Suma numerelor\n");  
    strcpy(messages[6], "6. Diferenta numerelor\n");  
    strcpy(messages[7], "7. Produsul numerelor\n");  
    strcpy(messages[8], "8. Catul numerelor\n");  
}  
  
int main(void){  
    init();      
    int option = -1;  
    while(option){  
       printf("Alegeti o optiune:\n");  
       for(int i=0;i<=ARG_SIZE;i++){  
          printf("%s",messages[i]);  
       }  
       scanf("%d",&option);  
       if(option == 0){  
          break;  
       }  
  
       printf("Introduceti doua numere\n");  
       int a,b;  
       scanf("%d%d", &a, &b);  
         
       if(option<=ARG_SIZE){  
          (functions)[option](a,b);    
       }  
         
    }  
    printf("Exiting program...\n");  
  
    return 0;  
}
```