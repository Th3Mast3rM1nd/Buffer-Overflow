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













EIP - The Instruction Pointer

EIP, the instruction pointer, is one of the most important registers for our purposes as it always points to the next code instruction to be executed. Since EIP essentially directs the flow of a program, it is an attacker's primary target when exploiting any memory corruption vulnerability such as a buffer overflow.


After sending the buffer ([MyVar][EBP][EIP][your code]), ESP will/should point at the beginning of [your code]. So if you can make EIP go to your code,
 you’re in control.



1. overflow the  buffer with ( “A" * 500 ) and see if the instruction pointer contains 41414141 which is AAAAA in Hex application dies . 
			
            ☐ EIP = 41414141  			
  
2. Determining the buffer size to write exactly into EIP


                  ☐ we send   ( “A” * 1000 + “B” * 1000  )
                  ☐  If EIP contains an 41414141 (AAAA)  WHICH means EIP sits between 500 and 1000 
                  ☐  if EIP contains 42424242 (BBBB), EIP sits between 1000 and 2000 
         					
3. Two Possibilities :
			
            1. EIP = 414141 which mean EIP has an offset  Betwwen 500 and 1000 .
                      ☐   Dump ESP to see if conatins bunch of BBBBBBBBB ( the buffer )
                                          ☒   Finding The exact location in our buffer that overwrite the EIP 
                                                    1.  ./pentest/exploits/framework3/tools/pattern_create.rb -l 1000  ( generate a string that contains unique patterns )
                                                    2. we send the uniq patterns + “B” * 1000 to application and we see what is the value of EIP 
                                                    3. lets say EIP = 0x35614234  which is HEX of  34 42 61 35 = 4Ba5
                                                    4./pentest/exploits/framework3/tools/pattern_offset.rb -q 0x35614234 -l 1000 ( OUTPUT = 794 )
                                                		 5. 794 bytes  That’s the buffer length needed to overwrite EIP .
                                                		 6. create a string with “A” * 764  + “BBBB” + “C” * 1000 ( C is the ESP buffer after we overwrite the EIP ) 
                                                		 7. EIP should  =  424242 and ESP points to our buffer (C’s) 
                                                                                              														
                                        				
| Buffer  | EBP | EIP | ESP |
| A * 790  | AAAA | BBBB | CCCCCCCCCCCCCCCCCC.... |
| 4141414141414141414141... | 41414141 | 42424242 |  |
| 790 bytes | 4 bytes | 4 bytes | 1000 bytes but could be less or more deoends how much you needed to fill the buffer  |

           2. EIP = 424242  which mean EIP has an offset  Betwwen 1000 and 2000
           						☐   Dump ESP to see if conatins bunch of BBBBBBBBB ( the buffer )
           						 					☒   Finding The exact location in our buffer that overwrite the EIP.
           																 1.  ./pentest/exploits/framework3/tools/pattern_create.rb -l 1000  ( generate a string that contains unique patterns )
                                                    2. we send “A" * 1000 + uniq Pattern  to application and we see what is the value of EIP 
                                                    3. lets say EIP = 0x35614234  which is HEX of  34 42 61 35 = 4Ba5
                                                    4./pentest/exploits/framework3/tools/pattern_offset.rb -q 0x35614234 -l 1000 ( OUTPUT = 794 )
                                                		 5. 794 bytes  That’s the buffer length needed to overwrite EIP + 1000 
                                                		 6. create a string with “A”  1764  + “BBBB” + “C” * 1000 ( C is the ESP buffer after we overwrite the EIP ) 
                                                		 7. EIP should  =  424242 and ESP points to our buffer (C’s) 
                                                										
| Buffer  | EBP | EIP | ESP |
| A * 1790  | AAAA | BBBB | CCCCCCCCCCCCCCCCCC.... |
| 4141414141414141414141... | 41414141 | 42424242 |  |
| 1790 bytes | 4 bytes | 4 bytes | 1000 bytes but could be less or more deoends how much you needed to fill the buffer  |
