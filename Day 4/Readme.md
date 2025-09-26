<div align="center"><h1> 🚀 Day 4: GLS, Synthesis-Simulation Mismatch & Blocking/Non-blocking Statements </h1></div>

<div align="justify"><b>Welcome to Day 4 of the RISC-V Reference SoC Tapeout Program! 🎉 Today you'll explore Gate Level Simulation (GLS), uncover synthesis-simulation mismatches, and master blocking vs non-blocking statements in Verilog! ⚡💻</b></div>
 
### Table of Content
1️⃣ **GLS Concepts & Flow** 🏗  
2️⃣ **Synthesis-Simulation Mismatch** 🔄  
3️⃣ **Lab Excercise** ⏩⏸  
4️⃣ 

## 1️⃣ GLS Concepts & Flow 🏗  

### 🧩 What is GLS?  

GLS (Gate Level Simulation) is the process of simulating the **synthesized netlist** (gates + flip-flops + interconnections) instead of the RTL code.  
It ensures that the **synthesized design** behaves the same as the RTL description before tape-out.  

- 📝 **RTL Simulation** → Fast, functional check using high-level Verilog constructs  
- 🏗 **GLS** → Simulates the *actual synthesized netlist* with standard cells and timing delays  

👉 In simple words:  
**GLS verifies that the hardware (post-synthesis design) behaves exactly like your RTL model.**  

---
## 🎯 Why do we run GLS?  

We run GLS to catch issues that **RTL simulation cannot detect**.  

✔️ **Check Synthesis-Simulation Mismatch**  
- Sometimes, the RTL code may work fine in simulation but synthesize differently.  
- GLS ensures that the netlist still matches the intended design.  

✔️ **Check Reset/Initialization behavior**  
- Flip-flops in netlist don’t always power up in a known state.  
- GLS helps catch missing resets.  

✔️ **Timing Verification**  
- With SDF (Standard Delay Format) back-annotation, GLS verifies real timing delays.  

✔️ **Final Confidence Before Tape-out**  
- Ensures no logical bug exists in the final hardware. 🚀  

---
## 🛠 GLS using Icarus Verilog (iverilog)  

To perform **Gate Level Simulation (GLS)**, we use the **synthesized netlist** along with the **testbench** and simulate it using `iverilog`.  
This verifies that the **post-synthesis design** behaves the same as the RTL. ✅  

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%204/Images/GLSFlow.png?raw=true" width="800"/>
</p>

#### Example:
- consider a netlist
- and uand (.a(a),.b(b))
- or uor (.a(a),.b(b))
- There is a need to define the meaning of and and or
- Thus we need netlist, testbench and verilog models of the standard cells
  
- Netlist consists of all standard cells instantiated and it's meaning is conveyed to the iVerilog using Gate Level Verilog Models. Gate Level Verilog Models can be functional or timing aware. If the gate level models are delay annotated, then GLS can be performed for timing validation also in addition to functional validation.

---

## 2️⃣ Synthesis-Simulation Mismatch 🔄  

### 🔄 What is Synthesis-Simulation Mismatch?  

A **Synthesis-Simulation Mismatch** happens when the behavior of a design during **RTL simulation** 📝 does not match the behavior of the design after **synthesis** 🏗.  

---
### 🧩 Types of Synthesis-Simulation Mismatches  

- 1️⃣ Missing Sensitivity List ⚡  
- 2️⃣ Blocking vs Non-Blocking Assignments 🔀  
- 3️⃣ Non-Standard Verilog Coding 🚫  

#### ⚡ 1.Missing Sensitivity List  

- In Verilog, **sensitivity lists** tell the simulator when to re-evaluate an `always` block.  
- If a signal is **missing** from the sensitivity list, the RTL simulation may not update outputs correctly,  
- but the synthesis tool will still build the correct hardware → leading to a **mismatch**. ⚠️

**Example**
```bash
module mux(
input i0,input i1
input sel,
output reg y
);
always @ (sel)
begin
   if (sel)
            y = i1;
   else 
            y = i0;          
end
endmodule
```
The output of Simulator changes only when the input changes. The output is not evaluated when there is no activity. In the above 2x1 mux code, when select is changing (when select is 1), the output is 1 when input is 1 else the output is 0. The always block evaluates only when there is a transition change in select pin, and is not sensitive (output does not reflect) to changes in the inputs 0 and 1.

