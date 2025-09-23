<div align="center">
  <h1>🚀 Day 2 - Timing Libraries, Hierarchical vs Flat Synthesis, and Efficient Flop Coding Styles ⚡</h1>
  </div>

  <p align="justify">⚡ Welcome to <strong>Day 2</strong> of the RISC-V Reference SoC Tapeout Program! Today, you'll dive deep into <strong>timing libraries (.libs)</strong>, explore <strong>hierarchical vs flat synthesis</strong> methodologies, and master <strong>efficient flop coding styles</strong> for optimal design implementation. 🏗️ Through comprehensive analysis and hands-on experiments, you'll learn to make informed synthesis decisions, optimize sequential logic, and achieve better PPA (Power, Performance, Area) results using advanced synthesis techniques. 🔄 Get ready to elevate your digital design skills with professional-grade optimization strategies!</p>

  ---


## Table of Content
1. Introduction to Timing Libraries (.libs) ⏰
2. Hierarchical vs Flat Synthesis 🏗️
3. Various Flop Coding Styles and Optimization 🔄

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
| **Parameter** | **Value** | **Description** |
|---------------|-----------|-----------------|
| **Process** | 😐 TT (Typical-Typical) | Nominal fabrication conditions |
| **Temperature** | 🌡️ 25°C | Room temperature operation |
| **Voltage** | ⚡ 1.8V | Nominal supply voltage |
| **Use Case** | 📊 Baseline analysis | Standard operating conditions |


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
   <b>.lib Library File</b>
</div>

---




  
  
