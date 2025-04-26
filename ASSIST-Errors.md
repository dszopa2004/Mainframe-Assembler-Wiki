# ASSIST Errors

Info about ASSIST can be found here: https://faculty.cs.niu.edu/~byrnes/csci360/ho/asusergd.shtml#part5a

## 222 RECORD LIMIT EXCEEDED
By definition, this error means the user attempted to print or punch more records than was given by combination of R, RD, and RX option values. Execution has been terminated, and at least a partial dump given.

#### Debugging by Checking Carriage Control Characters
A potential fix for this error is to look at the carriage control characters used in your output/header buffers and make sure that they are correct.    

For example, having multiple headers with a carriage control character of '1' can cause this error:
```
HEADER1  DC    C'1'
...
...
...
HEADER2  DC    C'1'
...
...
...
```

To fix this, `HEADER2` should not have a carriage control character of '1'. This is because '1' as a carriage control character means "new page", which creates conflicts when there are multiple "new page" lines. Here is the final result:
```
HEADER1  DC    C'1'
...
...
...
HEADER2  DC    C' '
...
...
...
```

## 224 BRANCH OUT OF PROGRAM AREA
By definition, this error means the user program attempted to branch outside of its area. The only branch outside not flagged this way is a branch to the return address originally supplied to the user program in register 14.   

#### Debugging by Checking Input Buffer Size
A potential fix for this error is to declare a full 80-byte input buffer.  

For example, the following input buffer will throw a `224 BRANCH OUT OF PROGRAM AREA` error:
```
RECORD   DS    0H       INPUT BUFFER
LABEL1   DS    ZL8
LABEL2   DS    ZL5
LABEL3   DS    ZL5
LABEL4   DS    ZL5
LABEL5   DS    ZL5
LABEL6   DS    CL24
```

This is because the total number of bytes in the input buffer only adds up to 52.   

To fix this, uninitialized storage must be added to the end of the input buffer to make the input buffer add up to 80 bytes. Here is the final result:
```
RECORD   DS    0H       INPUT BUFFER
LABEL1   DS    ZL8
LABEL2   DS    ZL5
LABEL3   DS    ZL5
LABEL4   DS    ZL5
LABEL5   DS    ZL5
LABEL6   DS    CL24
         DS    CL28
```

## AS131 UNRESOLVED EXTERNAL REFERENCE

The symbol used in a V-type constant is not defined in the assembly, or is defined but not declared a CSECT or ENTRY. ASSIST does not link multiple assemblies, so this is an error. 
