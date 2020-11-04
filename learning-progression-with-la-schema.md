# CPE 1040 - Fall 2020

This is learning progression 004 for the Fall 2020 installment of the course CPE 1040: Introduction to Computer Engineering at MSU Denver.

Table of Contents
=================

* [CPE 1040 \- Fall 2020](#cpe-1040---fall-2020)
  * [Learning Progression 004: External LEDs](#learning-progression-004-external-leds)
    * [Step 3: Computation](#step-3-computation)
      * [1\. Study](#1-study)
        * [Addition](#addition)
        * [Subtraction in 2s complement](#subtraction-in-2s-complement)
        * [Shifting](#shifting)
        * [Multiplication](#multiplication)
        * [Floating point arithmetic](#floating-point-arithmetic)
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
The dots in the top row signify carry. We started with 4-bit numbers but at the 15-th addition, we either `[<cept>]`_overflow_, or need to add another bit position. The overflow is a carry out of the leftmost position (aka `[<cept>]`_most significant bit_). Each time we have a binary number with all 1s (not counting any leading 0s), and add 1, we overflow to the next power of the base 2. These cases are underlined in the example above. 

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

Subtraction `a - b` is performed by the addition `a + twosCompl(b)`. Let's take a look at an example and explain why this works. Remember that 2s complement only makes sense for _fixed-width_ signed integers (usu. 8, 16, 32, 64, or 128 bits). Let's perform the operation 13<sub>10</sub> - 7<sub>10</sub> = 6<sub>10</sub> in 2s complement binary, using 8-bit numbers:
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

Adding and subtracting the same number to an algebraic expression doesn’t change its value. If our bit-width is **n**, we add and subtract **2<sup>n</sup>** to the subtraction expression, use the positive power of 2 to convert the subtrahend to 2s complement, perform an addition with the minuend, and finally subtract (drop) the remaining negative power of 2 by silent overflow. So, subtraction is done essentially by addition. The name **2s complement** comes from the fact that the original subtrahend and its 2s complement add up (complement each other) to the **n-th power of 2**.

##### Shifting   
[[toc](#table-of-contents)]

Shifting is a simple but key operation in binary arithmetic. It involves moving bits left or right in their original order. For example, shifting the number `00000110` to the left by 1 position results in `00001100`. Notice that we dropped a 0 on the left and added a 0 on the right. How are the two numbers related? Let's see:  
00000110<sub>2</sub> = 6<sub>10</sub>, and  
00001100<sub>2</sub> = 12<sub>10</sub>.  

Indeed, a left shift doubles the number. Try it with some other numbers and then prove it in general (e.g. by examining the relation of the sums of powers of the base of the two numbers).  

Notice that the left shift can _overflow_ if the number that is dropped on the left is a 1. For example, shifting the number `10000000` (128<sub>10</sub>) to the left by 1 bit results in `0000000` (zero).  

What happens if we shift to the right? Let's see what happens if we shift the number `00000110` to the right by 1 position, which results in `00000011`:  
00000110<sub>2</sub> = 6<sub>10</sub>, and  
00000011<sub>2</sub> = 3<sub>10</sub>.  

The right shift halves the number. Again, try to prove it in general.

There are two things to note about the right shift:
1. First, there are two types of right shift, called `[<cept>]`_logical_ and `[<cept>]`_arithmetic_. The logical shift always adds 0s at the left, whereas the arithmetic shift adds `[<cept>]`_sign bits_ at the left. Remember that in 2s complement signed integers, all negative numbers have a 1 for the most significant (that is, the leftmost) bit (MSB). The arithmetic right shift would pad the shifted negative number with 1s on the left and would pad the shifted non-negative number with 0s.  
2. Second, the division by two upon right shift is an `[<cept>]`_integer division_. This means that any fractional part is lost. For example, shifting our last number `00000011` (3<sub>10</sub>) 1 more bit to the right results in `00000001` (1<sub>10</sub>).  

##### Multiplication
[[toc](#table-of-contents)]

Multiplication is just addition and shifting. Let's look at an example:  
```
   10 * 11
   -------
        10
   +   10<
   -------
       110
```
We are multiplying the left multiplicand by the bits of the right one, shifting the result to the left each time. The shift is indicated by a `<`. Let's try larger numbers and commute the multiplication (meaning change the order of the operands) to double check:   
```
   101 * 10101
   -----------
           101
            0<
   +     101<<
          0<<<
       101<<<<
   -----------
       1101001
```
and  
```
   10101 * 101
   -----------
         10101
   +        0<
       10101<<
   -----------
       1101001
```
Of course, the result is the same, but we did more shifting in the first version.  Let's look at the second version in a table to show the order of operations:

Operation | Left operand | Right operand bit | Accumulator result
--- | --: | --: | --:
Start | `10101` | `10`**`1`** | `00000000`
Add left operand to accumulator |  `10101` | `10`**`1`** | `00010101`
Shift left the left operand by 1 bit | `101010` | `1`**`0`**`1` | `00010101`
Shift left the left operand by 1 bit | `1010100` | **`1`**`01` | `00010101`
Add left operand to accumulator | `1010100` | **`1`**`01` | `01101001`

Notice two things:
1. The accumulator is as wide as the two multiplicative operands combined.  
2. Whenever there is a 0 bit in the right operand, there is just a left shift of the left operand; whenever there is a 1 bit in the right operand, there is a left shift of the left operand and addition of the left operand to the accumulator.  

But how can we get the correct bit of the right operand? We right shift it and `[<cept>]`_mask out_ all but the `[<cept>]`_least significant bit (LSB)_ (meaning the rightmost). Let's recall the logical function and operation AND:

a | b | a AND b
--- | --- | ---
0 | 0 | 0
0 | 1 | 0
1 | 0 | 0
1 | 1 | 1

AND is 1 only when **both** operands are 1. Now, let's see what that operation does when we apply it to the right operand:  
```
      101
AND
      001
---------
      001
```
The second operand of the AND operation is called a `[<cept>]`_mask_, because it masks out all but the bit of interest, which happens to be 1 in this case. Let's see how this works along with a right shift of the first operand (which is the right multiplicand from the above multiplication example):

Operation | Right multiplicand | Result from operation | Corresponding operations on left multiplicand
--- | --: | --: | ---
Mask out LSB of right multiplicand (AND with `001`) | `101` | `001` | Left shift and add to accumulator
Shift right the right multiplicand by 1 bit         | `101` | `010` | _none_
Mask out LSB of right multiplicand (AND with `001`) | `010` | `000` | Left shift
Shift right the right multiplicand by 1 bit         | `010` | `001` |_none_
Mask out LSB of right multiplicand (AND with `001`) | `001` | `001` | Left shift and add to accumulator

##### Floating point arithmetic
[[toc](#table-of-contents)]

Floating-point arithmetic is not difficult to understand, but requires specialized hardware to do fast. For example, the current version of the micro:bit (v1.5) does not have specialized hardware to do floating-point arithmetic, but the new version (v2) does. 

First, let's get some intuition about the `[<cept>]`_significand_ (meaning the fractional part of the floating-point number). Here's the interpretation of the number `0.1011`:

Symbol | `0` | `.` | `1` | `0` | `1` | `1`
--- | :-: | :-: | :-: | :-: | :-: | :-:
Power of the base 2 | 1 | n/a | -1 | -2 | -3 | -4
Interpretation | Zero whole number | Fractional point | 1 half | 0 quarter | 1 eight | 1 sixteenth

Remember that the IEEE 754 floating-point standard utilized the `[<cept>]`_scientific notation_. The floating-point addition and subtraction operations consist of 3 steps:
1. Shift the two numbers so that you arrive at equal exponents.  
2. Perform the operation on the two non-exponentiated operand multipliers.  
3. Bring the result back to scientific notation.  

For example:  
<img src="https://render.githubusercontent.com/render/math?math=1.101 * 2^{10} %2B 1.011 * 2^{9} =">  
<img src="https://render.githubusercontent.com/render/math?math=1.101 * 2^{10} %2B 0.1011 * 2^{10} =">  
<img src="https://render.githubusercontent.com/render/math?math=1.101 * 2^{10} %2B 0.1011 * 2^{10} =">  
<img src="https://render.githubusercontent.com/render/math?math=10.0101 * 2^{10} =">  
<img src="https://render.githubusercontent.com/render/math?math=1.00101 * 2^{9}">  

For multiplication and division:  
1. The exponents are summed or subtracted.  
2. The non-exponentiated operand multipliers are multiplied or divided.   
3. The result is brought back to scientific notation.  

For example:  
<img src="https://render.githubusercontent.com/render/math?math=1.101 * 2^{10} * 1.011 * 2^{9} =">  
<img src="https://render.githubusercontent.com/render/math?math=(1.101 * 1.011) * 2^{19} =">  
<img src="https://render.githubusercontent.com/render/math?math=10.001111 * 2^{19} =">  
<img src="https://render.githubusercontent.com/render/math?math=1.0001111 * 2^{20}">  

The multiplication on the second line above, in decimal, is <img src="https://render.githubusercontent.com/render/math?math=1.625 * 1.375 = 2.234375">.   


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

7. `[<lernact-disc>]`**[Optional challenge, max 10 extra step points]** We know that floating-point arithmetic is inexact. Why is the error in a floating-point arithmetic operation proportional to the `[<cept>]`[_absolute difference_](https://en.wikipedia.org/wiki/Absolute_difference) of the two operands (meaning <img src="https://render.githubusercontent.com/render/math?math=|a - b|">)?

8. `[<lernact-prac>]`**[Optional challenge, max 5 extra step points]** Prove that the left shift doubles an integer (bar overflow) and an arithmetic right shift halves it (bar loss of the fractional part).  

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
11. Show your work for 3.2.7 in well-formatted Markdown, including whatever images, tables, or other graphical elements you find necessary.  
12. Show your work for 3.2.8 in well-formatted Markdown, including whatever images, tables, or other graphical elements you find necessary.  
