<div align="center"> <h1> 📘 Day 5 – Optimization in Synthesis </h1></div>

<div align="justify"> 
On <b>Day 5</b>, we focus on <b>optimization techniques</b> in RTL coding and synthesis.You will learn how coding styles such as <b>if-case constructs, incomplete cases, overlapping cases, and loops</b> affect synthesized hardware.  
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

### CASE 3: partial case statement

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

**Realization of Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/partial_design_synth.png?raw=true" width="800"/>
</p>

---

## 4. 🔹 For Loop and For Generate 
### 📌 What is a **For Loop**?  
- 🌀 A **for loop** is a control flow statement used in **testbenches and behavioral blocks**.  
- 📍 It is **written inside `always` or `initial` blocks**.  
- 🎯 Mainly used for **simulation, initialization, and repetitive operations**.

#### 📝Syntax
```bash
  always @(*) begin
  for(initialization; condition; update) begin
    // behavioral code
  end
end
```

#### ✅ Example: 
```bash
module sum_array;
  reg [7:0] arr[0:3];
  integer i;
  reg [9:0] total;

  initial begin
    // 🎲 Initialize values
    arr[0] = 10; arr[1] = 20; arr[2] = 30; arr[3] = 40;
  end

  always @(*) begin
    total = 0;
    for (i = 0; i < 4; i = i + 1) begin
      total = total + arr[i]; // 🔁 runs inside always
    end
  end
endmodule
```
### 📌 What is a **Loop Generator?**  
- 🔁 A **loop generator** (`generate for`) is used in **RTL design** to create **repetitive hardware structures automatically**.  
- 🛠️ It is **synthesizable**, meaning the hardware is actually built during elaboration (before simulation).  
- 📍 Unlike a normal `for` loop, it is **not written inside `always` or `initial` blocks** — it is used at the **module level**.  
- ⚡ Commonly used to **instantiate multiple gates, adders, flip-flops, etc.** without manually writing each one.  

---

### 📝 Syntax
```bash
genvar i; // 🔁 declare generator variable
generate
  for(i = 0; i < N; i = i + 1) begin : label_name
    // Hardware instantiation or assign
  end
endgenerate
```
#### ✅ Example: 

```bash
// ⚙️ Create N AND gates using generate-for loop
module and_array #(parameter N = 4) (
  input  [N-1:0] A, B,
  output [N-1:0] Y
);

  genvar i; // 🔁 generator variable
  generate
    for(i = 0; i < N; i = i + 1) begin : AND_GEN
      assign Y[i] = A[i] & B[i]; // 🔌 creates N AND gates
    end
  endgenerate
endmodule
```

### Comparision of 🔁for loop and ⚙️for generator:

| 🏷️ Feature              | 🔁 **For Loop** (Behavioral)             | ⚙️ **For-Generate** (RTL)         |
|--------------------------|------------------------------------------|-----------------------------------|
| 📍 Location              | Inside `always` / `initial` blocks       | Outside, at **module level**      |
| 🎯 Purpose               | Used for **simulation, initialization**  | Used for **hardware replication** |
| 🛠️ Synthesizable         | ❌ No (mostly for sim/testbench)         | ✅ Yes (creates real hardware)     |
| ⏳ Executes during        | Simulation runtime                      | Elaboration (before simulation)   |
| 🔄 Variable type          | `integer`                               | `genvar`                          |
| 🧪 Common usage           | Loops inside testbenches, counters, etc | Repeating logic (gates, modules)  |
| 📝 Example placement      | `always @(*) begin ... for(...) ... end`| `generate ... for(...) ... endgenerate` |

---
## 5. 🔹 Labs on "For Loop" and "For Generate"

### For Loop Labs

#### Case 1: 2-to-1 MUX with For Loop
**Verilog Code**
```bash
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
for(k = 0; k < 4; k=k+1) begin
	if(k == sel)
		y = i_int[k];
end
end
endmodule
```

**GTKWave Simulation**

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/mux_gernerator_sim.png?raw=true" width="800"/>
</p>


#### Case 2: demux using case statement

**Verilog Code**
```bash
module demux_case (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
	case(sel)
		3'b000 : y_int[0] = i;
		3'b001 : y_int[1] = i;
		3'b010 : y_int[2] = i;
		3'b011 : y_int[3] = i;
		3'b100 : y_int[4] = i;
		3'b101 : y_int[5] = i;
		3'b110 : y_int[6] = i;
		3'b111 : y_int[7] = i;
	endcase

end
endmodule
```
**GTKWave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/demux_case_sim.png?raw=true" width="800"/>
</p>

**Realization of Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/Demux_case_synth.png?raw=true" width="800"/>
</p>


#### Case 3: demux using generate if statement


**Verilog Code**
```bash
module demux_generate (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
for(k = 0; k < 8; k++) begin
	if(k == sel)
		y_int[k] = i;
end
end
endmodule
```

**GTKWave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/demux_generator_sim.png?raw=true" width="800"/>
</p>

**Realization of Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/demux_generator_synth.png?raw=true" width="800"/>
</p>

#### Case 4: Ripple Carry Adder

**Verilog Code**
module rca (
    input [7:0] num1,
    input [7:0] num2,
    output [8:0] sum
);
wire [7:0] int_sum;
wire [7:0] int_co;

genvar i;
generate
    for (i = 1; i < 8; i = i + 1) begin
        fa u_fa_1 (.a(num1[i]), .b(num2[i]), .c(int_co[i-1]), .co(int_co[i]), .sum(int_sum[i]));
    end
endgenerate

fa u_fa_0 (.a(num1[0]), .b(num2[0]), .c(1'b0), .co(int_co[0]), .sum(int_sum[0]));

assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule

module fa (input a, input b, input c, output co, output sum);
    assign {co, sum} = a + b + c; // Math magic!
endmodule
```
***Simulating RIpple Carry Adder***
```bash
iverilog fa.v rca.v tb_rca.v
./a.out
gtkwave tb_rca.v
```
**GTKWave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/RCA_sim.png?raw=true" width="800"/>
</p>

**Realization of Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%205/Images/demux_generator_synth.png?raw=true" width="800"/>
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


<div align ="center">🚀 End of Day 5</div>
