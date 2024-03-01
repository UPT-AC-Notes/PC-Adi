- Section 4 ( slide 156: [[CursPC.pdf]] )
- ASCII was invented as a way to communicate with printers ( by sending 1 byte of data for each character )
	- As a standard, char is unsigned and uses 1 byte of memory: 0...255
	- Signed char has no purpose, since it loses half of the bytes: -2^7...2^7-1 = -128...127
- After the first version of the ASCII table (0...127), the **extended** version appeared
	- A good example of usage for the extended ASCII table is ``mc (Midnight Commander)``, which is a **CLI-ONLY** explorer for Linux.
-  Escape sequence was invented to include quotation mark, special characters inside of character notation:
	- ``'\\' ; '\''``
- Use man for ``ctype.h`` functions like ``isupper``, etc.
- **stderr** can be used instead of **stdout** to print errors (as a standard)
- The stdin and stdout files are opened when the program starts. Once they're closed they cannot be opened.
- It's a common mistake to say stdin and stdout function print to the 'screen' and read from the 'keyboard'. These functions have no idea of the place the input is coming from/ the output is going to.
	- ex:  ``./test.o < test.txt `` this uses a text file as stdin for the c executable
- Stdin can be closed with 'CTRL+D' on Linux and 'CTRL+Z' on Windows. When input is expected, if one of those key combinations is hit, it's translated to the **EOF** character and stdin is closed.
- program --> **buffer ( operating system )** --> stdout
	- The C program does not directly write to stdout, it writes to the buffer and the operating system decides when to send the data to stdout.
		- We can force the OS to flush the buffer by:
			- Printing the ``\n`` character
			- Filling the buffer ( usually 4096 bytes )
			- When hitting the EOF character
	- This is important during debugging, because a simple character won't be printed when the breakpoint is hit. It will only be printed if the buffer is flushed ('\n' etc.)
- The same procedure works for stdin since it waits for you to press enter until the data is sent to stdin. Until then, the data is buffered.
- The difference between stderr and stdout is primarily that stderr does **NOT** have a buffer. The data is instantly sent. This is important during debugging since the message sent in that exact point, the OS does not decide when to send it.
- The reason for these buffers is to optimise the data transfer time: it's being sent in chunks instead of individually.
- Microcontrollers do NOT have buffers, you can implement them if you want.
```C
#include <stdio.h>

int main(void){

int ch; // it will convert it automatically char to int

	while( (ch = getchar()) != EOF ){
	
		putchar(ch);
	
	}

	printf("finished");
	
	return 0;

}
```

- In C, an assignment expression returns the value assigned:

```C
#include <stdio.h>

int main(void){

	int test;
	
	int p;
	
	p = (test=12);
	
	printf("%d", p);
	
	return 0;

}
```
- This will print 12, since we assign the value of the assignment, which is 12. That's why we can use the syntax above `` ( ch = getchar() ) != EOF ``

Output:
```bash
adrian@Ioanas-MacBook-Air testProject % gcc -Wall main.c
adrian@Ioanas-MacBook-Air testProject % ./a.out 
test
test
test
test
finished%                                                                                                                                        
adrian@Ioanas-MacBook-Air testProject % ./a.out
test
test
teasdsadas
teasdsadas
^C
adrian@Ioanas-MacBook-Air testProject %    
```

- We can see that each character is buffered and then displayed when we hit 'enter'. When we press 'CTRL+D' on Linux / 'CTRL+Z' on Windows, the while ends and the program prints the 'finished' message. If we press 'CTRL+C' to end the program it will instantly end and exit, so the message won't be displayed. 
```bash
**RETURN** **VALUES**

     The functions, **fputc**(), **putc**(), **putchar**(), **putc_unlocked**(), and **putchar_unlocked**() return the character written.  If an error occurs, the value EOF is returned.  The **putw**()

     function returns 0 on success; EOF is returned if a write error occurs, or if an attempt is made to write a read-only stream.
```

- Because our C program is **POSIX COMPLIANT**, we can replace the stdin with a text file
	- replacing stdin: ``./test.o < test.txt `` this uses a text file as stdin for the c executable
	- replacing stdout: ``>`` means discard and will **create or replace** a specified file. ``./test.o > output.txt``
	- ``>>`` means append and will **create or append** to an existing file. ``./test.o >> output.txt``


- In Linux there's a word-count command which reads from stdin ``wc < file.txt``
```bash
adrian@Ioanas-MacBook-Air testProject % wc < ./main.c
       8      16     106
```
- man wc:
```bash
WC(1)                                                                                                      General Commands Manual                                                                                                     WC(1)

**NAME**

     **wc** – word, line, character, and byte count
```

- C program that replicates wc program:
```C
#include <stdio.h>

#include <ctype.h>

int main(void){

	int line_count = 0, word_count = 0, byte_count = 0;
	
	int ch, prev_ch;

	while( (ch = getchar()) != EOF ){
	
		byte_count++;
		
		if(ch=='\n'){
		
			line_count++;
		
		}
		
		if(isspace(ch) && !isblank(prev_ch)){
		
			word_count++;
		
		}
		
		prev_ch = ch;
	
	}
	
	printf("%d %d %d\n", line_count, word_count, byte_count);
	
	return 0;

}
```

- HW slide 173 [[CursPC.pdf]]
	- Read from stdin and remove all code comments

- Printf is a **VARIADIC** function (it takes a variable number of arguments)
	- For small and fast applications we can stick to ``putchar() and getchar()`` since printf function is very big.
	- Formatting directives for the printf function + examples  -> slide 176, 177, 178 [[CursPC.pdf]]
	- ex: (for number 178) `` %5d -> _ _ 178 ; %05d -> 00178 ``
- ``Scanf`` matches the string given to the expected input
	- ``scanf("nr%d", &var)`` expects as input ``nr123``
- ``Scanf`` returns how many items matched and read. If it returns zero, that means nothing was matched, so the input given wasn't correct.
	- See slide 182 for **formatted input**