- The `EOF` symbol represents the value ``-1``.
	- Since ``char c;`` is usually the declaration for a ``signed character``(-127...128), we can certainly use the syntax ``while((letter = getchar()) != EOF)``, because ``letter`` will be -1 once ``STDIN`` has reached the end.
	- However, on certain systems, the declaration ``char c;`` translates to ``unsigned char c;`` (0...255). In this case, the same syntax ``while((letter = getc(f)) != EOF)`` will run into an infinite loop.

- Example machine to reproduce the 'bug':
	- Machine OS, Architecture, and gcc version [[UbuntuDockerNeofetch.png]]
```Dockerfile
FROM ubuntu:latest

RUN apt update && apt install  openssh-server sudo -y

RUN useradd -rm -d /home/ubuntu -s /bin/bash -g root -G sudo -u 1000 test 

RUN  echo 'test:test' | chpasswd

RUN service ssh start

EXPOSE 22

CMD ["/usr/sbin/sshd","-D"]
```
## Test ran on the Ubuntu Machine
- Simple ``char`` declaration:
```C
#include <stdio.h>
  
int main(void){  
  
    char c;  
  
    c = 255;  
    printf("Letter is: %d\n", c);  
  
    return 0;  
}
```
Output:
```Bash
test@d573efdc39b5:~/CStuff$ ./a.out  
Letter is: 255  
test@d573efdc39b5:~/CStuff$
```

------------
## Test ran on MacOS (clang version 14.0.3)
Output:
```Bash
adrian@192-168-0-225 lab8_characters % gcc anotherTest.c 
anotherTest.c:8:9: warning: implicit conversion from 'int' to 'char' changes value from 255 to -1 [-Wconstant-conversion]
    c = 255;
      ~ ^~~
1 warning generated.
adrian@192-168-0-225 lab8_characters % ./a.out 
Letter is: -1
adrian@192-168-0-225 lab8_characters % 
```

- This shows that the same syntax ``char c;`` is translated different (as ``unsigned char c;`` and ``signed char c;``).


# Conclusion
- For better cross-platform support, the ``signed`` or ``unsigned`` modifiers should always be present when declaring variables.

TODO: Compare Assembly code and find the reason for this happening (gcc reference, kernel difference, etc.)