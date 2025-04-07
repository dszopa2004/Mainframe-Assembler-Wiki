# ASSIST Errors

Info about ASSIST can be found here: https://faculty.cs.niu.edu/~byrnes/csci360/ho/asusergd.shtml#part5a

## 224 BRANCH OUT OF PROGRAM AREA
By definition, this error means the user program attempted to branch outside of its area. The only branch outside not flagged this way is a branch to the return address originally supplied to the user program in register 14.   

A potential fix for this error is not declaring a full 80-byte input buffer.  

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

To fix this, uninitialized storage must be added to the end of the input buffer. Here is the final result:
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
