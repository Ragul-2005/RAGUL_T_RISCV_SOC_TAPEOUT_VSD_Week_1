  <h1>🚀 Day 2 - Timing Libraries, Hierarchical vs Flat Synthesis, and Efficient Flop Coding Styles ⚡</h1>
  </div>

  <p align="justify">⚡ Welcome to <strong>Day 2</strong> of the RISC-V Reference SoC Tapeout Program! Today, you'll dive deep into <strong>timing libraries (.libs)</strong>, explore <strong>hierarchical vs flat synthesis</strong> methodologies, and master <strong>efficient flop coding styles</strong> for optimal design implementation. 🏗️ Through comprehensive analysis and hands-on experiments, you'll learn to make informed synthesis decisions, optimize sequential logic, and achieve better PPA (Power, Performance, Area) results using advanced synthesis techniques. 🔄 Get ready to elevate your digital design skills with professional-grade optimization strategies!</p>

  ---


## Table of Content
1. Introduction to Timing Libraries (.libs) ⏰
2. Hierarchical vs Flat Synthesis 🏗️
3. Submodule Synthesis
4. Various Flop Coding Styles and Optimization 🔄

---

## 📚 1.Introduction to Timing Libraries (.libs) 

### SKY130 PDK Overview

The **SKY130 PDK*** is an open-source Process Design Kit based on SkyWater Technology's 130nm CMOS technology. It provides essential models and libraries for integrated circuit (IC) design, including timing, power, and process variation information.


## 🏗️ SKY130 Library Structure

### 📝 Naming Convention
```
sky130_[source]_[type]_[name]__[corner]
```

**Focus Library:** `sky130_fd_sc_hd__tt_025C_1v80` 🎯
- 🏭 `sky130` → Process technology name
- 🔧 `fd` → Foundry provided (Library Source)
- ⚙️ `sc` → Standard Cell (Library Type)
- 📦 `hd` → High Density variant
- 😐 `tt` → **Typical-Typical**
- 🌡️ `025C` → **25°C**
- ⚡ `1v80` →  **1.8V**

## 📊 TT Corner Analysis

### 🌡️ Typical-Typical (TT) Corner Characteristics
<div align="center">
  
| **Parameter** | **Value** | **Description** |
|---------------|-----------|-----------------|
| **Process** | 😐 TT (Typical-Typical) | Nominal fabrication conditions |
| **Temperature** | 🌡️ 25°C | Room temperature operation |
| **Voltage** | ⚡ 1.8V | Nominal supply voltage |
| **Use Case** | 📊 Baseline analysis | Standard operating conditions |

</div>


## 🔧 Liberty File Structure: `sky130_fd_sc_hd__tt_025C_1v80.lib`

### ⚙️ Technology Parameters
```liberty
library(sky130_fd_sc_hd__tt_025C_1v80) {
  technology: "CMOS" 🔬
  delay_model: "table_lookup" 📈
  time_unit: "1ns" ⏰
  voltage_unit: "1V" ⚡
  current_unit: "1mA" 🔌
  capacitive_load_unit: "1pf" 📡
  
  /* Operating Conditions */
  nom_process: 1.0 😐
  nom_temperature: 25.0 🌡️
  nom_voltage: 1.8 ⚡
}
```
### Opening and Exploring the .lib File

