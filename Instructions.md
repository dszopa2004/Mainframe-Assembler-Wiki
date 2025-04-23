# Instructions

This page documents any instructions covered by the lecture slides in the order of the slides.
\*\*(EQU) Equates Instruction is not allowed in CSCI 360 as of fall 2025, so it is not covered on this page.

## Assembler Language Program

### CSECT

**Format 1:** `label		CSECT`  
**Format 2:** `label		CSECT ,		comment`

- `label` begins in column1, `CSECT` in column 10, and `,` in column 16
- Comment can begin in any column, 18 through 71, inclusive
- CSECT defines beginning of \_C_ontrol \_SECT_ion
- `label` is required on the `CSECT` and names the program

Labels and names in Assembler (and on mainframe in general):

- 1 to 8 characters in length
- Capital letters, digits 0-9, $, # and @
- Cannot begin with a digit
- $, # and @ reserved for special designation

* Only asterisks (\*) in column 1 can come above or before _CSECT_
* Also `DSECT` (\_D_ummy \_SECT_ion) definitions but discussed later

### END

**Format 1:** `END		label`  
**Format 2:** `END		label		comment`

- `END` begins in column 10 and label in column 16.
- Comment can begin in any column, 18 through 71, inclusive.
- label must match the label on the `CSECT` above!

### BCR (Basic Assember Program)

**Format 1:** `BCR	B'1111',14`  
**Format 2:** `BCR	B'1111',14 	comment`

- BCR begins in column 10 and operands in column 16.
- Comment can begin in any column, 18 through 71, inclusive.
- Returns control to operating system or calling program

### DS

- Use DS (Define Storage) to define uninitialized storage
- MUST BE ON A FULLWORD BOUNDARY

**Format:** `label DS rSLn`**, where**

- r = repetition factor (repeats the definition); default is 1
- S = storage class: F for fullword numeric or C for character data
- Ln = length where n is the number of bytes; must use L if using n

**Examples of Declaring Uninitialized Fullword Storage**

```
NUM1 DS F    → 1 fullword → 4 bytes
NUM2 DS 5F   → 5 fullwords → 20 bytes
NUM3 DS FL3  → 3 bytes
NUM4 DS 4FL2 → 8 bytes
```

- Used for numbers to do arithmetic with, i.e., numeric data
- Default length: 4 bytes
- Data type: 32-bit two's complement, or signed, integer
- Alignment: fullword boundary (absolute address is evenly divisible by 4)

**Examples of Declaring Uninitialized Character Storage**

```
TEXT1 DS CL4   → 4 bytes  → 4 characters
TEXT2 DS 10CL6 → 60 bytes → 60 characters
TEXT3 DS 10C   → 10 bytes → 10 characters
TEXT4 DS 5C    → 5 bytes  → 5 characters
```

- Used for character data stored in EBCDIC encoding, 1 character / byte
- Default length: 1 byte
- Alignment: none

### DC

- Use DC (Define Constant) to define initialized storage
  **Format:** `label DC rSLn'value'`**, where**

* r = repetition factor (repeats the definition); default is 1
* S = storage class: F for fullword numeric or C for character data
* Ln = length where n is the number of bytes; must use L if using n
* value = constant (or initial) value (enclosed in single quotes)

**_NOTE: A 'value' may be specified on a DS statement but it will be ignored by the Assembler_**

**Examples of Declaring Initialized Fullword Storage**

```
Declarations            Resulting Storage in Hex
NUM1 DC F'27'       →   0000001B
NUM2 DC F'-1'       →   FFFFFFFF
NUM3 DC F'2,-1,18'  →   00000002FFFFFFFF00000012
NUM4 DS 3F'1'       →   F5F5F5F5F5F5F5F5F5F5F5F5 (Uninitialized)
```

**Examples of Declaring Initialized Character Storage**

```
Declarations            Resulting Storage in Hex
TEXT1 DC C'HELLO'   →   C8C5D3D3D6
TEXT2 DC CL8'HELLO' →   C8C5D3D3D6404040
TEXT3 DC CL3'HELLO' →   C8C5D3
TEXT4 DS CL4'BYE'   →   F5F5F5F5 (Uninitialized)
```

## Basic Assembler and XDUMP

**Generic Format of an Assembler Instruction**

```
label (opt.) mnemonic operands line_doc
1            10       16       up to 71
```

