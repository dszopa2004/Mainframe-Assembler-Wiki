# Loops
This page covers loops via a basic read loop.  

### Priming the input
Priming the input is **essential** because it ensures the input file isn't empty before continuing the program.  

Here is how that would look:
```
* Here is where you will write your program.
*
         XREAD RECORD,80     GET FIRST EMPLOYEE RECORD
*
```

Again, this is **very** important for ensuring that the input file isn't empty.

## Basic Loop Structure
A loop always starts with some sort of label. In this case, it is `LOOP`.  
Following the label is the branch condition statement. In this case, it is `BC   B'0100',ENDLOOP`:
* `BC` means branch on condition.
* `B'0100'` is the condition code that is needed to branch to `ENDLOOP`.
* `ENDLOOP` is the label that shows the end of the loop.

Inside the loop is the branch condition that branches to the top of the read loop. This always goes at the bottom of the read loop:
* `BC` means branch on condition.
* `B'1111'` is the condition code that is needed to branch to `LOOP`, which is the top of the loop.

```
LOOP     BC   B'0100',ENDLOOP BRANCH TO ENDLOOP1 IF END-OF-FILE (EOF)
*
*         CODE GOES HERE
*
         BC    B'1111',LOOP   BRANCH TO TOP OF LOOP1 TO CHECK EOF
*
ENDLOOP  DS    0H
*
```

### Creating a basic read loop
Because `XREAD` sets the condition code, it is a key component in looping in a read loop. It is what sets the condition code used in `BC    B'1111',LOOP`:
```
* Here is where you will write your program.
*
         XREAD RECORD,80     GET FIRST EMPLOYEE RECORD
*
LOOP     BC   B'0100',ENDLOOP BRANCH TO ENDLOOP1 IF END-OF-FILE (EOF)
*
*        MORE INSTRUCTIONS HERE, SUCH AS ARITHMETIC
*
         XREAD RECORD,80             READ NEXT EMPLOYEE RECORD
*
         BC    B'1111',LOOP   BRANCH TO TOP OF LOOP1 TO CHECK EOF
*
ENDLOOP  DS    0H
*
```

This means if `XREAD` returns a valid condition code of `B'1111'`, it will loop back to the top of the loop structure and continue the program.  
If `XREAD` reaches the end-of-file, it will return a condition code of `B'0100'`, which will make the loop branch to `ENDLOOP`.  

### Printing output in a basic read loop
With a basic read loop, it is very simple to print output.  
All that is necessary is an `XPRNT` instruction above the `XREAD` inside the loop to print the detail line.  
```
* Here is where you will write your program.
*
         XREAD RECORD,80     GET FIRST EMPLOYEE RECORD
*
LOOP     BC   B'0100',ENDLOOP BRANCH TO ENDLOOP1 IF END-OF-FILE (EOF)
*
*        OTHER INSTRUCTIONS HERE
*
         XPRNT DETAIL,133            PRINT THE EMPLOYEE DETAIL RECORD
*
         XREAD RECORD,80             READ NEXT EMPLOYEE RECORD
*
         BC    B'1111',LOOP   BRANCH TO TOP OF LOOP1 TO CHECK EOF
*
ENDLOOP  DS    0H
*
```
