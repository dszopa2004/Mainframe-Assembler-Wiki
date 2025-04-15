# ABEND Errors
This page covers ABEND errors and what they mean.  

## S0C1

## S0C2

## S0C3

## S0C4

## S0C5

## S0C6


## S0C7 - Data Exception Error
**Critical rules:**  
1. All packed decimal fields need to start with a P. 
2. Need to specify a length.
3. Always DC and initialized to 0 (Unless there is a different value that is specified)
4. Always specify the lengths when using PACK. Never let the lengths default

If these rules aren't followed, you will get a S0C7 (Data Exception).  
*A S0C7 is only possible with packed decimals, but PACK and UNPACK will NOT cause S0C7s*  