- Some instructions set a 2-bit condition code (cc)
- The _condition code_ reflects the results of execution of the instruction that sets it

**RX-type Assembler Instructions**

- RX-type instructions involve a D(X,B) type address
- It refers to one of the instructions' two operands
- RX-type instructions are 4-byte-long instructions
- RX-type layout for encoding found in fourth layout from the bottom of page 2 of the System/370 Reference Summary, or "Yellow Card"

**RR-type Assembler Instructions**

- RR-type instructions involve two registers as operands
- RR-type instructions are 2-byte-long instructions
- RR-type layout for encoding found in first layout at the top of page 2 of the System/370 Reference Summary, or "Yellow Card"

### (L) Load Instruction

**Mnemonic: L**  
**Instruction Type: RX**  
**Format:** `label L R,D(X,B)`

- Copies, or loads, the 4 bytes at the absolute address represented by `D(X,B)` into `R`, where `R` represents a register number, 0 through 15.
- `R`'s contents are overwritten.
- `D(X,B)` must be on a fullword boundary.

### (ST) Store Instruction

**Mnemonic: ST**  
**Instruction Type: RX**  
**Format:** `label ST R,D(X,B)`  
**Requires D(X,B) to be on a fullword boundary**

- Stores the contents of `R` at the absolute address represented by `D(X,B)`
- `D(X,B)` must be on a fullword boundary

### (LA) Load Address Instruction

**Mnemonic: LA**  
**Instruction Type: RX**  
**Format:** `label LA R,D(X,B)`

- Loads the 24-bit absolute address represented by `D(X,B)` into the last three bytes of `R`
- Overwrites the contents of `R` and zeros out the first byte of `R`
- Again, _NOT_ the contents at the `D(X,B)` address, but the actual absolute address!

### (A) Add Instruction

**Mnemonic: A**  
**Instruction Type: RX**  
**Format:** `label A R,D(X,B)`  
**Sets the condition code**

- Adds the fullword value at the absolute address represented by `D(X,B)` to the value stored in `R`
- The result overwrites the original value in `R`
- `D(X,B)` must be on a fullword boundary

**Condition Code Interpretation**

- 0 means the result is equal to 0
- 1 means the result is less than 0
- 2 means the result is greater than 0
- 3 means there was overflow

### (S) Subtract Instruction

**Mnemonic: S**  
**Instruction Type: RX**  
**Format:** `label S R,D(X,B)`  
**Sets the condition code**

- Subtracts the fullword value at the absolute address represented by `D(X,B)` from the value stored in `R`.
- The result overwrites the original value in `R`.
- `D(X,B)` must be on a fullword boundary

**Condition Code Interpretation**

- 0 means the result is equal to 0
- 1 means the result is less than 0
- 2 means the result is greater than 0
- 3 means there was overflow

### (LR) Load Register Instruction

**Mnemonic: LR**  
**Instruction Type: RR**  
**Format:** `label LR R1,R2`

- Copies the contents of `R2` into `R1`
- `R1`'s contents are overwritten

### (AR) Add Register Instruction

**Mnemonic: AR**  
**Instruction Type: RR**  
**Format:** `label AR R1,R2`  
**Sets the condition code**

- Adds the contents of `R2` to contents of `R1`
- `R1`'s contents are overwritten

**Condition Code Interpretation**

- 0 means the result is equal to 0
- 1 means the result is less than 0
- 2 means the result is greater than 0
- 3 means there was overflow

### (SR) Subtract Register Instruction

**Mnemonic: SR**  
**Instruction Type: RR**  
**Format:** `label SR R1,R2`  
**Sets the condition code**

- Subtracts the contents of `R2` from `R1`
- `R1`'s contents are overwritten

**Condition Code Interpretation**

- 0 means the result is equal to 0
- 1 means the result is less than 0
- 2 means the result is greater than 0
- 3 means there was overflow

### (XDUMP) XDUMP Instruction

- Used to dump contents of GPRs or program storage

**Mnemonic: XDUMP**  
**Format 1:** `XDUMP , comment`**, where**

- `XDUMP` is in column 10
- `,` is in column 16

* Format 1 produces a hexadecimal dump of the contents of the 16 general purpose registers only

**Format 2:** `XDUMP D(X,B),length comments`**, where**

