# QUARK

<div align="center">
<img src="https://img.shields.io/badge/language-C%2B%2B-blue.svg">
<img src="https://img.shields.io/badge/assembler-NASM-orange.svg">
<img src="https://img.shields.io/badge/platform-linux-lightgrey.svg">
<img src="https://img.shields.io/badge/license-MIT-green.svg">
</div>

This language is designed for easy development of your own operating systems and programs for them.

Requires g++, NASM and Qemu. 
To install on Ubuntu/Debian:
```bash
sudo apt update
sudo apt install g++ nasm qemu-system-x86
```
If you have changed the compiler, compile it with the command:
```bash
g++ -o compiler compiler.cpp
```
Compile the .qkl file:
```bash
./compiler input.qkl -o output.asm
```
Compile the .asm file:
```bash
nasm -f bin output.asm -o boot.bin
```
Run the .bin file:
```bash
qemu-system-x86_64 boot.bin
```
# Documentation (version 0.2.0)

**Variables**

Variables are declared with the var directive. The format:
```
var name size value
```
where size can be:
    = - byte (db)
    
    1= – byte (db)

    2= – word (dw)

    4= – double word (dd)

    8= – quad word (dq)
  
Examples:
```
var counter 1= 0
var msg 1= 'Hello',0
var width 2= 320
```
### Assignment and Arithmetic

**Assignment:**
```
[var] = 10
al = 10
```

**Addition assignment:**
```
[var] += 5
al += 10
```
**Subtraction assignment:**
```
[var] -= 5
al -= 10
```
**Increment:**
```
bx ++
[var] ++
```
**Decrement**
```
bx --
[var] --
```

**Convert character to number:**
```
[var] <tonum>
```
**Convert number to character:**
```
[var] <tochar>
```
### Flow Control

**Label:**
```
POINT label:
```
**Unconditional jump:**
```
jump label
```
**Subroutine call:**
```
call label
```
**Return from subroutine:**
```
term
```
**Conditional jump:**
```
if variable operator value (any word) label :type
```

Compares a variable with a value. If the condition is true, control jumps to the label.
Operators: ==, !=, <, >, <=, >=.
 :type can be :BYTE, :WORD, :DWORD – specifies the size of the data being compared.
```
if [x] < 10 then label :BYTE
```
or
```
if [x] < 10 then label
```
**Print a character:**
```
print: char [var]
print: char 'A'
```
**Print a num:**
```
print: num [var]
print: num 10
```
**Read a character from the keyboard:**
```
readch scancode ascii
```
Waits for a key press. Returns the scan code and the ASCII code. If the corresponding parameter is given as :NULL, the value is not stored.

### Graphics

**Draw a pixel:**
```
putpixel x y
```
**Clear the screen:**
```
cls textmode
cls vgamode
```
textmode – sets text mode 80x25 (mode 0x03).

vgamode – sets VGA graphics mode 320x200 (mode 0x13).

### other

Everything else works as in NASM (data addressing, etc.)
