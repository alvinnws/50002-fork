---
layout: default
permalink: /problemset/assemblersandcompilers
title: Assemblers and Compilers
description: Practice questions containing topics from Assembler and Compilers
parent: Problem Set
nav_order: 8
---

* TOC
{:toc}

**50.002 Computation Structures**
<br>
Information Systems Technology and Design
<br>
Singapore University of Technology and Design

# Assemblers and Compilers
{: .no_toc}

Each topic's questions are grouped into **three** categories: basic, intermediate, and challenging. You are recommended to do all basic problem set before advancing further. 

{: .note}
The amount of practice problems in this set is smaller than usual because the topics learned this week is mainly to **set up** the knowledge required for the next topic. You will have more practice hand-assembling C instructions during our **lab**. 



## Byte Memory Array Loading (Basic)
The memory is loaded as a byte array `{14, 00, 3F, 60, 18, 00, 5F, 60, 00, 10, 01, 80, 1C, 00, 1F, 64, 00, 00, 00, 00, 02, 00, 00, 00, 04, 00, 00, 00, 00, 00, 00, 00}` before starting execution. 

For example, this means that `MEM[0] = 0x14` and `MEM[2] = 0x3F` and so on.

What is the WORD at memory address `0xC`? And what is the word at memory address `0x14`? 

<div cursor="pointer" class="collapsible">Show Answer</div><div class="content_answer"><p>
<code>MEM[0xC] = 64 1F 00 1C</code>, and <code>MEM[0x14] = 00 00 00 02</code>.
</p></div><br>




## Hand Assembly (Intermediate)

This beta assembly code provided below counts the number of "bulls" in two sets of 4-integer numbers, each valued between 0-9. A "bull" happens when we have the same number in the same position. This code will be used for the next 2 questions as well.

For example, the following set has 2 "bulls", because there's a match in the last digit "0" and in the largest digit "8".
```
set_1 = 8190
set_2 = 8920
```

And this set has 4 bulls because all 4 numbers and 4 positions matches:
```
set_1 = 1234
set_2 = 1234
```

We can represent each set in 32-bit, especially the lower 16-bit. Each digit takes up 4-bit of value. For example:
```
set_1 = 0x2298
set_2 = 0x5678
```

Here's the full code.

```nasm
.include beta.uasm
 
BR(start)
 
.=0x100 | address 256
start:
	CMOVE(0,R1)		| set initial val of var bulls = 0
	CMOVE(0xF,R8)		| set initial val of var mask = 0xF
	CMOVE(0,R3)		| set initial val of var i = 0
	CMOVE(0x0118, R11)	| number set 1 
	CMOVE(0x1218, R12)	| number set 2
 
forbull:
	AND(R11,R8,R9)		| a&mask
	AND(R12,R8,R10)		| b&mask
	CMPEQ(R9,R10,R9)	| if R9==R10 bitwise, R9 = 1
	BEQ(R9, endifbull, R30)	| if R9==0, go to the endifbull
	ADDC(R1,1,R1)		| bull = bull+1
	OR(R11,R8,R11)		| a = a|mask
	OR(R12,R8,R12)		| b = b|mask
 
endifbull:
	SHLC(R8,4,R8)		| mask = mask<<4
	ADDC(R3,1,R3)		| i = i+1
	
endforbull:
	CMPLTC(R3,4,R9)		| if i<4, R9 is 1
	BT(R9, forbull)		| if R9 is 1, go to the forbull loop
 
end:
	LD(R31, answer_key, R2)	| load answer key
	CMPEQ(R2, R1, R0)	| check R2 == R1
    HALT()
 
.=0x200 | address 512
answer_key:
LONG(2)
```


Hand-assemble certain instructions by providing the higher and lower 16-bit of the machine code for these selected instructions. Present your answer in hexadecimal.
1. `AND(R11, R8, R9)`
   <div cursor="pointer" class="collapsible">Show Answer</div><div class="content_answer"><p>
    Higher 16-bit: `0xA12B`, Lower 16-bit: `0x4000`.
    </p></div><br>
2. `BT(R9, forbull)`
   <div cursor="pointer" class="collapsible">Show Answer</div><div class="content_answer"><p>
    Higher 16-bit: `0x7BE9`, Lower 16-bit: `0xFFF5`.
    </p></div><br>
3. `LD(R31, answer_key, R2)`
    <div cursor="pointer" class="collapsible">Show Answer</div><div class="content_answer"><p>
    Higher 16-bit: `0x605F`, Lower 16-bit: `0x0200`.
    </p></div><br>


What is the content of Reg[R0] when the CPU halts?

<div cursor="pointer" class="collapsible">Show Answer</div><div class="content_answer"><p>
The content is 1, which means that the computed answer matches the answer key.
</p></div><br>

The equivalent C code for the above Beta assembly code is shown below, but it is incomplete. Fill in the blanks accordingly without any trailing or leading spaces.

```cpp
int main(void) {
    int bulls = 0;
    int mask = 0x[Blank 1];
    int i = 0;
 
    int a = 0x0118;
    int b = 0x1218;
 
    while (i < [Blank 2]) {
        int x = a & [Blank 3];
        int [Blank 4] = b & mask;
 
        if ([Blank 5] == y) {
            bulls = [Blank 6] + 1;
            a = [Blank 7] | mask;
            b = b | [Blank 8];
        }
 
        mask = mask << [Blank 9];
        i = i + 1;
    }
 
    int answer_key = [Blank 10];
    int result = (answer_key == [Blank 11]);
 
    return [Blank 12];
}
```

<div cursor="pointer" class="collapsible">Show Answer</div><div class="content_answer"><p>
<pre><code class="language-c">int main(void) {
    int bulls = 0;
    int mask = 0xF; 
    int i = 0;
 
    int a = 0x0118;
    int b = 0x1218;
 
    while (i &lt; 4) {
        int x = a &amp; mask;
        int y = b &amp; mask;
 
        if (x == y) {
            bulls = bulls + 1;
            a = a | mask;
            b = b | [mask];
        }
 
        mask = mask &lt;&lt; 4;
        i = i + 1;
    }
 
    int answer_key = 2;
    int result = (answer_key == bulls);
 
    return result;
}</code></pre>
</p></div><br>