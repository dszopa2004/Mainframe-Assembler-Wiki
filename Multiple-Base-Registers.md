# Multiple Base Registers
This page covers how to create multiple base registers in one program.  
For some programs, it may be necessary to have two or more base registers.  

Below the standard entry linkage, add the following block:  
```
         LA    11,4095(,12)      ESTABLISH                                                  
         LA    11,1(,11)               SECOND                                               
         USING CNAME+4096,11              BASE REGISTER    
```
                                       
Here is what the full implementation looks like:            
```
*****************************************************************                           
*                                                                                           
CNAME CSECT                                                                                                                                                                         
* STANDARD ENTRY LINKAGE WITH R12 AS BASE REGISTER                                          
*                                                                                           
         STM   14,12,12(13)    SAVE REGS IN CALLER'S SAVE AREA                              
         LR    12,15 COPY      CSECT ADDR INTO R12                                          
         USING CNAME,12        ESTABLISH R12 AS THE BASE REG                                
         LA    14,SAVEAREA     R14 POINTS TO THIS CSECT'S SAVE AREA                         
         ST    14,8(,13)       STORE ADDR OF THIS CSECT'S SAVE AREA                         
         ST    13,4(,14)       STORE ADDR OF CALLER'S SAVE AREA                             
         LR    13,14           POINT R13 AT THIS CSECT'S SAVE AREA                          
*                                                                                           
         LA    11,4095(,12)      ESTABLISH                                                  
         LA    11,1(,11)               SECOND                                               
         USING CNAME+4096,11              BASE REGISTER                                   
*                                                                                           
* Write your program below                                              
*                          
```
