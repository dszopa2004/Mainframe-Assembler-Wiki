# ABEND Errors
This page covers ABEND errors and what they mean.  

## S0C1 - Operation Exception:  
* Adding a fullword of 0 in the middle of the code will quickly ABEND the code with a S0C1
* "ASSIST COMPLETION DUMP" means there was an ABEND
* Completion code "System = 0C1 OPERATION" means S0C1
* Bad op code, not an instruction

## S0C2

## S0C3

## S0C4 - Protection Exception:
* Trying to access storage that is outside of the limits of my program
* The PSW key doesn't match the PSW key of that storage
* Have a good, valid address in the register, but its displacement isn't within the scope of the program storage

## S0C5 - Addressing Exception:
* This means there is an operand or an address that is not valid

## S0C6 - Specification Exception (a.k.a Spec Exception):
* This is when you're trying to refer to an address not on a proper boundary. Meaning the instruction requires a doubleword/fullword/halfword boundary, but it is not on a doubleword/fullword/halfword boundary
* Improper boundary alignment
* Example: `NUM1 DC C'0356'` is trying to store an integer in a character string

## S0C7 - Data Exception Error
**Critical rules:**  
1. All packed decimal fields need to start with a P. 
2. Need to specify a length.
3. Always DC and initialized to 0 (Unless there is a different value that is specified)
4. Always specify the lengths when using PACK. Never let the lengths default

If these rules aren't followed, you will get a S0C7 (Data Exception).  
*A S0C7 is only possible with packed decimals, but PACK and UNPACK will NOT cause S0C7s*  