- `XDUMP` is in column 10
- `D(X,B),length` is in column 16

* Format 2 produces a hexadecimal dump of the contents of the program storage beginning at the `D(X,B)` address or at the label substituted for the `D(X,B)` address

**_NOTE: the dump will not begin exactly at the `D(X,B)` address or the label but rather at the 32-byte boundary preceding it or exactly on it if the `D(X,B)` or the label happens to be ON a 32-byte boundary. RARE!_**

- Note that each line of a storage dump is 8 fullwords, or 32 bytes, long and begins on a 32-byte boundary
- In ASSIST, the 32-byte boundaries start with addresses 000000, 000020, 000040, 000060, etc
- To find the part of storage requested with an XDUMP, determine that part of storage's address from the location counter value in the program listing and then count the bytes into the correct 32-byte line of dump to find that part of storage

## Compare Instructions and Branching

### (C) Compare Instruction

**Mnemonic: C**  
**Instruction Type: RX**  
**Format:** `label C R,D(X,B)`  
**Sets the condition code**  
**Requires D(X,B) to be on a fullword boundary**

- Compares the fullword in R with the fullword at `D(X,B)`
- `D(X,B)` must be on a fullword boundary

**Condition Code Interpretation**

- 0 means there is equality
- 1 means the fullword in `R` is less than the fullword at `D(X,B)`
- 2 means the fullword in `R` is greater than the fullword at `D(X,B)`

### (CR) Compare Register Instruction

**Mnemonic: CR**  
**Instruction Type: RR**  
**Format:** `label CR R1,R2`  
**Sets the condition code**

- Compares the fullword in `R1` to the value in `R2`

**Condition Code Interpretation**

- 0 means there is equality
- 1 means the contents of `R1` are less than the contents of `R2`
- 2 means the contents of `R1` are greater than the contents of `R2`

### (BCR) Branch on Condition Register Instruction

**Mnemonic: BCR**  
**Instruction Type: RR**  
**Format:** `label BCR B'mask',R`

- `mask` is a 4-bit binary mask indicating which condition code(s) to branch on
- `R` is the register with the address to branch to

### (BC) Branch on Condition Instruction

**Mnemonic: BC**  
**Instruction Type: RX**  
**Format:** `label BC B'mask',D(X,B)`

- `mask` is a 4-bit binary mask indicating which condition code(s) to branch on
- `D(X,B)` is the address to branch to

- B in the mask indicates what's inside the apostrophes is in binary.
- The mask is interpreted positionally, not numerically: B'bbbb'
- Think of the positions as yes or no – or true or false – flags.
- The first position is the flag for condition code 0, the second for condition code 1, the third for condition code 2 and the fourth for condition code 3.
- If a binary position is 1, take a branch on that condition code.
- If a binary position is 0, do not take a branch on that condition code.

## ASSIST X-Type Instructions

ASSIST Assembler has a number of X-type instructions that assist beginners in learning important concepts without having to code complex programs

### XREAD Instruction

**Mnemonic: XREAD**  
**Instruction Type: X**  
**Format:** `label XREAD D(X,B),length`  
**Sets the condition code**

- Reads length bytes into the input buffer located at `D(X,B)`
- Used to read an input record from instream data following the program's END statement of a file indicated on an FT05F001 DD card

**Condition Code Interpretation**

- 0 means there was a successful read
- 1 means end-of-file has been reached

### XPRNT Instruction

**Mnemonic: XPRNT**  
**Instruction Type: X**  
**Format:** `label XPRNT D(X,B),length`

- Used to print a line of output
- Prints length bytes of the print line defined at D(X,B), usually 133 bytes, including carriage control byte and spaces
- The first character of print line defined is used to indicate carriage control

#### Carriage Control Characters

- Space is for single spacing: C' '
- Zero (0) is for double spacing: C'0'
- Hyphen (-) is for triple spacing: C'-'
- Number 1 is for a new page: C'1'

### XDECI Instruction

**Mnemonic: XDECI**  
**Instruction Type: X**  
**Format:** `label XDECI R,D(X,B)`  
**Sets the condition code**

- Used to convert a number from its character representation to its binary representation so that it can be used in arithmetic
- Converts the number at `D(X,B)` to binary and stores it in `R`
- Stops scanning for digits when a space is reached
- First character may be a digit, a plus sign (+) or negative sign (-)
- Register 1 is set to the address of where to begin scanning for next number, if one