```bash
# to open and explore the file
gvim ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/library.png?raw=true" width="600"/>
</p>
<div align="center">
   <h3>.lib Library File</h3>
</div>

---
## 2. Hierarchical vs Flatten Synthesis 🏗️

### 🏗️ Hierarchical Synthesis

Hierarchical synthesis is a method where a digital design is synthesized by preserving its module hierarchy instead of flattening everything into a single block. It enables modular reuse, faster compilation, and easier debugging, but may reduce global optimization compared to flat synthesis.

### 🔧 Features
- 📦 **Preserves Design Hierarchy** - Maintains original module boundaries
- 🔄 **Module-by-Module Processing** - Synthesizes each block independently  
- 🎯 **Boundary Constraints** - Respects interface definitions between modules
- 📊 **Incremental Compilation** - Re-synthesize only modified modules

### ✅ Advantages
- 🚀 **Faster Compilation** - Parallel processing of independent modules
- 🐛 **Better Debugging** - Easy to trace issues to specific modules
- 🔄 **Incremental Updates** - Modify single blocks without full re-synthesis
- 💾 **Memory Efficient** - Lower peak memory usage during synthesis
- 👥 **Team Development** - Multiple engineers can work on different blocks
- 🎯 **Design Reuse** - Modules can be easily ported to other projects

### ❌ Disadvantages
- 🚧 **Suboptimal Optimization** - Cannot optimize across module boundaries
- ⏰ **Timing Challenges** - Interface timing may not be globally optimal
- 📊 **Area Overhead** - Duplicate logic cannot be shared between modules
- 🔌 **Interface Constraints** - Fixed I/O requirements limit flexibility
- 🎯 **Local Optimization Only** - Misses global optimization opportunities

### Method for Hierarchical Synthesis

```bash
# Load the standard cell library in Liberty format (for mapping and timing)
read_liberty -lib ../Lib/sky130_fd_sc_hd__tt_025C-1v80.lib

# Read the Verilog source file (design with multiple modules)
read_verilog multiple_modules.v

# Run synthesis with the specified top module
synth -top multiple_modules

# Perform technology mapping using the standard cell library
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Show the synthesized design (schematic viewer)
show multiple_modules

# Write out the synthesized Verilog netlist
write_verilog multiple_modules.v
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/Hierarchical%20Synthesis.png?raw=true" width="600"/>
</p>
<div align="center">
<h3> Hierarchical Synthesis</h3>
  </div>

---

## 🎯 Flatten Synthesis
Flatten synthesis is a method where the synthesis tool removes the design hierarchy and combines all modules into a single-level netlist. This allows maximum global optimization for area, speed, and power, but makes debugging harder and prevents module-level reuse.

### 🔧 Features
- 🌐 **Single Design Unit** - Flattens entire hierarchy into one module
- 🔄 **Global Optimization** - Cross-boundary logic optimization enabled
- 📊 **Unified Netlist** - Single flat netlist output
- 🎯 **Complete Logic Sharing** - Maximum resource sharing potential

### ✅ Advantages
- 🎯 **Global Optimization** - Best possible timing and area results
- ⏰ **Superior Timing Closure** - No hierarchical timing boundaries
- 📦 **Maximum Area Efficiency** - Optimal logic sharing and reduction
- 🔄 **Cross-Module Logic Sharing** - Duplicate functions eliminated
- 📊 **Better QoR (Quality of Results)** - Optimal Power, Performance, Area
- 🎯 **Holistic View** - Synthesis sees complete design context

### ❌ Disadvantages
- 🐌 **Slower Compilation** - Must process entire design together
- 💾 **High Memory Usage** - Peak memory requirements increase significantly
- 🐛 **Difficult Debugging** - Harder to trace issues in flat netlist
- 🔄 **Full Re-synthesis** - Any change requires complete re-compilation
- 📊 **Scalability Issues** - May not handle very large designs
- ⏱️ **Longer Runtimes** - Exponential increase with design size