**Corrected code for missing sensitivity list:**
```bash
module mux(
input i0,input i1
input sel,
output reg y
);
always @ (*)
begin
   if (sel)
            y = i1;
   else 
            y = i0;        
end
endmodule
```
- mismatch is corrected by having always @ (*) where the always block is evaluated when any signal changes. So, any changes in inputs will also be seen in the output.

#### 2.Blocking vs Non-Blocking Assignments 🔀

**Blocking** (=): Executes statements sequentially, one after another; used in combinational logic.
**Non-blocking** (<=): Executes statements concurrently, all RHS values are evaluated first; used in sequential logic (flip-flops).


#### CAVEAT 1:-
```bash
module code (input clk,input reset,
input d,
output reg q);
always @ (posedge clk,posedge reset)
begin
if(reset)
begin
        q0 = 1'b0;
        q = 1'b0;
end
else
        q = q0;
        q0 = d;    
end
endmodule
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%204/Images/dff_blocking.png?raw=true" width="500"/>
</p>
- The assignments inside the code represent the blocking statements. q0 and q are assigned to 1 bit 0s - so asynchronous reset connection happens. However, in the later parts, q0 is assigned to q and then d gets assigned to q0. If suppose, there is a change in the code.

```bash
module code (input clk,input reset,
input d,
output reg q);
always @ (posedge clk,posedge reset)
begin
if(reset)
begin
        q0 = 1'b0;
        q = 1'b0;
end
else
        q0 = d;
        q = q0;    
end
endmodule
```
- In this case, d is assigned to q0 and then q0 is assigned to q. So, by the time the second statment gets executed, q0 has the value of d. This will lead to implementation of only one flop. Previously, q has the value of q0 and q0 has the value of d - which lead to implementation of 2 storage elements._

#### Always Use Non- Blocking Statements when writng the Sequential circuits code

#### CAVEAT 2:- Causing Synthesis Simulation Mismatch
```bash
module code (input a,b,c
output reg y);
reg q0;
always @ (*)
begin
        y = q0 & c;
        q0 = a|b ;    
end 
endmodule
```
- The code is aimed to create a function of y = (A+B).C. In the above code, when the code enters always block, due to the presence of blocking statements, they get evaulated in order. So y gets evaluated first (q0.C), where the q0 results corresponds to the previous iteration's result. The q0 value gets updated only in the second statement._

When the order of the statements is changed: In this case, a OR b is evaluated first and the latest value is used for calculating y.
```bash
module code (input a,b,c
output reg y);
reg q0;
always @ (*)
begin
        q0 = a|b ;
        y = q0 & c;
end 
endmodule
```
**Therefore there is a paramount importance to run the GLS on the netlist and match the specifications, to ensure there is no simulation synthesis mismatch.**

## 3️⃣ Lab Excercise ⏩⏸  

#### (i) ternary_operator.v
**Verilog Code**

```bash
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
```
**GTK Wave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%204/Images/terinary_mux_sim.png?raw=true" width="800"/>
</p>

**Realization of Synthesis**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%204/Images/terinary_mux_synth.png?raw=true" width="800"/>
</p>

**GLS OUTPUT**

```bash
# After synthesis

# Run Iverilog
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v

# Dumpfile
./a.out

# Waveform viewer
gtkwave tb_ternary_operator_mux.vcd
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%204/Images/ternarymux_gls.png?raw=true" width="800"/>
</p>

#### MISSING SENSITIVITY LIST

#### (ii) bad_mux.v showing mismatch due to missing sensitivity list

**Verilog File**
```bash
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```

**GTK Wave Simulation**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%204/Images/bad_mux_sim.png?raw=true" width="800"/>
</p>
- during Simulation, the logic acts as a latch and during synthesis, it acts as a mux._


**Realization of Synthesis**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%204/Images/bad_mux_synth.png?raw=true" width="800"/>
</p>
- Confirms the functionality of 2x1 mux after synthesis where when the select is low, activity of input 0 is reflected on y. Similarly, when the select is hight, activity of input 1 is reflected on y. Hence there is a synthesis simulation mismatch due to missing sensitivity list._


**GLS Output**
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%204/Images/bad_mux_gls.png?raw=true" width="800"/>
</p>









## 👉 Learning Outcome  
- Run GLS on your synthesized design 🛠  
- Detect and fix synthesis-simulation mismatches 🕵️  
- Write robust RTL using correct assignment styles 🏆  
