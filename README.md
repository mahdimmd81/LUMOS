## Computer_Organization_project
## The Marauder's Map
## Authors:
# Mahdi_mohammadi_400413269
# Amirhossein_karami_400413206
# Golnaz_jalvandi_400411349
# Fixed Point Unit

## Overview

This repository contains a Verilog implementation of a Fixed Point Unit (FPU) designed to perform various arithmetic operations such as addition, subtraction, multiplication, and square root computation on fixed-point numbers.

## Files

- `Fixed_Point_Unit.v`: Contains the main Verilog module implementing the fixed point arithmetic operations.
- `Multiplier.v`: Contains a submodule for performing 16-bit multiplications, used by the main FPU module.
- `Defines.vh`: Header file containing necessary definitions for operation codes.

## Parameters

- `WIDTH`: The width of the fixed-point operands (default: 32).
- `FBITS`: The number of fractional bits in the fixed-point representation (default: 10).

## I/O Ports

### Inputs

- `clk`: Clock signal.
- `reset`: Reset signal.
- `operand_1`: First operand (fixed-point number).
- `operand_2`: Second operand (fixed-point number).
- `operation`: Operation code (2 bits) specifying the arithmetic operation.

### Outputs

- `result`: Result of the arithmetic operation.
- `ready`: Signal indicating the operation is complete and the result is valid.

## Operations

The FPU supports the following operations:

- `FPU_ADD`: Addition of `operand_1` and `operand_2`.
- `FPU_SUB`: Subtraction of `operand_2` from `operand_1`.
- `FPU_MUL`: Multiplication of `operand_1` and `operand_2`.
- `FPU_SQRT`: Square root of `operand_1`.

## Square Root Calculator

The square root calculation in the FPU is implemented using a digit-by-digit method. Here’s a step-by-step breakdown of the process:

1. **Initialization**: Initialize the root and other temporary registers.
2. **Loop through each bit**: For each bit pair of the operand:
    - Shift the partial result and append the next bit pair of the operand.
    - Compute the next possible value for the root.
    - If the subtraction result is non-negative, update the root.
3. **Finalization**: Set the ready signal to indicate the completion of the operation.

The algorithm is implemented as follows in the Verilog code:

```verilog
// Square Root Circuit
reg [WIDTH - 1 : 0] root = 32'b0;
reg root_ready;
reg [WIDTH-1:0] co_oprand1 = operand_1;
reg [1:0] pair;
reg [31:0] count = 16;
reg [31:0] radicand;
reg [31:0] sbb = 32'b0;
reg [31:0] sub_result = 32'b0;
reg [31:0] co_sub_result;

always @(*)
begin
    for (i = 0; i < count; i = i + 1)
    begin
        pair = co_oprand1[31:30];
        radicand = (sub_result << 2) + pair;
        sbb = (root << 2) + 1;
        co_sub_result = sub_result;
        sub_result = radicand - sbb;
        if (sub_result)
            root = (root << 1) + 1;
        else
        begin
            sub_result = co_sub_result;
            root = (root << 1);
        end
        co_oprand1 = co_oprand1 << 2;
    end
end
root_ready = 1;

## 


![Alt text](file:///C:/Users/ASUS/Desktop/New%20folder%20(2)/image.png.jpeg)
![Alt text](file:///C:/Users/ASUS/Desktop/New%20folder%20(2)/image.png%20(2).jpeg)
![Alt text](file:///C:/Users/ASUS/Desktop/New%20folder%20(2)/image.png%20(1).jpeg)

- Explain the code in assembly.s:

main:
        li          sp,     0x3C00
        addi        gp,     sp,     392
loop:
        flw         f1,     0(sp)
        flw         f2,     4(sp)
       
        fmul.s      f10,    f1,     f1
        fmul.s      f20,    f2,     f2
        fadd.s      f30,    f10,    f20
        fsqrt.s     x3,     f30
        fadd.s      f0,     f0,     f3

        addi        sp,     sp,     8
        blt         sp,     gp,     loop
        ebreak




//.............................................................//
main:
        li          sp,     0x3C00          # Initialize stack pointer (sp) to memory address 0x3C00
        addi        gp,     sp,     392     # Set global pointer (gp) to sp + 392 (points to end of data)

loop:
        flw         f1,     0(sp)           # Load floating-point word from memory at address sp into register f1
        flw         f2,     4(sp)           # Load floating-point word from memory at address sp+4 into register f2
       
        fmul.s      f10,    f1,     f1      # Multiply f1 by itself (square it) and store the result in f10
        fmul.s      f20,    f2,     f2      # Multiply f2 by itself (square it) and store the result in f20
        fadd.s      f30,    f10,    f20     # Add f10 and f20 (sum of squares) and store the result in f30
        fsqrt.s     x3,     f30             # Compute the square root of f30 and store the result in x3 (integer register)
        fadd.s      f0,     f0,     f3      # Add f3 to f0 and store the result in f0 (accumulate result)

        addi        sp,     sp,     8       # Increment stack pointer by 8 (move to next pair of numbers)
        blt         sp,     gp,     loop    # If sp < gp, jump back to the beginning of the loop
        ebreak                           # End of program (break)



        //........................................................//