**Condition Code Interpretation**

- 0 means converted number is 0
- 1 means converted number is less than 0
- 2 means converted number is greater than 0
- 3 means there was an attempt to convert an invalid number

### XDECO Instruction

**Mnemonic: XDECO**  
**Instruction Type: X**  
**Format:** `label XDECO R,D(X,B)`

- Used to convert a number from its binary representation in a register to its character representation so that it can be printed
- Converts the binary number in R to printable character representation and stores it at `D(X,B)`
- The number is converted to a 12-byte character representation
- If the number is negative, a negative sign (-) is printed to the left of first non-zero digit
- Does NOT set the condition code

## Multiplication and Divison

This section does **NOT** cover even-odd pairs. It only covers the multiplication and division instructions.

### (M) Multiply Instruction

**Mnemonic: M**  
**Instruction Type: RX**  
**Format:** `label M R,D(X,B)`

- `R`, the first operand, is always an even numbered register representing an even-odd pair of registers
- The number to multiply must be in the odd numbered register of the even-odd pair before multiplication
- `D(X,B)` is the address of a fullword number to multiply by

### (MR) Multiply Register Instruction

**Mnemonic: MR**  
**Instruction Type: RR**  
**Format:** `label MR R1,R2`

- `R1` is always an even numbered register representing an even-odd pair of registers
- The number to multiply must be in the odd numbered register before multiplication
- `R2` is the number of a register containing the number to multiply by

### (D) Divide Instruction

**Mnemonic: D**  
**Instruction Type: RX**  
**Format:** `label D R,D(X,B)`

- `R` is always an even numbered register representing an even-odd pair of registers
- The number to divide must be in the odd numbered register of the even-odd pair with the sign bit extended through the even numbered register before division
- `D(X,B`) is the address of a fullword number to divide by

### (DR) Divide Register Instruction

**Mnemonic: DR**  
**Instruction Type: RR**  
**Format:** `label DR R1,R2`

- `R1` is always an even numbered register representing an even-odd pair of registers
- The number to divide must be in the odd numbered register of the even-odd pair with the sign bit extended through the even numbered register before division
- `R2` is the number of a register containing the number to divide by

## Moving, Comparing, and More Loading Instructions

This section introduces storage-to-storage (SS-Type) instructions. SS-type instructions have three different formats for encoding as presented on p. 3 of the Yellow Card

### (MVC) Move Character Instruction

**Mnemonic: MVC**  
**Instruction Type: SS**  
**Format:** `MVC D1(L,B1),D2(B2)`

- Moves `L` bytes from `D2(B2)` to `D1(B1)`
- `L`, or length, is coded as a decimal number between 1 and 256
- `MVC` does not actually "move" bytes but rather copies them from the origin address in storage represented by the second operand to the destination address in storage represented by the first operand
- Used to copy character data from one place to another, **NOT** numeric data

### (CLC) Compare Logical Character

**Mnemonic: CLC**  
**Instruction Type: SS**  
**Format:** `label CLC D1(L,B1),D2(B2)`
**Sets the condition code**

- Compares `L` EBCDIC bytes at `D1(B1)` with `L` EBCDIC bytes at `D2(B2)`, one byte at a time from left to right and sets the condition code appropriately
- `L`, or length, is coded as a decimal number between 0 and 256
- Only to be used to compare character bytes, i.e., EBCDIC, and **NOT** numeric data
- As it works its way from left to right, byte-by-byte, logically comparing bytes in the two storage areas, the moment it finds inequality, the comparison stops and the condition code is set

**Condition Code Interpretation**

- 0 means there is equality
- 1 means that the first operand < second operand
- 2 means that the first operand > second operand

### (LTR) Load and Test Register Instruction

**Mnemonic: LTR**  
**Instruction Type: RR**  
**Format:** `label LTR R1,R2`
**Sets the condition code**

- Loads the contents of `R2` into `R1` and sets only sets the condition code
- `R1`'s contents are lost but register being tested, `R2`, is unaffected

**Condition Code Interpretation**

- 0 means `R1` contains 0
- 1 means `R1` contains a negative number
- 2 means `R1` contains a positive number

### (LPR) Load Positive Register Instruction

**Mnemonic: LPR**  
**Instruction Type: RR**  
**Format:** `label LPR R1,R2`
**Sets the condition code**

