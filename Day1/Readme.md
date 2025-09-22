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
