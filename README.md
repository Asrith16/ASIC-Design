# ASIC-Design
---
# Lab 1A:Create a simple C program to find the sum of the numbers from 1 to n.
### Step 1:
Use the Leafpad editor to write the C code. You can install Leafpad using the following command:<br>
``sudo snap install leafpad``<br>
Create a file named ``sum1ton.c``<br>
![Screenshot 2024-08-07 144122](https://github.com/user-attachments/assets/dd646c7a-636a-45bc-9097-cfe4378df4a9)
### Step 2:
Write c program to find the sum of the numbers from 1 to n.<br>
For an example set the value of n to 45.<br>
Save the file in Leafpad and return to the terminal.
```
#include<stdio.h>
int main(){
  int sum=0;
  int n=100;
  for(int i=1;i<=n;i++){
    sum=sum+i;
  }
  printf("sum of the numbers form 1 to %d is %d",n,sum);
  return 0;
}
```
![Screenshot 2024-08-07 215530](https://github.com/user-attachments/assets/b390cc4f-aa0e-4121-9597-f53345261c88)

### Step 3:
For the compilation of the code, use the following command:<br>
``GCC sum1ton.c``<br>
Make sure there are no errors.<br>
After compilation, an executable file is generated. Run this executable in the terminal to get the final output using the following command:<br>
``./a.out``<br>
![Screenshot 2024-08-07 215759](https://github.com/user-attachments/assets/66bd1819-3ec7-4192-99f0-54b03fa1e782)

The final output will be:<br>
`` Sum of the numbers form 1 to 100 is 5050.``
Check the output using a calculator to verify the result.

# Lab 1B:Compile the same C program using the RISC-V GCC compiler.
### Step 1:
To run the same C program file use the below command:<br>
`` riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o Lab1.o Lab1.c`` <br>
After compilation it will generate a file named sum1ton.o<br>
![Screenshot 2024-08-07 154120](https://github.com/user-attachments/assets/11f8bea0-3719-4b5b-9c91-446d28cef16d)
### step 2:
To get the aasembly code run the following command:<br>
``riscv64-unkown-elf-objdump -d Lab1.o ``
We will get a large output of code, but we are only interested in the main section. To view just the main section, add | less to the previous command.
`` riscv64-unkown-elf-objdump -d Lab1.o |less``
![Screenshot 2024-08-07 154806](https://github.com/user-attachments/assets/9fcef9a3-4598-479b-9efa-09ab9e3ea56b)<br>

After running the second command, we will get some output. Since we are interested in the main section, search for "main." We will see a bunch of instructions in the main snippet. To count the number of instructions, subtract the address of the first instruction from that of the next section, then divide by 4 (as the increment at each step is 4). This method reveals that there are 15 instructions when we are using O1. We can recheck this using a calculator as well.<br>
![Screenshot 2024-08-07 160155](https://github.com/user-attachments/assets/39208124-f050-4b7c-82f2-f789259dd133)<br>

Now, move back to the first terminal. Here, change the optimization level from `-O1` to `-Ofast` and follow the same procedure as above.<br>
![Screenshot 2024-08-07 160617](https://github.com/user-attachments/assets/dc9393fe-82fe-4a1c-a19f-8b45debbcb6d)<br>

Here, if we use `-Ofast` instead of `-O1`, we get only 12 instructions. Therefore, the number of instructions changes with the optimization level.<br>
![Screenshot 2024-08-07 160729](https://github.com/user-attachments/assets/73b6bcaa-2ca8-4330-b5aa-4f5a1ff4257b)<br>
For `O2`, the number of instructions are 12.<br>
Using `-Ofast` reduces our set of instructions because this optimization level applies a broader range of aggressive optimizations. These optimizations streamline the code, eliminate redundancies, and leverage parallelism, resulting in a lower instruction count compared to `-O1`.<br>

---
# Lab 2:Running the Object File Generated by the RISC-V GCC Compiler with the Spike Simulator.
We will compile the object dump file using the RISC-V GCC compiler and check the output against the GCC results from Lab 1. This includes debugging the main part of the program and watching the register values after each step. By looking closely at these values, we can understand how the program behaves and how different compilation stages affect it. This helps make sure the compiled output is accurate and consistent across different compiler settings.<br>
1. For executing an object file created by RISC-V compiler use the below command in the terminal.<br>
```spike pk sum1ton.o```<br>
![Screenshot 2024-08-07 181544](https://github.com/user-attachments/assets/f145ba38-f692-426f-b639-862219a9da23)<br>
2. The following command will help us enter debugging mode using the Spike simulator:<br>
```spike -d pk sum1ton.o```<br>
Use the following command to jump to the beginning of the main section:<br>
``` until pc 0 0x100b0```<br>
![Screenshot 2024-08-07 182238](https://github.com/user-attachments/assets/c0c1d00d-f2ea-481b-8111-2e21f3fe2823)<br>
![Screenshot 2024-08-07 184013](https://github.com/user-attachments/assets/cb1696fb-0d6f-4017-a3ad-a7e51376a026)<br>

3. To check any register value the following command is used:
   ```reg 0 a0```<br>
   Press enter to run the next instruction.
   Here the next instruction is ``lui a2 0x1``, which load upper bits of a2 by 01.<br>
   Intially value of a2 register is 0x0000000000000000.<br>
   After the first instruction it has changed to 0x0000000000001000.<br>
![Screenshot 2024-08-07 183148](https://github.com/user-attachments/assets/1021f417-9713-4e2c-bb46-ee4d9c1c7149)
4. The next instructions are:<br>
   ``lui a0 0x21`` which updates the a0 register value<br>
   ``addi sp sp -16`` This updates the content of the stack pointer 'sp' by -16 (or -10 in hexadecimal).<br>
   ![Screenshot 2024-08-07 185218](https://github.com/user-attachments/assets/dd1798a5-9fdc-44e0-8bfc-d82f64d3793c)<br>
   In the above image we can see that intially sp value is 0x0000003ffffffb50.<br
   After the addi instruction sp value is 0x0000003ffffffb40.<br>
  We can recheck using the calculator. <br>
![Screenshot 2024-08-07 185623](https://github.com/user-attachments/assets/d5b753c4-3ee7-4f0c-9eaf-66802fb67ec1)<br>

---


# Lab 3: Overview of RISC-V Instruction Formats.
The RISC-V architecture employs six distinct instruction formats—R-Type, I-Type, S-Type, B-Type, U-Type, and J-Type—each designed for particular operations such as arithmetic and logic processing, immediate value handling, branching, memory access, and jumps. These formats are crucial in specifying the 32-bit instruction codes that drive the RISC-V system.These formats differ in their structure and the kind of operations they facilitate. Below is a detailed breakdown of each format:<br>

### 1) R-Type (Register Type)
**Purpose:** The R-Type format is used for operations like arithmetic and logic that only involve register values. These operations don’t need immediate values or memory access, making them direct and efficient.

**Structure:**
- **Opcode (7 bits):** Instructs the processor on what operation to carry out, such as arithmetic or logic tasks.
- **funct3 (3 bits):** Works with `funct7` to specify the exact operation (e.g., add, subtract, AND, OR).
- **rs1 (5 bits):** The first source register that holds the first operand.
- **rs2 (5 bits):** The second source register that holds the second operand.
- **rd (5 bits):** The destination register where the result is stored.
- **funct7 (7 bits):** Used together with `funct3` to define the operation more accurately, especially to differentiate between similar tasks.

### 2) I-Type (Immediate Type)
**Purpose:** The I-Type format is used for operations that involve an immediate value (a constant within the instruction), loading from memory, and certain arithmetic tasks.

**Structure:**
- **Opcode (7 bits):** Defines the type of operation, such as arithmetic with an immediate value or loading from memory.
- **funct3 (3 bits):** Further specifies the operation, like adding with an immediate value or loading a byte from memory.
- **rs1 (5 bits):** The source register that provides the operand for arithmetic tasks.
- **rd (5 bits):** The destination register where the result is stored.
- **Immediate (12 bits):** The signed immediate value used in the operation, often for offset calculation or as an operand.

### 3) S-Type (Store Type)
**Purpose:** The S-Type format is used for store operations, where data is written from a register to a memory location. This is crucial for storing data in RAM or other memory areas.

**Structure:**
- **Opcode (7 bits):** Specifies the type of store operation, such as storing a word (32-bit) or a byte.
- **funct3 (3 bits):** Further defines the store operation, indicating whether it’s a byte, half-word, or word store.
- **rs1 (5 bits):** The base register that holds the address where the data will be stored.
- **rs2 (5 bits):** The source register that contains the data to be stored.
- **Immediate (12 bits):** The immediate value, split into two parts, that provides the offset to the base address for the store operation.

### 4) B-Type (Branch Type)
**Purpose:** The B-Type format handles conditional branch operations, allowing the program to jump to a different part of the code based on a comparison between two registers. This is key for control flow structures like loops and conditional statements.

**Structure:**
- **Opcode (7 bits):** Specifies the branch operation.
- **funct3 (3 bits):** Indicates the type of comparison (e.g., equal, not equal, less than).
- **rs1 (5 bits):** The first source register involved in the comparison.
- **rs2 (5 bits):** The second source register involved in the comparison.
- **Immediate (12 bits):** A signed offset used to calculate the branch target address. The immediate is split across the instruction fields and combined to compute the jump destination.

### 5) U-Type (Upper Immediate Type)
**Purpose:** The U-Type format is used for operations that require a large immediate value to be loaded into a register, often for constructing addresses or large constants. It allows loading a 20-bit immediate value into the upper 20 bits of a register.

**Structure:**
- **Opcode (7 bits):** Specifies the operation, such as loading an upper immediate value.
- **rd (5 bits):** The destination register where the upper immediate value will be stored.
- **Immediate (20 bits):** The upper immediate value that will be loaded into the register. The lower 12 bits of the register are typically zeroed out or combined with other operations.

### 6) J-Type (Jump Type)
**Purpose:** The J-Type format is used for jump operations, enabling the program to jump to a specific address unconditionally. This is essential for function calls, jump tables, and other control flow changes that require an absolute change in program flow.

**Structure:**
- **Opcode (7 bits):** Specifies the jump operation.
- **rd (5 bits):** The destination register where the return address will be stored, allowing the program to return to the point after the jump.
- **Immediate (20 bits):** A signed offset used to calculate the target address for the jump. The immediate is spread across multiple fields in the instruction and combined to form the jump target.
