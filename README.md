# Assembly x86-64 instruction set notes

These are all the notes I took while studying the AMD x86-64 instruction 
set from the book: "x86-64 Assembly Language Programming with Ubuntu" 
written by Ed Jorgensen. 

## Useful commands for compiling and linking

Throughout these notes all the compiling has been done using the yasm assembler and the 
default GNU linker ld, these two following commands were used:

1. Compiling:
		`` yasm -g dwarf2 -f elf64 example.asm -l example.lst``
		This command generates both the object file and the list file, with debug info.
2. Linking:
		``` ld -g -o example example1.o example2.o exampl3.o ... ```
		This links the example.o object file with debug info into the exec file example.
3. Debugging: gdb, GNU Debugger

## gdb degubber commands

1. Run program: ``run <args>``
2. continue execution: ``continue``, ``c``
3. continue skipping iterations: ``continue <n>`` (``c <n>``)
   skips n-1 crossing of the breakpoint, get quickly to nth iteration of loop
4. step into next line of code (dont enter func call): ``step``
5. step and enter func call: ``next``
6. ``F3``: restart program
7. ``where``: current activation (call depth)
8. Examine contents of stack/variable: ``x/<n><f><u> $rsp/&variable`` with:
		 ``<n>`` = number of locations to display
		 ``<f>`` = format: hex (x), decimal unsigned (u), char (c), string (s)
		             float (f)
		 ``<u>`` = unit size: byte (b) 8 bits, halfword (h), word (w), giant (g) 64bits
9. read commands from file: ``source <filename>``
10. set logging file to smth: ``set logging file <filename>``
11. ``set logging on/off``
12. overwrite previous log file: ``set logging overwrite``


## Notational conventions

General instruction: 
``<operator> <operand>, <operand>``

Operator: does something (mov, add and others)
Operand: where data is going or coming from

Generally an operand may be a register or memory, directly or indirectly accessed.

##  X86-64 Instruction set (Integer operations only)

### Defining constants

``<name> equ <value>``

### Data section, (declare, define and reserve space in memory for variables)

``<variable-name> <data-type> <initial-value>``

With the following data types:
	1. db 8 bits (declare byte)
	2. dw 16 bits (declare word);
	3. dd 32 bits (double word, dword);
	4. dq 64 bits (quadword, qword)
	5. ddq 128 bits (integer) (declare double quadword, dqword);
	6. dt 128 bits (float)
	
Initialized arrays can be defined comma separated: 
``array1 dd 100, 200, 300, 400  ; 4 element array``


``stringhina db "Hello World!" ; string``


``singlechar db "A"``

#### BSS (Uninitialized) section

`` <var-name> <dataType> <count> ``

This is basically: give me a name to witch I reserve count spaces for dataType 
variables to be stored during program execution.
Data types are: resb (reserve byte), resw (reserve word 16 bit), resd (reserve 32bits),
resq (quadword 64b), resdq (double quadword 128bits).



### Data Movement

Data typically is moved from RAM into the CPU registers before being operated on, 
then the result is sent back to RAM.
The basic data movement is performed with the ``mov`` operand like so:

``mov <dest>, <src>``

Where ``<dest>`` is a destination operand, either a register or memory, since it
is a destination operand, the contents will be overwritten. While ``<src>`` is 
an operand whose value is not changed after the operation.
The value of the source operand is copied into the destination operand which is 
overwritten. The source value retains its value after the operation. Both operands
MUST BE OF THE SAME SIZE!
Destination operand cannot be immediate and BOTH OPERANDS CANNOT BE BOTH
MEMORY, for a memory-to-memory operation is required, two operations must be used.

Thus the syntax is as follows:

``mov eax, dword [myVariable]``

We are accessing memory at "myVariable" mem address, we are asking to copy 
"dword" bits, thus 32bits, into the register eax. At the end of this operation
at the mem add of myVariable there will be still data, and the register eax 
will contain the same data as myVariable.

Remember: when the destination operand and the source operands are both double-words
thus 32bits each, the upper-order (first from left) is set to zero. (so that for
example you dont get the negative number instead).

### Addresses and Values

The only way to access memory is by using brackets. The name of the variable
holds the address of the variable, so:

`` mov eax, dword [myVar] ; copying the content of myVar into eax ``


`` mov eax, dword myVar   ; copying the mem address of myVar into eax``

There is also another way to find the mem address of a variable, by using
the instruction ``lea`` Load Effective Address like so:

`` lea rax, byte [bVar] ; load mem address of 1byte of bVar into rax``


`` lea rbx, dword [dVar] ; load mem address of 4 bytes addresses into rbx``

### Conversion instructions

How can I convert between data types ? It depends if its widening, narrowing,
signed or unsigned.


