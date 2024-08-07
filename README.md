# ASIC-Design
# Table of contents
1. [Compiling a simple C program using GCC compiler](# create-a-simple-c-program-to-find-the-sum-of-the-numbers-from-1-to-n.)
2. 


---
# Create a simple C program to find the sum of the numbers from 1 to n.
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
  int n=45;
  for(int i=1;i<=n;i++){
    sum=sum+i;
  }
  printf("sum of the numbers form 1 to %d is %d",n,sum);
  return 0;
}
```
![Screenshot 2024-08-07 145638](https://github.com/user-attachments/assets/5e0c85f4-44c9-4eb9-bea1-f2b9e295adaf)
### Step 3:
For the compilation of the code, use the following command:<br>
``GCC sum1ton.c``<br>
Make sure there are no errors.<br>
After compilation, an executable file is generated. Run this executable in the terminal to get the final output using the following command:<br>
``./a.out``<br>
![Screenshot 2024-08-07 150721](https://github.com/user-attachments/assets/ddb79470-3ce4-4d82-bb0a-77f1fc27522e)
The final output will be:<br>
`` Sum of the numbers form 1 to 45 is 1035.``
Check the output using a calculator to verify the result.

# Compile the same C program using the RISC-V GCC compiler.
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
![Screenshot 2024-08-07 160155](https://github.com/user-attachments/assets/39208124-f050-4b7c-82f2-f789259dd133)

Now, move back to the first terminal. Here, change the optimization level from `-O1` to `-Ofast` and follow the same procedure as above.<br>
![Screenshot 2024-08-07 160617](https://github.com/user-attachments/assets/dc9393fe-82fe-4a1c-a19f-8b45debbcb6d)

Here, if we use `-Ofast` instead of `-O1`, we get only 12 instructions. Therefore, the number of instructions changes with the optimization level.<br>
![Screenshot 2024-08-07 160729](https://github.com/user-attachments/assets/73b6bcaa-2ca8-4330-b5aa-4f5a1ff4257b)
For `O2`, the number of instructions are 12.<br>
