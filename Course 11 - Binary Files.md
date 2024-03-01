- [[Exercises Binary Files]]
## Endianness
- https://en.wikipedia.org/wiki/Endianness

- In which order are memory bytes placed?
- Little Endian (LSB is stored in the smallest address)
	- Intel - x86, x64, ARM
```
u32 -> 0x13 7B A9 CE
		  3  2  1  0
	13 = MSB
	CE = LSB
=> CE at address x, A9 at address x+1, 7B at address x+2, 13 at address x+3

x | x+1 | x+2 | x+3 | ...
CE| A9  | 7B  |  13 |
- We can recognize little endian it because it's the opposite of how we usually write it. They're actually not reversed in memory. They appear reversed for us.
```

- Big Endian (MSB is stored in the smallest address) - Less used
	- SPARC
```
u32 -> 0x13 7B A9 CE
		 3  2  1  0
x | x+1 | x+2 | x+3 | ...
13| 7B  | A9  |  CE |
- They're reversed in memory. They appear normal for us.
```

- A good paralel is Nvidia Quattro vs Nvidia GTX/RTX. Both are expensive and 'good', but they're specialised in different ways (their architecture is different): one works good on CAD and the other on Games. Each one performs terrible at the other task.

## Binary files

- Later tasks will be to read BMP and ELF files.
- ``size t fread(void *ptr, size t size, size t nmemb, FILE *stream);``
	- Function that allows us to read **bytes** from a file. It doesn't interpret those bytes as text or anything else, it just reads them.
	- Reads from the ``stream`` a number ``nmemb`` of elements of size ``size`` and writes them in the memory zone ``ptr``.
	- Returns the number of elements read.
- ``size t fwrite(void *ptr, size t size, size t nmemb, FILE *stream);``
	- Writes in the ``stream`` a number of ``nmemb`` elements of size ``size`` from the memory zone ``ptr``
	- Returns the number of elements written.
- If we don't have a documentation for a binary file, we can't tell what's in there
- It is possible to use ``fread`` to read text. Ex: histogram of letter because we don't interpret them. But if we would, we would over engineer out code too much. 
- ``fread()`` and ``fwrite()`` advance the cursor pointer after each operation.
	- However, we can move the cursor without reading/writing by using the following functions:
	- ``long ftell(FILE *stream);``
		- Returns the current position of the cursor
	- ``void rewind(FILE *stream);``
		- Positions the cursor at the beginning of the file
	- ``int fseek(FILE *stream, long offset, int whence);``
		- Positions the cursor at ``whence`` + ``offset``
		- The ``offset`` parameter is the amount we move the cursor with (can be positive or negative)
		- The ``whence`` parameter can be either
			- ``SEEK_SET`` = the new cursor is calculated by adding ``offset`` to the beginning of the file
			- ``SEEK_END`` = the new cursor is calculated by adding ``offset`` to the end of the file
			- ``SEEK_CUR`` = the new cursor is calculated by adding ``offset`` to the current cursor value
- ``fflush: int fflush(FILE *stream);``
	- Forcefully writes to file, not when the OS decides to write
	- In its internal implementation ``fclose()`` calls ``fflush()``
	- A good application to test this is to use a USB-Stick that has a flashing light when there is data being written to it. We can create a program that writes to a file to that USB-Stick. The LED won't blink until there's a flush done (ex: stop the program with a ``scanf()``). If we remove the stick before the program ends, the data won't be written.
	- In Linux, there's a command to forcefully flush and writes everything.
	
- Alternative for ``mcview`` is ``hexedit``
