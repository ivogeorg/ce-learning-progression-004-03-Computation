# CPE 1040 - Fall 2020

This is learning progression 004 for the Fall 2020 installment of the course CPE 1040: Introduction to Computer Engineering at MSU Denver.

Table of Contents
=================

* [CPE 1040 \- Fall 2020](#cpe-1040---fall-2020)
* [Table of Contents](#table-of-contents)
  * [Learning Progression 004: External LEDs](#learning-progression-004-external-leds)
    * [Step 3: Computation](#step-3-computation)
      * [1\. Study](#1-study)
        * [Addition](#addition)
        * [Subtraction in 2s complement](#subtraction-in-2s-complement)
        * [Shifting](#shifting)
        * [Multiplication](#multiplication)
        * [Instruction set architecture](#instruction-set-architecture)
        * [Registers](#registers)
      * [2\. Apply](#2-apply)
      * [3\. Present](#3-present)

## Learning Progression 004: External LEDs

This progression introduces fundamentals of computing, including the binary system of data representation as well as the basics of memory and processing. We introduce assembly language in the context of a minimal instruction set processor. This is where the lowest layer of the software stack and the highest layer of the hardware stack coexist, and where user programs are translated into machine code and executed by the processor one instruction at a time. This is also the level of computing which directly correpsonds to the simplest theoretical models of a computer. We also introduce the input-output capabilities of the micro:bit and build an external circuit to serve as an extension to the built-in 5x5 LED matrix to run our Screensavers program on.

### Step 3: Computation
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

##### Addition
[[toc](#table-of-contents)]

`[<lernact-rd>]`Binary arithmetic is the easiest numeral system to perform arithmetic in. Why? Because there are only two symbols, making for the easiest rules:
1. 0<sub>2</sub> + 0<sub>2</sub> = 0<sub>2</sub>.  
2. 1<sub>2</sub> + 0<sub>2</sub> = 1<sub>2</sub>.  
3. 0<sub>2</sub> + 1<sub>2</sub> = 1<sub>2</sub>.  
4. 1<sub>2</sub> + 1<sub>2</sub> = 10<sub>2</sub>. This means 0 for the current position and `[<cept>]`_carry_ (aka carry over or carry out) of 1 to the adjacent position to the left.    

Before we see an example with larger numbers, let's count in binary. Since counting is adding 1 repeatedly to an accumulated result:
```
         .          ..           .         ...           .          ..           .            ....
 0000  0001  0010  0011  0100  0101  0110  0111  1000  1001  1010  1011  1100  1101  1110      1111 
+     +     +     +     +     +     +     +     +     +     +     +     +     +     +        +      
 0001  0001  0001  0001  0001  0001  0001  0001  0001  0001  0001  0001  0001  0001  0001      0001
----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- -----     -----
 0001  0010  0011  0100  0101  0110  0111  1000  1001  1010  1011  1100  1101  1110  1111     10000
       ^^^^        ^^^^                    ^^^^                                               ^^^^^
```
The dots in the top row signify carry. We started with 4-bit numbers but at the 15-th addition, we either `[<cept>]`_overflow_, or need to add another bit position. The overflow is a carry out of the leftmost position (aka `[<cept>]`_most significant bit_). Each time we have a binary number with all 1s (not counting any leading 0s), and add 1, we overflow to the next power of the base 2. These cases are underlied in the example above. 

Here is an example with two random 9-bit numbers:
```
      .  ....
     101010101
   + 001001111
     ---------
     110010100       
```
Addition is the simplest and most often performed operation in binary. It is also very fast. The rest of the arithmetic operations (subtaction, multiplication, and division) are reduced to (performed via) additions.

##### Subtraction in 2s complement
[[toc](#table-of-contents)]

Subtraction `a - b` is performed by the addition `a + twosCompl(b)`. Let's take a look at an example and explain why this works. Remember that 2s complement only makes sense for _fixed-width_ signed integers (usu. 8, 16, 32, 64, or 128 bits). Let's perform the operation in 13<sub>10</sub> - 76<sub>10</sub> = 6<sub>10</sub> in 2s complement binary, using 8-bit numbers:
```
   00001101                                                             00001101            
 -                                                                    +
   00000111      -->      11111000      -->      11111001      -->      11111001
                                                                        --------
                                                                       100000110
                                                                       ^
      ^                      ^                      ^                      ^
      |                      |                      |                      |
  Original               1s complement          2s complement           Add minuend
  subtraction            of subtrahend          of subtrahend           and 2s complement
                         (flip the bits)        (add 1)                 of subtrahend
                                                                        and overflow
```                                                                       
The addition _overflows_ in 8-bits and the remaining bits show the correct result. This is called `[<cept>]`_silent overflow_, because we carry out of the allowed fixed bit width. We silently dropped 2<sup>8</sup><sub>10</sub> (that is, 256<sub>10</sub>) to get the 8-bit binary 6<sub>10</sub>, namely `00000110`. In other words, `100000110 - 100000000 = 00000110`.   

Let's verify this in decimal and show what we actually did:  
<img src="https://render.githubusercontent.com/render/math?math=13_{10} - 7_{10} =">  
<img src="https://render.githubusercontent.com/render/math?math=13_{10} - 7_{10} %2B 256_{10} - 256_{10} =">  
<img src="https://render.githubusercontent.com/render/math?math=13_{10} %2B (256_{10} - 7_{10}) - 256_{10} =">  
<img src="https://render.githubusercontent.com/render/math?math=13_{10} %2B 249_{10} - 256_{10} =">  
<img src="https://render.githubusercontent.com/render/math?math=272_{10} - 256_{10} =">  
<img src="https://render.githubusercontent.com/render/math?math=6_{10}">  

Adding and subtracting the same number to an algebraic expression doesn’t change its value. If our bitwidth is **n**, we add and subtract **2<sup>n</sup>** to the subtraction expression, use the positive power of 2 to convert the subtrahend to 2s complement, perform an addition with the minuend, and finally subtract (drop) the remaining negative power of 2 by silent overflow. So, subtraction is done essentially by addition. The name **2s complement** comes from the fact that the original subtrahend and its 2s complement add up (complement each other) to the **n-th power of 2**.

##### Shifting   
[[toc](#table-of-contents)]

1. Logical shift.  
2. Arithmetic shift.  
   - Right shift preserves sign.  

##### Multiplication
[[toc](#table-of-contents)]

Addition & shift  
2 2-bit numbers as an example  
```
   10 * 11
   -------
        10
   +   10|
   -------
       110
```

```
   101 * 10101
   -----------
           101
            0|
   +     101||
          0|||
       101||||
   -----------
       1101001
```

```
   10101 * 101
   -----------
         10101
   +        0|
       10101||
   -----------
       1101001
```

**TODO: Table (needed as example for 3.2.4)**  

##### Floating point arithmetic

**TODO** Explain the fractional (half, quarter, eighth, sixteenth, etc.).
**TODO** Scientific notation! Show in decimal and binary

#### 2. Apply
[[toc](#table-of-contents)]

1. `[<lernact-prac>]`Perform _manually_ the operation <img src="https://render.githubusercontent.com/render/math?math=101_{10} - 37_{10}"> in decimal and 2s-complement binary. Show that the results match.    
2. `[<lernact-prac>]`Write a function `addBin(a : string, b : string) : string` to add two binary unsigned integer strings (e.g. `0b00011` and `0b110`) and output (that is, `return`) the result in the same format. Perform the operation `[<cept>]`_bitwise_ and do not convert to and from decimal. Hints and guidelines:
   1. Assume you have unbounded bit width. That is, do not worry about overflow.  
   2. Make sure you align the two strings properly. You might want to `[<cept>]`_pad_ the shorter string, if there is one, before performing the operation.  
   3. Remember binary addition:  
      1. 0<sub>2</sub> + 0<sub>2</sub> = 0<sub>2</sub> and no carry.  
      2. 0<sub>2</sub> + 1<sub>2</sub> = 1<sub>2</sub> and no carry.  
      3. 1<sub>2</sub> + 0<sub>2</sub> = 1<sub>2</sub> and no carry.  
      4. 1<sub>2</sub> + 1<sub>2</sub> = 0<sub>2</sub> and carry of 1<sub>2</sub>.  
3. `[<lernact-prac>]`Perform _manually_ the operation <img src="https://render.githubusercontent.com/render/math?math=01010110_{2} * 10_{2}"> in binary. Show that it is equivalent to a shift. Specify the type (that is, _direction_ and _bit-distance_) of the shift.  
4. `[<lernact-prac>]`Perform the multiplication of two 3-bit numbers, `0b110` and `0b101`, in a table showing each step.    
5. `[<lernact-prac>]`**[Optional challenge, max 5 extra step points]** Write a function `mulBin(a : string, b : string) : string` to multiply two binary unsigned integer strings (e.g. `0b00011` and `0b110`) and output (that is, `return`) the result in the same format. Perform the operation bitwise and do not convert to and from decimal. Hints and guidelines:
   1. Assume you have 8-bit inputs and you have sufficient output bits. _What is the maximum output bit width of the product of two 8-bit unsigned integers?_  
   2. Make sure you advance your `[<cept>]`_operands_ and `[<cept>]`_accumulator_ properly.    
   3. Remember binary multiplication:  
      1. 0<sub>2</sub> * 0<sub>2</sub> = 0<sub>2</sub>.  
      2. 0<sub>2</sub> * 1<sub>2</sub> = 0<sub>2</sub>.  
      3. 1<sub>2</sub> * 1<sub>2</sub> = 1<sub>2</sub>.  
   4. How would you modify, if necessary, the function to work with 2s-complement signed integers?  
6. `[<lernact-prac>]`**[Optional challenge, max 10 extra step points]** Multiplication of two 16-bit numbers on an 8-bit processor. In particular:
   1. Modify the function from the previous task to work _with 8-bit registers only_. That is, assume that you your operands, intermediate values, and results cannot exceed 8-bit units, and numbers requiring more bits are broken down and stored in arrays of 8-bit number strings. `[<cept>]`_Registers_ are a small number of very fast memory locations, deep inside the processor, used in the execution of the processor's instructions. They hold instructions, operands, results, and control state.  
   2. The function signature should be `mulBin8(a : string[], b : string[], product : string[]) : void`. The parameter `a` will be an array, representing a 16-bit 2s-complement signed integer, in [big-endian](https://www.webopedia.com/TERM/B/big_endian.html) order. So will `b`. The third operand, `product`, will contain the result of the operation, in the same format.  
   3. Pay special attention to transfering the carry between consecutive bytes of the result, while it is being accumulated.  

#### 3. Present
[[toc](#table-of-contents)]

In the [programs](programs) directory:
1. Add your program from 3.2.2 with filename `microbit-program-3-2-2.js`.  
2. Add your program from 3.2.5 with filename `microbit-program-3-2-5.js`.  
3. Add your program from 3.2.6 with filename `microbit-program-3-2-6.js`.  

In the [Lab Notebook](README.md):

1. Show your work for 3.2.1 in well-formatted Markdown, including whatever images, tables, or other graphical elements you find necessary.  
2. Link to the program from 3.2.2.  
3. Link to a demo video showing the execution of the program from 3.2.2.  
4. Show your work for 3.2.3 in well-formatted Markdown, including whatever images, tables, or other graphical elements you find necessary.  
5. Show your work for 3.2.4 in well-formatted Markdown, including whatever images, tables, or other graphical elements you find necessary.  
6. Answer the question in 3.2.5.1.  
7. Link to the program from 3.2.5.  
8. Link to a demo video showing the execution of the program from 3.2.5.  
9. Link to the program from 3.2.6.  
10. Link to a demo video showing the execution of the program from 3.2.6.  
