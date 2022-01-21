# Buffer-Overflow

The x86 architecture implements dedicated PUSH and POP assembly instructions in order to add or remove data to the stack respectively.

registers: 

    EAX (accumulator): Arithmetical and logical instructions
    EBX (base): Base pointer for memory addresses
    ECX (counter): Loop, shift, and rotation counter
    EDX (data): I/O port addressing, multiplication, and division
    ESI (source index): Pointer addressing of data and source in string copy operations
    EDI (destination index): Pointer addressing of data and destination in string copy operations

EIP - The Instruction Pointer 

EIP, the instruction pointer, is one of the most important registers for our purposes as it always points to the next code instruction to be executed. 
Since EIP essentially directs the flow of a program, 
***it is an attacker's primary target when exploiting any memory corruption vulnerability such as a buffer overflow.*** 


```C
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[])
{
	char buffer[64]; # the array buffer accept just 64 char which mean which mean 64 byte in the stack .

	if (argc < 2)
	{
		printf("Error - You must supply at least one argument\n");
		
		return 1;
	}
	
	strcpy(buffer, argv[1]);
	
  return 0;
}

```
