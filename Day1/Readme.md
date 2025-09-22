<div align="center">
  <h1>🚀 Day 1 - Introduction to Verilog RTL Design and Synthesis</h1></div>
  <div align="justify">
  <p>🎯 Welcome to <strong>Day 1</strong> of the RISC-V Reference SoC Tapeout Program! Today, you'll learn <strong>Verilog RTL design</strong> and <strong>synthesis</strong> using open-source tools including <strong>Icarus Verilog (iverilog)</strong>, <strong>GTKWave</strong>, and <strong>Yosys</strong>. ⚡ Through hands-on labs and practical exercises, you'll master simulation workflows, waveform analysis, and logic synthesis with the industry-standard <strong>Sky130 PDK</strong>. 💡 Get ready to build a strong foundation in digital design using cutting-edge open-source tools!</p>
</div>
---

## Table of Content
1. Simulator, Design and Testbench

---

## 🔧1. What is Simulator, Design and Testbench
### 🖥️ Simulator
A **Simulator** is software that runs the <b>design</b> along with the <b>testbench</b>, allowing designers to check and debug the circuit’s functionality before building the actual hardware.🚀

### 📋 Design
A **Design** is the HDL code that describes the behavior or structure of a digital circuit. It acts as the blueprint for the hardware.🏗️

### 🧪 Testbench
A **Testbench** is used to apply inputs to the design, monitor outputs, and verify that the circuit works correctly under different conditions.✅

---
## ⚙️ How Simulator Works

- 👀The simulator continuously looks for changes in the input signals 
- ⚡Upon changes to the input, the output is evaluated 
- 📊If there's no change in input → no change in output 
- 🔄The simulator is always looking for change in the value of input to trigger evaluation
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/Stimulator_Workflow.png?raw=true" width="600"/>
</p>

<div align="center">
   <b>Simulator WorkFlow</b>
</div>

---

## 📟 Introduction to Iverilog

- 🚀 Icarus Verilog (iverilog) is a powerful open-source Verilog simulation and synthesis tool
- 💻 It's a free and lightweight simulator that compiles Verilog HDL code for digital circuit simulation
- 🔧 Iverilog supports IEEE 1364 Verilog standards and provides comprehensive simulation capabilities
- ⚡ The tool generates executable simulation files from your Verilog design and testbench code
- 📊 Works seamlessly with GTKWave to generate and view waveforms through VCD files
- 🎯 Perfect for educational purposes and professional RTL design verification workflows
- 🌟 Cross-platform support for Windows, Linux, and macOS making it accessible to all developers

  <p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/Iverilog_Simulation_Flow.png?raw=true" width="600"/>
</p>

<div align="center">
   <b>IVerilog Simulation WorkFlow</b>
</div>

  ---

  ## 3. Lab: Simulating a 2-to-1 Multiplexer

  ### Step 1 Clone SKY130 Files
  ```bash
mkdir VLSI
cd VLSI
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
### Step 2 Simulate the Design 
``` bash
# Compile the design and testbech
iverilog good_mux.v tb_good_mux.v

# Run the Simulaion
./a.out
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/RTLCode_Run.png?raw=true" width="600"/>
</p>

<div align="center">
   <b>Linux Run Command</b>
</div>

``` bash
# View Waveform
gtkwave tb_good_mux.vcd
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/Output_Mux.png?raw=true" width="600"/>
</p>

<div align="center">
   <b>2:1 Mux Waveform</b>
</div>

---

## 4. Verilog Code Analysis
 ``` bash
#To view the Code
gvim tb_good_mux.v -o good_mux.v
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/MUX_Code.png?raw=true" width="600"/>
</p>

---
## 5. Introduction to Yosys and Gate Libraries

Yosys is an open-source framework for Verilog RTL synthesis. It’s mainly used in digital design to convert high-level Verilog or SystemVerilog descriptions into a gate-level netlist that can be used for FPGA or ASIC implementation.

<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/Yosys_Setup.png?raw=true" width="600"/>
</p>

### Features
- 🚀 RTL Synthesis: Converts RTL (Verilog/SystemVerilog) to gate-level netlists. Supports common constructs like always, assign, if, case, for.
- ⚡ Logic Optimization: Performs optimizations like constant propagation, boolean simplification, and retiming for flip-flops to reduce area and improve speed.
- 🎯 Technology Mapping: Maps generic RTL logic to a specific target technology library (ASIC or FPGA cells).
- ✅ Formal Verification Support: Can be used with formal verification tools to check equivalence between RTL and netlist.
- 🌐 Support for Multiple Backends: Can target different formats for simulation, FPGAs, or ASICs (Verilog, BLIF, EDIF, etc.).
- 🏗️ Hierarchical Design Support: Handles modules and hierarchical designs efficiently, maintaining structure during synthesis.
- 🔓 Open-Source Extensible: You can extend Yosys with scripts or plugins for custom synthesis flows.

### Commands in yosys 
- <b>read_verilog</b>: Reads your RTL Verilog source files into the synthesis tool.
- <b>read_liberty</b>: Reads the standard cell library (.lib) files that describe the timing, power, and area characteristics of the technology you are targeting.
- <b>write_verilog</b>: Writes out the synthesized RTL netlist after optimizations are applied.

### Why Do Libraries Have Different Gate “Flavors”?

#### ⚡ Faster Cells vs Slower Cells

🔌 Load in Digital Logic circuit → Capacitance
⚡ Faster the charging/discharging of capacitance → Lesser the cell delay
🚀 To charge/discharge the capacitance fast, we need transistors capable of sourcing more current
📏 Wider transistors → Low Delay → More Area and Power as well!!
🔻 Narrow transistors → More Delay → Less Area and Power
⚠️ Faster cells don't come free, they come at penalty of area and power

#### 🎯 Selection of Cells

🎛️ Need to guide the Synthesizer to select the flavor of cells that is optimum for the implementation of logic circuit
⚡ More use of faster cells
❌ Bad circuit in terms of Power and Area
⚠️ Hold time violations??
🐌 More use of slower cells
📉 Sluggish circuit, may not meet the performance need
🎯 The guidance offered to the Synthesizer → "Constraints"

#### Verify the Synthesis
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/Synthesis_Verify.png?raw=true" width="600"/>
</p>
---

## 6. Synthesis Lab activity with Yosys

### Step by Step Yosys Flow

''' bash
# Invoke Yosys
yosys
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/Yosys.png?raw=true" width="600"/>
</p>
``` bash
# Read the Library file
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read the verilog file
read_verilog good_mux.v

# Synthesis Design
synth -top good_mux

# Technology mapping
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Visualize the gate-level netlist 
show
```
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/Gate_level.png?raw=true" width="600"/>
</p>

```bash
# Write Netlist File
write_verilog good_mux_netlist.v
'''
<p align="center">
  <img src="https://github.com/Ragul-2005/RAGUL_T_RISCV_SOC_TAPEOUT_VSD_Week_1/blob/main/Day1/Images/Netlist.png?raw=true" width="600"/>
</p>