- ### Method for Flatten Synthesis

  
```bash
# Load the standard cell library in Liberty format (for mapping and timing)
read_liberty -lib ../Lib/sky130_fd_sc_hd__tt_025C-1v80.lib

# Read the Verilog source file (design with multiple modules)
read_verilog multiple_modules.v

# Run synthesis with the specified top module
synth -top multiple_modules

# Perform technology mapping using the standard cell library
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
#To covert Hierarchiarly to Flatten
flatten

# Show the synthesized design (schematic viewer)
show multiple_modules

# Write out the synthesized Verilog netlist
write_verilog multiple_modules.v
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/Flatten%20Synthesis.png?raw=true" width="600"/>
</p>
<div align="center">
<h3> Flatten Synthesis</h3>
  </div>

---

## 📊 Comparison Summary
<div align="center">
  
| **Aspect** | **Hierarchical** 🏗️ | **Flat** 🎯 |
|------------|---------------------|-------------|
| **Compilation Time** | 🚀 Fast | 🐌 Slow |
| **Memory Usage** | 💾 Low | 💾 High |
| **Optimization Quality** | 📊 Local | 🎯 Global |
| **Debugging** | 🐛 Easy | 🐛 Difficult |
| **Timing Closure** | ⏰ Challenging | ⏰ Better |
| **Area Efficiency** | 📦 Good | 📦 Optimal |
| **Design Changes** | 🔄 Incremental | 🔄 Full rebuild |

</div>

---

## 3. Submodule Synthesis
Submodule synthesis is a hierarchical synthesis approach in digital design where individual modules (submodules) of a system are synthesized separately before being integrated into the top-level design. Instead of flattening the entire design, each submodule—like an ALU, multiplier, or memory block—is compiled into a technology-mapped netlist independently. This preserves modularity, allows reuse of pre-synthesized blocks, speeds up compilation, and makes debugging easier, though it may limit global optimizations across module boundaries compared to flat synthesis.

### Features
- ⚙️ Modularity → Each submodule is synthesized independently, making the design easier to manage.
- 🔁 Reusability → Pre-synthesized blocks (e.g., ALU, multiplier) can be reused across multiple projects.
- ⏱️ Faster compilation → Only modified submodules need to be re-synthesized, reducing runtime.
- 🐞 Isolation for debugging → Errors and timing violations can be checked at the submodule level.
- 📦 Supports IP integration → Third-party or pre-verified IP cores can be dropped in as black boxes.
- 📈 Scalability → Well-suited for large ASIC/FPGA projects where multiple teams work on different modules.

### Implementation of submodule Synthesis

```bash
# Load the standard cell library in Liberty format (for mapping and timing)
read_liberty -lib ../Lib/sky130_fd_sc_hd__tt_025C-1v80.lib

# Read the Verilog source file (design with multiple modules)
read_verilog multiple_modules.v

# Run synthesis with the specified top module
synth -top sub_module1

# Perform technology mapping using the standard cell library
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib


# Show the synthesized design (schematic viewer)
show 


