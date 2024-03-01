- [[CursPC.pdf]] - slide 54
- 1 byte = 8 biti
- ``int n = 0b00111001;`` C syntax to directly reference numbers in base2 ( **0b in front of the binary number**)
	- The number ``1011(base2) = 2^0 * 1 + 2^1 * 1 + 2^2 * 0 + 2^3 * 1 = 11(base10)``
- IIEE754 - standard for representing float numbers in binary (bit X,Y,.....,Z for a certain decimal, etc.)
- For negative numbers we use MSB ( most significant bit ) - if it's 0 that means the number is positive ( >0 ), otherwise if it's 1 the number is negative ( <0 )
- Operations between these negative/positive numbers is done using **2's complement (TODO: RESEARCH)**
- For base 16, letters are introduced too since we only have 10 digits (0...9)
	- The possible elements are: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, a, b, c, d, e, f
		- The letters can also appear as capital: A, B, C, D, E, F. There's no difference between them.
	- The number ``AAAEEA(base16) = 10 * 16^3 + 8 * 16^2 + 14 * 16^1 + 9 * 16^0 = 43241(base10)``
- To convert from base2 to base16 we group the bits in groups of 4(16 = 2^4):
	- ``|1001|1101|0011|1001|(base2) = 9|D|3|9``
	- 1001(base2) is 9(base10), so it translates directly to 9(base16)
	- 1101(base2) is 13(base10), so it translates to D(base16), same for the rest

- Since any base contains numbers from zero to (base-1), these would be the correspondences between base2, base 10 and base 16. 
	- base 2: 0,1
	- base 10: 0...9
	- base 16: 0...15 (instead of 10,11,12,13,14,15, we use A,B,C,D,E,F)

| Base10  | Base2  |  Base16 | 
|---|---|---|
| 0  | 0000  | 0  |
|  1 | 0001  |  1 |
| 2  | 0010  |  2 |
| 3  |  0011 |  3 |
| 4  |  0100 |  4 |
| 5  |  0101 |  5 |
| 6  |  0110 |  6 |
| 7  |  0111 |  7 |
| 8  |  1000 |  8 |
| 9  |  1001 |  9 |
| 10  | 1010  |  A |
| 11  | 1011  |  B |
| 12  | 1100  |  C |
| 13  | 1001  |  D |
| 14  | 1010  |  E |
| 15  | 1011  |  F |

- **!** Because basic data types like int/short int etc. are dependent on the CPU architecture and compiler version, the **stdint.h library** was introduced. 
	- This library contains data types like: ``int8_t, unint8_t, ...`` (check slide 71)
- Constants (when working with magic numbers) we can specify prefixes or suffixes when necessary ``345L/345l = 345 in long; 345U/354u = 345 in unsigned int; 345UL/345ul = 345 unsigned long; 0b101L = 101(base2) as long; 0x71 (as hex)``
	- 0b is **not portable** on all compilers, so avoid using it