- Loads absolute value of the contents of `R2` into `R1` and sets the condition code

**Condition Code Interpretation**

- 0 means `R1` contains 0
- 1 ---
- 2 means `R1` contains a positive non-zero number
- 3 means overflow occurred

### (LNR) Load Negative Register Instruction

**Mnemonic: LNR**  
**Instruction Type: RR**  
**Format:** `label LNR R1,R2`
**Sets the condition code**

- Loads negative of the absolute value of the contents of `R2` into `R1` and sets the condition code

**Condition Code Interpretation**

- 0 means `R1` contains 0
- 1 means `R1` contains a negative number
- 2 ---
- 3 ---

### (LCR) Load Complement Register Instruction

**Mnemonic: LCR**  
**Instruction Type: RR**  
**Format:** `label LCR R1,R2`
**Sets the condition code**

- Loads complement (opposite) value of the contents of `R2` into `R1` and sets the condition code

**Condition Code Interpretation**

- 0 means `R1` contains 0
- 1 means `R1` contains a negative number
- 2 means `R1` contains a positive number
- 3 means overflow occurred

## Counter Branching

### (BCT) Branch On Count Instruction

**Mnemonic: BCT**  
**Instruction Type: RX**  
**Format:** `label BCT R,D(X,B)`

- Decrements the value in `R` by 1 and branches to `D(X,B)` if the value in `R` is not 0.
- If `R` contains 0, execution continues with the instruction immediately following `BCT`

### (BCTR) Branch On Count Register

**Mnemonic: BCTR**  
**Instruction Type: RR**  
**Format:** `label BCTR R1,R2`

- Decrements the value in `R1` by 1 and branches to the address in `R2` if the value in `R1` is not 0
- If `R1` contains 0, execution continues with the instruction immediately following `BCTR`

#### Decrementing a register with BCTR

BCTR can be used to decrement a register by one: `BCTR 3,0`

## Immediate Byte Instructions

Immediate byte instructions are of the type SI, or Storage Immediate

### (MVI) Move Immediate Instruction

**Mnemonic: BCT**  
**Instruction Type: SI**  
**Format:** `label MVI D(B),byte`

- Moves the immediate byte specified by `byte` to `D(B)`

Example:

- `MVI 42(5),C'*'` - Moves a \* to the address 42(5)
- `MVI 42(5),X'5C'` - Moves a \* to the address 42(5)

### (CLI) Compare Logical Immediate Instruction

**Mnemonic: CLI**  
**Instruction Type: SI**  
**Format:** `label CLI D(B),byte`
**Sets the condition code**

- Does a logical – **NOT** numeric – comparison between the byte at `D(B)` and the immediate byte

**Condition Code Interpretation**

- 0 means byte at `D(B)` is the same as the immediate byte
- 1 means byte at `D(B)` is logically less than the immediate byte
- 2 means byte at `D(B)` is logically greater than the immediate byte

## Packed Decimals

### PACK Instruction

**Mnemonic: PACK**  
**Instruction Type: SS**  
**Format 1:** `label PACK D1(L1,B1),D2(L2,B2)`  
**Format 2:** `label PACK label1(L1),label2(L2)`

- A little like `XDECI`
- It translates numbers in character format into a format which can can be used for arithmetic
- `PACK` converts zoned decimal numbers into packed decimal numbers
- If the length of the first operand, the target packed decimal field, is not long enough, the number is truncated on the left
- If the length of the first operand, the target packed decimal field, is too long, the number is padded on the left with zeros
- A good rule of thumb: To start, pack numbers into the minimum!
- The maximum length for a packed decimal field is 16 bytes

**Formula for calculating packed decimal target field length: `(zoned decimal number length / 2) + 1`**

### UNPK Instruction

**Mnemonic: UNPK**  
**Instruction Type: SS**  
**Format 1:** `label UNPK D1(L1,B1),D2(L2,B2)`  
**Format 2:** `label UNPK label1(L1),label2(L2)`

- A little like `XDECO`
- It translates numbers in a format which can be used for arithmetic to numbers in character format
- `UNPK` converts packed decimal numbers into zoned decimal numbers
- The rightmost byte of the second operand is placed in the rightmost byte of the first operand, with the zone (sign) and numeric digits reversed
- Zone digit F is added to the remaining digits from right to left, each now taking a byte
- It does the exact reverse of the PACK instruction!