```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/Submodule%20Synthesis.png?raw=true" width="600"/>
</p>
<div align="center">
<h3> Submodule Synthesis</h3>
  </div>

---
## 4. Various Flop Coding Styles and Optimization 🔄

## Various Flop Coding Styles

### What is Flip Flop

- A 🔄 <strong>flip-flop</strong> is a fundamental ⌛ <b>sequential logic circuit</b> used in digital systems to store <strong>one bit of information</strong>.
- ⚡ Unlike <b>combinational circuits</b>, its output depends on the <strong>current input</strong> and the <strong>previous state</strong>.  
- 🧠 Acts as a <strong>memory element</strong> in digital systems.  
- ⏱️ <b>Clock-controlled</b> → changes output only at specific <strong>clock edges</strong> (rising/falling).  
- 🔗 Helps in <b>synchronizing data flow</b> across circuits.  
- 💾 Widely used in <strong>registers</strong>, 🔢 <strong>counters</strong>, 🔄 <strong>finite state machines (FSMs)</strong>, and 🖥️ <strong>processors</strong>.  
- ⭐ Common types: <b>SR, D, JK, and T Flip-Flops</b>, with the <strong>D Flip-Flop</strong> being the most popular for <b>data storage & transfer</b>.

### Types of Flip Flop
1. SR Flip Flop(Set-Reset)
2. D Flip Flop(Delay/Data)
3. JK Flip Flop
4. T Flip Flop(Toggle)

### Coding Style
## 🔹 Types of D Flip-Flops (DFF) used in Coding Style

1. 📌 <strong>Basic DFF</strong>  
   - Stores the input <code>D</code> on the clock edge.  
   - No reset or set functionality.  

2. 🔄 <strong>DFF with Asynchronous Reset (dffasyncre)</strong>  
   - Has an extra <code>reset</code> input.  
   - When <code>reset = 1</code>, output <code>Q → 0</code> immediately (ignores clock).  
   - Used when reset must work independent of clock.  

3. ⚡ <strong>DFF with Asynchronous Reset and Set (dffasyncres_set)</strong>  
   - Has both <code>reset</code> and <code>set</code> inputs.  
   - <code>reset = 1 → Q = 0</code>, <code>set = 1 → Q = 1</code>, works without waiting for clock.  
   - Priority usually: Reset > Set > D (depends on coding style).  

4. ⏱️ <strong>DFF with Synchronous Reset (dffsyncres)</strong>  
   - Reset input is checked only at the active clock edge.  
   - <code>reset = 1</code> → Q becomes 0, but only on clock edge.  
   - Used for controlled reset aligned with the clock.
     
## 🔹 Why Flip-Flops Are Used Between Combinational Circuits 

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/Combinational%20circuit.png?raw=true" width="600"/>
</p>

1. ⌛ <strong>Control Propagation Delay (Avoid Accumulation)</strong>  
   - Long chains of combinational logic → large delay.  
   - Without flip-flops, total delay may exceed <code>clock period</code>.  
   - Flip-flops break logic into smaller stages (pipelining), so each stage delay < clock cycle.  

2. 🧠 <strong>Ensure Data Stability (Setup/Hold)</strong>  
   - Flip-flops sample inputs only at <code>clock edges</code>.  
   - Glitches/hazards in combinational circuits don’t affect final result.  
   - Output is stable at clock boundaries.  

3. 🔗 <strong>Synchronize Signals</strong>  
   - Different paths → different delays (skew).  
   - Flip-flops align signals at the same <code>clock boundary</code>.  
   - Prevents race conditions & metastability.  

4. 🚀 <strong>Increase Maximum Clock Frequency</strong>  
   - Inserting flip-flops reduces the <code>critical path</code>.  
   - Allows design to run at higher clock speeds.  
   - Each stage delay becomes smaller → faster operation.  

5. 🛡️ <strong>Maintain Predictable Timing</strong>  
   - Combinational delay changes with <code>PVT (Process, Voltage, Temperature)</code>.  
   - Flip-flops act as timing checkpoints.  
   - Ensures valid outputs at every clock cycle.  

## 🔹 Verilog Codes for Different Types of D Flip-Flops
1️⃣ DFF with Asynchronous Reset (dffasyncre)

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/dff_asyncres.png?raw=true" width="600"/>
</p>

- **Asynchronous reset**: Overrides clock, setting q to 0 immediately.
- **Edge-triggered**: Captures d on rising clock edge if reset is low.

2️⃣ DFF with Asynchronous Reset and Set (dffasyncres_set)

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/dff_async_set.png?raw=true" width="600"/>
</p>

- **Asynchronous set**: Overrides clock, setting q to 1 immediately.

 3️⃣ DFF with Synchronous Reset (dffsyncres)
 
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/dff_syncres.png?raw=true" width="600"/>
</p>

- **Synchronous reset**: Takes effect only on the clock edge.

## Simulation Synthesis of Different Types of D Flip-Flops

### Icarus Verilog Simulation

``` bash
1. # Compile:
   iverilog <filename>.v tb_<filename>.v
   
2. # Run:
   ./a.out
   
3. # View Waveform:
   gtkwave <filename>.vcd
```
<b>filename</b>:  dff_asyncres, dff_asyncres_set, dff_syncres

1️⃣ DFF with Asynchronous Reset (dff_asyncres)

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/dff_asyncres_wave.png?raw=true" width="600"/>
</p>

2️⃣ DFF with Asynchronous Reset and Set (dff_asyncres_set)

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/dff_async_set_wave.png?raw=true" width="600"/>
</p>

3️⃣ DFF with Synchronous Reset (dff_syncres)

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/dff_syncres_wave.png?raw=true" width="600"/>
</p>

### Synthesis with Yosys

```bash

