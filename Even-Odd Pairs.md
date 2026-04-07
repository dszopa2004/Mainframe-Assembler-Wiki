# Even-Odd Pairs
Even-Odd pairs are a key concept in Mainframe Assembly.  
They are used in many instructions such as `MR`, `DR`, `M`, and `D`  

Here is the list of even-odd pairs:  
- 0 and 1
- 2 and 3
- 4 and 5
- 6 and 7
- 8 and 9
- 10 and 11
- 12 and 13
- 14 and 15

## Even-Odd Pairs Explained
Even-odd pairs are a way of representing big, 64-bit numbers while utilizing a 32-bit architecture. This can be difficult to understand, so here is a visual example.

Even-odd pairs can be explained visually by using drawers. Imagine that there are two drawers, a top drawer and a bottom drawer.  

For this example, let's use the even-odd pair registers 2 and 3. The top drawer will represent register 2, and the bottom drawer will represent register 3.    

In the top drawer (register 2), there is a value of `1234`. In the bottom drawer (register 3), there is a value of `5678`.  
When you put the two drawers together, that is how you get a value of `12345678`. This is essentially how even-odd pairs work.  

The top drawer (even register) holds the high-order 32-bits, while the bottom drawer holds the low-order 32-bits. When the bits are combined, it forms the 64-bit number.  
**Even-odd pairs are NOT concatenation. They are just storing the 64-bit value across two registers**  

An even-odd pair is basically two registers treated as one larger value.  
In instructions, you **ALWAYS** specify the even register first, and the system automatically uses the next odd register as part of the pair.  

The high-order 32-bit value in the even register and the low-order 32-bit value in the odd register is seen more clearly in division.

## Using Even-Odd Pairs within Instructions

### Even-Odd Pairs with RX-Type Instructions

### Even-Odd Pairs with RR-Type Instructions
