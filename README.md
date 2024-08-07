<h1>
  LAB 1:
</h1>

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

<h1>
  LAB 2:
</h1>

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
