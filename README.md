<details>
  <summary>LAB 1:</summary>

In this lab, we have to write a C program that calculates the sum of numbers from 1 to a certain number ("n" in my code) and prints the sum. 
Eventually, we must compare the outputs compiled by the GCC compiler and the RISC-V compiler. 


For this task, we are using the Leafpad text editor provided in Linux. 

![leafpad photo](https://github.com/user-attachments/assets/6226066a-c998-4846-85df-aa8cf11f9a42)

<br>

The following is the code to calculate the sum from "1" to "n" in C language.


```c
#include<stdio.h>
int main()
{
  int sum=0, n=100;
  for(int i=1;i<=n;i++)
  {
    sum+=i;
  }
  printf("The sum of numbers from 1 to %d is %d\n", n, sum);
  return 0;
}
```

![leafpad code photo](https://github.com/user-attachments/assets/7e44fd1e-681a-4fae-9a59-a21e5b7ef9ad)

<br>

After the GCC compilation, we get the following output:

![GCC aout photo](https://github.com/user-attachments/assets/00ab46b3-7e86-4709-be5a-abf7f41f9bc3)

<br>

The following photo shows both the screens (the code, and the output generated from the GCC compilation):

![both photo](https://github.com/user-attachments/assets/dad34694-b079-4d0b-909e-5e12d51800b0)

Next, we see the compilation obtained by the RISC-V compiler.

Using O1:

The following command generates the object file, ("sum1ton.o" in my case):

```console
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c
```
![O1 1st tab](https://github.com/user-attachments/assets/568859ae-65f1-4e34-b545-e843150005e9)

Next, to see the assembly-level code contained in the ".o" file, we enter this command:

```console
riscv64-unknown-elf-gcc-objdump -d sum1ton.o
riscv64-unknown-elf-gcc-objdump -d sum1ton.o | less
```

![O1 2nd tab aft](https://github.com/user-attachments/assets/fadf4b6e-6840-470c-9235-af066a5e6ea9)

Using Ofast:

The following command generates the object file, ("sum1ton.o" in my case):

```console
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c
```
![Ofast 1st tab ](https://github.com/user-attachments/assets/c025e3f0-0d47-4a5e-b6e4-8eebb5c43677)

Next, to see the assembly-level code contained in the ".o" file, we enter this command:

```console
riscv64-unknown-elf-gcc-objdump -d sum1ton.o
riscv64-unknown-elf-gcc-objdump -d sum1ton.o | less
```

![Ofast 2nd tab aft](https://github.com/user-attachments/assets/947988a5-9f52-4c27-b3af-ebf0f0447f33)

Now given the outputs generated by using the 2 flags (O1 and Ofast), we can say that less number of instructions were carried on in the Ofast flag.
This can be calculated by simply subtracting the address of the first instruction in the "main" section from the address of the first instruction in the next section ("register_fini" in this scenario).
<br>
<br>
So, on calculating:
<br>
Ofast - Hex(100e0 - 100b0) / 4 = 12 
<br>
O1 - Hex(101c0 - 10184) / 4 = 15

As, 12<15, we can say that less number of instructions were carried on in the Ofast, as compared to O1 flag. 
</details>

<details><summary>LAB 2:</summary>

In this lab we have to use the debug tool in the Spike simulator. It makes debugging a lot easier, as we can actually go through every instruction, and check the values.

Firstly, checking the output received using the command in the previous lab, such that it matches with the output generated by the GCC compiler.

![Q2 1st](https://github.com/user-attachments/assets/9cbd2546-ddd9-4855-b992-332d992a2fe4)

By this we can see the output generated is same, that is 5050.

Now to debug the code, we use the following, 

```console
spike -d pk sum1ton.o
```
Now, we instruct Spike to run the program in such a way, that it reaches the start of the "main" section. It cn be seen starting at the address "100b0". 
This can be done by typing:

```console
until pc 0 100b0
```

On reaching that point, we can see various instructions like "lui a2,Ox1", and "lui a0,Ox21", etc.
By this we can observe the value of a2, changes from "0x0000000000000000" to "0x0000000000001000", and value of a0 changes from "0x0000000000000001" to "0x0000000000021000". 
Also the value of the stack pointer following the instruction "addi sp,sp,-16" changes from "0x0000003ffffffb50" to "0x0000003ffffffb40".
The stack pointer is being decremented by the value 16, (or being incremented by -16 in Hex). 

![Q2 2nd](https://github.com/user-attachments/assets/299df7ea-2a92-46ad-aa76-81db53eee1ed)
</details>

<details>
  <summary>LAB 3:</summary>

  In this lab, we have to first identify various RISC-V instruction type (R, I, S, B, U, J) and exact 32-bit instruction code in the instruction type format for the given RISC-V instructions.

  ```
  ADD r6, r7, r8
  SUB r8, r6, r7
  AND r7, r6, r8
  OR r8, r7, r5
  XOR r8, r6, r4
  SLT r10, r2, r4
  ADDI r12, r3, 5
  SW r3, r1, 4
  SRL r16, r11, r2
  BNE r0, r1, 20
  BEQ r0, r0, 15
  LW r13, r11, 2
  SLL r15, r11, r2
  ```
  Here are the basic formats for the given instruction types (R, I, S, B, U, J):
  
  1. R-Type (Register)
   * Purpose: Used for arithmetic and logical operations where both operands are registers.
   * Examples: ADD, SUB, AND, OR, XOR, SLT

<br>

  2. I-Type (Immediate)
   * Purpose: Used for arithmetic operations with an immediate value, load instructions, and other operations.
   * Examples: ADDI, LW, SRLI

<br>

  3. S-Type (Store)
   * Purpose: Used for storing instructions, which write data from a register to memory.
   * Examples: SW

<br>

  4. B-Type (Branch)
   * Purpose: Used for conditional branch instructions.
   * Examples: BEQ, BNE
     
<br>

  5. U-Type (Upper Immediate)
   * Purpose: Used for instructions that operate with a 20-bit immediate value, usually for loading a high-order address part.
   * Examples: LUI (Load Upper Immediate), AUIPC (Add Upper Immediate to PC)

<br>

  6. J-Type (Jump)
   * Purpose: Used for jump instructions that transfer control to a target address.
   * Examples: JAL (Jump and Link)
<br>

![instr2](https://github.com/user-attachments/assets/4abeb1c5-48ba-435d-8419-d31b49aa6035)

1. ADD r6, r7, r8

    * Instruction Type: R-Type
    * 32-bit Pattern: 0000000 01000 00111 000 00110 0110011
    * opcode: 0110011 (7 bits) - Specifies this as an R-type instruction.
    * rd: 00110 (5 bits) - Register r6.
    * funct3: 000 (3 bits) - Specifies the ADD operation.
    * rs1: 00111 (5 bits) - Register r7.
    * rs2: 01000 (5 bits) - Register r8.
    * funct7: 0000000 (7 bits) - Specifies the ADD operation.

2. SUB r8, r6, r7

    * Instruction Type: R-Type
    * 32-bit Pattern: 0100000 00111 00110 000 01000 0110011
    * opcode: 0110011 (7 bits) - Specifies this as an R-type instruction.
    * rd: 01000 (5 bits) - Register r8.
    * funct3: 000 (3 bits) - Specifies the SUB operation.
    * rs1: 00110 (5 bits) - Register r6.
    * rs2: 00111 (5 bits) - Register r7.
    * funct7: 0100000 (7 bits) - Specifies the SUB operation.

3. AND r7, r6, r8

    * Instruction Type: R-Type
    * 32-bit Pattern: 0000000 01000 00110 111 00111 0110011
    * opcode: 0110011 (7 bits) - Specifies this as an R-type instruction.
    * rd: 00111 (5 bits) - Register r7.
    * funct3: 111 (3 bits) - Specifies the AND operation.
    * rs1: 00110 (5 bits) - Register r6.
    * rs2: 01000 (5 bits) - Register r8.
    * funct7: 0000000 (7 bits) - Specifies the AND operation.

4. OR r8, r7, r5

    * Instruction Type: R-Type
    * 32-bit Pattern: 0000000 00101 00111 110 01000 0110011
    * opcode: 0110011 (7 bits) - Specifies this as an R-type instruction.
    * rd: 01000 (5 bits) - Register r8.
    * funct3: 110 (3 bits) - Specifies the OR operation.
    * rs1: 00111 (5 bits) - Register r7.
    * rs2: 00101 (5 bits) - Register r5.
    * funct7: 0000000 (7 bits) - Specifies the OR operation.

5. XOR r8, r6, r4

    * Instruction Type: R-Type
    * 32-bit Pattern: 0000000 00100 00110 100 01000 0110011
    * opcode: 0110011 (7 bits) - Specifies this as an R-type instruction.
    * rd: 01000 (5 bits) - Register r8.
    * funct3: 100 (3 bits) - Specifies the XOR operation.
    * rs1: 00110 (5 bits) - Register r6.
    * rs2: 00100 (5 bits) - Register r4.
    * funct7: 0000000 (7 bits) - Specifies the XOR operation.

6. SLT r10, r2, r4

    * Instruction Type: R-Type
    * 32-bit Pattern: 0000000 00100 00010 010 01010 0110011
    * opcode: 0110011 (7 bits) - Specifies this as an R-type instruction.
    * rd: 01010 (5 bits) - Register r10.
    * funct3: 010 (3 bits) - Specifies the SLT operation.
    * rs1: 00010 (5 bits) - Register r2.
    * rs2: 00100 (5 bits) - Register r4.
    * funct7: 0000000 (7 bits) - Specifies the SLT operation.

7. ADDI r12, r3, 5

    * Instruction Type: I-Type
    * 32-bit Pattern: 000000000101 00011 000 01100 0010011
    * opcode: 0010011 (7 bits) - Specifies this as an I-type instruction.
    * rd: 01100 (5 bits) - Register r12.
    * funct3: 000 (3 bits) - Specifies the ADDI operation.
    * rs1: 00011 (5 bits) - Register r3.
    * imm: 000000000101 (12 bits) - Immediate value 5.

8. SW r3, r1, 4

    * Instruction Type: S-Type
    * 32-bit Pattern: 000000 00011 00001 010 00100 0100011
    * opcode: 0100011 (7 bits) - Specifies this as an S-type instruction.
    * imm[11:5]: 0000000 (7 bits) - Part of the immediate value.
    * rs2: 00011 (5 bits) - Register r3.
    * rs1: 00001 (5 bits) - Register r1.
    * funct3: 010 (3 bits) - Specifies the SW operation.
    * imm[4:0]: 00100 (5 bits) - Part of the immediate value (4).

9. SRL r16, r11, r2

    * Instruction Type: R-Type
    * 32-bit Pattern: 0000000 00010 01011 101 10000 0110011
    * opcode: 0110011 (7 bits) - Specifies this as an R-type instruction.
    * rd: 10000 (5 bits) - Register r16.
    * funct3: 101 (3 bits) - Specifies the SRL operation.
    * rs1: 01011 (5 bits) - Register r11.
    * rs2: 00010 (5 bits) - Register r2.
    * funct7: 0000000 (7 bits) - Specifies the SRL operation.

10. BNE r0, r1, 20

    * Instruction Type: B-Type
    * 32-bit Pattern: 000000 00001 00000 001 00101 1100011
    * opcode: 1100011 (7 bits) - Specifies this as a B-type instruction.
    * imm[12]: 0 (1 bit) - Part of the immediate value.
    * imm[10:5]: 000000 (6 bits) - Part of the immediate value.
    * rs2: 00001 (5 bits) - Register r1.
    * rs1: 00000 (5 bits) - Register r0.
    * funct3: 001 (3 bits) - Specifies the BNE operation.
    * imm[4:1]: 0101 (4 bits) - Part of the immediate value.
    * imm[11]: 0 (1 bit) - Part of the immediate value (20 is encoded as offset 001010).

11. BEQ r0, r0, 15

    * Instruction Type: B-Type
    * 32-bit Pattern: 0000000 00000 00000 000 01111 1100011
    * opcode: 1100011 (7 bits) - Specifies this as a B-type instruction.
    * imm[12]: 0 (1 bit) - Part of the immediate value.
    * imm[10:5]: 0000000 (7 bits) - Part of the immediate value.
    * rs2: 00000 (5 bits) - Register r0.
    * rs1: 00000 (5 bits) - Register r0.
    * funct3: 000 (3 bits) - Specifies the BEQ operation.
    * imm[4:1]: 1111 (4 bits) - Part of the immediate value.
    * imm[11]: 0 (1 bit) - Part of the immediate value (15 is encoded as offset 0001111).

12. LW r13, r11, 2

    * Instruction Type: I-Type
    * 32-bit Pattern: 000000000010 01011 010 01101 0000011
    * opcode: 0000011 (7 bits) - Specifies this as an I-type instruction.
    * rd: 01101 (5 bits) - Register r13.
    * funct3: 010 (3 bits) - Specifies the LW operation.
    * rs1: 01011 (5 bits) - Register r11.
    * imm: 000000000010 (12 bits) - Immediate value 2.

13. SLL r15, r11, r2

    * Instruction Type: R-Type
    * 32-bit Pattern: 0000000 00010 01011 001 01111 0110011
    * opcode: 0110011 (7 bits) - Specifies this as an R-type instruction.
    * rd: 01111 (5 bits) - Register r15.
    * funct3: 001 (3 bits) - Specifies the SLL operation.
    * rs1: 01011 (5 bits) - Register r11.
    * rs2: 00010 (5 bits) - Register r2.
    * funct7: 0000000 (7 bits) - Specifies the SLL operation.

</details>

<details><summary>LAB 4:</summary>


  
</details>