### (AP) Add Packed Instruction

**Mnemonic: AP**  
**Instruction Type: SS**  
**Format 1:** `label AP D1(L1,B1),D2(L2,B2)`  
**Format 2:** `label AP PNUM1(L1),PNUM2(L2)`  
**Sets the condition code**

- Add one packed decimal field to another
- `L1` and `L2` represent lengths coded on both operands, respectively, with 16 being the greatest
- A storage to storage (SS) instruction of format 2

**Condition Code Interpretation**

- 0 means the result is zero
- 1 means the result is negative
- 2 means the result is positive
- 3 means there is overflow

_Note: overflow does not automatically occur if the first operand is shorter than the second, only when the result of the arithmetic operation does not fit into the first operand_

### (SP) Subtract Packed Instruction

**Mnemonic: SP**  
**Instruction Type: SS**  
**Format 1:** `label SP D1(L1,B1),D2(L2,B2)`  
**Format 2:** `label SP PNUM1(L1),PNUM2(L2)`  
**Sets the condition code**

- Subtract one packed decimal field from another
- `L1` and `L2` represent lengths coded on both operands, respectively, with 16 being the greatest
- A storage to storage (SS) instruction of format 2

**Condition Code Interpretation**

- 0 means the result is zero
- 1 means the result is negative
- 2 means the result is positive
- 3 means there is overflow

_Note: overflow does not automatically occur if the first operand is shorter than the second, only when the result of the arithmetic operation does not fit into the first operand_

### (ZAP) Zero and Add Packed Instruction

**Mnemonic: ZAP**  
**Instruction Type: SS**  
**Format 1:** `label ZAP D1(L1,B1),D2(L2,B2)`  
**Format 2:** `label ZAP PNUM1(L1),PNUM2(L2)`  
**Sets the condition code**

- Copy one packed decimal field to another
- The packed decimal field in the first operand is zero'd out before the second operand is copied to it
- `L1` and `L2` represent lengths coded on both operands, respectively, with 16 being the greatest
- A storage to storage (SS) instruction of format 2

**Condition Code Interpretation**

- 0 means the result is zero
- 1 means the result is negative
- 2 means the result is positive
- 3 means there is overflow

_Note: overflow does not automatically occur if the first operand is shorter than the second, only when the result of the arithmetic operation does not fit into the first operand_

### (MP) Multiply Packed Instruction

**Mnemonic: MP**  
**Instruction Type: SS**  
**Format 1:** `label MP D1(L1,B1),D2(L2,B2)`  
**Format 2:** `label MP PNUM1(L1),PNUM2(L2)`

- Multiply one packed decimal field by another
- `L1` and `L2` represent lengths coded on both operands, respectively, with 16 being the greatest
- Also a storage to storage (SS) instruction of format 2
- Does **not** set the condition code

### (DP) Divide Packed Instruction

**Mnemonic: DP**  
**Instruction Type: SS**  
**Format 1:** `label DP D1(L1,B1),D2(L2,B2)`  
**Format 2:** `label DP PNUM1(L1),PNUM2(L2)`

- Divide one packed decimal field by another
- Both quotient **and** remainder are stored in the first operand
- `L1` and `L2` represent lengths coded on both operands, respectively, with 16 being the greatest
- Also a storage to storage (SS) instruction of format 2
- Does **not** set the condition code

### (CP) Compare Packed Instruction

**Mnemonic: CP**  
**Instruction Type: SS**  
**Format 1:** `label CP D1(L1,B1),D2(L2,B2)`  
**Format 2:** `label CP PNUM1(L1),PNUM2(L2)`
**Sets the condition code**

- Compare one packed decimal field with another
- **Always** use CP when comparing one packed decimal field with another and not some other compare instruction
- Does a numeric comparison so the value of the field is important, not the lengths of the operands
- Also a storage to storage (SS) instruction of format 2
- Does NOT change either operand

**Condition Code Interpretation**

- 0 means the two values compared are equal
- 1 means the first operand's value is less than the second operand's
- 2 means the first operand's value is greater than the second operand's
- 3 - not used

### (SRP) Shift and Round Packed Instruction

**Mnemonic: SRP**  
**Instruction Type: SS**  
**Format 1:** `label SRP D1(L,B1),D2(B2),i`  
**Format 2:** `label SRP PNUM1(L),D2(B2),i`
**Sets the condition code**

