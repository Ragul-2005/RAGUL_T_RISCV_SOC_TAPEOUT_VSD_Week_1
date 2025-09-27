<div align="center"> <h1> 📘 Day 5 – Optimization in Synthesis </h1></div>

<div align="justify"> 🌟 Content Overview  
On **Day 5**, we focus on **optimization techniques** in RTL coding and synthesis.  
You will learn how coding styles such as **if-case constructs, incomplete cases, overlapping cases, and loops** affect synthesized hardware.  
Hands-on labs will reinforce these concepts with practical Verilog/VHDL examples.  
</div>

---



## 📑 Table of Contents  

1. 🔹 **If Case Constructs**  
2. 🔹 **Labs on "Incomplete If Case"**  
3. 🔹 **Labs on "Incomplete Overlapping Case"**  
4. 🔹 **For Loop and For Generate**  
5. 🔹 **Labs on "For Loop" and "For Generate"**

## 1. 🔹 If Case Constructs

The **`if` statement** is a conditional statement that uses **boolean conditions** to determine which block of Verilog code should be executed.  

- ✅ `if` always translates into a **Multiplexer (MUX)** in hardware.  
- ✅ It is mainly used for **priority logic**.  
- ✅ It should always be written inside an **`always` block**.  
- ✅ The variable assigned inside should be declared as a **register (`reg`)**.

  ### 📝 Syntax for IF Statement  

```bash
if (condition) begin
    // statements if condition is true
end else begin
    // statements if condition is false
end
```

###  📝 Syntax for IF-ELSE-IF Statement  
```bash
if (cond1) begin
    // executes a
end
else if (cond2) begin
    // executes b
end e
lse if (cond3) begin
    // executes c
end
else if(cond4)begin
    // executes d
else begin
   // executes y
end

```

### Hardware Implementation

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/If_else.png?raw=true" width="800"/>
</p>

---

## ⚠️ Cautions with Using IF Statements  

Inferred latches can serve as a **warning sign** that the logic design might not be implemented as intended. They often represent **bad coding style**, caused by **incomplete `if` statements** or **crucial conditions missing** in the design.  

---

### 🚨 Why Latches Are Inferred?  
- If an **`else` statement is missing**, the hardware does not know what to do in the “other” condition.  
- As a result, synthesis infers a **latch** to **retain the last value** until a new condition is met.  
- This behavior may **not be intended** and can cause **unwanted hardware**.  

---

### 📝 Example of Latch Inference  

```bash
always @(*) begin
    if (en)
        q = d;    // Assign value when enable is high
    // No else branch -> latch inferred to hold previous value
end
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/inferred_latch.png?raw=true" width="800"/>
</p>

---
## 📌 What is a CASE Statement?
- A CASE statement is used inside an always block to select one of many actions
-based on the value of a variable. It is similar to a multiplexer in hardware.

- ✅ Case statements are written with a register variable and are sensitive to changes
-    in the input (usually in combinational always block: always @(*)).

reg y;
reg z;
reg [1:0] sel;

always @(*) begin
    case(sel)
        2'b00: begin
            // 🔹 Action when sel = 00
            y = 1'b0;
            z = 1'b0;
        end
        2'b01: begin
            // 🔹 Action when sel = 01
            y = 1'b1;
            z = 1'b0;
        end
        2'b10: begin
            // 🔹 Action when sel = 10
            y = 1'b0;
            z = 1'b1;
        end
        // ⚠️ Default branch to avoid inferred latches
        default: begin
            y = 1'b0;
            z = 1'b0;
        end
    endcase
end

### Caveats in CASE Statements
- Case statements are dangerous when there is an incomplete Case Statement Structure may lead to inferred latches. To avoid inferred latches, code Case with default conditions.

```bash
reg y
always @ (*)
begin
	case(sel)
		2'b00:begin
		      ....
		      end
		2'b01:begin
		      ....
		      end
		      .
		      .
	default:begin
         ....
		       end
	endcase	
end
```

- Partial Assignments in Case statements - not specifying the values. This will also create inferred latches. To avoid inferred latches, assign all the inputs in all the segments of the case statement.

```bash
reg y
always @ (*)
begin
	case(sel)
		2'b00:begin
		      ....
		      end
		2'b01:begin
		      ....
		      end
      2'b1?: begin
            ....
            end
		      .
		      .
	default:begin
         ....
		       end
	endcase	
end
```
---
## 2. 🔹 Labs on "Incomplete If Case"

### Case 1: incomplete if Statement

**Verilog Code**
```bash
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
end
endmodule
```
**GTK Wave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/incom_if_sim.png?raw=true" width="800"/>
</p>

**Else case is missing so there will be a D latch.**

**Realization of Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/incomp_if_syth.png?raw=true" width="800"/>
</p>

**synthesized design has a D Latch inferred due to incomplete if structure (missing else statement).**

---

### Case 2: incomplete if else if statement

**Verilog Code**
```bash
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;
end
endmodule
```
**GTK Wave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/incomp_if2_sim.png?raw=true" width="800"/>
</p>

**When i0 is high, the output follows i1. When i0 is low, the output latches to a constant value (when both i0 and i2 are 0). Presence of inferred latches due to incomplete if structure.**

**Realization of Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/incomp_if2_synth.png?raw=true" width="800"/>
</p>

---

## 3. 🔹 Labs on "Incomplete Overlapping Case"

### CASE 1: incomplete case statements

**Verilog Code**
```bash
module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
	endcase
end
endmodule
```
**GTK Wave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/incomp_case_sim.png?raw=true" width="800"/>
</p>

**When select signal is 00, the output follows i0 and is i1 when the select value is 01. Since the output is undefined for 10 and 11 values, the ouput latches to the previously available value.**


**Realization of Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/incomp_case_synth.png?raw=true" width="800"/>
</p>

**The synthesized design has a D Latch inferred due to incomplete case structure (missing output definition for 2 of the select statements).**

### Case 2: incomplete case statements with default

**Verilog Code**
```bash
module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
	endcase
end
endmodule
```
**GTK Wave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/comp_case_sim.png?raw=true" width="800"/>
</p>

**When select signal is 00, the output follows i0 and is i1 when the select value is 01. Since the output is undefined for 10 and 11 values, the presence of default sets the output to i2 when the select line is 10 or 11. The ouput will not latch and be a proper combinational circuit. Realization of Logic**

**Realization of Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/comp_case_synth.png?raw=true" width="800"/>
</p>







   

---

## 🎯 Learning Outcomes  
By the end of Day 5, you will be able to:  

- ✨ Understand how **if-case constructs** affect synthesized hardware.  
- ✨ Identify and resolve issues with **incomplete if-case conditions**.  
- ✨ Recognize the implications of **incomplete and overlapping case statements**.  
- ✨ Apply **for loops** and **for-generate constructs** effectively in RTL coding.  
- ✨ Optimize RTL code for better synthesis results in terms of **area, timing, and readability**.  

---
