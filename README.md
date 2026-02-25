# Quark-Lang-226

<div align="center">
<img src="https://img.shields.io/badge/language-C%2B%2B-blue.svg">
<img src="https://img.shields.io/badge/assembler-NASM-orange.svg">
<img src="https://img.shields.io/badge/platform-linux-lightgrey.svg">
<img src="https://img.shields.io/badge/license-MIT-green.svg">
</div>

This language is designed for easy development of your own 8 or 16-bit operating systems and programs for them.

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

# Documentation (version 0.3.0)

## Inline Assembly

You can embed raw NASM assembly code using the `asm:` directive. Everything after `asm:` is copied directly to the output.

```
asm: mov ax, 0x13
asm: int 0x10
```

## Variables

Variables are declared with the `var` directive. The format:
```
var name size value
```
where size can be:
- `=`  or `1=` – byte (db)
- `2=` – word (dw)
- `4=` – double word (dd)
- `8=` – quad word (dq)

Examples:
```
var counter 1= 0
var msg 1= 'Hello',0
var width 2= 320
```

## Assignment and Arithmetic

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

## Flow Control

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

**Conditional jump (if):**
```
if variable operator value then label :type
```
Compares a variable with a value. If the condition is true, control jumps to the label.
Operators: `==`, `!=`, `<`, `>`, `<=`, `>=`.
`:type` can be `:BYTE`, `:WORD`, `:DWORD` – specifies the size of the data being compared (optional, defaults to byte).
```
if [x] < 10 then label :BYTE
```
or
```
if [x] < 10 then label
```

**Conditional jump (untill):**
```
untill variable operator value label :type
```
Works like `if` but jumps when the condition is **false** (i.e., continues looping until the condition becomes true). Useful for implementing loops.

## Printing

**Print a character:**
```
print: char [var]
print: char 'A'
```

**Print a number (as decimal digit):**
```
print: num [var]
print: num 10
```

**Print a string:**
```
print: str "Hello, world!"
```
Supports escape sequences like `\n` (newline, which outputs CR+LF).

**Print a byte in hexadecimal:**
```
print: hex [address]
```
Prints the byte at the given memory address in hex. The address can be a direct value or a segment:offset enclosed in brackets, e.g., `[0x7c00:0]`.

**Generate hex printing routine:**
```
printhexcode
```
This command outputs a reusable hexadecimal printing routine (`hex_print_routine` and `hex_digit`) at the current position in the assembly. It is automatically generated when `print: hex` is used, but you can also place it manually.

## Keyboard Input

**Read a character from the keyboard:**
```
readch scancode ascii
```
Waits for a key press. Returns the scan code (in `ah`) and the ASCII code (in `al`). If a parameter is given as `:NULL`, that value is not stored. Example:
```
readch scan :NULL      ; store only scan code
readch :NULL ascii      ; store only ASCII
readch scan ascii       ; store both
```

## Graphics

**Draw a pixel:**
```
putpixel x y
```
Sets a pixel at (x, y) with color 15 (white). Requires VGA mode 0x13.

**Clear the screen:**
```
cls textmode
cls vgamode
```
- `textmode` – sets text mode 80x25 (mode 0x03).
- `vgamode` – sets VGA graphics mode 320x200 (mode 0x13).

## Disk Operations

**Initialize segment registers for disk operations:**
```
disk: init
```
Sets `ds`, `es`, `ss` to 0.

**Read a sector using CHS addressing:**
```
disk: read_chs drive cylinder head sector segment offset
```
Reads one sector into memory at `segment:offset`. Example:
```
disk: read_chs 0x80 0 0 1 0x1000 0x0000
```

**Write a sector using CHS addressing:**
```
disk: write_chs drive cylinder head sector segment offset
```
Writes one sector from memory at `segment:offset` to disk.

## Boot Sector

**Generate boot sector signature:**
```
bootsector
```
Writes `times 510-($-$$) db 0` and `db 0x55, 0xaa` to fill the remaining bytes and add the boot signature.

## Miscellaneous

**Include another file (NASM directive):**
```
include "file.inc"
```
Outputs `%INCLUDE "file.inc"`.

**Reboot the system:**
```
reboot
```
Calls `int 0x19` to reboot.

---

Everything else not listed here works as in NASM (data addressing, labels, etc.). The compiler simply passes through any line that does not match a known command, allowing you to write raw assembly when needed.
