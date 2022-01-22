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

Most functions start with this sequence :  PUSH EBP, followed by MOV EBP,ESP 



After sending the buffer ([MyVar][EBP][EIP][your code]), ESP will/should point at the beginning of [your code]. So if you can make EIP go to your code,
 you’re in control.



1. overflow the  buffer with ( “A" * 500 ) and see if the instruction pointer contains 41414141 which is AAAAA in Hex AND APPLICATION CRACH 
			
            ☐ EIP = 41414141  			
  
2. Determining the buffer size to write exactly into EIP


                  ☐ we send   ( “A” * 1000 + “B” * 1000  )
                  ☐  If EIP contains an 41414141 (AAAA)  WHICH means EIP sits between 500 and 1000 
                  ☐  if EIP contains 42424242 (BBBB), EIP sits between 1000 and 2000 
