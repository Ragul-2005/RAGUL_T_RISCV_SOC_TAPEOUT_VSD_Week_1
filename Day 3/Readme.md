<div align="center"># 🚀 Day 3 - Combinational and Sequential Optimizations ⚡🔥</div>

<div align="justify">Welcome to <strong>Day 3</strong> of the RISC-V Reference SoC Tapeout Program! 🎉 Today you'll master optimization techniques for both combinational and sequential logic circuits! 💪</div>

## 📚 Overview 🌟

**Combinational circuits** 🔌 are time-independent where outputs depend solely on current inputs - you'll learn advanced logic minimization and gate-level optimizations! **Sequential circuits** ⏰ are time-dependent, clock-driven circuits with state memory - you'll explore timing-driven synthesis, clock gating, and power optimization strategies! 🧠

## 📋 Table of Content 🧪

- 🔧 1. Introduction to Optimizations 📖
- ⚡  2. Combinational Logic Optimizations 🧮
- 🔄 3. Sequential Logic Optimizations 🕐
- 🎛️ 4. Sequential Optimizations for Unused Outputs 🗑️

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

#### COMBINATIONAL LOGIC OPTIMIZATION  

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







## 🎯 Learning Objectives 📈

Through comprehensive labs on:
- **Combinational logic optimizations** ⚙️🔧
- **Sequential logic optimizations** 🔄⚡ 
- **Unused output optimizations** 🎛️✂️

