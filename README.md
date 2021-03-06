# data-type-convert (FASM)

**16BIT x86 minimal library written in FASM assembly**, focused into provide the essential resources, to **convert byte values to diferent ASCII representations**. Code is no OS dependant, so should run under all kind of 16BIT enviroments, and could be easilly ported to 32BIT architectures.

-----
###  Available procedures:

  - `itoa` : Signed Integer to ASCII
  - `uitoa` : Unsigned Integer to ASCII
  - `hextoa` : Hexadecimal to ASCII
  - `bintoa` : Binary to ASCII
  - `atoi` : ASCII to Integer

###  Usage Specifications:
Procedure | Input Registers
------------ | -------------
`itoa`   | Operates over the signed **WORD** value of the **AX** register
`uitoa`  | Operates over the unsigned **WORD** value of the **AX** register
`hextoa` | Operates over the **BYTE** value of the **AL** register
`bintoa` | Operates over the **BYTE** value of the **AL** register
`atoi`   | Uses the **AX** string pointer and returns an integer in **AX**

**Output**: All procedures store their results into a (null terminated) string buffer, referenced by a pointer stored in CX register, after procedure execution is complete (exept: atoi).

:information_source: **`IMPORTANT: This library uses the STACK to preserve the values of the registers on procedure calls. Ensure you have setted your stack segment and pointer.`**

#### Usage Example:
The following code implements an enviroment to run the example conversion, and provides a print procedure (using BIOS interrupt) to output the result to screen.

```asm
use16

main:
    ; set Data and Stack Segments, and Stack Pointer
	xor	ax,	ax
	mov	ds,	ax
	mov	ax,	0x8000
	mov	ss,	ax
	mov	sp,	0x0000
    ;*** Make converstion! ***
    mov ax, -25231
    call    itoa
    ; *** Conversion done ***
    mov  ax, cx
    call print
    halt:
    	hlt
    	jmp halt


print:
    mov si, ax              ; copy AX pointer to SI
    .nextChar:
        lodsb               ; load string byte and stores it in AL
        cmp al, 0
        je .done            ; If char is zero, end of string detected!
        mov ah, 0x0E        ; single character BIOS print service
        mov bh, 0x00        ; Page num 0
        mov bl, 0x07        ; Text Color attributes
        int 0x10            ; Call BIOS video interrupt
        jmp .nextChar       ; follow with next char
    .done:
        RET

include 'lib/data-type-convert.asm'
```

### Todo

 - Implement inverse procedures: `atoi` | `atoui` | `atohex` | `atobin`