1. Start Yosys:

   yosys

2. Read Liberty library:

   read_liberty -lib /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib

3. Read Verilog code:

   read_verilog /path/to/<filename>.v

4. Synthesize:

   synth -top <modulename>

5. Map flip-flops:

   dfflibmap -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib

6. Technology mapping:

   abc -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib

7. Visualize the gate-level netlist:
   
   show
```
- <b>filename</b>:  dff_asyncres, dff_asyncres_set, dff_syncres
- <b>modulename</b>:  dff_asyncres, dff_asyncres_set, dff_syncres

1️⃣ DFF with Asynchronous Reset (dff_asyncres)

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/dff_asyncres_synth.png?raw=true  " width="600"/>
</p>

2️⃣ DFF with Asynchronous Reset and Set (dff_asyncres_set)

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/dff_async_set_synth.png?raw=true" width="600"/>
</p>

3️⃣ DFF with Synchronous Reset (dff_syncres)

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day%202/Images/dff_syncres_synth.png?raw=true" width="600"/>
</p>

## Optimization


### 🔧 Synthesis Optimization 

Synthesis optimization 🔧 means mapping RTL to standard cells in a way that improves area 🏗️, timing ⏱️, and power 🔋. The tool resizes gates, inserts buffers 🪛, and picks the best cell variants (hd, hs, lp) ⚡ to meet design constraints efficiently.

1️⃣ Area Optimization 🏗️
   - Use smaller cells ➡️ `NAND2_1` instead of `NAND2_4`
   - Goal: reduce area 🟦
   - Tradeoff: smaller = slower 🐢

2️⃣ Timing Optimization ⏱️
   - Upsize cells on critical path ➡️ `INV_8` instead of `INV_2`
   - Insert buffers 🪛 to fix long wires
   - Logic restructuring 🔄
   - Goal: meet setup/hold requirements ⚡

3️⃣ Power Optimization 🔋
   - Use low-power cells where possible 🌱
   - Insert clock gating 🚪 to cut dynamic power
   - Goal: reduce leakage & switching power

4️⃣ Multi-Vt & Multi-Drive 🔀
   - SKY130 has `hd` (high-density), `hs` (high-speed), `lp` (low-power)
   - Pick right mix 🧩 for performance vs. leakage
   - Goal: balance speed 🏎️ vs. power 🔋

### Functional Summary ✨

We are taking mul2 and mult8 as examples of simple Verilog multiplier modules. The mul2 module multiplies a 3-bit input a by 2, producing a 4-bit output, while the mult8 module multiplies the same 3-bit input by 9, producing a 6-bit output. These examples show how synthesis in the Sky130 PDK can optimize multiplications using shift and add operations, which helps reduce area 🏗️, lower power consumption 🔋, and improve timing ⏱️.

#### Code for multiply by 2
```bash
```
#### Code for multiply by 8
```bash
```





#### Truth Table for mul2 module

<div align="center">

  
/*
Truth Table for mul2 module

a[2:0] | a(dec) | y[3:0] | y(dec)
-----------------------------------
000     |   0    | 0000    |   0
001     |   1    | 0010    |   2
010     |   2    | 0100    |   4
011     |   3    | 0110    |   6
100     |   4    | 1000    |   8
101     |   5    | 1010    |  10
110     |   6    | 1100    |  12
111     |   7    | 1110    |  14
*/


</div>

#### Truth Table for mul8 module

<div align="center">


|a[2:0] | a(dec) | y[5:0] | y(dec) |
-----------------------------------
|000     |   0    | 000000  |   0|
|001     |   1    | 001001  |   9|
|010     |   2    | 010010  |  18|
|011     |   3    | 011011  |  27|
|100     |   4    | 100100  |  36|
|101     |   5    | 101101  |  45|
|110     |   6    | 110110  |  54|
|111     |   7    | 111111  |  63|


</div>



---








  
  
