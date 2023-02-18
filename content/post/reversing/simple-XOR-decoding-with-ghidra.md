---
title: "Simple XOR Decoding With Ghidra"
date: 2023-02-16T16:46:10+09:00
draft: false
tags: [ghidra,reversing,XOR,obfuscation,encoding,decoding]
---

## References
  - Talks by Dr. Josh Stroschein, "Using Ghidra to Statically XOR Obfuscated Shellcode" (https://www.youtube.com/watch?v=DgaPPixn9k4)
  - The author's blog: https://0xevilc0de.com/
  - "Repository that provides sample codes mimicking real malware" (https://github.com/jstrosch/learning-malware-analysis )

## Steps
- Create a new project
- Import a shellcode (shellcode.bin provided by the author)
    - Tell Language: x86 (visual studio)
    ![image](/home/user/Work-ghidra/DrJoshStroschein/drjoshstroschein-01.png)
- The analyzed result shows up
    - Go to the first offset (00000000)
    - (at this moment, the codes seem to be a series of random bytes)
    - 'D' or 'Disassemble'
    - (now, the unpacking codes will show up)

## Codes 
- The first jump (at 00000000) goes to 00000016 `CALL FUN_00000002`
- Looking at the codes from 00000002, there is function that works as a XOR decoder:
    - 0x186 and 0x97 seem to be the size of the packed code and the encoding key
    ![image](/home/user/Work-ghidra/DrJoshStroschein/drjoshstroschein-02.png)
- from the code above, 
    ```
    00000009 XOR byte ptr DS:[EAX + ECX*1],0x97
    0000000e TEST ECX,ECX
    00000010 JNZ LAB_00000008
    ```
    - EAX is a location (of the encoded shell codes). This value was popped from the stack at `00000002 POP EAX`, and again it was pushed when the function was called at `00000016 CALL FUN_00000002` as a return address. So, the value must directs to the offset, `00000001b` (the next opcode).
    - ECX is a counter, if ECX is non-zero, it jumps to 00000008(loops back and continue the process). Otherwise(if it reached to the end), it calls EAX (again, EAX has the base address of the shellcodes)
    - At this moment, the encoded codes have been all XOR decoded. 

## Insights?    
- Before decoded, they (the codes below 0000001b) look like weird operations that doesn't make sense at all, or look just random data.
![image](/home/user/Work-ghidra/DrJoshStroschein/drjoshstroschein-03.png)
- Also, withinin the encoded data section, 0x97 appears a lot. We can say that the NULL bytes in the original codes are encoded as the same value with the XOR key (since `0 XOR key == key`)

## How we can obtain the encoded codes?
Two options
1. Run the codes from the debugger placing a break point.
    - The breakpoint can be set at 0000001b (where the shellcode begins), HOWEVER software breakpoints can be overwritten.
    - The better position to place the breakpoint is at 00000012, which is just a moment before the shell code is called, while the codes are decoded and can be _extracted_.
2. A static way
    - the shell codes are located from 
        - the start of the shellcode (00000001b offset)
        - and the offset 000001a1 (0000001b + loop size(0x186))
    - set block over the ecoded codes, then Right-click,  and choose "Clear Code Bytes" from the encoded shellcodes
    - press the "play" menu icon (Display Script Manager)
    - if the "Script Manager" window pops up, filter with "xor", then "XorMemoryScript.java" appers
    ![image](/home/user/Work-ghidra/DrJoshStroschein/drjoshstroschein-04.png)
    - Choose "the Play" icon, again
    - Then ghidra asks which the XorValue is used; enter 0x97
    - Then the decoded codes are displayed on the screen