- Shifts a packed decimal number by decimal digits, left or right
- Used to multiply and divide packed decimal numbers by factors of 10
- L represents the length coded on the first operand with 16 being the greatest
- Often used to add extra decimal places to a number preparing it for division and then to get rid of one or more decimal places and round
- Decimal-overflow Exception – `S0CA` - if a left shift results in losing non-zero digits
- The first operand is the packed decimal field with the number being shifted, the second indicates a left shift by `n` digits and the third is the rounding factor
- Shifting right is done with numbers between 32 and 64 inclusive. This can be done with `n - displacement`
- Shifting left is done with numbers between 1 and 32 inclusive
- The rounding factor can be between 0 and 9

**Condition Code Interpretation**

- 0 means the result is zero
- 1 means the result is negative
- 2 means the result is positive
- 3 means overflow has occurred

### (CVB) Convert to Binary Instruction

**Mnemonic: CVB**  
**Instruction Type: NEED TO DOUBLE CHECK**  
**Format 1:** `label CVB R1,D2(X2,B2)`  
**Format 2:** `label CVB R1,DWORD`

- Converts a packed decimal number in a doubleword of storage on a doubleword boundary to its binary equivalent and stores it in a register
- This instruction is not used often
- It can cause a S0C6 if the second operand is not on a doubleword boundary
- It can also cause a S0C7 if the doubleword does not hold a valid packed decimal number
- It can also cause a S0C9 if the packed decimal number at the `D(X,B)` address is too large to be represented in 32 SIGNED bits in the register

### (CVD) Convert to Decimal Instruction

**Mnemonic: CVD**  
**Instruction Type: NEED TO DOUBLE CHECK**  
**Format 1:** `label CVD R1,D2(X2,B2)`  
**Format 2:** `label CVD R1,DWORD`

- Converts a binary number in the first operand register to its packed decimal equivalent in a doubleword on a doubleword boundary
- This instruction is not used often
- It can cause a S0C6 if the second operand is not on a doubleword boundary

### (ED) Edit Instruction

**Mnemonic: ED**  
**Instruction Type: NEED TO DOUBLE CHECK**  
**Format 1:** `label ED D1(L1,B1),D2(B2)`  
**Format 2:** `label ED ONUM1(15),PNUM1`  
**Sets the condition code**

- Converts a packed decimal number to its printable EBCDIC equivalent
- Takes the packed decimal number at `D2(B2)`, converts it to EBCDIC and places it at `D1(L1,B1)` according to a pre-placed edit pattern
- Note that we do not code a length for the second operand
- It can edit the number inserting special characters as desired
- A hexadecimal edit pattern must be moved into the output field prior to executing the `ED` instruction
- The edit pattern can insert commas and/or a decimal point as desired
- The edit pattern can also supply a character with which we can suppress leading zeros in the number displayed in the print line

**Condition Code Interpretation**

- 0 means the source field is zero
- 1 means the source field is negative
- 2 means the source field is positive
- 3 means the source field is unused

**EDIT PATTERNS ARE COVERED ON A DIFFERENT PAGE**

### (EDMK) Edit and Mark Instruction

**Mnemonic: EDMK**  
**Instruction Type: NEED TO DOUBLE CHECK**  
**Format 1:** `label EDMK D1(L1,B1),D2(B2)`  
**Format 2:** `label EDMK ONUM1(15),PNUM1`
**Sets the condition code**

- `EDMK` is used exactly the same as `ED`, but `EDMK` places the address of the first non-zero digit (from left to right in the output field) in register 1
- If `EDMK` reaches a significance on digit selector (X'21') before reaching a non-zero digit, the address of the byte following the X'21' is placed into register 1
- The address in register 1 can then be used to place a dollar sign, positive sign, negative sign or some other character to the immediate left of the first non-zero digit or where significance is turned on

**Condition Code Interpretation**

- 0 means the source field is zero
- 1 means the source field is negative
- 2 means the source field is positive
- 3 means the source field is unused

#### Notes about ED and EDMK Instructions

- Digits are moved from the source field, one at a time, and from left to right
- If not enough digit selectors, the result will be truncated on the right
- A Data Exception – S0C7 – can occur if the source field is not a valid packed decimal number
