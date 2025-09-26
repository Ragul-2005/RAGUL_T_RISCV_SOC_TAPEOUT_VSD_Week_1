<div align="center"><h1> 🚀 Day 4: GLS, Synthesis-Simulation Mismatch & Blocking/Non-blocking Statements </h1></div>

<div align="justify"><b>Welcome to Day 4 of the RISC-V Reference SoC Tapeout Program! 🎉 Today you'll explore Gate Level Simulation (GLS), uncover synthesis-simulation mismatches, and master blocking vs non-blocking statements in Verilog! ⚡💻</b></div>
 
### Table of Content
1️⃣ **GLS Concepts & Flow** 🏗  
2️⃣ **Synthesis-Simulation Mismatch** 🔄  
3️⃣ **Blocking vs Non-Blocking Statements in Verilog** ⏩⏸  
4️⃣ **Caveats with Blocking Statements** ⚡  

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



## 👉 Learning Outcome  
- Run GLS on your synthesized design 🛠  
- Detect and fix synthesis-simulation mismatches 🕵️  
- Write robust RTL using correct assignment styles 🏆  
