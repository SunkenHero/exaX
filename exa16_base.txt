


# Fixed memory map
0x0000 - 0x01FF         Stack
0x0200 - 0x02FF         Interrupt vector map
0x0300 - 0x03FF         reserved

Programm counter starts at 0x0400


# Interrupts
When a interrupt is called, it pushes ALL registers onto the stack and jumps to the interrupt handler address / interrupt vector

## Interrupt vector map
The interrupt vector map is located at 0x0200 to 0x02FF
It consists of a list of byte triples: [address lower] [address mid] [address higher]
Each of these triples is where the interrupt handler for the interrupt with the id ((interrupt handler address - 512) / 3) is located in programm memory



# Instruction layout
8 bit = opcode
8 bit = argument one
8 bit = argument two
8 bit = argument three
..... = argument ....

## arguments:

### other arguments:
[8 bit = value]

### register arguments:
       msb                lsb
[4 bit = reserved] [4 bit = register]


# Registers

        SPECIAL
0000    24 bit          PC          Programm counter
0001    8  bit          SP          Stack pointer (addres / 2)
0010    16 bit          F           Flags register
        GENERAL PURPOSE
0011    16 bit          A           GPR A
0100    16 bit          B           GPR B
0101    16 bit          C           GPR C
0110    16 bit          D           GPR D
0111    16 bit          E           GPR E
1000    16 bit          F           GPR F
1001    16 bit          G           GPR G
1010    16 bit          H           GPR H


# Flag register
msb                                    lsb
        [negative] [reserved] [carry] [zero]


# Instructions

0000 0000        nop

0000 0001       add     [a] [b] [out]                                   Adds register [a] and register [b] into register [out]
0000 0010       adc     [a] [b] [out]                                   Adds register [a] and register [b] with carry into register [out]
0000 0011       neg     [a] [b] [out]                                   Negates register [a] and register [b] into register [out]
0000 0100       sub     [a] [b] [out]                                   Subtracts register [a] and register [b] into register [out]
0000 0101       sbc     [a] [b] [out]                                   Subtracts register [a] and register [b] with carry into register [out]
0000 0110       mul     [a] [b] [out]                                   Multiplies register [a] and register [b] into register [out]
0000 0111       inc     [a]                                             Increments register [a]
0000 1000       dec     [a]                                             Increments register [a]

0001 0000       shl     [a] [x] [out]                                   Shifts the register [a] left by [x] bits into register [out]
0001 0001       shr     [a] [x] [out]                                   Shifts the register [a] right by [x] bits into register [out]
0001 0010                                        reserved
0001 0011       asr     [a] [x] [out]                                   Arithmetic shifts the register [a] right by [x] bits into register [out]
0001 0100       rol     [a] [x] [out]                                   Rotates the register [a] left by [x] bits into register [out]
0001 0101       ror     [a] [x] [out]                                   Rotates the register [a] right by [x] bits into register [out]

0010 0000       and     [a] [b] [out]                                   Bitwise and register [a] and register [b] into register [out]
0010 0001       orr     [a] [b] [out]                                   Bitwise nor register [a] and register [b] into register [out]
0010 0010       not     [a] [b] [out]                                   Bitwise not register [a] and register [b] into register [out]
0010 0011       xor     [a] [b] [out]                                   Bitwise xor register [a] and register [b] into register [out]

0011 0000       phr     [a]                                             Pushes register [a] onto the stack
0011 0001       plr     [a]                                             Pulls register [a] from the stack
0011 0010       phf                                                     Pushes the flags register onto the stack
0011 0011       plf                                                     Pulls the flag register from the stack

0100 0000       scf                                                     Set carry flag
0100 0001       ucf                                                     Unset carry flag
0100 0010       unf                                                     Unset negative flag  

0101 0000       rsr                                                     Pulls the programm counter from the stack       (Return from Sub Routine)
0101 0001       jmp     [addrees]                                       Jumps to the specified address if the zero flag is set (24 bit)
0101 0010       jnz     [address]                                       Jumps to the specified address (24 bit)
0101 0011       jmc     [address]                                       Jumps to the specified address if the carry flag is set (24 bit)
0101 0100       jmz     [address]                                       Jumps to the specified address if the zero flag is not set (24 bit)
0101 0101       jsr     [address]                                       Pushes the value of the programm counter onto the stack and jumps to the specified address (24 bit)             (Jump to Sub Routine)
0101 0110       jmn     [address]                                       Jumps to the specified address if the negative flag is set (24 bit)
0101 0111       jnn     [address]                                       Jumps to the specified address if the negative flag is not set (24 bit)

0110 0000       tst     [a]                                             If register [a] is less than register [b] (value), it unsets the zero flag (->0) / sets the zero flag (->1) if they are not euqal
0110 0001       cle     [a] [b]                                         Checks the register [a] and updates the flag register
0110 0010       bit     [a] [b]                                         Extracts the [b]th bit of the register [a] into the zero flag
0110 0011       ceq     [a] [b]                                         If register [a] and register [b] (value) are equal, it unsets the zero flag (->0) / sets the zero flag (->1) if they are not euqal
0110 0100       cls     [a] [b]                                         If register [a] is less than or equal to register [b] (value), it unsets the zero flag (->0) / sets the zero flag (->1) if they are not euqal

0111 0000       cop     [inst] [reg]                                    Set the coprocessor pin on the cpu, sets the address bus to [inst] (16 bit), sets the data bus to the value of register [reg], and waits for any external interrupt (vector ignored)
0111 0001       cor     [ints] [reg] [out]                              Set the coprocessor pin on the cpu, sets the address bus to [inst] (16 bit), sets the data bus to the value of register [reg], and waits for any external interrupt. When the external interrupt is caused, it stores the value on the data bus into the register [out]
0111 0010       wai                                                     Wait for any external interrupt (interrupt vector is ignored)
0111 0011       was     [int]                                           Wait for a specific external interrupt with id [int]
0111 0100       int     [int]                                           Cause a software interrupt with id [int]
0111 0101       rti                                                     Return from interrupt


                TODO: MOV
        /       immediates, relative, registers, addresses.....
