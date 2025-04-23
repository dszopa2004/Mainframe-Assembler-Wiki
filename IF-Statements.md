# IF statements
Mainframe Assembler does not have traditional `if` statements. Instead, Mainframe Assembler uses conditional statements that check for specific conditions.

## IF Statements for Dynamically Sized Counters
The following is very useful when it is necessary to print a certain number of lines, but the table or file can grow OR shrink in size.  
A good example is an employee counter, where the number of employees can change.

1. Begin by declaring a doubleword in storage, like so:
```
         DS    0D       USE 0D TO DECLARE AS DOUBLEWORD
TEMPCTR  DC    PL8'0'   A PACKED DECIMAL LABEL TO STORE EMP CTR
```

2. Between the read loop and the print loop, convert the employee counter to binary and store it in a register
```
         ZAP   TEMPCTR(8),PEMPCTR(3)   STORE EMP CTR IN TEMPORARY COUNTER
         CVB   2,TEMPCTR               CONVERT EMP CTR TO BINARY AND STORE IN REGISTER 2
```

3. Finally, at the bottom of the print loop, after printing a detail line, check to see if it is time to branch back up to the top of the print loop
```
         XPRNT DETAIL,133
         LA    2,42(,2)       MOVE TO THE NEXT LINE IN THE TABLE
         BCT   2,LOOP2        BRANCH TO LOOP2 IF R2 == 0
*
* BCT CHECKS IF REG 2 == 0, AND BRANCHES UP TO LOOP2 IF IT IS NOT TO CONTINUE PRINTING THE TABLE
* IF REG 2 == 0, THE PROGRAM CONTINUES WITHOUT BRANCHING TO THE TOP OF LOOP 2 AND DOES NOT PRINT ANY MORE OF THE TABLE
*
         DROP  4              DROP THE REGISTER THAT THE TABLE HAS ESTABLISHED ADDRESSIBILITY
```

### The Full Example Program
```
*
* TOP OF PROGRAM
*
********************************************************************
* LOOP1 (READ LOOP)                                                *
********************************************************************
*
         ZAP   TEMPCTR(8),PEMPCTR(3)   STORE EMP CTR IN TEMPORARY COUNTER
         CVB   2,TEMPCTR               CONVERT EMP CTR TO BINARY AND STORE IN REGISTER 2
*
********************************************************************
* LOOP2 (PRINT LOOP)                                               *
********************************************************************
*
LOOP2    DS    0H
*
* CALCULATIONS SOMEWHERE UP HERE
*
         XPRNT DETAIL,133     PRINT DETAIL LINE
         LA    2,42(,2)       MOVE TO THE NEXT LINE IN THE TABLE
         BCT   2,LOOP2        BRANCH TO LOOP2 IF R2 == 0
*
* BCT CHECKS IF REG 2 == 0, AND BRANCHES UP TO LOOP2 IF IT IS NOT TO CONTINUE PRINTING THE TABLE
* IF REG 2 == 0, THE PROGRAM CONTINUES WITHOUT BRANCHING TO THE TOP OF LOOP 2 AND DOES NOT PRINT ANY MORE OF THE TABLE
*
         DROP  4              DROP THE REGISTER THAT THE TABLE HAS ESTABLISHED ADDRESSIBILITY
*
********************************************************************
* STORAGE DECLARATIONS                                             *
********************************************************************
         LTORG
*
* OTHER STORAGE DECLARATIONS
*
         DS    0D       USE 0D TO DECLARE AS DOUBLEWORD
TEMPCTR  DC    PL8'0'   A PACKED DECIMAL LABEL TO STORE EMP CTR
```
