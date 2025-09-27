<div align="center"><h1> 🚀 Day 3 - Combinational and Sequential Optimizations ⚡🔥</h1></div>

<div align="justify">Welcome to <strong>Day 3</strong> of the RISC-V Reference SoC Tapeout Program! 🎉 Today you'll master optimization techniques for both combinational and sequential logic circuits! 💪</div>

## 📚 Overview 🌟

**Combinational circuits** 🔌 are time-independent where outputs depend solely on current inputs - you'll learn advanced logic minimization and gate-level optimizations! **Sequential circuits** ⏰ are time-dependent, clock-driven circuits with state memory - you'll explore timing-driven synthesis, clock gating, and power optimization strategies! 🧠

## 📋 Table of Content 🧪

- 🔧 [1. Introduction to Logic Optimizations 📖](#1-introduction-to-logic-optimizations-📖)
- ⚡ [2. Combinational Logic Optimizations 🧮](#2-combinational-logic-optimizations-🧮)
- 🔄 [3. Sequential Logic Optimizations 🕐](#3-sequential-logic-optimizations-🕐)
- 🎛️ [4. Sequential Optimizations for Unused Outputs 🗑️](#4-sequential-optimizations-for-unused-outputs-🗑️)
- 🎯 [Learning Outcomes 📈](#learning-outcomes-📈)�️

## 🔧 1. Introduction to Logic Optimizations 📖

### What is Logic Optimizations

**Optimization** is the process of **improving a design, system, or algorithm** so that it performs the **same function more efficiently** by minimizing or maximizing parameters such as **power, performance (speed), area, cost, or energy**, while preserving correct functionality. 🔧⚡📐

---

### Types of Optimizations
## 🔹 Types of Optimization

### 1. 🔁 Combinational Optimization
- **Works on:** combinational logic (gates, adders, multipliers).  
- **Goal:** reduce gate count, area, delay, power. ⏱️🏷️  
#### Types of Combinational Optimizations

* Constant Propagation 
	* Direct Optimization technique
* Boolean Logic Optimization.
	* Karnaugh map
	* Quine Mckluskey

#### CONSTANT PROPAGATION

In Constant propagation techniques, inputs that are no way related or affecting the changes in the output are ignored/optimized to simplify the combination logic thereby saving area and power usage by those input pins.
```
Y =((AB)+ C)'
If A = 0
Y =((0)+ C)' = C'
```
#### BOOLEAN LOGIC OPTIMIZATION

Boolean logic optimization is nothing simplifying a complex boolean expression into a simplified expression by utilizing the laws of boolean logic algebra.
```
assign y = a?(b?c:(c?a:0)):(!c)
```
above is simplified as
```
y = a'c' + a(bc + b'ca) 
y = a'c' + abc + ab'c 
y = a'c' + ac(b+b') 
y = a'c' + ac
y = a xor c
```
---

### 2. 🔁 Sequential Optimization
- **Works on:** sequential elements (flip-flops, registers, FSMs).  
- **Goal:** improve timing, reduce registers, save power. 🕒🔋  
#### Types of Sequential Optimizations

* Basic Technique
	* Sequential Constant Propagation
* Advanced Technique
	* State Optimization
	* Retiming
	* Sequential Logic cloning(Floorplan aware synthesis)

#### 🔹 Example: Constant Propagation

##### Before Optimization
- Next-state logic of a flip-flop:
  - `D = (A AND 1) OR 0` 🤦
- Issues:
  - Includes unnecessary **AND** and **OR** operations 🔗
  - Increases **area** 📐 and **power** 🔋

##### After Optimization
- Apply constant propagation:
  - `D = A` ✅
- Benefits:
  - Removes redundant logic ✂️
  - Reduces **area and power** 📉
  - Functional behavior remains **unchanged** ✅

---

## 🔹 Key Rules of Constant Propagation
- `X & 1 = X`  
- `X | 0 = X`  
- `X & 0 = 0`  
- `X | 1 = 1`  

- These rules simplify the next-state logic of sequential circuits, improving performance without changing functionality. 🧠⚡

---

### ⚡  2. Combinational Logic Optimizations 🧮
```bash
//to view all optimization files
$ ls *opt_check*

//Invoke Yosys
$ yosys

//Read library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

//Read Design
$ read_verilog opt_check.v

//Synthesize Design - this controls which module to synthesize
$ synth -top opt_check

//To perform constant propogation optimization
$ opt_clean -purge

//Generate Netlist
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

//Realizing Graphical Version of Logic for single modules
$ show
```

#### 1.opt_check.v
**Logic from Verilog file**
```bash
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```
- <b>value of y depends on a, y = ab</b>

**Realization Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/opt_check.png?raw=true" width="600"/>
</p>

**optimized realization shows a 2-input AND gate being implemented.**

#### 2.opt_check2
**Logic from Verilog file**
```bash
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```
- <b> value of y depends on a, y = a+b</b>

**Realization Logic**

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/opt_check2.png?raw=true" width="600"/>
</p>

**optimized realization shows the 2-input OR gate being implemented. Although OR gate can be realized using NOR, it can lead to having stacked PMOS configuration which is not a design recommendation. So the OR gate is realized using NAND and NOT gates (which has stacked NMOS configuration).**

#### 3.opt_check3
***Logic from Verilog file**
```bash
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```
- <b>value of y depends on a, y = abc</b>

**Realization Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/opt_check3.png?raw=true" width="600"/>
</p>

**Optimized graphical realization thus shows 3-input AND gate being implemented**

#### 4.opt_check4
**Logic from Verilog file**
```bash
module opt_check4 (input a , input b , input c , output y);
 assign y = a?(b?(a & c ):c):(!c);
 endmodule
```
- <b>The value of y depends on a, y = a'c + ac</b>

**Realization Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/opt_check4.png?raw=true" width="600"/>
</p>

**Optimized graphical realization thus shows A XNOR C gate being implemented.**

---

### 🔄 3. Sequential Logic Optimizations 🕐
```bash
//To view all optimization files
$ ls *df*const*

//To open multiple files 
$ dff_const1.v -o dff_const2.v

//Performing Simulation

//Load the design in iVerilog by giving the verilog and testbench file names
$ iverilog dff_const1.v tb_dff_const1.v

//To dump the VCD file
$ ./a.out

//To load the VCD file in GTKwaveform
$ gtkwave tb_dff_const1.vcd

//Performing Synthesis

//Invoke Yosys
$ yosys

//Read library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd_-tt_025C_1v80.lib

//Read Design
$ read_verilog dff_const1.v

//Synthesize Design - this controls which module to synthesize
$ synth -top dff_const1

//There will be a separate flop library under a standard library
//so we need to tell the design where to specifically pick up the DFF

//But here we point back to the same library and tool looks only for DFF instead of all cells
$ dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd_-tt_025C_1v80.lib

//Generate Netlist
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd_-tt_025C_1v80.lib

//Realizing Graphical Version of Logic for single modules
$ show 
```
#### 1)dff_const1
**Verilog Code**
```bash
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end

endmodule
```

**GTKWave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const1.png?raw=true" width="800"/>
</p>

**Realization Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const1_syn.png?raw=true" width="800"/>
</p>


#### 2)dff_const2
**Verilog Code**
```bash
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end
endmodule
```

**GTKWave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const2.png?raw=true" width="800"/>
</p>

**Realization Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const2_syn.png?raw=true" width="800"/>
</p>


#### 3)dff_const3
**Verilog Code**
```bash
module dff_const3(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule
```

**GTKWave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const3.png?raw=true" width="800"/>
</p>

**Realization Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const3_syn.png?raw=true" width="800"/>
</p>


#### 4)dff_const4
**Verilog Code**
```bash
module dff_const4(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b1;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule
```
**GTKWave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const4.png?raw=true" width="800"/>
</p>

**Realization Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const4_syn.png?raw=true" width="800"/>
</p>


#### 5)dff_const5
**Verilog Code**
```bash
module dff_const5(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b0;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule
```
**GTKWave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const5.png?raw=true" width="800"/>
</p>

**Realization Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/dff_const5_syn.png?raw=true" width="800"/>
</p>

---

### 🎛️ 4. Sequential Optimizations for Unused Outputs 🗑️
```bash
//Steps Followed for each of the unused output optimization problems:

//opening the file
$ gvim counter_opt.v

//Invoke Yosys
$ yosys

//Read library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

//Read Design
$ read_verilog opt_check.v

//Synthesize Design - this controls which module to synthesize
$ synth -top opt_check

//To perform constant propogation optimization
$ opt_clean -purge

//Generate Netlist
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd_-tt_025C_1v80.lib

//Realizing Graphical Version of Logic for single modules
$ show 
```
#### counter_opt
**Verilog code**
```bash
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
- <b>If there is a reset, the counter is intialised to 0, else it is incremented - performing like an upcounter. Since it is a 3 bit signal, the counter rolls back after 7. However, the final output q is sensing only the count [0], so the bit is toggling in every clock cycle (000, 001, 010 ...111). The other two outputs are unused and does not create any output dependency. Hence, these unused outpus need not be present in the design.</b>

**Realization Logic**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%203/Images/counter_opt.png?raw=true" width="800"/>
</p>

**optimized realization of output Q (count0) being fed to NOT gate so as to perform the toggle function. The other outputs which has no dependency on the primary out is optimized off._**


## 🎯 Learning Outcomes 📈

Through comprehensive labs on:
- **Combinational logic optimizations** ⚙️🔧
- **Sequential logic optimizations** 🔄⚡ 
- **Unused output optimizations** 🎛️✂️
 
<div align="center">
<b>🌅 End of Day 3 Summary</b>
</div>

