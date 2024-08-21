# ASIC-Design
---
<details>
  <summary><strong>Lab 1:Cprogram compilation.</strong> </summary>
  
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
</details>

---

<details>
  <summary><strong>Lab2 :RISC-V Spike simulator.</strong></summary>
  
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
</details>

---

<details>
  <summary><strong>Lab 3:RISC-V Instructions</strong></summary>
  
# Lab 3: Overview of RISC-V Instruction Formats.
The RISC-V architecture employs six distinct instruction formats—R-Type, I-Type, S-Type, B-Type, U-Type, and J-Type—each designed for particular operations such as arithmetic and logic processing, immediate value handling, branching, memory access, and jumps. These formats are crucial in specifying the 32-bit instruction codes that drive the RISC-V system.These formats differ in their structure and the kind of operations they facilitate. Below is a detailed breakdown of each format:<br>
![WhatsApp Image 2024-08-10 at 23 31 46_35a88ac2](https://github.com/user-attachments/assets/63377927-7d1a-4f39-8c51-250e2d3f3a42)


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

<img width="772" alt="3808 1535301636" src="https://github.com/user-attachments/assets/d0f05dac-2191-4ade-89f6-8b371a1e3a29">

## Identifying RISC-V Instruction Types (R, I, S, B, U, J) and Determining the Exact 32-bit Instruction Encoding.
```
ADD r5, r4, r5
SUB r5, r5, r4
AND r4, r5, r5
OR r8, r4, r5
XOR r8, r5, r4
SLT r10, r2, r4
ADDI r12, r3, 5
SW r3, r1, 4
SRL r16, r11, r2
BNE r0, r1, 20
BEQ r0, r0, 15
LW r13, r11, 2
SLL r15, r11, r2
```

### 1. `ADD r5, r4, r5`
- **Opcode:** `0110011`
- **rd:** `r5 = 00101`
- **rs1:** `r4 = 00100`
- **rs2:** `r5 = 00101`
- **funct3:** `000`
- **funct7:** `0000000`
- **Instruction Type:** R-Type
- **32-bit Instruction:** `0000000_00101_00100_000_00101_0110011`
- **Hexadecimal Representation:** `0x005202B3`

### 2. `SUB r5, r5, r4`
- **Opcode:** `0110011`
- **rd:** `r5 = 00101`
- **rs1:** `r5 = 00101`
- **rs2:** `r4 = 00100`
- **funct3:** `000`
- **funct7:** `0100000`
- **Instruction Type:** R-Type
- **32-bit Instruction:** `0100000_00100_00101_000_00101_0110011`
- **Hexadecimal Representation:** `0x404282B3`

### 3. `AND r4, r5, r5`
- **Opcode:** `0110011`
- **rd:** `r4 = 00100`
- **rs1:** `r5 = 00101`
- **rs2:** `r5 = 00101`
- **funct3:** `111`
- **funct7:** `0000000`
- **Instruction Type:** R-Type
- **32-bit Instruction:** `0000000_00101_00101_111_00100_0110011`
- **Hexadecimal Representation:** `0x0052F233`

### 4. `OR r8, r4, r5`
- **Opcode:** `0110011`
- **rd:** `r8 = 01000`
- **rs1:** `r4 = 00100`
- **rs2:** `r5 = 00101`
- **funct3:** `110`
- **funct7:** `0000000`
- **Instruction Type:** R-Type
- **32-bit Instruction:** `0000000_00101_00100_110_01000_0110011`
- **Hexadecimal Representation:** `0x00526433`

### 5. `XOR r8, r5, r4`
- **Opcode:** `0110011`
- **rd:** `r8 = 01000`
- **rs1:** `r5 = 00101`
- **rs2:** `r4 = 00100`
- **funct3:** `100`
- **funct7:** `0000000`
- **Instruction Type:** R-Type
- **32-bit Instruction:** `0000000_00100_00101_100_01000_0110011`
- **Hexadecimal Representation:** `0x0042C433`

### 6. `SLT r10, r2, r4`
- **Opcode:** `0110011`
- **rd:** `r10 = 01010`
- **rs1:** `r2 = 00010`
- **rs2:** `r4 = 00100`
- **funct3:** `010`
- **funct7:** `0000000`
- **Instruction Type:** R-Type
- **32-bit Instruction:** `0000000_00100_00010_010_01010_0110011`
- **Hexadecimal Representation:** `0x00412533`

### 7. `ADDI r12, r3, 5`
- **Opcode:** `0010011`
- **rd:** `r12 = 01100`
- **rs1:** `r3 = 00011`
- **Immediate:** `000000000101`
- **funct3:** `000`
- **Instruction Type:** I-Type
- **32-bit Instruction:** `000000000101_00011_000_01100_0010011`
- **Hexadecimal Representation:** `0x00518613`

### 8. `SW r3, r1, 4`
- **Opcode:** `0100011`
- **rs1:** `r1 = 00001`
- **rs2:** `r3 = 00011`
- **Immediate:** `000000000100`
- **funct3:** `010`
- **Instruction Type:** S-Type
- **32-bit Instruction:** `0000000_00011_00001_010_00100_0100011`
- **Hexadecimal Representation:** `0x0030A223`

### 9. `SRL r16, r11, r2`
- **Opcode:** `0110011`
- **rd:** `r16 = 10000`
- **rs1:** `r11 = 01011`
- **rs2:** `r2 = 00010`
- **funct3:** `101`
- **funct7:** `0000000`
- **Instruction Type:** R-Type
- **32-bit Instruction:** `0000000_00010_01011_101_10000_0110011`
- **Hexadecimal Representation:** `0x0025D833`

### 10. `BNE r0, r1, 20`
- **Opcode:** `1100011`
- **rs1:** `r1 = 00001`
- **rs2:** `r0 = 00000`
- **Immediate:** `000000000101`
- **funct3:** `001`
- **Instruction Type:** B-Type
- **32-bit Instruction:** `0_000001_00001_00000_001_0100_0_1100011`
- **Hexadecimal Representation:** `0x02101463`

### 11. `BEQ r0, r0, 15`
- **Opcode:** `1100011`
- **rs1:** `r0 = 00000`
- **rs2:** `r0 = 00000`
- **Immediate:** `000000000111`
- **funct3:** `000`
- **Instruction Type:** B-Type
- **32-bit Instruction:** `0_000000_00000_00000_000_1111_0_1100011`
- **Hexadecimal Representation:** `0x00000F63`

### 12. `LW r13, r11, 2`
- **Opcode:** `0000011`
- **rd:** `r13 = 01101`
- **rs1:** `r11 = 01011`
- **Immediate:** `000000000010`
- **funct3:** `010`
- **Instruction Type:** I-Type
- **32-bit Instruction:** `000000000010_01011_010_01101_0000011`
- **Hexadecimal Representation:** `0x0025A683`

### 13. `SLL r15, r11, r2`
- **Opcode:** `0110011`
- **rd:** `r15 = 01111`
- **rs1:** `r11 = 01011`
- **rs2:** `r2 = 00010`
- **funct3:** `001`
- **funct7:** `0000000`
- **Instruction Type:** R-Type
- **32-bit Instruction:** `0000000_00010_01011_001_01111_0110011`
- **Hexadecimal Representation:** `0x002597B3`

## Summary of RISC-V Instructions

| Instruction        | Type   | 32-bit Representation                       | Hexadecimal Representation |
|--------------------|--------|---------------------------------------------|----------------------------|
| `ADD r5, r4, r5`   | R-Type | `0000000_00101_00100_000_00101_0110011`     | `0x005202B3`               |
| `SUB r5, r5, r4`   | R-Type | `0100000_00100_00101_000_00101_0110011`     | `0x404282B3`               |
| `AND r4, r5, r5`   | R-Type | `0000000_00101_00101_111_00100_0110011`     | `0x0052F233`               |
| `OR r8, r4, r5`    | R-Type | `0000000_00101_00100_110_01000_0110011`     | `0x00526433`               |
| `XOR r8, r5, r4`   | R-Type | `0000000_00100_00101_100_01000_0110011`     | `0x0042C433`               |
| `SLT r10, r2, r4`  | R-Type | `0000000_00100_00010_010_01010_0110011`     | `0x00412533`               |
| `ADDI r12, r3, 5`  | I-Type | `000000000101_00011_000_01100_0010011`      | `0x00518613`               |
| `SW r3, r1, 4`     | S-Type | `0000000_00011_00001_010_00100_0100011`     | `0x0030A223`               |
| `SRL r16, r11, r2` | R-Type | `0000000_00010_01011_101_10000_0110011`     | `0x0025D833`               |
| `BNE r0, r1, 20`   | B-Type | `0_000001_00001_00000_001_0100_0_1100011`     | `0x02101463`               |
| `BEQ r0, r0, 15`   | B-Type | `0_000000_00000_00000_000_1111_0_1100011`     | `0x00000F63`               |
| `LW r13, r11, 2`   | I-Type | `000000000010_01011_010_01101_0000011`      | `0x0025A683`               |
| `SLL r15, r11, r2` | R-Type | `0000000_00010_01011_001_01111_0110011`     | `0x002597B3`               |


</details>

---

<details>
  <summary><strong> Lab 4:Results with GTKWave</strong></summary>
  
#  Lab 4:Visualizing Functional Simulation Results with GTKWave
In the provided Verilog code, the RISC-V instructions are represented with some variations compared to the standard RISC-V ISA. Specifically, each instruction type is assigned a unique opcode in the Verilog implementation. The `func3` and `func7` values, which are used to identify the specific operation, also differ from the standard RISC-V ISA. Notably, `func7` is employed to differentiate between operations involving immediate values and other arithmetic functions; if `func7` is not required for this distinction, it is set to 0 in the Verilog code. The table below illustrates how the 32-bit instruction patterns are hardcoded using these opcodes, `func3`, and `func7` values as defined in the Verilog code.<br>

## Hardcoded table based on the provided Verilog code
| Instruction     | Hardcoded 32bit pattern | Hardcoded hexadecimal pattern| 32bit pattern   | Hexadecimal pattern |
|-----------------|-------------------------|------------------------------|---------------------|---------------|
| ADD r5, r4, r5  | 0000001_00101_00100_000_00101_0000000 | 0x02520280 | 0000000_00101_00100_000_00101_0110011  | 0x005202B3 |
| SUB r5, r5, r4  | 0000001_00100_00101_001_00101_0000000 | 0x02429280  | 0100000_00100_00101_000_00101_0110011  | 0x404282B3      |
| AND r4, r5, r5  | 0000001_00101_00101_010_00100_0000000 | 0x0252A200 |0000000_00101_00101_111_00100_0110011  | 0x0052F233     |
| OR r8, r4, r5  | 0000001_00101_00100_011_01000_0000000  | 0x02523400 |0000000_00101_00100_110_01000_0110011  | 0x00526433      |
| XOR r8, r5, r4  | 0000001_00100_00101_100_01000_0000000 | 0x0242C400 |0000000_00100_00101_100_01000_0110011  | 0x0042C433      |
| SLT r10, r2, r4  | 0000001_00100_00010_101_01010_0000000| 0x02415500 | 0000000_00100_00010_010_01010_0110011  | 0x00412533      |
| ADDI r12, r3, 5  | 000000000101_00011_000_01100_0000000 | 0x00518600 |000000000101_00011_000_01100_0010011  | 0x00518613      |
| SW r3, r1, 4  | 0000000_00011_00001_001_00100_0000001   | 0X00309201| 0000000_00011_00001_010_00100_0100011  | 0x0030A223     |
| LW r13, r11, 2  | 000000000010_01011_000_01101_0000001   | 0x0258681 |000000000010_01011_010_01101_0000011  | 0x0025A683      |
| BEQ r0, r0, 15  | 0_000000_00000_00000_000_1111_0_1100011   | 0X00f00002 |0_000000_00000_00000_000_1111_0_1100011  | 0x00000F63      |
| SRL r16, r11, r2  | 0000000_00010_01011_001_10000_0000011 |0x00259803  | 0000000_00010_01011_101_10000_0110011 | 0x0025D833      |

## Output Waveforms for the instructions provided in the verilog code:

| Operation          | Standard RISCV ISA | Hardcoded ISA |
|--------------------|---------------------|---------------|
| ADD R6, R1, R2     | 0x00110333        | 0x02208300  |
| SUB R7, R1, R2     | 0x402083b3        | 0x02209380  |
| AND R8, R1, R3     | 0x0030f433        | 0x0230a400  |
| OR R9, R2, R5      | 0x005164b3        | 0x02513480  |
| XOR R10, R1, R4    | 0x0040c533        | 0x0240c500  |
| SLT R11, R2, R4     | 0x0045a0b3        | 0x02415580  |
| ADDI R12, R4, 5    | 0x004120b3        | 0x00520600  |
| BEQ R0, R0, 15     | 0x00000f63        | 0x00f00002  |
| SW R3, R1, 2       | 0x0030a123        | 0x00209181  |
| LW R13, R1, 2      | 0x0020a683        | 0x00208681  |
| ADD R14, R2, R2    | 0x00210733         | 0x00210700  |

### Wave forms
``` ADD R6,R1,R2 ```<br>

![Screenshot 2024-08-12 190747](https://github.com/user-attachments/assets/65b65e0f-c0a6-47c9-bc7c-2764d2083987)

``` SUB R7,R1,R2```<br>

![Screenshot 2024-08-12 191520](https://github.com/user-attachments/assets/e21346eb-6dd0-41d3-b0f0-0912873312cd)

```AND R8,R1,R3```<br>

![Screenshot 2024-08-12 191918](https://github.com/user-attachments/assets/b3105bb2-ebf6-4e55-8625-19ecf0829432)


``` OR R9,R2,R5```<br>

![Screenshot 2024-08-12 192010](https://github.com/user-attachments/assets/f208c33c-d457-4cc8-8c42-25492ba113ee)


``` XOR R10,R1,R4```<br>

![Screenshot 2024-08-12 192041](https://github.com/user-attachments/assets/82d42570-0a06-4e38-b036-2e86a75ba284)


```SLT R11,R2,R4```<br>
![Screenshot 2024-08-12 192105](https://github.com/user-attachments/assets/fda0c504-de9a-4ffd-9165-551b6147a055)

```ADDI R12,R4,5```<br>

![Screenshot 2024-08-12 192136](https://github.com/user-attachments/assets/125fa7b9-0683-4aba-afd4-17b2415318f4)


```BEQ R0,R0,15```<br>
![Screenshot 2024-08-12 192202](https://github.com/user-attachments/assets/c89e8614-962d-4514-ba2e-aac9573dc578)


``` SW R3,R1,2```<br>

![Screenshot 2024-08-12 192259](https://github.com/user-attachments/assets/5227f230-3623-457a-8591-d1acae01da30)


```LW R13, R1, 2 ```<br>

![Screenshot 2024-08-12 192407](https://github.com/user-attachments/assets/9197cc14-8ce6-464a-93f4-62b6b9514cc8)


```ADD R14, R2, R2```<br>

![Screenshot 2024-08-12 192457](https://github.com/user-attachments/assets/80c63b58-e584-4539-81c2-d202864b357a)


Output form:<br>

![Screenshot 2024-08-12 192601](https://github.com/user-attachments/assets/964f03ae-ccbd-4a26-a117-703cae7b548f)


</details>

---


<details>
  <summary><strong> Lab 5: RISC-V GCC and GCC Compiler</strong></summary>
  
# Comparing Output Consistency Between RISC-V GCC and GCC Compiler

The code provided is a Palindrome Checker where the user inputs an integer, and the program checks whether the number is a palindrome. The program reverses the digits of the input number and compares the reversed number with the original. If both are identical, the number is identified as a palindrome; otherwise, it is not.<br>
<br>
![Screenshot 2024-08-14 203937](https://github.com/user-attachments/assets/0aed22de-b6cb-4551-becb-32197dc06697)

The image below displays the output produced when the C code is executed using a standard GCC compiler:<br>

![Screenshot 2024-08-14 204553](https://github.com/user-attachments/assets/440881db-fab0-4e59-86e2-4da259a753c5)

The image below displays the output generated when the C code is executed using a RISC-V GCC compiler:<br>

![Screenshot 2024-08-14 205024](https://github.com/user-attachments/assets/f13f862e-de61-4c15-9491-d080ffb1710c)


By examining the images above, we can observe that the output is identical when using both the GCC and RISC-V GCC compilers. This consistency indicates that the code behaves as expected across different compiler architectures, ensuring reliability and compatibility in various development environments. This demonstrates that the program's logic is platform-independent and confirms the accuracy of the compilation process on both compilers.


  
</details>

---

<details>
  <summary><strong>Lab 6: </strong></summary>

  <details>
  <summary><strong>Day 3</strong></summary>

# Designing Digital Logic with TL-Verilog using Makerchip

### Logic Gates
Logic gates are the fundamental building blocks of digital circuits, executing vital logical operations on binary inputs. These gates are crucial for designing intricate systems such as processors, memory units, and controllers. In digital circuits, logic gates work with binary signals, where "0" represents low voltage and "1" represents high voltage. They process one or more input signals to generate an output signal according to defined logical functions.<br>

![Screenshot 2024-08-21 093753](https://github.com/user-attachments/assets/28ddbce8-c199-4daa-93f3-fc8d0773913e)<br>

### Makerchip
Makerchip IDE is a robust tool for digital design, providing an all-in-one environment for coding, simulating, and testing HDL designs. It supports languages such as TL-Verilog, SystemVerilog, Verilog, and VHDL, offering a visual platform for real-time construction and simulation of digital systems. With its user-friendly interface and comprehensive features, Makerchip is suitable for both beginners and seasoned designers. Makerchip enables efficient prototyping, debugging, and refinement of digital designs, ensuring that circuits perform correctly before transitioning to hardware implementation.<br>


### Transaction Level (TL) - Verilog:
TL Verilog is a contemporary extension of traditional Verilog, developed by Redwood EDA to streamline hardware modeling and design. It offers a more abstract and efficient syntax while maintaining compatibility with standard Verilog. TL-Verilog facilitates transaction-level modeling, simplifying the management of complex microarchitectures. In this method, a transaction progresses through the architecture, interacting with components such as pipelines, arbiters, and queues. TL-Verilog is especially effective in minimizing bugs and optimizing design, particularly when used with tools like Makerchip.<br>


## Some Basic Combinational circuits:
### 1. Inverter:
   The resulting block diagram and waveforms are presented as depicted:<br>
   ![Screenshot 2024-08-21 152754](https://github.com/user-attachments/assets/2867695f-706b-4047-9662-dc6c65b36a31)

### 2. 2- input AND gate:
   The resulting block diagram and waveforms are presented as depicted:<br>
   ![Screenshot 2024-08-21 153033](https://github.com/user-attachments/assets/d44bf330-ce4f-4b21-bf7b-43d63f1891f2)

### 3. 2-input OR gate:
   The resulting block diagram and waveforms are presented as depicted:<br>
   ![Screenshot 2024-08-21 153131](https://github.com/user-attachments/assets/50edfc0a-c23a-4d43-94b4-7e091aa27586)

### 4. 2:1 MUX
   The resulting block diagram and waveforms are presented as depicted:<br>
   ![Screenshot 2024-08-21 154614](https://github.com/user-attachments/assets/41df9ee3-0116-4e27-a1bd-6d7e5cb972f9)

### 5. Implementation of a Combinational Calculator in TL-Verilog
Overview of the Calculator: This section showcases a simple combinational calculator created using TL-Verilog on the Makerchip platform. The calculator performs the four basic arithmetic operations: addition, subtraction, multiplication, and division.<br>
The resulting block diagram and waveforms are presented as depicted:<br>

![image](https://github.com/user-attachments/assets/8036afeb-76f3-486c-b4f6-52f5c8793583)

In this code snippet, two random 4-bit values, `$rand1[3:0]` and `$rand2[3:0]`, are assigned to the 32-bit variables `$val1[31:0]` and `$val2[31:0]`, respectively. The calculator then performs four arithmetic operations on these values.<br>

A multiplexer (MUX), controlled by the selection bits `$sel[1:0]`, chooses the result of one of these operations. The selected output is then assigned to `$out[31:0]`.<br>

![Screenshot 2024-08-21 160425](https://github.com/user-attachments/assets/0ed3f8e0-eee7-45b9-b140-5f991597dff0)

## SEquential Circuits

A sequential circuit is a type of digital circuit that utilizes memory elements to store data, enabling it to produce outputs based on both current inputs and the circuit’s prior state. In contrast to combinational circuits, which rely solely on present inputs, sequential circuits incorporate feedback loops and storage components like flip-flops or registers to monitor their internal state. This internal state, combined with the current inputs, dictates the circuit’s functionality, making it capable of performing tasks that require a history of inputs, such as counting, data storage, or event sequencing.<br>

### 1.Counter Series:

The block diagram and waveforms generated are displayed below:<br>
![Screenshot 2024-08-21 161052](https://github.com/user-attachments/assets/85cff099-3dfd-4bc9-b474-a9d22589224a) <br>

![Screenshot 2024-08-21 161219](https://github.com/user-attachments/assets/2cf54660-8876-4055-8120-956a6acbc705)

### 2. Fibbonacci Series:

The block diagram and waveforms generated are displayed below:<br>

![Screenshot 2024-08-21 161435](https://github.com/user-attachments/assets/2779d46b-a43d-49b2-bb97-be76abd6b9ce) <br>


![image](https://github.com/user-attachments/assets/4b490f01-0945-4a28-bdc6-7237da52e88e)

### 3. Sequential Calculator:

This design is similar to the previous combinational calculator but simulates a real-world scenario where the result of the previous operation is used as one of the inputs for the next operation. When the circuit is reset, the result is cleared to zero.<br>

![Screenshot 2024-08-21 161837](https://github.com/user-attachments/assets/0f901d7a-5ff6-4560-b804-d19da54cc1a4)<br>

![Screenshot 2024-08-21 161920](https://github.com/user-attachments/assets/d90fe0a2-c3d3-4b1e-8262-f388e9f63960) <br>

![Screenshot 2024-08-21 161805](https://github.com/user-attachments/assets/6192171d-ed81-4b7e-be3f-6d11441192c2) <br>


## Pipelined Logic
In Transaction-Level Verilog (TL-Verilog), pipelined logic is effectively modeled using pipeline constructs that represent data movement through various stages, with each stage corresponding to a clock cycle. This method streamlines the modeling of sequential logic by managing state transitions automatically and providing clear, concise representations of complex multi-stage operations. This enhances both the clarity and maintainability of the design.<br>

### 1. Recreating the Design

![Screenshot 2024-08-21 162218](https://github.com/user-attachments/assets/71aff8b9-aa48-4c88-8c09-5ec86ce791fb) <br>


![Screenshot 2024-08-21 162446](https://github.com/user-attachments/assets/82066fc0-54a3-4944-bf7e-e13f830e1cb6) <br>

![Screenshot 2024-08-21 162521](https://github.com/user-attachments/assets/c86b3a8f-1412-45fd-a76a-52fa833d1d7f) <br>

Therefore, it can be observed that the given pipeline design and the recreated design are identical.<br>


### 2. Pipelined Calculator
This design is similar to the previous Sequential Calculator but incorporates a pipelined architecture and utilizes `$valid` to clear alternate values.<br>

![Screenshot 2024-08-21 162923](https://github.com/user-attachments/assets/50e391c9-8ed2-4bcd-a228-49e505a054a1) <br>

![Screenshot 2024-08-21 163012](https://github.com/user-attachments/assets/d3d5a167-51a3-4bc5-9c55-51469a5d321a) <br>

### 3. Cycle Calculator with validity
We introduce `$valid_or_reset = $valid || $reset;` as a condition for triggering calculations, replacing the previous method of setting `$out` to zero.<br>

![Screenshot 2024-08-21 175517](https://github.com/user-attachments/assets/e1cf5b8d-fb89-4e1e-ac2d-cb1ace330c50) <br>

  </details>

<details>
  <summary><strong> Day 4</strong></summary>

# Fundamentals of RISC-V CPU Micro-architecture
This section will discuss the implementation of a basic 3-stage RISC-V Core/CPU. The three stages include: Fetch, Decode, and Execute. The diagram below illustrates the fundamental block diagram of the CPU core:<br>

![Screenshot 2024-08-21 180115](https://github.com/user-attachments/assets/8d26c27f-ed72-4cbb-abbd-53ab154c5dcc) <br>

### Implementation plan:

![Screenshot 2024-08-21 180155](https://github.com/user-attachments/assets/d1e368ee-b8de-4819-b25e-11794e32706d) <br>


### 1. Program counter

The Program Counter, also known as the Instruction Pointer, is a component that holds the address of the next instruction to be executed. Typically, the program counter increments by 4 to fetch the subsequent instruction from memory. If a reset occurs, the program counter is reinitialized to zero for the next instruction, after which it resumes normal operation.<br>

The diagram below illustrates the functioning of the program counter.<br>

![Screenshot 2024-08-21 180439](https://github.com/user-attachments/assets/7e2f298d-8e19-47f5-9315-9fbcebadc888)<br>


![Screenshot 2024-08-21 180624](https://github.com/user-attachments/assets/f49c3724-d7b7-438f-8873-ee651dee1ee8) <br>

Code:<br>
``` $pc[31:0] = >>1$reset ? 0 : ( >>1$pc + 31'h4 ); ```

### 2. Adding the instruction memory

![Screenshot 2024-08-21 181059](https://github.com/user-attachments/assets/9ecf6c22-9838-4fec-88dc-c3eeef8ffad6) <br>

![Screenshot 2024-08-21 181120](https://github.com/user-attachments/assets/1b07fa46-04bc-4358-96a4-6ce66fa87e05) <br>

The output of the Program Counter (PC) is fed into the instruction memory, which then outputs the instruction to be executed. The program counter increments by 4 with each valid iteration. This incremented output is used to fetch the next instruction from the instruction memory.<br>

The instruction memory provides a 32-bit instruction based on the input address. During the Fetch Stage, the processor retrieves the instruction from the instruction memory (IM) at the address specified by the PC.<br>
















</details>
</details>
